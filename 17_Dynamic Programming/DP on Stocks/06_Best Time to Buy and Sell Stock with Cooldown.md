# Best Time to Buy and Sell Stock with Cooldown (LeetCode 309)

## Problem Statement

You are given an array:

```java
prices[i]
```

representing the stock price on day `i`.

You may complete as many transactions as you like, but after selling a stock, you cannot buy stock on the next day.

```text
Sell Today
↓
Cooldown Tomorrow
↓
Can Buy Again Day After Tomorrow
```

Return the maximum profit.

---

## Example 1

```text
Input:
prices = [1,2,3,0,2]

Output:
3
```

### Explanation

```text
Buy at 1
Sell at 2

Cooldown

Buy at 0
Sell at 2

Profit = 1 + 2 = 3
```

---

## Example 2

```text
Input:
prices = [1]

Output:
0
```

---

# Intuition

This problem is almost identical to:

```text
Best Time to Buy and Sell Stock II
```

The only additional rule is:

```text
After selling,
we cannot buy on the very next day.
```

Therefore after selling:

```text
Move to index + 2
```

instead of:

```text
Move to index + 1
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
    We are allowed to buy

buy = 0
    We currently hold stock and must sell
```

---

# Recurrence

## Buy State

Choices:

### Buy

```java
-prices[index] + solve(index+1,0)
```

### Skip

```java
solve(index+1,1)
```

Therefore

```java
dp[index][1] =
Math.max(

    -prices[index] + solve(index+1,0),

    solve(index+1,1)
);
```

---

## Sell State

Choices:

### Sell

After selling:

```text
Cooldown for one day
```

Hence jump to:

```java
index + 2
```

```java
prices[index] + solve(index+2,1)
```

### Skip

```java
solve(index+1,0)
```

Thus

```java
dp[index][0] =
Math.max(

    prices[index]
    + solve(index+2,1),

    solve(index+1,0)
);
```

---

# Approach 1: Memoization

## Code

```java
class Solution {

    public int maxProfit(int[] prices) {

        Integer[][] dp =
                new Integer[prices.length][2];

        return solve(0, 1, prices, dp);
    }

    private int solve(int index,
                      int buy,
                      int[] prices,
                      Integer[][] dp) {

        if (index >= prices.length)
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
                            dp),

                    solve(index + 1,
                            1,
                            prices,
                            dp)
            );
        }

        return dp[index][buy] =
                Math.max(

                prices[index]
                        + solve(index + 2,
                        1,
                        prices,
                        dp),

                solve(index + 1,
                        0,
                        prices,
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

Since we use:

```java
index + 1
index + 2
```

we need:

```java
n + 2 rows
```

---

## Code

```java
class Solution {

    public int maxProfit(int[] prices) {

        int n = prices.length;

        int[][] dp =
                new int[n + 2][2];

        for (int index = n - 1;
             index >= 0;
             index--) {

            dp[index][1] =
                    Math.max(

                    -prices[index]
                            + dp[index + 1][0],

                    dp[index + 1][1]
            );

            dp[index][0] =
                    Math.max(

                    prices[index]
                            + dp[index + 2][1],

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

Observation:

```text
Current state depends only on:

index+1
index+2
```

Keep:

```java
ahead1
ahead2
curr
```

---

## Code

```java
class Solution {

    public int maxProfit(int[] prices) {

        int[] ahead1 = new int[2];
        int[] ahead2 = new int[2];

        for (int index = prices.length - 1;
             index >= 0;
             index--) {

            int[] curr = new int[2];

            curr[1] = Math.max(

                    -prices[index]
                            + ahead1[0],

                    ahead1[1]
            );

            curr[0] = Math.max(

                    prices[index]
                            + ahead2[1],

                    ahead1[0]
            );

            ahead2 = ahead1;
            ahead1 = curr;
        }

        return ahead1[1];
    }
}
```

---

## Complexity

```text
Time  : O(N)

Space : O(1)
```

---

# State Machine Interpretation

We can model 3 states.

```text
1. Hold
2. Sold
3. Rest
```

---

## State Meaning

### Hold

```text
Currently holding a stock
```

### Sold

```text
Just sold today
```

### Rest

```text
Not holding stock
and cooldown completed
```

---

# Transition

## Hold

```java
hold =
max(
    hold,
    rest - price
)
```

Either:

```text
Continue holding
```

or

```text
Buy today
```

---

## Sold

```java
sold =
hold + price
```

Sell today.

---

## Rest

```java
rest =
max(
    rest,
    previousSold
)
```

Cooldown completed.

---

# Optimal State-Machine Solution

```java
class Solution {

    public int maxProfit(int[] prices) {

        int hold = -prices[0];
        int sold = 0;
        int rest = 0;

        for (int i = 1; i < prices.length; i++) {

            int prevSold = sold;

            sold = hold + prices[i];

            hold = Math.max(
                    hold,
                    rest - prices[i]
            );

            rest = Math.max(
                    rest,
                    prevSold
            );
        }

        return Math.max(sold, rest);
    }
}
```

---

# Dry Run

```text
prices = [1,2,3,0,2]
```

Day 0

```text
hold = -1
sold = 0
rest = 0
```

Day 1

```text
sold = 1
hold = -1
rest = 0
```

Day 2

```text
sold = 2
hold = -1
rest = 1
```

Day 3

```text
sold = -1
hold = 1
rest = 2
```

Day 4

```text
sold = 3
hold = 1
rest = 2
```

Answer:

```text
3
```

---

# Why Jump to Index + 2?

Normal Stock II:

```text
Buy -> Sell -> Buy
```

Allowed immediately.

```text
Sell at day i

Buy at day i+1
```

---

Cooldown Stock:

```text
Sell at day i

Day i+1 = Cooldown

Buy possible at day i+2
```

That's why:

```java
sell =
price + solve(index+2,1)
```

This is the only change compared to Stock II.

---

# Pattern Recognition

### Stock I

```text
One Transaction
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
2 Transactions
```

State:

```text
(index,buy,cap)
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

### Stock With Fee

```text
Sell = price - fee
```

---

### Stock With Cooldown

```text
Sell = price + dp[index+2]
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
    price[i] + dp[i+2][1],
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
| State Machine | O(N) | O(1) |

✅ **Interview Preferred:** Space Optimized DP

✅ **Most Elegant:** State Machine (`hold`, `sold`, `rest`)
