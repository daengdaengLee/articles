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

## Report

<img width="418" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/3458d805-4878-4539-818d-2edaf6c708ba">
<img width="1252" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/e6b01cca-a054-441b-803f-4cdf3a804dfa">
<img width="1266" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/1b660f45-ef95-43f6-9e12-4c5d824f34ba">

## Is it good?

- 평균보다 시간이 많이 소요됨
- 현재 값보다 큰 원소를 탐색하기 위한 순회가 계속 실행되기 때문
    - `1 2 2 2 2 3 4` 인 경우
    - `i: 0`: continue -> `1 2 2 2 2 3 4`
    - `i: 1`: index 2 부터 index 5 까지 순회, 큰 값 찾아서 swap -> `1 2 3 2 2 2 4`
    - `i: 2`: index 3 부터 index 6 까지 순회, 큰 값 찾아서 swap -> `1 2 3 4 2 2 2`
    - `i: 3`: index 4 부터 index 6 까지 순회, 큰 값 없음 -> break
    - 중복된 숫자 길이만큼 계속 반복해서 순회하게 됨
- 한 번 큰 값 위치를 찾으면 다음부턴 그 위치부터 탐색하도록 하면 개선할 수 있음
    - `1 2 2 2 2 3 4` 인 경우
    - `i: 0`: continue -> `1 2 2 2 2 3 4`
    - `i: 1`: index 2 부터 index 5 까지 순회, 큰 값 찾아서 swap -> `1 2 3 2 2 2 4`, index 6 캐싱
    - `i: 2`: index 6 부터 index 6 까지 순회, 큰 값 찾아서 swap -> `1 2 3 4 2 2 2`, index 7 캐싱
    - `i: 3`: index 7 부터 index 6 까지 순회, 순회할 값 없음 -> break
    - 마지막 swap 한 인덱스를 캐싱해서 중복된 숫자는 건너뛰고 찾을 수 있음

## Improved Implementation

```java
// runtime : java 17

class Solution {
    public int removeDuplicates(int[] nums) {
        int i = 0;
        int j = 0;
        boolean isCached = false;

        while (i < nums.length - 1) {
            int current = nums[i];
            int next = nums[i + 1];

            if (current < next) {
                i += 1;
                continue;
            }

            boolean isFound = false;
            if (!isCached) {
                j = i + 2;
            }
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
            j += 1;
        }

        return i + 1;
    }
}
```

## Report

<img width="419" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/5d1a74ad-ffd0-4446-b9db-310763d695bb">
<img width="1221" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/083ff9fe-5b7c-4d8b-a920-3c5a0bda7283">
<img width="1242" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/4b655708-ac82-49bf-ac7b-0389e0744d9b">

## Improved?

- 기대했던 결과가 나오지 않음
- 실행 속도에 영향을 줄 다른 요소는?: XOR 연산
- 이 문제에서는 현재값, 다음값, 찾은값이 있을 때 현재값 뒤에 찾은값을 두는 게 목표
- 뒤로 보낸 다음값은 결과에 영향을 주지 않는다면 숫자가 바뀌어도 상관없음
- 뒤로 보낸 값은 현재 값보다 크지만 않으면 이후 로직에 문제 없기 때문에 현재값을 보내도 상관 없음
- 그러면 XOR 연산을 하지 않고 다음값 위치에 찾은값을 덮어쓰고, 찾은값 위치에 현재값을 덮어쓰면 됨

## Improved Implementation 2

```java
// runtime : java 17

class Solution {
    public int removeDuplicates(int[] nums) {
        int i = 0;
        int j = 0;
        boolean isCached = false;

        while (i < nums.length - 1) {
            int current = nums[i];
            int next = nums[i + 1];

            if (current < next) {
                i += 1;
                continue;
            }

            boolean isFound = false;
            if (!isCached) {
                j = i + 2;
            }
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

            nums[i + 1] = nums[j];
            nums[j] = nums[i];
            i += 1;
            j += 1;
        }

        return i + 1;
    }
}
```

## Report

<img width="433" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/1c6a8d0c-ae3a-479d-b2f1-237b1caa2226">
<img width="1244" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/9837629e-aa5f-4b24-a5fb-9075d807327a">
<img width="1242" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/7235518b-c56e-4df4-8d74-4d5baa08eeb6">

## Improved?

- 기대했던 결과가 나오지 않음
- 가장 빠른 속도 샘플 코드 참고:
  ```java
    class Solution {
        public int removeDuplicates(int[] nums) {
            int i = 0;
            for (int j = 1; j < nums.length; j += 1) {
                if (nums[i] != nums[j]) {
                    i += 1;
                    nums[i] = nums[j];  
                }
            }
            return i + 1;
        }
    }
  ```
- 이미 non-decreasing order 로 정렬되어 있는 조건 사용
- 이후 값들 중 다른 값이 나오면 무조건 현재 값보다 큰 값이 됨
- 커서 2개를 준비하고 뒤쪽 커서를 이동시키면서 앞쪽 커서 값과 다른 값 탐색, 찾으면 앞쪽 커서를 이동시키고 뒤쪽 커서 값으로 덮어씀
- swap 이 필요 없는 이유는 덮어쓴 순간 두 커서에 위치한 값이 같이지기 때문, 뒤쪽 값이 더 커지지만 않으면 로직에 문제 없음
- 만약 2개 커서가 붙어 있었던 경우: 자기 자신으로 덮어쓰기 때문에 분기해서 처리할 필요 없음
- 이전 구현에서는 앞쪽 커서를 기준으로 생각했음. 그러면 항상 중복되지 않는 다음 값을 찾기 위해 뒤쪽 커서를 이동시키는 nested loop 발생
- 샘플 코드에서는 뒤쪽 커서를 기준으로 중복되지 않는 값을 바로 찾기 때문에 nested loop 가 없음
- 시간을 단축하는 효과적인 방법은 불필요한 순회를 제거하는 것! 다른 요인을 찾기 전에 내 아이디어에서 순회를 제거할 수 있는 방법이 있는지 먼저 고민하자!
