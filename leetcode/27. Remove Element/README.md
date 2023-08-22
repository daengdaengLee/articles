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

## Pseudocode

```text
i = 0 // 앞 -> 뒤 커서
j = length(nums) - 1 // 뒤 -> 앞 커서

while i < j:
  while nums[i] != val:
    i += 1
    continue
  
  while nums[j] == val:
    j -= 1
    continue

  nums[i] ^= nums[j]
  nums[j] ^= nums[i]
  nums[i] ^= nums[j]
  
i 반환
```

## Implementation

```java
// runtime : java 17

class Solution {
    public int removeElement(int[] nums, int val) {
        int i = 0;
        int j = nums.length - 1;

        while (i < j) {
            if (nums[i] != val) {
                i += 1;
                continue;
            }

            if (nums[j] == val) {
                j -= 1;
                continue;
            }

            nums[i] ^= nums[j];
            nums[j] ^= nums[i];
            nums[i] ^= nums[j];
        }

        return i;
    }
}
```
