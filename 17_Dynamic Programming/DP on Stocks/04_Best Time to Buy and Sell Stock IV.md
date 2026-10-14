# Best Time to Buy and Sell Stock IV (LeetCode 188)

## Problem Statement

You are given:

```java
int[] prices
int k
```

where:

- `prices[i]` = stock price on day `i`
- You may complete **at most k transactions**
- You cannot engage in multiple transactions simultaneously

Return the maximum profit.

---

## Example 1

```text
Input:
k = 2
prices = [2,4,1]

Output:
2

Explanation:
Buy at 2
Sell at 4

Profit = 2
```

---

## Example 2

```text
Input:
k = 2
prices = [3,2,6,5,0,3]

Output:
7

Explanation:

Buy at 2
Sell at 6 = 4

Buy at 0
Sell at 3 = 3

Total = 7
```

---

# Intuition

Stock III allowed:

```text
At most 2 transactions
```

State:

```text
(index, buy, cap=2)
```

Stock IV generalizes this:

```text
At most k transactions
```

State:

```text
(index, buy, cap)
```

where

```text
cap ∈ [0...k]
```

---

# DP State

```java
dp[index][buy][cap]
```

### Meaning

```text
index -> current day

buy = 1
    allowed to buy

buy = 0
    holding stock, can sell

cap
    transactions remaining
```

---

# Recurrence

## Buy State

Choices:

```text
1. Buy
2. Skip
```

```java
dp[index][1][cap] =
Math.max(
    -prices[index] + dp[index+1][0][cap],
     dp[index+1][1][cap]
);
```

---

## Sell State

Choices:

```text
1. Sell
2. Skip
```

```java
dp[index][0][cap] =
Math.max(
    prices[index]
    + dp[index+1][1][cap-1],

    dp[index+1][0][cap]
);
```

Transaction count decreases after selling.

---

# Approach 1: Memoization

## Code

```java
class Solution {

    public int maxProfit(int k, int[] prices) {

        int n = prices.length;

        Integer[][][] dp =
                new Integer[n][2][k + 1];

        return solve(0, 1, k, prices, dp);
    }

    private int solve(int index,
                      int buy,
                      int cap,
                      int[] prices,
                      Integer[][][] dp) {

        if (index == prices.length || cap == 0)
            return 0;

        if (dp[index][buy][cap] != null)
            return dp[index][buy][cap];

        if (buy == 1) {

            return dp[index][buy][cap] =
                    Math.max(

                    -prices[index]
                            + solve(index + 1,
                            0,
                            cap,
                            prices,
                            dp),

                    solve(index + 1,
                            1,
                            cap,
                            prices,
                            dp)
            );
        }

        return dp[index][buy][cap] =
                Math.max(

                prices[index]
                        + solve(index + 1,
                        1,
                        cap - 1,
                        prices,
                        dp),

                solve(index + 1,
                        0,
                        cap,
                        prices,
                        dp)
        );
    }
}
```

---

## Complexity

```text
Time  : O(N × 2 × K)

Space : O(N × 2 × K)
```

---

# Approach 2: Tabulation

## Code

```java
class Solution {

    public int maxProfit(int k, int[] prices) {

        int n = prices.length;

        int[][][] dp =
                new int[n + 1][2][k + 1];

        for (int index = n - 1; index >= 0; index--) {

            for (int buy = 0; buy <= 1; buy++) {

                for (int cap = 1; cap <= k; cap++) {

                    if (buy == 1) {

                        dp[index][buy][cap] =
                                Math.max(

                                -prices[index]
                                + dp[index + 1][0][cap],

                                dp[index + 1][1][cap]
                        );

                    } else {

                        dp[index][buy][cap] =
                                Math.max(

                                prices[index]
                                + dp[index + 1][1][cap - 1],

                                dp[index + 1][0][cap]
                        );
                    }
                }
            }
        }

        return dp[0][1][k];
    }
}
```

---

## Complexity

```text
Time  : O(N × K)

Space : O(N × K)
```

---

# Approach 3: Space Optimized

Observation:

```text
Current day depends only on next day.
```

So keep:

