# 26. Remove Duplicates from Sorted Array

- [문제 링크](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

## Requirements

- non-decreasing order 로 정렬된 정수 배열 `nums` 에서 중복이 아닌 값들만 앞으로 모아라
- 앞으로 모은 정수는 원래 순서를 유지
- 중복이 아닌 앞쪽 값만 검사, 뒤로 보낸 값은 신경 X
- 원소를 뒤로 보내는 로직은 `in-place` 방식으로
- 중복이 아닌 값 개수 반환
