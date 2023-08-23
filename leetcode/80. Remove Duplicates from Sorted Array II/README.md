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
- 메모리 사용이 적은 다른 코드
  ```java
    class Solution {
        public int removeDuplicates(int[] nums) {
            int k = 1, i = 1, count = 0;
            for (; i < nums.length; i++) {
                if (nums[i] == nums[i - 1]) {
                    count++;
                } else {
                    count = 0;
                }
                if (count < 2) {
                    nums[k++] = nums[i];
                }
            }
            System.gc();
            return k;
        }
    }
  ```
- 루프 종료 이후 `System.gc()` 를 호출하고 있음
- 해동 코드를 실행하면 트레이드 오프로 실행 시간이 느려짐
- GC를 강제로 돌리는 건 의미가 없다고 판단, 메모리 사용에 대한 개선은 중지
- 샘플 코드는 분기 처리가 더 단순함
- 매 순회마다 중복 카운트를 검사해서 미리 최대 허용 중복만큼 값을 덮어쓰면서 커서를 이동시켰기 때문에
  마지막에 전체 개수를 계산하기 위해 루프 밖에서 분기처리를 할 필요가 없음

### Pseudocode

```text
i = 0
n = 1
for j = 1; j < nums.length; j += 1:
  if 원소j-1 == 원소j:
    n <- 1 증가
  else:
    n <- 1 로 초기화
    
  if n <= 2:
    i <- 1 증가
    i 자리에 원소j 대입
리턴 i + 1
```

### Example

```text
   // 처음
   n = 1
   2   2   2   2   3   3   3   4   5   5
   i   j
   
   // n 증가 -> i 이동 -> j로 덮어쓰기 -> j 이동
   n = 2
   2   2   2   2   3   3   3   4   5   5
       i   j
       
   // n 증가 -> j 이동
   n = 3
   2   2   2   2   3   3   3   4   5   5
       i       j
       
   // n 증가 -> j 이동
   n = 4
   2   2   2   2   3   3   3   4   5   5
       i           j
       
   // n 초기화 -> i 이동 -> j로 덮어쓰기 -> j 이동
   n = 1
   2   2   3   2   3   3   3   4   5   5
           i           j
           
   // n 증가 -> i 이동 -> j로 덮어쓰기 -> j 이동
   n = 2
   2   2   3   3   3   3   3   4   5   5
               i           j
               
   // n 증가 -> j 이동
   n = 3
   2   2   3   3   3   3   3   4   5   5
               i               j
               
   // n 초기화 -> i 이동 -> j로 덮어쓰기 -> j 이동
   n = 1
   2   2   3   3   4   3   3   4   5   5
                   i               j
                   
   // n 초기화 -> i 이동 -> j로 덮어쓰기 -> j 이동
   n = 1
   2   2   3   3   4   5   3   4   5   5
                       i               j
                       
   // n 증가 -> i 이동 -> j로 덮어쓰기 -> j 이동 -> 루프 종료
   n = 2
   2   2   3   3   4   5   5   4   5   5
                           i               j
                           
   // 전체 개수 : i + 1
```

### Improved Implementation

```java
// runtime : java 17

class Solution {
    public int removeDuplicates(int[] nums) {
        int i = 0;
        int n = 1;
        for (int j = 1; j < nums.length; j += 1) {
            if (nums[j - 1] == nums[j]) {
                n += 1;
            } else {
                n = 1;
            }

            if (n <= 2) {
                i += 1;
                nums[i] = nums[j];
            }
        }
        return i + 1;
    }
}
```

### Report

<img width="429" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/f3fd02d5-1ae7-4300-a8ed-23aa84ffa257">
<img width="1268" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/55f22b72-b135-46a4-98ee-39f09261e7ff">
<img width="1247" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/2dbae9b5-8268-4de7-861a-12b039efa4cd">

### Improved?

- 실행 시간과 메모리 사용 수준을 유지한 채 코드 개선 완료
