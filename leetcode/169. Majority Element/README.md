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

Accepted

Runtime 12ms, Beats 32.29%

- 89.08% of solutions used 8 ms of runtime
- 선형 시간 복잡도를 가졌는데 상대적으로 안 좋은 결과가 나옴
- 메모리 공간을 많이 사용하더라도 더 빠른 솔루션을 제출한 사람이 많은 것으로 예상

8 ms solution 샘플 코드 확인:

```java
class Solution {
    public int majorityElement(int[] nums) {
        return helper(nums, 0, nums[0]);
    }

    private int helper(int[] nums, int si, int ref) {
        var c = 0;
        for (var i = si; i < nums.length; i += 1) {
            if (nums[i] == ref) {
                c += 1;
            } else {
                c -= 1;
            }

            if (c == -1) {
                return helper(nums, i, nums[i]);
            }
        }
        return ref;
    }
}
```

샘플 코드 동작 원리:

- 배열을 순회하면서 후보인 값(배열의 첫 원소로 시작)의 출현 횟수가 절반 이상을 유지하는지 검사
- 절반 미만으로 떨어진 경우 해당 지점의 원소를 새 후보와 시작점으로 삼고 검사 로직을 재귀로 실행
- 과반 출현한 값이 없는 부분 배열은 나머지 배열의 과반 출현에 영향이 없기 때문

예시 1: 앞쪽 부분이 반반

```text
// 뒤쪽 부분에서 원래 후보였던 1이 과반인 경우
1   1   2   2 | 2   1   1   

// 뒤쪽 부분에서 원래 후보였던 1과 다른 2가 과반인 경우
1   1   2   2 | 2   2   1
```

- `|` 앞쪽 부분 절반 + 뒤쪽 부분 과반 -> 전체 과반으로 유지

예시 2: 앞쪽 부분이 1로 쏠림

```text
// 뒤쪽 부분에서 원래 후보였던 1이 과반인 경우
1   1   2   3 | 2   1   1
```

- `|` 앞쪽 부분 절반 + 뒤쪽 부분 과반 -> 전체 과반으로 유지

```text
// 뒤쪽 부분에서 원래 후보였던 1과 다른 2가 과반인 경우
1   1   2   3 | 2   2   1 -> majority element X
1   1   2   3 | 2   2   2   1 -> majority element X
1   1   2   3 | 2   2   2   2   1 -> majority element O
```

- 뒤쪽 부분에서 2가 충분히 많이 출현하지 않으면 배열 전체에서 majority element 가 존재하지 않게 됨
- 뒤쪽 부분에서 1의 출현 횟수가 줄어들어서 앞쪽 절반만큼 출현 횟수를 더해도 과반은 2로 유지됨

샘플 코드 Runtime:

- 결과적으로 `nums` 배열을 처음부터 끝까지 1 번 순회하기 때문에 선형 시간 복잡도 만족
- 같은 시간 복잡도를 가져도 힙 메모리를 사용하는 맵을 쓰지 않고 스택 메모리만 사용해서 속도가 더 빠른 것으로 추측

샘플 코드 Memory:

- `nums` 배열을 순회하기 위해 재귀 함수를 사용하기 때문에 입력 데이터에 따라서 동적으로 메모리를 사용함, 공간 복잡도 조건은 아직 만족하지 못 함

Memory 46.6 MB, Beats 91.73%

- 공간 복잡도 조건은 못 만족시켰는데 상대적으로 좋은 결과가 나옴
- 고정 메모리 공간만 사용해서 제출한 사람이 적은 것으로 예상
