# Best Time to Buy and Sell Stock with Transaction Fee (LeetCode 714)

## Problem Statement

You are given:

```java
int[] prices
int fee
```

where:

- `prices[i]` is the stock price on day `i`
- You may perform unlimited transactions
- For every completed transaction, a fixed transaction fee is charged

Return the maximum profit.

---

## Example 1

```text
Input:
prices = [1,3,2,8,4,9]
fee = 2

Output:
8
```

### Explanation

```text
Buy  at 1
Sell at 8

Profit = 8 - 1 - 2 = 5

Buy  at 4
Sell at 9

Profit = 9 - 4 - 2 = 3

Total Profit = 8
```

---

# Intuition

This problem is almost identical to:

```text
Best Time to Buy and Sell Stock II
```

where:

```text
Unlimited Transactions
```

The only difference is:

```text
Whenever we sell,
we must pay a fee.
```

---

# DP State

Let:

```java
dp[index][buy]
```

where

```text
buy = 1
    We can buy

buy = 0
    We currently hold stock and can sell
```

---

# Recurrence

## Buy State

Choices:

```text
1. Buy stock
2. Skip
```

```java
profit =
Math.max(

    -prices[index] + solve(index+1,0),

    solve(index+1,1)
);
```

---

## Sell State

Choices:

```text
1. Sell stock
2. Skip
```

Fee is deducted during selling.

```java
profit =
Math.max(

    prices[index] - fee
        + solve(index+1,1),

    solve(index+1,0)
);
```

---

# Approach 1: Memoization

## Code

```java
class Solution {

    public int maxProfit(int[] prices, int fee) {

        Integer[][] dp =
                new Integer[prices.length][2];

        return solve(0, 1, prices, fee, dp);
    }

    private int solve(int index,
                      int buy,
                      int[] prices,
                      int fee,
                      Integer[][] dp) {

        if (index == prices.length)
            return 0;

        if (dp[index][buy] != null)
            return dp[index][buy];

        if (buy == 1) {

            return dp[index][buy] =
                    Math.max(

                    -prices[index]
                            + solve(index + 1,
                            0,
                            prices,
                            fee,
                            dp),

                    solve(index + 1,
                            1,
                            prices,
                            fee,
                            dp)
            );
        }

        return dp[index][buy] =
                Math.max(

                prices[index]
                        - fee
                        + solve(index + 1,
                        1,
                        prices,
                        fee,
                        dp),

                solve(index + 1,
                        0,
                        prices,
                        fee,
                        dp)
        );
    }
}
```

---

## Complexity

```text
Time  : O(N)
Space : O(N)
```

---

# Approach 2: Tabulation

## Code

```java
class Solution {

    public int maxProfit(int[] prices, int fee) {

        int n = prices.length;

        int[][] dp =
                new int[n + 1][2];

        for (int index = n - 1;
             index >= 0;
             index--) {

            dp[index][1] = Math.max(

                    -prices[index]
                            + dp[index + 1][0],

                    dp[index + 1][1]
            );

            dp[index][0] = Math.max(

                    prices[index]
                            - fee
                            + dp[index + 1][1],

                    dp[index + 1][0]
            );
        }

        return dp[0][1];
    }
}
```

---

## Complexity

```text
Time  : O(N)

Space : O(N)
```

---

# Approach 3: Space Optimized

Since:

```text
Current state depends only on next day.
```

Keep only:

```java
aheadBuy
aheadSell
```

---

## Code

```java
class Solution {

    public int maxProfit(int[] prices, int fee) {

        int aheadBuy = 0;
        int aheadSell = 0;

        for (int index = prices.length - 1;
             index >= 0;
             index--) {

            int currBuy = Math.max(

                    -prices[index] + aheadSell,

                    aheadBuy
            );

            int currSell = Math.max(

                    prices[index]
                            - fee
                            + aheadBuy,

                    aheadSell
            );

            aheadBuy = currBuy;
            aheadSell = currSell;
        }

        return aheadBuy;
    }
}
```

---

# Most Interview-Friendly Solution

Instead of DP arrays, maintain only two states.

---

## State Meaning

```java
hold
```

Maximum profit if we currently hold a stock.

```java
cash
```

Maximum profit if we do not hold a stock.

---

### Initially

```java
hold = -prices[0]
cash = 0
```

---

### Transition

#### Buy

```java
hold = Math.max(
        hold,
        cash - price
);
```

#### Sell

```java
cash = Math.max(
        cash,
        hold + price - fee
);
```

---

# Optimal Solution

```java
class Solution {

    public int maxProfit(int[] prices, int fee) {

        int hold = -prices[0];
        int cash = 0;

        for (int i = 1; i < prices.length; i++) {

            hold = Math.max(
                    hold,
                    cash - prices[i]
            );

            cash = Math.max(
                    cash,
                    hold + prices[i] - fee
            );
        }

        return cash;
    }
}
```

---

# Dry Run

```text
prices = [1,3,2,8,4,9]

fee = 2
```

Initially:

```text
hold = -1
cash = 0
```

Day 2:

```text
price = 3

hold = max(-1,0-3)
     = -1

cash = max(0,-1+3-2)
     = 0
```

Day 4:

```text
price = 8

cash = max(0,-1+8-2)

cash = 5
```

Day 6:

```text
price = 9

cash = max(5,1+9-2)

cash = 8
```

Answer:

```text
8
```

---

# Why Fee Is Deducted During Sell?

A transaction is:

```text
Buy -> Sell
```

Fee is charged once per completed transaction.

Therefore:

```java
sellProfit =
price - fee
```

is the most natural representation.

Some solutions deduct fee during buy:

```java
-(price + fee)
```

Both approaches are equivalent.

---

# Pattern Recognition

### Stock I

```text
Single Transaction
```

State:

```text
Min Price
```

---

### Stock II

```text
Unlimited Transactions
```

State:

```text
(index,buy)
```

---

### Stock III

```text
At Most 2 Transactions
```

State:

```text
(index,buy,cap)
```

---

### Stock IV

```text
At Most K Transactions
```

State:

```text
(index,buy,k)
```

---

### Stock with Cooldown

```text
After sell:
jump to index + 2
```

---

### Stock with Transaction Fee

```text
(index,buy)

Sell:
price - fee
```

---

# DP Formula

### Buy State

```text
dp[i][1] =
max(
    -price[i] + dp[i+1][0],
     dp[i+1][1]
)
```

### Sell State

```text
dp[i][0] =
max(
    price[i] - fee + dp[i+1][1],
    dp[i+1][0]
)
```

---

# Complexity Summary

| Approach | Time | Space |
|-----------|--------|--------|
| Memoization | O(N) | O(N) |
| Tabulation | O(N) | O(N) |
| Space Optimized | O(N) | O(1) |
| Hold/Cash State Machine | O(N) | O(1) |

✅ **Interview Preferred:** Hold/Cash State Machine Solution

```java
hold = max(hold, cash - price);

cash = max(cash, hold + price - fee);
```
