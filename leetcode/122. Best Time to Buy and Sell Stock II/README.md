# 122. Best Time to Buy and Sell Stock II

- [문제 링크](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)

## Requirements

- `i` 번째 날 주식 가격 배열 `prices`
- 매일 주식을 사거나 팔 수 있다.
- 보유할 수 있는 주식은 최대 1개
- 산 주식을 다일에 바로 팔 수 있다.
- 최대 누적 수익을 계산하라.

## Idea

- 왼쪽 -> 오른쪽으로 순회하면서 연속한 두 날 가격을 비교
- 수익이 난 경우 어제 사서 오늘 판 것으로 계산
- 당일에 사고 팔 수 있으므로, 연속해서 수익이 난 경우 그저께 사서 어제 팔고, 어제 사서 오늘 판 것으로 처리

```text
l = 길이(prices)
누적수익 = 0
for 0 < i < l:
  어제가격 = prices[i - 1]
  오늘가격 = prices[i]
  수익 = 오늘가격 - 어제가격
  if 수익 > 0:
    누적수익 += 수익
return 누적수익
```

## Implementation

```java
class Solution {
    public int maxProfit(int[] prices) {
        var l = prices.length;
        var maxProfit = 0;
        for (var i = 1; i < l; i += 1) {
            var price1 = prices[i - 1];
            var price2 = prices[i];
            var profit = price2 - price1;
            if (profit > 0) {
                maxProfit += profit;
            }
        }
        return maxProfit;
    }
}
```

## Report

Accepted

Runtime 1 ms, Beats 86.69%

Memory 44.2 MB, Beats 78.42%
