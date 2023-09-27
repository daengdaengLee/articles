# PDF 톺아보기 - 기본 객체

## TL;DR

- PDF는 기본 객체들의 조합으로 구성됩니다.
- 8가지 기본 타입 객체로 Boolean, Numeric, String, Name, Array, Dictionary, Stream, Null 객체가 있고
  고유한 식별자를 가지고 참조할 수 있는 Indirect 객체가 있습니다.

## 기본 타입 객체

### Boolean 객체

- 참과 거짓을 표현하는 객체입니다.
- `true`, `false` 키워드로 표현합니다.

### Numeric 객체

- 정수와 실수를 표현하는 객체입니다.
- 부호, 소수점을 표현할 수 있습니다.
- ISO 표준을 따르는 경우 10진법으로만 표현합니다.

### String 객체

- 문자열은 두 가지 방법으로 표현할 수 있습니다.

리터럴 문자열:

- `(` 와 `)` 사이에 문자열 리터럴을 적습니다.
  ```text
  (This is a string)
  ```
- 역슬래시 문자로 여러 줄에 걸쳐 표현할 수 있습니다.
  ```text
  (These \
  two strings \
  are the same.)
  (These two strings are the same.)
  ```

16진법 문자열:

- `<` 와 `>` 사이에 16진법 숫자 표현을 적습니다.
  ```text
  <4E6F762073686D6F7A206B6120706F702E>
  ```
- 16진법 숫자 2개를 한 쌍으로 1바이트씩 처리합니다.
- 마지막에 숫자가 모자란 경우 `00`을 자동으로 덧붙여서 처리합니다.

### Name 객체

- 역슬래시 문자 뒤에 문자열 표현을 붙여서 정의합니다. `/Type`은 Type이라는 Name 객체입니다.
- 원자적 심볼을 정의합니다. 다른 객체를 포함하지 않습니다.
- 유일하게 정의됩니다. 같은 문자열 표현이면 같은 객체입니다.
- JS의 `Symbol.for`와 유사한 동작 방식입니다.

### Array 객체

- 객체들을 순서대로 모아 놓은 1차원 배열입니다.
- 다른 타입 객체를 한 배열에 넣을 수 있습니다.
- `[` 와 `]` 사이에 객체들을 선언합니다.
  ```text
  [549 3.14 false (Ralph) /SomeName]
  ```
- 다른 Array 객체를 원소로 포함해 고차원 배열을 선언할 수 있습니다.

### Dictionary 객체

- 키-값 쌍 엔트리로 구성된 테이블입니다.
- 키에는 Name 객체를 사용합니다.
- 값에는 Dictionary 객체를 포함해 모든 타입의 객체를 사용할 수 있습니다.
- 값이 Null 객체이면 해당 엔트리는 없는 것으로 간주합니다.
- `<<` 와 `>>` 사이에 키-값 엔트리를 표현합니다.
  ```text
  << /Type /Example
     /Subtype /DictionaryExample
     /Version 0.01
     /IntegerItem 12
     /StringItem (a string)
     /Subdictionary << /Item1 0.4
                       /Item2 true
                       /LastItem (not!)
                       /VeryLastItem (OK)
                    >>
  >>
  ```

### Stream 객체

- 연속된 바이트 데이터를 표현합니다.
- 메타 데이터를 표현하는 Dictionary 객체와 `stream`, `endstream` 으로 감싼 바이트 데이터로 구성됩니다.
  ```text
  <<
     ...entries...
  >>
  stream
  ...Zero or more bytes...
  endstream
  ```
- Stream 객체는 항상 Indirect 객체 형태로 선언해야 합니다.
- 이미지 같은 큰 데이터를 표현할 때 사용합니다.

### Null 객체

- `null` 키워드로 표현합니다.
- 다른 어떤 타입의 값과도 같지 않습니다.

## Indirect 객체

- 고유 식별자로 레이블 된 객체입니다.
- 고유 식별자는 object number와 generation number의 조합입니다.
- object number는 자연수입니다. 보통 차례대로 할당되지만, 필수는 아닙니다.
- generation number는 음이 아닌 정수입니다. 0부터 시작해 객체를 수정할 때마다 증가합니다.

### 정의

아래와 같은 형식으로 정의합니다.

```text
<object number> <generation number> obj
...Object...
endobj
```

예시: object number가 12, generation number가 0인 문자열 Brillig 객체

```text
12 0 obj
  ( Brillig )
endobj
```

### 참조

- Indirect 객체는 다른 곳에서 `<object number> <generation number> R` 형태로 참조할 수 있습니다.
- 예를 들어 위 예시에서 정의한 Indirect 객체는 `12 0 R` 형태로 참조할 수 있습니다.

## Next

- PDF 파일 구조에 대해 알아봅시다.
