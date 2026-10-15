# Minimum Coins (Coin Change - Minimum Number of Coins)

## Problem Statement

Given an array of coin denominations `coins[]` and a target amount `T`.

Find the minimum number of coins required to make the target amount.

You may use a coin:

```text
Unlimited number of times
```

If it is impossible to form the target, return:

```text
-1
```

---

# Example 1

```text
Input:
coins = [1,2,3]
target = 7

Output:
3
```

Explanation:

```text
3 + 3 + 1 = 7

Total coins = 3
```

---

# Example 2

```text
Input:
coins = [2]
target = 3

Output:
-1
```

Explanation:

```text
Target cannot be formed.
```

---

# Intuition

At every coin we have two choices:

```text
1. Take the coin
2. Skip the coin
```

Since coins can be reused infinitely:

After taking a coin:

```text
Remain at same index
```

Unlike 0/1 Knapsack where we move to:

```text
index - 1
```

---

# Recursive Formulation

Define:

```text
f(index, target)
```

Meaning:

```text
Minimum coins needed
to make target using
coins[0...index]
```

---

## Not Take

```text
f(index - 1, target)
```

---

## Take

Allowed if:

```text
coins[index] <= target
```

Then:

```text
1 +
f(index,
  target - coins[index])
```

Notice:

```text
index remains same
```

because the coin can be reused.

---

## Recurrence

```text
min(take, notTake)
```

---

# Base Case

When:

```text
index == 0
```

Only first coin remains.

If target is divisible:

```java
target % coins[0] == 0
```

then:

```java
target / coins[0]
```

coins are needed.

Otherwise:

```java
Infinity
```

---

# Memoization Solution

```java
class Solution {

    public int coinChange(int[] coins,
                          int amount) {

        int n = coins.length;

        int[][] dp =
            new int[n][amount + 1];

        for (int[] row : dp)
            Arrays.fill(row, -1);

        int ans =
            solve(n - 1,
                  amount,
                  coins,
                  dp);

        return ans >= 1e9 ? -1 : ans;
    }

    private int solve(int index,
                      int target,
                      int[] coins,
                      int[][] dp) {

        if (index == 0) {

            if (target % coins[0] == 0)
                return target / coins[0];

            return (int)1e9;
        }

        if (dp[index][target] != -1)
            return dp[index][target];

        int notTake =
            solve(index - 1,
                  target,
                  coins,
                  dp);

        int take = (int)1e9;

        if (coins[index] <= target) {

            take =
                1 + solve(index,
                          target - coins[index],
                          coins,
                          dp);
        }

        return dp[index][target]
                = Math.min(take,
                           notTake);
    }
}
```

---

# Tabulation Solution

## DP State

```text
dp[i][t]
```

Meaning:

```text
Minimum coins needed
to make target t
using coins[0...i]
```

---

## Initialization

For first coin:

```java
if (t % coins[0] == 0)
    dp[0][t] = t / coins[0];
else
    dp[0][t] = INF;
```

---

## Code

```java
class Solution {

    public int coinChange(int[] coins,
                          int amount) {

        int n = coins.length;

        int[][] dp =
            new int[n][amount + 1];

        int INF = (int)1e9;

        for (int t = 0;
             t <= amount;
             t++) {

            if (t % coins[0] == 0)
                dp[0][t] = t / coins[0];
            else
                dp[0][t] = INF;
        }

        for (int i = 1; i < n; i++) {

            for (int t = 0;
                 t <= amount;
                 t++) {

                int notTake =
                        dp[i - 1][t];

                int take = INF;

                if (coins[i] <= t) {

                    take =
                        1 + dp[i]
                               [t - coins[i]];
                }

                dp[i][t] =
                    Math.min(
                        take,
                        notTake);
            }
        }

        int ans =
            dp[n - 1][amount];

        return ans >= INF
                ? -1
                : ans;
    }
}
```

---

# Space Optimized Solution

Since:

```text
Current row depends on:
1. Previous row
2. Current row
```

We can use:

```text
O(Target)
```

space.

```java
class Solution {

    public int coinChange(int[] coins,
                          int amount) {

        int n = coins.length;

        int INF = (int)1e9;

        int[] dp =
            new int[amount + 1];

        for (int t = 0;
             t <= amount;
             t++) {

            if (t % coins[0] == 0)
                dp[t] = t / coins[0];
            else
                dp[t] = INF;
        }

        for (int i = 1; i < n; i++) {

            for (int t = 0;
                 t <= amount;
                 t++) {

                int notTake = dp[t];

                int take = INF;

                if (coins[i] <= t) {

                    take =
                        1 +
                        dp[t - coins[i]];
                }

                dp[t] =
                    Math.min(
                        take,
                        notTake);
            }
        }

        return dp[amount] >= INF
                ? -1
                : dp[amount];
    }
}
```

---

# Relation with Unbounded Knapsack

### 0/1 Knapsack

```text
Take:

value +
f(index-1,
  capacity-weight)
```

Move to:

```text
index - 1
```

because item can be chosen once.

---

### Minimum Coins

```text
Take:

1 +
f(index,
  target-coin)
```

Stay on:

```text
same index
```

because coin can be reused infinitely.

---

# Dry Run

```text
coins = [1,2,3]
target = 7
```

Best choice:

```text
3 + 3 + 1
```

Coins used:

```text
3
```

Answer:

```text
3
```

---

# Pattern Recognition

Whenever you see:

```text
Minimum number of coins

Infinite supply

Minimum elements required

Make target sum
```

Think:

```text
✅ Unbounded Knapsack

State:
(index, target)

Take:
stay on same index

Answer:
min(take, notTake)
```

---

# Complexity Analysis

### Memoization

```text
Time  : O(N × Target)

Space : O(N × Target)
         + O(N)
```

### Tabulation

```text
Time  : O(N × Target)

Space : O(N × Target)
```

### Space Optimized

```text
Time  : O(N × Target)

Space : O(Target)
```

where

```text
N = number of coin denominations
```

and

```text
Target = required amount
```
