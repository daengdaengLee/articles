# 27. Remove Element

- [문제 링크](https://leetcode.com/problems/remove-element/)

## Requirements

- 정수 배열 `nums` 에서 `val` 이 아닌 값들을 앞으로 모아라
- `val` 이 아닌 앞쪽 값만 검사, 뒤로 보낸 값은 신경 X
- 원소를 뒤로 보내는 로직은 `in-place` 방식으로
- `val` 이 아닌 값 개수 반환

## Idea

- 두 개의 커서를 사용
- 앞 -> 뒤로 가면서 `val` 인 값 탐색
- 뒤 -> 앞으로 가면서 `val` 이 아닌 값 탐색
- 탐색한 두 값을 `in-place` 로 스왑
    - XOR 연산 사용
- 두 커서가 만날 때까지 반복하면
    - 앞 -> 뒤 커서가 지나온 곳은 모두 `val` 이 아닌 값
    - 뒤 -> 앞 커서가 지나온 곳은 모두 `val` 인 값
- 앞 -> 뒤 커서 값이 `val` 이 아닌 값 개수
