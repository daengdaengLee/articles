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

- 대문자를 모두 소문자로 변환한다. :
  `"a man, a plan, a canal: panama"`
- alphanumeric 하지 않은 문자를 모두 제거한다.
  공백 문자도 제거한다. :
  `"amanaplanacanalpanama"`
- 문자열을 뒤집는다. :
  `"amanaplanacanalpanama"`
- 뒤집기 전과 후를 비교한다. :
  `"amanaplanacanalpanama" == "amanaplanacanalpanama"`
- 결과는? :
  `true`

Example 2:

- 입력: `s = "race a car"`
- 출력: `false`

Example 2 설명:

- 대문자를 모두 소문자로 변환한다. :
  `"race a car"`
- alphanumeric 하지 않은 문자를 모두 제거한다.
  공백 문자도 제거한다. :
  `"raceacar"`
- 문자열을 뒤집는다. :
  `"racaecar"`
- 뒤집기 전과 후를 비교한다. `[]` 로 감싼 부분이 다르다. :
  `"rac[e]acar" != "rac[a]ecar"`
- 결과는? :
  `false`

Example 3:

- 입력: `s = " "`
- 출력: `true`

Example 3 설명:

- 대문자를 모두 소문자로 변환한다. :
  `" "`
- alphanumeric 하지 않은 문자를 모두 제거한다.
  공백 문자도 제거한다. :
  `""`
- 문자열을 뒤집는다. :
  `""`
- 뒤집기 전과 후를 비교한다. :
  `"" == ""`
- 결과는? :
  `true`

## Idea

가장 단순한 아이디어:

- 문자열을 문자 배열로 쪼갠다.
- 문자 배열을 순회하면서 다른 배열에 복사한다.
- 그 과정에서 대문자는 모두 소문자로 변환하고 alphanumeric 하지 않은 문자는 제외한다.
- 편의상 만든 배열을 배열1로 부르자.
- 배열1을 뒤집어서 배열2를 만든다. 배열1은 수정하지 않는다.
- 배열1을 순회하면서 배열2의 같은 인덱스에 위치한 값과 같은지 확인한다.
- 다른 경우가 있다면 `false` 를 반환한다.

```text
문자_배열 = 문자로_쪼개기(s) 
배열1 = 문자_배열
       |> filter(isAlphanumeric?)
       |> map(toLowerCase)
       |> toArray
배열2 = reverse(배열1)

isPalindrome? = true
for 0 <= i < 길이(배열1):
  문자1 = 배열1[i]
  문자2 = 배열2[i]
  if 문자1 != 문자2:
    isPalindrome? = false
    break
    
return isPalindrome?
```
