# 209. Minimum Size Subarray Sum

- [문제 링크](https://leetcode.com/problems/minimum-size-subarray-sum/)

## 요구사항

- 자연수로 이루어진 배열 `nums` 와 자연수 `target` 이 입력된다.
- 원소의 합이 `target` 이상인 `nums` 의 부분 배열중 가장 작은 길이를 출력해라.
- 조건에 맞는 부분 배열이 없다면 `0` 을 출력해라.

제약:

- `1 <= target <= 109`
- `1 <= nums.length <= 105`
- `1 <= nums[i] <= 104`

추가 요구사항:

- 시간 복잡도가 `O(n)` 인 해법을 찾았다면, 시간 복잡도가 `O(n log(n))` 인 다른 해법을 찾아라.

## 예시

Example 1:

- 입력: `target = 7`, `nums = [2,3,1,2,4,3]`
- 출력: `2`

Example 1 설명:

- 길이 `1` 인 부분 배열 중 원소 합이 `7` 이상인 경우는 없다.
- 길이 `2` 인 부분 배열 중 `[4, 3]` 은 원소 합이 `7` 로 조건에 맞는다.
- 따라서 길이 `2` 를 출력한다.

Example 2:

- 입력: `target = 4`, `nums = [1,4,4]`
- 출력: `1`

Example 2 설명:

- 길이 `1` 인 부분 배열 중 `[4]` 은 원소 합이 `4` 로 조건에 맞는다.
- 따라서 길이 `1` 를 출력한다.

Example 3:

- 입력: `target = 11`, `nums = [1,1,1,1,1,1,1,1]`
- 출력: `0`

Example 3 설명:

- `nums` 의 모든 원소를 더해도 `8` 로 `11` 보다 작다.
- 원소 합이 `11` 이상인 부분 배열은 존재하지 않는다.
- `0` 을 출력한다.

## 아이디어

- 모든 부분 배열을 구하고 원소 합이 `target` 이상인 부분 배열만 거른다.
- 가장 작은 길이를 찾는다.

```text
fn 해법(target, nums):
  return 모든_부분_배열(nums)
         |> filter(조건_만족?(target))
         |> map(길이)
         |> min(시작값=0)
  
fn 모든_부분_배열(nums):
  전체_배열_길이 = 길이(nums)
  return range(1, 전체_배열_길이 + 1)
         |> flatMap(부분_배열_길이 => range(0, 전체_배열_길이 - 부분_배열_길이 + 1)
                                   |> map(시작_인덱스 => nums[시작_인덱스:시작_인덱스 + 부분_배열_길이]))
  
fn 조건_만족?(target, 부분_배열):
  원소_합 = sum(부분_배열)
  return 원소_합 >= target
```

시간 복잡도:

- 전체 배열 길이를 `n` 이라고 했을 때
- `1 <= 부분_배열_길이 <= n` 반복문 안에서
- `0 <= 시작_인덱스 <= n - 부분_배열_길이` 반복문 안에서
- `0 <= i < 부분_배열_길이` 만큼 순회하면서 부분 합을 구한다.
- 시간 복잡도는 `O(n ^ 3)` 이다.

## 최적화

- 같은 시작 인덱스를 갖는 부분 배열은 원소 합이 `target` 이상이 된 후에는 더 이상 검사할 필요 없다.
- 특정 시작 인덱스에서 조건을 만족하는 부분 배열을 찾은 후 다른 시작 인덱스를 검사할 땐 찾았던 부분 배열 길이보다 긴 경우는 검사할 필요 없다.
- 부분 배열 원소 합은 시작 인덱스나 끝 인덱스가 변할 때 빠지는 값을 빼고, 추가되는 값을 더해서 구할 수 있다. 이 경우 상수 시간만 필요하다.

```text
fn 해법(target, nums):
  전체_배열_길이 = 길이(nums)
  부분_배열_길이 = 0
  시작_인덱스 = 0
  끝_인덱스 = 0
  부분합 = nums[0]
  while 시작_인덱스 < 전체_배열_길이 그리고 끝_인덱스 < 전체_배열_길이 그리고 시작_인덱스 <= 끝_인덱스:
    if 부분합 < target:
      끝_인덱스 += 1
      부분합 += nums[끝_인덱스]
      continue
      
    이번_부분_배열_길이 = 끝_인덱스 - 시작_인덱스 + 1
    if 부분_배열_길이 == 0 또는 이번_부분_배열_길이 < 부분_배열_길이:
      부분_배열_길이 = 이번_부분_배열_길이
      
    부분합 -= nums[시작_인덱스]
    시작_인덱스 += 1
  return 부분_배열_길이
```

시간 복잡도:

- 최악의 경우 시작 인덱스와 끝 인덱스가 각각 처음부터 배열 끝까지 순회하는 경우이다.
- 따라서 시간 복잡도는 `O(n + n) = O(n)` 이다.

## 구현

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        var totalL = nums.length;
        var subL = 0;
        var startIdx = 0;
        var endIdx = 0;
        var subSum = nums[0];
        while (startIdx <= endIdx && endIdx < totalL) {
            if (subSum < target) {
                endIdx += 1;
                subSum += endIdx < totalL ? nums[endIdx] : 0;
                continue;
            }

            var currentSubL = endIdx - startIdx + 1;
            if (subL == 0 || currentSubL < subL) {
                subL = currentSubL;
            }

            subSum -= nums[startIdx];
            startIdx += 1;
        }
        return subL;
    }
}
```

실행 결과: Accepted

- Runtime 1 ms, Beats 99.96%
- Memory 53.4 MB, Beats 98.53%
 