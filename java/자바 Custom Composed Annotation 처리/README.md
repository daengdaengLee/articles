# 자바 Custom Composed Annotation 처리

## TL;DR

- 다른 애노테이션을 조합하여 애노테이션을 만들 수 있습니다.
- `Annotation.annotationType().getAnnotations()` 를 통해 애노테이션 위에 선언한 다른 애노테이션 정보를 읽을 수 있습니다.

## Problem

- 애노테이션에 붙이는 애노테이션을 메타 애노테이션이라고 합니다.
- 자바는 기본적으로 `@Target`, `@Retention` 같은 정해진 메타 애노테이션을 제외하고 커스템 메타 애노테이션을 지원하지 않습니다.

문제 예시:

- Simple 애노테이션과 Composed 애노테이션을 정의합니다.
  ```java
  @Target(ElementType.TYPE)
  @Retention(RetentionPolicy.RUNTIME)
  public @interface SimpleAnnotation {
  }
  ```
  ```java
  @SimpleAnnotation
  @Target(ElementType.TYPE)
  @Retention(RetentionPolicy.RUNTIME)
  public @interface ComposedAnnotation {
  }
  ```
- Composed 애노테이션을 붙인 클래스를 정의합니다.
  ```java
  @ComposedAnnotation
  public class ComposedAnnotated {
  }
  ```
- 아래와 같이 Simple 애노테이션이 붙어있는지 확인해 봅시다.
  ```java
  public class SimpleAnnotatedValidator {
      public boolean validate(Object object) {
          return Arrays.stream(object.getClass().getAnnotations()).anyMatch(this::isTargetAnnotation);
      }
  
      private boolean isTargetAnnotation(Annotation annotation) {
          return annotation instanceof SimpleAnnotation;
      }
  }
  ```
  ```java
  class SimpleAnnotatedValidatorTest {
      @Test
      void validateComposedAnnotated() {
          // given
          var target = new ComposedAnnotated();
          var validator = new SimpleAnnotatedValidator();

          // when
          var result = validator.validate(target);

          // then
          // FAIL! : SimpleAnnotatedValidator cannot find out SimpleAnnotation in ComposedAnnotation.
          assertThat(result).isTrue();
      }
  }
  ```  
- 테스트를 실행해 보면 Composed 애노테이션에 포함된 Simple 애노테이션을 찾지 못하는 것을 알 수 있습니다.

## Fix:

- 대상 객체의 `Object.getClass().getAnnotations()` 를 호출했을 때 직접 붙어있는 애노테이션만 조회되는 것을 확인했습니다.
- 조회한 애노테이션에 대해 `Annotation.annotationType().getAnnotations()` 를 호출하면 애노테이션에 붙어있는 애노테이션을 조회할 수 있습니다.

Show me the code:

- 아래와 같이 Simple 애노테이션이 붙어있는지 확인하는 코드를 수정합니다.
  ```java
  public class ComposedAnnotatedValidator {
      public boolean validate(Object object) {
          return Arrays.stream(object.getClass().getAnnotations())
              .flatMap(annotation -> {
                  var annotations = this.getChildAnnotations(annotation);
                  annotations.addFirst(annotation);
                  return annotations.stream();
              })
              .anyMatch(this::isTargetAnnotation);
      }
  
      private LinkedList<Annotation> getChildAnnotations(Annotation annotation) {
          var result = new LinkedList<Annotation>();
          Collections.addAll(result, annotation.annotationType().getAnnotations());
          return result;
      }
  
      private boolean isTargetAnnotation(Annotation annotation) {
          return annotation instanceof SimpleAnnotation;
      }
  }
  ```
  ```java
  class ComposedAnnotatedValidatorTest {
      @Test
      void validateComposedAnnotated() {
          // given
          var target = new ComposedAnnotated();
          var validator = new ComposedAnnotatedValidator();

          // when
          var result = validator.validate(target);

          // then
          // SUCCESS! : ComposedAnnotatedValidator can find out SimpleAnnotation in ComposedAnnotation.
          assertThat(result).isTrue();
      }
  }
  ```  
- 테스트를 실행해 보면 Composed 애노테이션에 포함된 Simple 애노테이션을 제대로 찾는 것을 알 수 있습니다.

## Improve

- 예제에서는 1 단계 깊이까지만 탐색하고 있습니다.
  ```java
  @ComposedAnnotation
  @Target(ElementType.TYPE)
  @Retention(RetentionPolicy.RUNTIME)
  public @interface ComposedComposedAnnotation {
  }
  ```
  처럼 깊이가 깊어지면 더 이상 Simple 애노테이션을 탐색하지 못합니다.
- 메타 애노테이션 탐색을 재귀적으로 수행하면 모든 메타 애노테이션을 탐색할 수 있습니다.

## Real World

- Spring, Jackson 같은 라이브러리, 프레임워크에서 Composed 애노테이션을 만들어서 사용하고 있습니다.
- 위와 같은 라이브러리, 프레임워크에선 Composed 애노테이션을 처리하는 기능을 API로 제공하기 때문에 직접 애노테이션 처리 로직을 작성하지 않아도 됩니다.

## Reference

전체 예제 및 테스트
코드는 [예제 저장소](https://github.com/daengdaengLee/til/tree/main/%EC%9E%90%EB%B0%94%20Composed%20Annotation%20%EC%B2%98%EB%A6%AC)
에서 확인할 수 있습니다.
