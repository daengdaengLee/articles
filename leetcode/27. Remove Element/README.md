# 27. Remove Element

- [문제 링크](https://leetcode.com/problems/remove-element/)
- [솔루션 링크](https://leetcode.com/problems/remove-element/solutions/3947848/in-place-swap-using-two-cursors/)

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

## Is it good?

- 예외 케이스를 처리하느라 분기가 많고 코드가 길어짐
- 같은 아이디어를 구현하면서 분기 없이 단순하게 구현할 수 없을까?
- 가장 적은 메모리를 사용한 샘플 코드를 참고
  ```java
    class Solution {
        public int removeElement(int[] nums, int val) {
            int s = 0;
            int e = nums.length - 1;
            while (s <= e) {
                if (nums[e] == val) {
                    e--;
                } else {
                    if (nums[s] == val) {
                        int temp = nums[e];
                        nums[e] = nums[s];
                        nums[s] = temp;
                        s++;
                        e--;
                    } else {
                        s++;
                    }
                }
            }
            return e + 1;
        }
    }
  ```
- 두 커서를 같거나 작다로 비교해서 Error Case 1, Error Case 2 상황이 안 발생하게 처리
- 이렇게 하면 예외 조건을 분기할 필요 없고 비즈니스 로직만 남길 수 있음, 분기가 없기 때문에 중복 코드도 발생 안 함
- swap 이후 두 커서를 이동시켜 순회 횟수 줄임
- 예제 코드에서는 임시 변수를 사용했는데, 저렇게 해도 In-place swap 으로 인정하는 듯, 나는 계속 XOR swap 방식 유지

### Improved Implementation

```java
// runtime : java 17

class Solution {
    public int removeElement(int[] nums, int val) {
        int i = 0;
        int j = nums.length - 1;

        while (i <= j) {
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
            i += 1;
            j -= 1;
        }

        return i;
    }
}
```

- 코드가 훨씬 짧아지고 분기가 없어져서 읽기 편해짐
- swap 이후 커서 이동을 바로 해서 순회 횟수 줄임

#### Accepted

<img width="425" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/879a14d2-4fab-4f60-ad48-3f24f24477c9">

##### Runtime

<img width="1257" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/cd99b3a4-4ee8-4ec0-ad4d-3f41b26ea61a">

##### Memory

<img width="1248" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/ac6bcfdc-f7fb-40dd-9554-d045d56091b9">

- 이상하게 메모리 사용이 다시 올라감
- 분리한 메소드도 없고 변수 사용도 그대로: 앞선 시도에서 개선한 건 우연일 가능성이 있음
- 동일한 코드로 재시도

#### Accepted 2

<img width="430" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/cf7eef1d-b18c-4f16-8405-1683a0e2b9c1">

##### Runtime

<img width="1265" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/0aa4967f-2740-4d09-b08b-7fd01657b71d">

##### Memory

<img width="1234" alt="image" src="https://github.com/daengdaengLee/articles/assets/30795415/4e343e25-97b5-47c8-abc5-a04f1ae8c678">

- 동일한 코드로 실행했을 때 메모리 사용이 다시 줄어듦
- 이 정도 차이는 실행 시 오차 범위라고 판단
- 다음부터는 반복 시도한 결과를 종합하여 개선 여부를 판단할 것!
