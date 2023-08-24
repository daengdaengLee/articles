ㅑ# 121. Best Time to Buy and Sell Stock

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
