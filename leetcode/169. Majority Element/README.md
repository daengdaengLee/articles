# 169. Majority Element

- [문제 링크](https://leetcode.com/problems/majority-element/)

## Requirements

- 길이 `n` 인 숫자 배열 `nums` 가 있을 때 majority element 를 찾아라
- majority element 는 출현 횟수가 `|n / 2|` 보다 큰 값
- majority element 는 반드시 하나 존재
- Optional: linear time + `O(1)` space

```text
Example 1
  Input: nums = [3,2,3]
  Output: 3
Example 2
  Input: nums = [2,2,1,1,1,2,2]
  Output: 2
```

## Idea

처음 생각한 방법:

```text
// Pseudocode

n = 길이(nums)
기준 출현 횟수 = |n / 2|
카운터 = map{num -> 출현 횟수} 
for num in nums:
  현재 출현 횟수 = 1
  if num in 카운터:
    현재 출현 횟수 = 카운터[num] + 1
    
  if 현재 출현 횟수 > 기준 출현 횟수:
    return num
    
  카운터[num] = 현재 출현 횟수
```

- 가장 단순하고 직관적
- 이 방법은 선형 시간 복잡도 조건은 만족하지만 카운터 맵이 가변 공간을 사용하기 때문에 공간 복잡도 조건은 만족하지 못함

## Implementation

```java
import java.util.HashMap;

class Solution {
    public int majorityElement(int[] nums) {
        var n = nums.length;
        var half = n / 2;
        var counter = new HashMap<Integer, Integer>();
        for (var num : nums) {
            var current = counter.getOrDefault(num, 0) + 1;
            if (current > half) {
                return num;
            }
            counter.put(num, current);
        }
        return 0;
    }
}
```

## Report

```text
Accepted

Runtime 12ms, Beats 32.29%
  89.08% of solutions used 8 ms of runtime
  
Memory 46.6 MB, Beats 91.73%
```
