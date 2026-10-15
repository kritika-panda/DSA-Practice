# Coin Change II (LeetCode 518)

## Problem Statement

You are given:

```text
coins[] = coin denominations
amount  = target amount
```

Return the number of combinations that make up the amount.

You may use each coin:

```text
Unlimited number of times
```

If no combination is possible:

```text
Return 0
```

---

## Example 1

```text
Input:
amount = 5
coins = [1,2,5]

Output:
4
```

Explanation:

```text
5

2 + 2 + 1

2 + 1 + 1 + 1

1 + 1 + 1 + 1 + 1
```

Total combinations:

```text
4
```

---

## Example 2

```text
Input:
amount = 3
coins = [2]

Output:
0
```

---

## Example 3

```text
Input:
amount = 10
coins = [10]

Output:
1
```

---

# Intuition

This is an **Unbounded Knapsack Counting Problem**.

For every coin:

```text
1. Take the coin
2. Skip the coin
```

Since a coin can be reused infinitely:

```text
Take:
stay at same index
```

Unlike 0/1 Knapsack:

```text
Take:
move to index - 1
```

---

# Recursive Formulation

Define:

```text
f(index, target)
```

Meaning:

```text
Number of ways
to form target
using coins[0...index]
```

---

## Not Take

```text
f(index - 1, target)
```

---

## Take

If:

```text
coins[index] <= target
```

then:

```text
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
ways =
take + notTake
```

---

# Base Case

When:

```text
index == 0
```

Only one coin remains.

If target can be formed entirely using this coin:

```java
target % coins[0] == 0
```

there is exactly:

```text
1 way
```

Otherwise:

```text
0 ways
```

```java
if(target % coins[0] == 0)
    return 1;

return 0;
```

---

# Memoization Solution

```java
class Solution {

    public int change(int amount,
                      int[] coins) {

        int n = coins.length;

        int[][] dp =
            new int[n][amount + 1];

        for (int[] row : dp)
            Arrays.fill(row, -1);

        return solve(
                n - 1,
                amount,
                coins,
                dp);
    }

    private int solve(
            int index,
            int target,
            int[] coins,
            int[][] dp) {

        if (index == 0) {

            if (target % coins[0] == 0)
                return 1;

            return 0;
        }

        if (dp[index][target] != -1)
            return dp[index][target];

        int notTake =
            solve(index - 1,
                  target,
                  coins,
                  dp);

        int take = 0;

        if (coins[index] <= target) {

            take =
                solve(index,
                      target - coins[index],
                      coins,
                      dp);
        }

        return dp[index][target]
                = take + notTake;
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
Number of ways
to make sum t
using coins[0...i]
```

---

## Initialization

```java
for (int t = 0;
     t <= amount;
     t++) {

    if (t % coins[0] == 0)
        dp[0][t] = 1;
}
```

---

## Code

```java
class Solution {

    public int change(int amount,
                      int[] coins) {

        int n = coins.length;

        int[][] dp =
            new int[n][amount + 1];

        for (int t = 0;
             t <= amount;
             t++) {

            if (t % coins[0] == 0)
                dp[0][t] = 1;
        }

        for (int i = 1; i < n; i++) {

            for (int t = 0;
                 t <= amount;
                 t++) {

                int notTake =
                    dp[i - 1][t];

                int take = 0;

                if (coins[i] <= t) {

                    take =
                        dp[i]
                          [t - coins[i]];
                }

                dp[i][t] =
                    take + notTake;
            }
        }

        return dp[n - 1][amount];
    }
}
```

---

# Space Optimized Solution

```java
class Solution {

    public int change(int amount,
                      int[] coins) {

        int[] dp =
            new int[amount + 1];

        for (int t = 0;
             t <= amount;
             t++) {

            if (t % coins[0] == 0)
                dp[t] = 1;
        }

        for (int i = 1; i < coins.length; i++) {

            for (int t = 0;
                 t <= amount;
                 t++) {

                int notTake = dp[t];

                int take = 0;

                if (coins[i] <= t) {

                    take =
                        dp[t - coins[i]];
                }

                dp[t] = take + notTake;
            }
        }

        return dp[amount];
    }
}
```

---

# Most Optimal 1D DP

This is the standard interview solution.

## DP Definition

```text
dp[t] = number of ways
to form amount t
```

Initialize:

```java
dp[0] = 1
```

Reason:

```text
One way to make amount 0

Choose nothing
```

---

## Code

```java
class Solution {

    public int change(int amount,
                      int[] coins) {

        int[] dp =
            new int[amount + 1];

        dp[0] = 1;

        for (int coin : coins) {

            for (int t = coin;
                 t <= amount;
                 t++) {

                dp[t] +=
                    dp[t - coin];
            }
        }

        return dp[amount];
    }
}
```

---

# Why Coin Loop Outside?

Correct:

```java
for (coin)
    for (amount)
```

This counts:

```text
Combinations
```

Example:

```text
1 + 2
2 + 1
```

treated as:

```text
same combination
```

---

Wrong:

```java
for (amount)
    for (coin)
```

This counts:

```text
Permutations
```

Example:

```text
1 + 2
2 + 1
```

counted separately.

---

# Dry Run

```text
amount = 5
coins = [1,2,5]
```

Initially:

```text
dp = [1,0,0,0,0,0]
```

After coin = 1:

```text
dp = [1,1,1,1,1,1]
```

After coin = 2:

```text
dp = [1,1,2,2,3,3]
```

After coin = 5:

```text
dp = [1,1,2,2,3,4]
```

Answer:

```text
dp[5] = 4
```

---

# Relation with Other DP Problems

```text
Minimum Coins
        ↓
min(take, notTake)

Coin Change II
        ↓
take + notTake

0/1 Knapsack
        ↓
max(take, notTake)

Subset Sum
        ↓
take OR notTake
```

Notice the only thing changing is the DP operation:

```text
Boolean      → OR

Maximum      → max

Minimum      → min

Counting     → +
```

---

# Pattern Recognition

Whenever you see:

```text
Infinite supply

Count ways

Number of combinations

Make target amount
```

Think:

```text
✅ Unbounded Knapsack

✅ Counting DP

Transition:
take + notTake
```

---

# Complexity Analysis

### Memoization

```text
Time  : O(N × Amount)

Space : O(N × Amount)
         + O(N)
```

### Tabulation

```text
Time  : O(N × Amount)

Space : O(N × Amount)
```

### Optimal 1D DP

```text
Time  : O(N × Amount)

Space : O(Amount)
```

Where:

```text
N = number of coin denominations
```
