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

## Idea

- 위에서 살펴본 샘플 코드는 재귀를 통해서 검사하고 있는 `num` 값을 추적했지만 로컬 변수로 추적해도 충분하지 않을까?
- 알고리즘을 유지한 상태로 재귀 없이 리팩토링하면 선형 시간 복잡도와 `O(1)` 공간 복잡도 조건을 모두 만족할 수 있음

```text
// Pseudocode

카운터 = 0
후보 = nums 의 첫 번째 원소 // nums 길이는 1 이상이기 때문에 무조건 값이 있음
for num in nums:
  if 후보 == num:
    카운터 1 증가
  else:
    카운터 1 감소
    
  if 카운터 == -1:
    카운터 = 0
    후보 = num
return 후보
```

## Implementation

```java
class Solution {
    public int majorityElement(int[] nums) {
        var counter = 0;
        var candidate = nums[0];
        for (var num : nums) {
            if (candidate == num) {
                counter += 1;
            } else {
                counter -= 1;
            }

            if (counter == -1) {
                counter = 0;
                candidate = num;
            }
        }
        return candidate;
    }
}
```

## Report

Accepted

Runtime 1ms, Beats 99.84%

- 추가적인 힙 메모리 사용이 없어져서 속도 개선된 걸로 예상

Memory 48.5 MB, Beats 44.64%

- 공간 복잡도 조건은 만족시켰는데 상대적으로 안 좋은 결과가 나옴
- 메모리 사용량 측정에 제어할 수 없는 요소가 있는 걸로 예상

## Study

다른 솔루션을 살펴보니 위 샘플 코드는 사실 "Boyer-Moore 과반수 투표 알고리즘"

Boyer-Moore 과반수 투표 알고리즘 코드:

```java
class Solution {
    public int majorityElement(int[] nums) {
        var count = 0;
        var major = 0;
        for (var num : nums) {
            if (count == 0) {
                major = num;
            }

            if (major == num) {
                count += 1;
            } else {
                count -= 1;
            }
        }
        return major;
    }
}
```

Boyer-Moore 과반수 투표 알고리즘 실행 예시:

```text
nums       1   1   2   1   2   3   3   2   2   2   1   2   2   3   2   2 
count  0   1   2   1   2   1   0   1   0   1   2   1   2   3   2   3   4  
major  0   1   1   1   1   1   1   3   3   2   2   2   2   2   2   2   2
```

Boyer-Moore 과반수 투표 알고리즘 증명:

- 리턴한 major 외의 배열의 다른 원소가 과반수 원소라고 가정하고 1번 원소라고 부름
- 편의를 위해 리턴한 major 를 1번 원소, 과반수 원소라고 가정한 걸 2번 원소로 부름
- 배열의 길이를 K라고 가정한다면, 2번 원소는 (|K / 2| + 1) 번 이상 배열에 등장 (2번 원소가 과반수 원소라고 가정했기 때문에)
- 그렇다면 1번 원소는 아무리 많아봤자 |K / 2| 번 등장: K가 짝수이면 (|K / 2| - 1), 홀수이면 |K / 2| (물론 또 다른 원소가 있다면 이것보다 더 적음)
- 등장 횟수가 동률일 때 그 다음 원소가 major 가 되기 때문에 등장 횟수를 서로 차감했을 때 원소가 남아있지 않으면 major가 될 수 없음
- 1번 원소와 2번 원소의 등장 횟수를 서로 차감하면 1번 원소는 모두 차감되어서 없어짐
- 남은 1번 원소가 없기 때문에 1번 원소는 major가 될 수 없음 -> 모순!
