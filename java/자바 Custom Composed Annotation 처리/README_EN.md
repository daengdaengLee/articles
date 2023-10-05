# Handling Java Custom Composed Annotations

## TL;DR

- It's possible to create an annotation by combining other annotations.
- You can read metadata about other annotations declared on top of an annotation
  using `Annotation.annotationType().getAnnotations()`.

## Problem

- Annotations attached to annotations are known as meta-annotations.
- Java does not natively support custom meta-annotations except for predefined meta-annotations
  like `@Target` and `@Retention`.

Problem example:

- Define the `SimpleAnnotation` and the `ComposedAnnotation`.
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
- Define a class with the `ComposedAnnotation`.
  ```java
  @ComposedAnnotation
  public class ComposedAnnotated {
  }
  ```
- Check if the `SimpleAnnotation` is present as follows.
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
- When running the test, it can be observed that the `SimpleAnnotation` inside the `ComposedAnnotation` is not found.

## Fix:

- It was observed that when calling `Object.getClass().getAnnotations()` on the target object, only directly attached
  annotations are retrieved.
- By calling `Annotation.annotationType().getAnnotations()` on the retrieved annotations, annotations attached to the
  annotation can be accessed.

Show me the code:

- Modify the code to check if the `SimpleAnnotation` is attached as follows.
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
- When running the test, it can be observed that the `SimpleAnnotation` inside the `ComposedAnnotation` is found.

## Improve

- In the provided example, the code only searches up to a depth of 1 level.
  If the depth increases, it won't search for the `SimpleAnnotation` anymore.
  ```java
  @ComposedAnnotation
  @Target(ElementType.TYPE)
  @Retention(RetentionPolicy.RUNTIME)
  public @interface ComposedComposedAnnotation {
  }
  ```
- Recursive meta-annotation search allows for exploring all meta-annotations even with increased depth.

## Real World

- Libraries and frameworks like Spring and Jackson create and use composed annotations.
- In such libraries and frameworks, functionality to handle composed annotations is provided through APIs,
  eliminating the need to write annotation processing logic directly.

## Reference

Complete example and test code can be found in
the [example repository](https://github.com/daengdaengLee/til/tree/main/%EC%9E%90%EB%B0%94%20Composed%20Annotation%20%EC%B2%98%EB%A6%AC).
