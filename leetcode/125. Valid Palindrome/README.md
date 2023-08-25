# 125. Valid Palindrome

- [문제 링크](https://leetcode.com/problems/valid-palindrome/)

## Requirements

- 입력 문자열 `s` 가 palindrome 이면 `true` 를 아니면 `false` 를 리턴하시오.

palindrome 이란?:

- 모든 대문자를 소문자로 변환한다.
- alphanumeric 하지 않은 모든 문자를 제거한다.
- 왼쪽에서 오른쪽으로 읽으나, 오른쪽에서 왼쪽으로 읽으나 동일하다면 palindrome 이다.
- alphanumeric 인 문자는 영어 알파벳과 숫자를 말한다.

제약 사항:

- `1 <= s.length <= 2 * (10 ^ 5)`
- `s` 는 오직 출력 가능한 아스키 문자만 포함한다.

## Examples

Example 1:

- 입력: `s = "A man, a plan, a canal: Panama"`
- 출력: `true`

Example 1 설명:

1. 대문자를 모두 소문자로 변환한다. :
   `"a man, a plan, a canal: panama"`
2. alphanumeric 하지 않은 문자를 모두 제거한다.
   공백 문자도 제거한다. :
   `"amanaplanacanalpanama"`
3. 문자열을 뒤집는다. :
   `"amanaplanacanalpanama"`
4. 뒤집기 전과 후를 비교한다. :
   `"amanaplanacanalpanama" == "amanaplanacanalpanama"`
5. 결과는? :
   `true`

Example 2:

- 입력: `s = "race a car"`
- 출력: `false`

Example 2 설명:

1. 대문자를 모두 소문자로 변환한다. :
   `"race a car"`
2. alphanumeric 하지 않은 문자를 모두 제거한다.
   공백 문자도 제거한다. :
   `"raceacar"`
3. 문자열을 뒤집는다. :
   `"racaecar"`
4. 뒤집기 전과 후를 비교한다. `[]` 로 감싼 부분이 다르다. :
   `"rac[e]acar" != "rac[a]ecar"`
5. 결과는? :
   `false`

Example 3:

- 입력: `s = " "`
- 출력: `true`

Example 3 설명:

1. 대문자를 모두 소문자로 변환한다. :
   `" "`
2. alphanumeric 하지 않은 문자를 모두 제거한다.
   공백 문자도 제거한다. :
   `""`
3. 문자열을 뒤집는다. :
   `""`
4. 뒤집기 전과 후를 비교한다. :
   `"" == ""`
5. 결과는? :
   `true`
