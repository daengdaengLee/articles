# 88. Merge Sorted Array

[문제 링크](https://leetcode.com/problems/merge-sorted-array/)

## Requirements

- non-decreasing order 로 정렬된 두 정수 배열을 non-decreasing order 를 유지하면서 하나의 배열로 합쳐라.
- 입력:
    - 정수 배열 : `num1`, `num2`
    - 각 정수 배열의 원소 개수 : `m`, `n`
    - `num1` 에 합칠 것
    - `num1` 의 실제 길이는 `m + n`, 뒤에 `n` 개의 `0` 으로 채워져 있음, 해당 `0` 은 무시할 것
- (Optional) `O(m + n)` 시간안에 실행

## Idea

- `m + n` 순회하면서 `num1`, `num2` 배열의 첫 번째 원소 비교
- 둘 중 작은 원소를 뽑아서 결과 배열에 추가
- 두 배열의 유효한 원소를 모두 뽑아낼 때까지 반복
- `num1`, `num2` 모두 이미 non-decreasing order 로 정렬되어 있기 때문에 첫 원소들 중 작은쪽이 남은 원소 전체 중 가장 작음
- 따라서 non-decreasing order 순으로 원소를 뽑아낼 수 있음

## Pseudocode

```text
result = { 길이 m + n 배열 }

i = 0 // num1 배열 커서 0 ~ m-1
j = 0 // num2 배열 커서 0 ~ n-1

for 0 ~ m+n-1 인 k:
  if i 또는 j 가 커서 범위에서 벗어났으면:
    아직 남은 쪽 배열 원소를 result 빈 칸에 순서대로 추가
    break

  v1 = num1[i]
  v2 = num2[j]
  
  result[k] = v1, v2 중 작은 쪽
  result 에 반영한 쪽 커서 1 증가
```

## Implementation

```java
// runtime : java 17

class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int[] result = new int[m + n];

        int i = 0;
        int j = 0;

        for (int k = 0; k < m + n; k += 1) {
            if (i == m) {
                System.arraycopy(nums2, j, result, k, n - j);
                break;
            }
            if (j == n) {
                System.arraycopy(nums1, i, result, k, m - i);
                break;
            }

            int v1 = nums1[i];
            int v2 = nums2[j];
            if (v1 < v2) {
                result[k] = v1;
                i += 1;
            } else {
                result[k] = v2;
                j += 1;
            }
        }

        System.arraycopy(result, 0, nums1, 0, m + n);
    }
}
```

## Improvement

- [래퍼런스](https://leetcode.com/problems/merge-sorted-array/solutions/3436053/beats-100-best-c-java-python-and-javascript-solution-two-pointer-stl/)
  ```java
  class Solution {
      public void merge(int[] nums1, int m, int[] nums2, int n) {
          int i = m - 1;
          int j = n - 1;
          int k = m + n - 1;
  
          while (j >= 0) {
              if (i >= 0 && nums1[i] > nums2[j]) {
                  nums1[k--] = nums1[i--];
              } else {
                  nums1[k--] = nums2[j--];
              }
          }
      }
  }
  ```
- `result` 배열을 사용하지 않기 때문에 배열 복사 연산도 필요없고, 메모리도 적게 사용함
- non-decreasing order 로 정렬해야 한다고 해서 꼭 해당 순서대로 순회하는 로직이어야 할 필요는 없음, 유연한 사고 필요
- 위 래퍼런스의 경우 역순으로 진행하면서 반대로 더 큰 원소를 뽑아서 빈 칸에 채워넣는 방법을 사용
- 문제에서 `num1` 배열의 뒤에 빈 칸이 있도록 출제했기 때문에 래퍼런스의 방법을 사용하면 자연스럽게 빈 칸부터 채워넣을 수 있음
- 다음부턴 같은 시간복잡도를 가지더라도 상수 시간과 공간복잡도도 신경쓰도록 하자
