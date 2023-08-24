# 55. Jump Game

- [문제 링크](https://leetcode.com/problems/jump-game/)

## Requirements

- 정수 배열 `nums`
- `0` 번 인덱스부터 시작해서 오른쪽으로 끝까지 이동할 수 있는지 판단
- 각 인덱스의 값은 최대로 이동할 수 있는 거리를 나타냄
- `1 <= nums.length <= 10^4`
- `0 <= nums[i] <= 10^5`

## Idea

- `0` 인 원소가 함정카드, 해당 원소를 밟으면 더 이상 움직일 수 없음
- 가장 마지막 원소는 `0` 이어도 상관없음, 더 이상 움직일 필요가 없기 때문
- 첫 원소가 `0` 이면 무조건 실패
- 처음, 마지막을 제외한 나머지 위치에서 `0` 인 칸을 어떻게 피하는가?
- 현재 위치를 나타내는 커서와 `0` 을 찾아 움직이는 커서 2개를 사용
- `0` 을 찾았으면 해당 위치부터 현재 위치까지 거꾸로 탐색하면서 `0` 인 칸을 넘을 수 있는지 검사

```text
// Pseudocode

if nums[0] == 0:
  return false
  
l = 길이(nums) 
i = 0
for 1 <= j <= l - 1:
  if nums[j] != 0:
    continue
    
  점프? = false
  for k 는 (j-1)(포함) -> i(포함):
    거리 = j - k
    if nums[k] > 거리:
      i = j + 1
      점프? = true
      break
  if !점프?:
    return false
return true
```

## Implementation

```java
class Solution {
    public boolean canJump(int[] nums) {
        if (nums[0] == 0) {
            return false;
        }

        var l = nums.length;
        var i = 0;
        for (var j = 1; j <= l - 1; j += 1) {
            if (nums[j] != 0) {
                continue;
            }

            boolean isJump = false;
            for (var k = j - 1; k >= i; k -= 1) {
                var distance = j - k;
                if (nums[k] > distance) {
                    i = j + 1;
                    isJump = true;
                    break;
                }
            }
            if (!isJump) {
                return false;
            }
        }
        return true;
    }
}
```

## Report

Wrong Answer

```text
input = [0]
output = false
expected = true
```

- 길이가 `1` 인 경우 첫 칸이 `0` 이어도 이미 끝까지 이동한 셈

## Idea

길이가 `1` 인 경우 성공으로 처리하는 분기를 먼저 처리

```text
// Pseudocode

l = 길이(nums) 

if l == 1:
  return true

if nums[0] == 0:
  return false  

i = 0
for 1 <= j <= l - 1:
  if nums[j] != 0:
    continue
    
  점프? = false
  for k 는 (j-1)(포함) -> i(포함):
    거리 = j - k
    if nums[k] > 거리:
      i = j + 1
      점프? = true
      break
  if !점프?:
    return false
return true
```

## Implementation

```java
class Solution {
    public boolean canJump(int[] nums) {
        var l = nums.length;

        if (l == 1) {
            return true;
        }

        if (nums[0] == 0) {
            return false;
        }

        var i = 0;
        for (var j = 1; j <= l - 1; j += 1) {
            if (nums[j] != 0) {
                continue;
            }

            boolean isJump = false;
            for (var k = j - 1; k >= i; k -= 1) {
                var distance = j - k;
                if (nums[k] > distance) {
                    i = j + 1;
                    isJump = true;
                    break;
                }
            }
            if (!isJump) {
                return false;
            }
        }
        return true;
    }
}
```

## Report

Wrong Answer

```text
input = [2,0,0]
output = false
expected = true
```

- `j + 1` 로 점프한 후 다음 번 `0` 탐색 시작 지점을 조정해야 함

## Idea

- 단순히 `0` 을 만난 순간 점프할 방법을 찾는 게 아니라 연속한 `0` 을 확인해서 가장 끝 `0` 을 뛰어넘기 위한 방법을 찾아야 함
- 연속한 `0` 중 가장 오른쪽 `0` 이 마지막 원소라면 해당 지점까지, 마지막 원소가 아니라면 해당 지점 다음 칸까지 점프해야 함
- `0` 이 아닌 칸으로 점프한 후 그 다음 칸으로 `0` 탐색 커서를 옮겨야 함

```text
// Pseudocode

l = 길이(nums) 

if l == 1:
  return true

if nums[0] == 0:
  return false  

i = 0
j1 = 1
while j1 < l - 1:
  if nums[j1] != 0:
    j1 += 1
    continue
    
  j2 = j1 // 연속한 0 이 끝나는 점프할 목적지
  while j2 < l - 1:
    if nums[j2] != 0:
      break
    j2 += 1
    
  점프? = false
  for k 는 (j1-1)(포함) -> i(포함):
    거리 = j2 - k
    if nums[k] >= 거리:
      i = j2
      j1 = j2
      점프? = true
      break
  if !점프?:
    return false
return true
```

## Implementation

```java
class Solution {
    public boolean canJump(int[] nums) {
        var l = nums.length;

        if (l == 1) {
            return true;
        }

        if (nums[0] == 0) {
            return false;
        }

        var i = 0;
        var j1 = 1;
        while (j1 < l - 1) {
            if (nums[j1] != 0) {
                j1 += 1;
                continue;
            }

            var j2 = j1;
            while (j2 < l - 1) {
                if (nums[j2] != 0) {
                    break;
                }
                j2 += 1;
            }

            boolean isJump = false;
            for (var k = j1 - 1; k >= i; k -= 1) {
                var distance = j2 - k;
                if (nums[k] >= distance) {
                    i = j2;
                    j1 = j2;
                    isJump = true;
                    break;
                }
            }
            if (!isJump) {
                return false;
            }
        }
        return true;
    }
}
```

## Report

Wrong Answer

```text
nums = [8,2,4,4,4,9,5,2,5,8,8,0,8,6,9,1,1,6,3,5,1,2,6,6,0,4,8,6,0,3,2,8,7,6,5,1,7,0,3,4,8,3,5,9,0,4,0,1,0,5,9,2,0,7,0,2,
        1,0,8,2,5,1,2,3,9,7,4,7,0,0,1,8,5,6,7,5,1,9,9,3,5,0,7,5]
output = false
expected true
```

- `0` 이 한 칸씩 건너뛰면서 연속해서 나오는 경우 실패
- 한 번에 `0` 인 구간 모두 건너뛸 수 있지만 현재 로직에서는 하나씩 건너뛰다가 실패함

## Idea

- 앞에서부터 탐색하지 말고 뒤에서부터 탐색
- 뒤에서부터 탐색하다가 `0` 을 만나면 그 앞으로 탐색하면서 해당 `0` 을 뛰어넘을 수 있는지 판단
- 뛰어넘을 수 있으면 `0` 추적 커서 위치를 현재 위치로 옮기고 다시 앞으로 가면서 `0` 탐색

```text
// Pseudocode

l = 길이(nums) 

if l == 1:
  return true

if nums[0] == 0:
  return false  

i = l - 1
j = l - 2
while j > 0:
  if nums[j] != 0:
    j -= 1
    continue
    
  i = j - 1
  점프? = false
  while i >= 0:
    거리 = j - i
    if nums[i] > 거리:
      j = i
      점프? = true
      break
    i -= 1
  if !점프?:
    return false 
return true
```

## Implementation

```java
class Solution {
    public boolean canJump(int[] nums) {
        var l = nums.length;

        if (l == 1) {
            return true;
        }

        if (nums[0] == 0) {
            return false;
        }

        var i = l - 1;
        var j = l - 2;
        while (j > 0) {
            if (nums[j] != 0) {
                j -= 1;
                continue;
            }

            i = j - 1;
            var isJump = false;
            while (i >= 0) {
                var distance = j - i;
                if (nums[i] > distance) {
                    j = i;
                    isJump = true;
                    break;
                }
                i -= 1;
            }
            if (!isJump) {
                return false;
            }
        }
        return true;
    }
}
```

## Report

Accepted

Runtime 1 ms, Beats 99.98%

Memory 44.2 MB, Beats 64.36%
