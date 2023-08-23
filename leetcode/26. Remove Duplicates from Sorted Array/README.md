# 26. Remove Duplicates from Sorted Array

- [문제 링크](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

## Requirements

- non-decreasing order 로 정렬된 정수 배열 `nums` 에서 중복이 아닌 값들만 앞으로 모아라
- 앞으로 모은 정수는 원래 순서를 유지
- 중복이 아닌 앞쪽 값만 검사, 뒤로 보낸 값은 신경 X
- 원소를 뒤로 보내는 로직은 `in-place` 방식으로
- 중복이 아닌 값 개수 반환

## Idea

- 앞 -> 뒤 방향으로 커서 순회
- 현재 값이 다음 값보다 작으면: 커서 이동 + continue
- 다음 값 이후 값들 중 현재 값보다 큰 값 탐색
    - 없으면: break
    - 있으면: 다음 값과 찾은 값 swap + 커서 이동
- 순회 종료 후 커서가 중복되지 않은 마지막 값 위치
- In-place swap
  은 [XOR swap 방식](https://github.com/daengdaengLee/articles/blob/main/leetcode/27.%20Remove%20Element/README.md#in-place-swap-%EC%9B%90%EB%A6%AC)
  사용

## Pseudocode

```text
커서 = 0

while 커서 < 전체 개수 - 1:
  현재 = nums[커서]
  다음 = nums[커서 + 1]

  if 현재 < 다음:
    커서 1 증가
    continue
  
  다다음~끝 사이에서 현재보다 큰 원소 탐색
  if 그런 원소 없으면:
    break
  다음 <-> 찾은 원소 swap
  커서 1 증가
  
return 커서 + 1
```

## Implementation

```java
// runtime : java 17

class Solution {
    public int removeDuplicates(int[] nums) {
        int i = 0;

        while (i < nums.length - 1) {
            int current = nums[i];
            int next = nums[i + 1];

            if (current < next) {
                i += 1;
                continue;
            }

            boolean isFound = false;
            int j = i + 2;
            while (j < nums.length) {
                int candidate = nums[j];
                if (current < candidate) {
                    isFound = true;
                    break;
                }
                j += 1;
            }

            if (!isFound) {
                break;
            }

            nums[i + 1] ^= nums[j];
            nums[j] ^= nums[i + 1];
            nums[i + 1] ^= nums[j];
            i += 1;
        }

        return i + 1;
    }
}
```
