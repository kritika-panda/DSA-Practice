# Best Time to Buy and Sell Stock III (LeetCode 123)

## Problem Statement

You are given an integer array `prices` where:

```java
prices[i]
```

is the stock price on day `i`.

You may complete **at most two transactions**.

A transaction consists of:

```text
Buy → Sell
```

You cannot hold multiple stocks simultaneously.

Return the maximum profit.

---

## Example 1

```text
Input:
prices = [3,3,5,0,0,3,1,4]

Output:
6

Explanation:

Buy at 0, Sell at 3 → Profit = 3
Buy at 1, Sell at 4 → Profit = 3

Total Profit = 6
```

---

## Example 2

```text
Input:
prices = [1,2,3,4,5]

Output:
4
```

---

## Example 3

```text
Input:
prices = [7,6,4,3,1]

Output:
0
```

---

# Intuition

In Stock-I we had:

```text
Buy once
Sell once
```

Now we can perform:

```text
Buy → Sell → Buy → Sell
```

Maximum 2 transactions.

---

# DP State Definition

We need three variables:

```text
index      -> current day

buy        -> can buy or must sell

cap        -> transactions remaining
```

State:

```java
dp[index][buy][cap]
```

where:

```text
buy = 1 → allowed to buy

buy = 0 → holding stock, must sell

cap = remaining transactions
```

---

# Recurrence

## Buy State

Either:

```text
Buy stock
Skip
```

```java
profit = Math.max(
        -prices[index] + solve(index+1,0,cap),
         solve(index+1,1,cap)
);
```

---

## Sell State

Either:

```text
Sell stock
Skip
```

```java
profit = Math.max(
        prices[index] + solve(index+1,1,cap-1),
        solve(index+1,0,cap)
);
```

Notice:

```text
Transaction count decreases only after selling.
```

---

# Approach 1: Recursion

### Code

```java
class Solution {

    public int maxProfit(int[] prices) {
        return solve(0, 1, 2, prices);
    }

    private int solve(int index,
                      int buy,
                      int cap,
                      int[] prices) {

        if (index == prices.length || cap == 0)
            return 0;

        if (buy == 1) {

            return Math.max(
                    -prices[index]
                            + solve(index + 1, 0, cap, prices),

                    solve(index + 1, 1, cap, prices)
            );
        }

        return Math.max(

                prices[index]
                        + solve(index + 1, 1,
                        cap - 1, prices),

                solve(index + 1, 0, cap, prices)
        );
    }
}
```

### Complexity

```text
Time  : O(2^n)
Space : O(n)
```

---

# Approach 2: Memoization

### Code

```java
class Solution {

    public int maxProfit(int[] prices) {

        Integer[][][] dp =
                new Integer[prices.length][2][3];

        return solve(0, 1, 2, prices, dp);
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
                            + solve(index + 1, 0,
                            cap, prices, dp),

                    solve(index + 1, 1,
                            cap, prices, dp)
            );
        }

        return dp[index][buy][cap] =
                Math.max(

                prices[index]
                        + solve(index + 1, 1,
                        cap - 1, prices, dp),

                solve(index + 1, 0,
                        cap, prices, dp)
        );
    }
}
```

### Complexity

```text
Time  : O(N × 2 × 3)
Space : O(N × 2 × 3)
```

```text
≈ O(N)
```

---

# Approach 3: Tabulation

### Code

```java
class Solution {

    public int maxProfit(int[] prices) {

        int n = prices.length;

        int[][][] dp =
                new int[n + 1][2][3];

        for (int index = n - 1; index >= 0; index--) {

            for (int buy = 0; buy <= 1; buy++) {

                for (int cap = 1; cap <= 2; cap++) {

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

        return dp[0][1][2];
    }
}
```

### Complexity

```text
Time  : O(N)
Space : O(N)
```

---

# Approach 4: Space Optimized

Since:

```text
Current row depends only on next row
```

Keep only:

```java
ahead[2][3]
curr[2][3]
```

---

### Code

```java
class Solution {

    public int maxProfit(int[] prices) {

        int[][] ahead = new int[2][3];

        for (int index = prices.length - 1;
             index >= 0;
             index--) {

            int[][] curr = new int[2][3];

            for (int buy = 0; buy <= 1; buy++) {

                for (int cap = 1; cap <= 2; cap++) {

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

        return ahead[1][2];
    }
}
```

### Complexity

```text
Time  : O(N)
Space : O(1)
```

---

# Most Important Interview Optimization

Instead of 3D DP, we can track 4 states.

```text
First Buy
First Sell
Second Buy
Second Sell
```

---

## State Meaning

```java
buy1  = maximum profit after first buy

sell1 = maximum profit after first sell

buy2  = maximum profit after second buy

sell2 = maximum profit after second sell
```

---

## Transitions

```java
buy1  = max(buy1, -price)

sell1 = max(sell1, buy1 + price)

buy2  = max(buy2, sell1 - price)

sell2 = max(sell2, buy2 + price)
```

---

# Optimal Interview Solution

```java
class Solution {

    public int maxProfit(int[] prices) {

        int buy1 = Integer.MIN_VALUE;
        int buy2 = Integer.MIN_VALUE;

        int sell1 = 0;
        int sell2 = 0;

        for (int price : prices) {

            buy1 = Math.max(buy1, -price);

            sell1 = Math.max(
                    sell1,
                    buy1 + price
            );

            buy2 = Math.max(
                    buy2,
                    sell1 - price
            );

            sell2 = Math.max(
                    sell2,
                    buy2 + price
            );
        }

        return sell2;
    }
}
```

---

# Dry Run

```text
prices =
[3,3,5,0,0,3,1,4]
```

After processing all days:

```text
buy1  = 0
sell1 = 4

buy2  = 2
sell2 = 6
```

Answer:

```text
6
```

---

# Pattern Recognition

### Stock-I

```text
1 Transaction
```

State:

```text
Min Price
```

---

### Stock-II

```text
Infinite Transactions
```

State:

```text
(index,buy)
```

---

### Stock-III

```text
At Most 2 Transactions
```

State:

```text
(index,buy,cap)
```

where:

```text
cap = 2
```

---

### Stock-IV

```text
At Most K Transactions
```

State:

```text
(index,buy,k)
```

---

# DP Formula

## Buy

```text
max(
   -price + nextSell,
    nextBuy
)
```

## Sell

```text
max(
    price + nextBuy(cap-1),
    nextSell
)
```

---

# Complexity Summary

| Approach | Time | Space |
|-----------|--------|--------|
| Recursion | O(2^N) | O(N) |
| Memoization | O(N) | O(N) |
| Tabulation | O(N) | O(N) |
| Space Optimized | O(N) | O(1) |
| 4-State Optimal | O(N) | O(1) |

### Interview Preferred

✅ Space Optimized DP

✅ 4-State State-Machine Solution
