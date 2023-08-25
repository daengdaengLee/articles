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
