# 189. Rotate Array

- [문제 링크](https://leetcode.com/problems/rotate-array/)

## Requirements

- 정수 배열 `nums`, 음이 아닌 정수 `k` 입력
- `nums` 를 `k` 번 오른쪽으로 밀어냄
- 오른쪽 끝에서 밀려난 값은 왼쪽 처음 위치로 이동
- Optional: 풀이 방법은 최소 3개 이상 존재
- Optional: in-place with `O(1)` extra space, 즉 고정 크기 메모리만 사용

## Idea

- 편의상 `nums` 의 길이를 `l` 로 표기
- `k` 가 `l` 과 같을 때 한 바퀴 회전해서 원래대로 됨
- `k` 번 회전하나 `k` 를 `l` 로 나눈 나머지만큼 회전하나 결과는 동일함
- `k` 를 `l` 로 나눈 나머지를 `k2` 로 표기

메모리 사용을 신경쓰지 않는 가장 단순한 방법:

```text
// Pseudocode

l = 길이(nums)
k2 = k % l
부분배열1 = nums[0:l-k2] // [include:exclude]
부분배열2 = nums[l-k2:l]
return 부분배열2 + 부분배열1
```

## Implementation

```java
import java.util.Arrays;

class Solution {
    public void rotate(int[] nums, int k) {
        var l = nums.length;
        var k2 = k % l;
        if (k2 == 0) {
            return;
        }
        var part1 = Arrays.copyOfRange(nums, 0, l - k2);
        var part2 = Arrays.copyOfRange(nums, l - k2, l);
        System.arraycopy(part1, 0, nums, k2, l - k2);
        System.arraycopy(part2, 0, nums, 0, k2);
    }
}
```

## Report

Accepted

Runtime 0 ms, Beats 100%

Memory 55.4 MB, Beats 79.77%

## Idea

더 단순한 방법:

- 오른쪽으로 1칸 회전하는 함수를 만들고
- `k` 번 실행

```text
fn 오른쪽으로1칸(nums):
  l = 길이(nums)
  마지막원소 = nums[l - 1]
  nums[0:l-1] 을 nums[1:l] 에 복사
  nums[0] = 마지막원소
  
for k번:
  오른쪽으로1칸(nums)
```

## Implementation

```java
class Solution {
    public void rotate(int[] nums, int k) {
        for (var i = 0; i < k; i += 1) {
            step(nums);
        }
    }

    private void step(int[] nums) {
        var l = nums.length;
        var last = nums[l - 1];
        System.arraycopy(nums, 0, nums, 1, l - 1);
        nums[0] = last;
    }
}
```

## Report

Time Limit Exceeded

- 테스트 실행일 땐 통과하지만 submit 하면 시간 초과

## Idea

시간 초과한 버전에서 `k` 대신 `k2` 로 보정하는 로직만 추가

```text
fn 오른쪽으로1칸(nums):
  l = 길이(nums)
  마지막원소 = nums[l - 1]
  nums[0:l-1] 을 nums[1:l] 에 복사
  nums[0] = 마지막원소
  
l = 길이(nums)
k2 = k % l
for k2번:
  오른쪽으로1칸(nums)
```

## Implementation

```java
class Solution {
    public void rotate(int[] nums, int k) {
        var l = nums.length;
        var k2 = k % l;
        for (var i = 0; i < k2; i += 1) {
            step(nums);
        }
    }

    private void step(int[] nums) {
        var l = nums.length;
        var last = nums[l - 1];
        System.arraycopy(nums, 0, nums, 1, l - 1);
        nums[0] = last;
    }
}
```

## Report

Time Limit Exceeded

- 마찬가지로 테스트 실행일 땐 통과하지만 submit 하면 시간 초과
