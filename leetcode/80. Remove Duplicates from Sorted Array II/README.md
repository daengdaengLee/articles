# 80. Remove Duplicates from Sorted Array II

- [문제 링크](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)

## Requirements

- non-decreasing order 로 정렬된 정수 배열 `nums` 에서 최대 2번까지 중복을 허용해서 값들을 앞으로 모아라
- 앞으로 모은 정수는 원래 순서를 유지
- 앞으로 모은 값만 검사, 뒤로 보낸 값은 신경 X
- 원소를 뒤로 보내는 로직은 `in-place` 방식으로
- 앞으로 모은 값 개수 반환

## Idea

- 기본적으로
  [26번 문제](https://github.com/daengdaengLee/articles/blob/main/leetcode/26.%20Remove%20Duplicates%20from%20Sorted%20Array/README.md)
  와 동일한 아이디어 사용
- 커서 2개와 로컬 중복 개수 카운터를 사용하면 최대 2번까지 중복 허용하는 요구사항에 대응 가능
- 앞쪽 커서를 무조건 1칸 옮기는 게 아니라 중복 개수 카운터에 따라 옮기는 거리 다르게 함
- 앞쪽 커서가 옮겨가면서 값을 덮어쓰도록 함

### Pseudocode

```text
i = 0
n = 1
for j = 1; j < nums.length; j += 1:
  if 원소i == 원소j:
    n <- 1 증가
    continue
  
  다음i = n 이 1 이면 (i + 1) 아니면 (i + 2)
  (i + 1) 부터 (다음i - 1) 까지 모두 원소i 로 덮어씀
  i = 다음i
  
  i 자리에 원소j 대입
  
  n 을 1 로 초기화
  
if n > 1:
  (i + 1) 자리에 원소i 대입
  
리턴 n 이 1 이면 (i + 1) 아니면 (i + 2)
```

### Example

```text
   // 처음
   n = 1
   2   2   2   2   3   3   3   4   5   5
   i   j
   
   // n 증가 -> j 이동
   n = 2
   2   2   2   2   3   3   3   4   5   5
   i       j
   
   // n 증가 -> j 이동
   n = 3
   2   2   2   2   3   3   3   4   5   5
   i           j
   
   // n 증가 -> j 이동
   n = 4
   2   2   2   2   3   3   3   4   5   5
   i               j
   
   // i 이동 (사이 덮어쓰기) -> j로 덮어쓰기 -> n 초기화 -> j 이동
   n = 1
   2   2   3   2   3   3   3   4   5   5
           i           j
           
   // n 증가 -> j 이동
   n = 2
   2   2   3   2   3   3   3   4   5   5
           i               j
           
   // n 증가 -> j 이동
   n = 3
   2   2   3   2   3   3   3   4   5   5
           i                   j
           
   // i 이동 (사이 덮어쓰기) -> j로 덮어쓰기 -> n 초기화 -> j 이동
   n = 1
   2   2   3   3   4   3   3   4   5   5
                   i               j
                   
   // i 이동 (사이 덮어쓰기) -> j로 덮어쓰기 -> n 초기화 -> j 이동
   n = 1
   2   2   3   3   4   5   3   4   5   5
                       i               j
                       
   // n 증가 -> j 이동 -> 루프 종료
   n = 2
   2   2   3   3   4   5   3   4   5   5
                       i                   j
   
   // n > 1 이면 (i + 1) 자리에 i로 덮어쓰기
   n = 2
   2   2   3   3   4   5   5   4   5   5
                       i                   j
   // 전체 개수 : n 이 1 이면 (i + 1) 아니면 (i + 2)
```

## Implementation

```java
// runtime : java 17

class Solution {
    public int removeDuplicates(int[] nums) {
        int i = 0;
        int n = 1;
        for (int j = 1; j < nums.length; j += 1) {
            if (nums[i] == nums[j]) {
                n += 1;
                continue;
            }

            int nextI = n == 1 ? i + 1 : i + 2;
            for (int k = i + 1; k < nextI; k += 1) {
                nums[k] = nums[i];
            }
            i = nextI;

            nums[i] = nums[j];

            n = 1;
        }

        if (n > 1) {
            nums[i + 1] = nums[i];
        }

        return n == 1 ? i + 1 : i + 2;
    }
}
```

## Report

<img width="426" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/2c0eb51c-5f61-460b-bcf9-6fa9344a2363">
<img width="1237" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/e7aa610f-83bf-40c9-81ef-95f42f6e330b">
<img width="1260" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/639a6a7c-a132-4c82-9d03-91b492bf031e">

## Is it good?

- 실행 시간은 괜찮은데 메모리를 많이 사용하고 있음
- 다른 코드를 살펴봐도 알고리즘은 동일함
- 하지만 루프 종료 이후 `System.gc()` 를 호출하고 있음
- 하지만 해동 코드를 실행하면 트레이드 오프로 실행 시간이 느려짐
- 알고리즘 문제 경쟁 때문에 GC를 강제로 돌리는 건 의미가 없다고 판단, 메모리 사용에 대한 개선은 중지
