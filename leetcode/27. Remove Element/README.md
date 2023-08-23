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

### In-place swap 원리

- 정수 `a` 에 대해 `a ^ a = 0`: 모든 비트가 같기 때문에
- 정수 `a` 에 대해 `a ^ 0 = a` : 비트 단위에서 1인 비트는 `1 ^ 0 = 1`, 0인 비트는 `0 ^ 0 = 0` 이기 때문에
- 두 정수 `a`, `b`에 대해 `a ^ b = b ^ a`: 비트 단위에서 서로 같은지 다른지만 판단하기 때문에
    - `1 ^ 1`, `0 ^ 0`: 바꿔도 `1 ^ 1`, `0 ^ 0`
    - `1 ^ 0`, `0 ^ 1`: 둘 다 계산 결과 `1`
- 두 정수 `a`, `b`에 대해 `(a ^ b) ^ c = a ^ (b ^ c)`
    - `(1 ^ 1) ^ 1`, `1 ^ (1 ^ 1)`: 둘 다 계산 결과 `1`
    - `(0 ^ 0) ^ 1`, `0 ^ (0 ^ 1)`: 둘 다 계산 결과 `1`
    - `(1 ^ 0) ^ 0`, `1 ^ (0 ^ 0)`: 둘 다 계산 결과 `1`
    - `(0 ^ 1) ^ 0`, `0 ^ (1 ^ 0)`: 둘 다 계산 결과 `1`
    - `(1 ^ 0) ^ 1`, `1 ^ (0 ^ 1)`: 둘 다 계산 결과 `0`
    - `(0 ^ 1) ^ 1`, `0 ^ (1 ^ 1)`: 둘 다 계산 결과 `0`
    - `(1 ^ 1) ^ 0`, `1 ^ (1 ^ 0)`: 둘 다 계산 결과 `0`
    - `(0 ^ 0) ^ 0`, `0 ^ (0 ^ 0)`: 둘 다 계산 결과 `0`
- 두 정수가 있는 배열 `{a, b}` 에 대해
    - 인덱스 1 위치에 `(인덱스 0) ^ (인덱스 1)` 값 할당: `{a, a ^ b}`
    - 인덱스 0 위치에 `(인덱스 0) ^ (인덱스 1)` 값 할당: `{a ^ (a ^ b), a ^ b}`
    - `a ^ (a ^ b) = (a ^ a) ^ b = 0 ^ b = b ^ 0 = b` 이기 때문에 계산 결과: `{b, a ^ b}`
    - 인덱스 1 위치에 `(인덱스 0) ^ (인덱스 1)` 값 할당: `{b, b ^ (a ^ b)}`
    - `b ^ (a ^ b) = b ^ (b ^ a) = (b ^ b) ^ a = 0 ^ a = a ^ 0 = a` 이기 때문에 계산 결과: `{b, a}`
    - In-place swap 완성!

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

## Error Case

- `nums = [2]`, `val = 3`
    - `expected = [2]`
    - `recieved = []`
- `nums` 길이가 0, 1, 2 이면 커서 순회가 아예 안 하게 됨
- 해당 케이스는 커서 순회 전에 미리 처리

### Fix Implementation

```java
// runtime : java 17

class Solution {
    public int removeElement(int[] nums, int val) {
        int l = nums.length;
        if (l == 0) {
            return 0;
        }
        if (l == 1) {
            return nums[0] == val ? 0 : 1;
        }
        if (l == 2) {
            if (nums[0] == val && nums[1] == val) {
                return 0;
            }
            if (nums[0] != val && nums[1] != val) {
                return 2;
            }

            if (nums[0] == val) {
                nums[0] ^= nums[1];
                nums[1] ^= nums[0];
                nums[0] ^= nums[1];
            }
            return 1;
        }

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

## Error Case 2

- `nums = [2, 2, 2]`, `val = 0`
    - `expected = [2, 2, 2]`
    - `recieved = [2, 2]`
- `nums` 의 모든 원소가 `val` 이 아닌 경우 `i < j` 조건에서는 마지막 `j` 커서의 원소가 누락됨
- 순회 종료 후 `i` 커서가 `l - 1` 인 경우 `j` 커서의 값 추가로 검사

### Fix Implementation

```java
// runtime : java 17

class Solution {
    public int removeElement(int[] nums, int val) {
        int l = nums.length;

        if (l == 0) {
            return 0;
        }
        if (l == 1) {
            return nums[0] == val ? 0 : 1;
        }
        if (l == 2) {
            if (nums[0] == val && nums[1] == val) {
                return 0;
            }
            if (nums[0] != val && nums[1] != val) {
                return 2;
            }

            if (nums[0] == val) {
                this.inPlaceSwap(nums, 0, 1);
            }
            return 1;
        }

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

            this.inPlaceSwap(nums, i, j);
        }

        if (i == l - 1 && nums[l - 1] != val) {
            return i + 1;
        }

        return i;
    }

    private void inPlaceSwap(int[] nums, int i, int j) {
        nums[i] ^= nums[j];
        nums[j] ^= nums[i];
        nums[i] ^= nums[j];
    }
}
```

## Accepted

<img width="428" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/3a6af1b4-08da-4a73-926e-ab10543f3e45">

### Runtime

<img width="1233" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/df0bdbff-ea58-4aac-9945-76e14cb0cedf">

### Memory

<img width="1242" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/3df2bf49-376d-41bd-86ed-4aca0d78bc4c">

- 생각보다 메모리를 많이 사용함
- 로컬 변수를 많이 사용한 부분을 의심
- 메소드를 분리해서 클래스 영역 메모리를 많이 사용한 건 아닌지 의심

## Improvement

- 로컬 변수 `l` 삭제하고 `nums.length` 사용
- 분리한 메소드 다시 inlining

```java
// runtime : java 17

class Solution {
    public int removeElement(int[] nums, int val) {
        if (nums.length == 0) {
            return 0;
        }
        if (nums.length == 1) {
            return nums[0] == val ? 0 : 1;
        }
        if (nums.length == 2) {
            if (nums[0] == val && nums[1] == val) {
                return 0;
            }
            if (nums[0] != val && nums[1] != val) {
                return 2;
            }

            if (nums[0] == val) {
                nums[0] ^= nums[1];
                nums[1] ^= nums[0];
                nums[0] ^= nums[1];
            }
            return 1;
        }

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

        if (i == nums.length - 1 && nums[nums.length - 1] != val) {
            return i + 1;
        }

        return i;
    }
}
```

### Accepted

<img width="424" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/f951e748-1889-478b-9e52-227886d78fa2">

#### Runtime

<img width="1239" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/6f116a69-bcec-4f70-9781-3c0ad826212c">

- 런타임은 이전 시도 결과를 유지: OK

#### Memory

<img width="1258" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/47f82641-75be-4f13-8886-8e40b162b98f">

- 메모리 사용이 약간 줄어듦
- 다른 경우와 비교했을 때 68.77% -> 96.12% 로 꽤 상승함
- 두 가지 가정이 효과가 있다고 판단