```java
ahead[2][k+1]
curr[2][k+1]
```

---

## Code

```java
class Solution {

    public int maxProfit(int k, int[] prices) {

        int[][] ahead = new int[2][k + 1];

        for (int index = prices.length - 1;
             index >= 0;
             index--) {

            int[][] curr = new int[2][k + 1];

            for (int buy = 0; buy <= 1; buy++) {

                for (int cap = 1; cap <= k; cap++) {

                    if (buy == 1) {

                        curr[buy][cap] =
                                Math.max(

                                -prices[index]
                                        + ahead[0][cap],

                                ahead[1][cap]
                        );

                    } else {

                        curr[buy][cap] =
                                Math.max(

                                prices[index]
                                        + ahead[1][cap - 1],

                                ahead[0][cap]
                        );
                    }
                }
            }

            ahead = curr;
        }

        return ahead[1][k];
    }
}
```

---

## Complexity

```text
Time  : O(N × K)

Space : O(K)
```

---

# Most Important Optimization

Instead of:

```text
(index, buy, cap)
```

use transaction number.

---

## State Compression

Total operations:

```text
Buy Sell Buy Sell Buy Sell ...
```

For k transactions:

```text
2k states
```

Example:

```text
k = 2

0 -> Buy1
1 -> Sell1
2 -> Buy2
3 -> Sell2
```

---

## State DP

```java
dp[index][transaction]
```

where:

```text
transaction even -> Buy

transaction odd -> Sell
```

---

### Transition

Buy State:

```java
max(
   -price + next[trans+1],
    next[trans]
)
```

Sell State:

```java
max(
    price + next[trans+1],
    next[trans]
)
```

---

# Most Interview-Friendly Solution

```java
class Solution {

    public int maxProfit(int k, int[] prices) {

        int n = prices.length;

        int[] ahead = new int[2 * k + 1];

        for (int index = n - 1; index >= 0; index--) {

            int[] curr = new int[2 * k + 1];

            for (int trans = 2 * k - 1;
                 trans >= 0;
                 trans--) {

                if (trans % 2 == 0) {

                    curr[trans] = Math.max(

                            -prices[index]
                                    + ahead[trans + 1],

                            ahead[trans]
                    );

                } else {

                    curr[trans] = Math.max(

                            prices[index]
                                    + ahead[trans + 1],

                            ahead[trans]
                    );
                }
            }

            ahead = curr;
        }

        return ahead[0];
    }
}
```

---

# Dry Run

```text
k = 2

prices = [3,2,6,5,0,3]
```

Transactions:

```text
Buy at 2
Sell at 6  => 4

Buy at 0
Sell at 3  => 3
```

Answer:

```text
7
```

---

# Pattern Recognition

### Stock I

```text
1 Transaction
```

State:

```text
Min Price
```

---

### Stock II

```text
Infinite Transactions
```

State:

```text
(index,buy)
```

---

### Stock III

```text
2 Transactions
```

State:

```text
(index,buy,2)
```

---

### Stock IV

```text
K Transactions
```

State:

```text
(index,buy,k)
```

---

### Cooldown

```text
(index,buy)
```

with index+2 after sell.

---

### Transaction Fee

```text
(index,buy)
```

with:

```text
sell = price - fee
```

---

# Generic Formula For Stock IV

### Buy

```text
dp[i][1][cap] =
max(
   -price + dp[i+1][0][cap],
    dp[i+1][1][cap]
)
```

### Sell

```text
dp[i][0][cap] =
max(
    price + dp[i+1][1][cap-1],
    dp[i+1][0][cap]
)
```

### Answer

```java
dp[0][1][k]
```

---

# Complexity Summary

| Approach | Time | Space |
|-----------|--------|--------|
| Recursion | O(2^N) | O(N) |
| Memoization | O(N*K) | O(N*K) |
| Tabulation | O(N*K) | O(N*K) |
| Space Optimized | O(N*K) | O(K) |
| Transaction-State DP | O(N*K) | O(K) |

✅ **Interview Preferred:** Space Optimized DP (`buy/cap`) or Transaction-State DP (`2*k states`).
