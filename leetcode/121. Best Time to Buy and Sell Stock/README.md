# 121. Best Time to Buy and Sell Stock

- [문제 링크](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

## Requirements

- `i` 번째 날 주식 가격이 담긴 `prices` 배열 존재
- 특정 날에 주식을 사서 이후 다른 날에 팔았을 때 최대 수익을 구하라
- 수익을 낼 수 없다면 `0` 으로 처리

## Idea

- 2중 루프로 처리
- 왼쪽 -> 오른쪽으로 순회하면서 내부에서 다음 원소 -> 오른쪽으로 다시 순회
- 수익을 계산해서 현재까지의 최대 수익보다 크면 최대 수입 업데이트

```text
l = 길이(prices)
최대수익 = 0
for 0 <= i < l:
  for i < j < l:
    가격1 = prices[i]
    가격2 = prices[j]
    수익 = 가격2 - 가격1
    if 수악 > 최대수익:
      최대수익 = 수익
```

## Implementation

```java
class Solution {
    public int maxProfit(int[] prices) {
        var l = prices.length;
        var maxProfit = 0;
        for (var i = 0; i < l; i += 1) {
            for (var j = i + 1; j < l; j += 1) {
                var price1 = prices[i];
                var price2 = prices[j];
                var profit = price2 - price1;
                if (profit > maxProfit) {
                    maxProfit = profit;
                }
            }
        }
        return maxProfit;
    }
}
```

## Report

Time Limit Exceeded

- 길이 `67552` 인 `prices` 가 입력된 경우

## Idea

- 기본 로직은 유지
- 내부 루프를 돌면서 그 안에서 최소값 위치를 저장
- 외부 루프의 커서를 해당 최소값 위치에서부터 시작

```text
l = 길이(prices)
최대수익 = 0
i = 0
while i < l:
  최소가격위치 = i + 1
  for i < j < l:
    가격1 = prices[i]
    가격2 = prices[j]
    수익 = 가격2 - 가격1
    if 수악 > 최대수익:
      최대수익 = 수익
    최소가격 = prices[최소가격위치]
    if 가격2 < 최소가격:
      최소가격위치 = j
  i = 최소가격위치
```

## Implementation

```java
class Solution {
    public int maxProfit(int[] prices) {
        var l = prices.length;
        var maxProfit = 0;
        var i = 0;
        while (i < l) {
            var minIndex = i + 1;
            for (var j = i + 1; j < l; j += 1) {
                var price1 = prices[i];
                var price2 = prices[j];
                var profit = price2 - price1;
                if (profit > maxProfit) {
                    maxProfit = profit;
                }
                var minPrice = prices[minIndex];
                if (price2 < minPrice) {
                    minIndex = j;
                }
            }
            i = minIndex;
        }
        return maxProfit;
    }
}
```

## Report

Wrong Answer

```text
prices = [3,2,6,5,0,3]
output = 3
expected 4
```

- `3` 이후 최소값은 `0`
- 하지만 최대 수익이 발생하는 구간은 그 앞인 `2`
- 최소값이 항상 최대 수익을 보장하지 않음

## Idea

- 최소값 위치를 추적하지 말고 처음 시작 위치 값보다 작아지는 위치를 추적
- 시작 위치 값 이상인 값은 현재 루프에서 계산하는 수익보다 클 수 없음

```text
l = 길이(prices)
최대수익 = 0
i = 0
while i < l:
  다음i = i + 1
  다음i찾음? = false
  for i < j < l:
    가격1 = prices[i]
    가격2 = prices[j]
    수익 = 가격2 - 가격1
    if 수악 > 최대수익:
      최대수익 = 수익
    if !다음i찾음? 이고 가격2 < 가격1:
      다음i = j
      다음i찾음? = true
  i = 다음i
```

## Implementation

```java
class Solution {
    public int maxProfit(int[] prices) {
        var l = prices.length;
        var maxProfit = 0;
        var i = 0;
        while (i < l) {
            var nextI = i + 1;
            var isNextIFound = false;
            for (var j = i + 1; j < l; j += 1) {
                var price1 = prices[i];
                var price2 = prices[j];
                var profit = price2 - price1;
                if (profit > maxProfit) {
                    maxProfit = profit;
                }
                if (!isNextIFound && price2 < price1) {
                    nextI = j;
                    isNextIFound = true;
                }
            }
            i = nextI;
        }
        return maxProfit;
    }
}
```

## Report

Time Limit Exceeded

- 길이 `100000` 인 `prices` 가 입력된 경우
- 해당 배열은 끝 부분이 대량으로 `0` 인 경우

## Idea

- 최소값, 최대값이 꼭 최대 수익을 보장해주는 시작점은 아님
- 하지만 오른쪽 -> 왼쪽 순회하면서 처음으로 오른쪽 값보다 왼쪽 값이 작아지는 지점을 찾으면 탐색 범위를 좁힐 수 있음
- 왼쪽 -> 오른쪽 기준으로 우상향해야 수익이 나기 때문, 오른쪽 -> 왼쪽 기준 상승하는 구간은 수익이 발생 안 함

```text
l = 길이(prices)

k = l - 1
while k > 0:
  오른쪽가격 = prices[k]
  왼쪽가격 = prices[k - 1]
  if 왼쪽가격 < 오른쪽가격:
    break
  k -= 1

최대수익 = 0
i = 0
while i < k:
  다음i = i + 1
  다음i찾음? = false
  for i < j <= k:
    가격1 = prices[i]
    가격2 = prices[j]
    수익 = 가격2 - 가격1
    if 수악 > 최대수익:
      최대수익 = 수익
    if !다음i찾음? 이고 가격2 < 가격1:
      다음i = j
      다음i찾음? = true
  i = 다음i
```

## Implementation

```java
class Solution {
    public int maxProfit(int[] prices) {
        var l = prices.length;

        var k = l - 1;
        while (k > 0) {
            var rightPrice = prices[k];
            var leftPrice = prices[k - 1];
            if (leftPrice < rightPrice) {
                break;
            }
            k -= 1;
        }

        var maxProfit = 0;
        var i = 0;
        while (i < k) {
            var nextI = i + 1;
            var isNextIFound = false;
            for (var j = i + 1; j <= k; j += 1) {
                var price1 = prices[i];
                var price2 = prices[j];
                var profit = price2 - price1;
                if (profit > maxProfit) {
                    maxProfit = profit;
                }
                if (!isNextIFound && price2 < price1) {
                    nextI = j;
                    isNextIFound = true;
                }
            }
            i = nextI;
        }
        return maxProfit;
    }
}
```

## Report

Accepted

Runtime 274 ms, Beats 5.46%

Memory 61 MB, Beats 66.32%

## Study

2중 루프를 제거하는 방법은?

다른 풀이 참고:

```java
class Solution {
    public int maxProfit(int[] prices) {
        int minPrice = Integer.MAX_VALUE;
        int maxProfit = 0;

        for (int price : prices) {
            if (price < minPrice) {
                minPrice = price;
            }
            var profit = price - minPrice;
            if (maxProfit < profit) {
                maxProfit = profit;
            }
        }
        return maxProfit;
    }
}
```

풀이 설명:

- 왼쪽 -> 오른쪽 순회하면서 항상 처음~현재 범위에서 최소값을 추적
- 오늘 판매한다면 과거 최소값이었을 때 샀을 때 최대 수익
- 오늘 팔았을 때 최대 수익이 기존 최대 수익보다 크면 업데이트
