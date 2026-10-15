# 0 or 1 Knapsack

## Problem Statement

Given:

```text
N items

weight[i] = weight of ith item
value[i]  = value of ith item

Bag capacity = W
```

Find the maximum value that can be obtained by selecting items such that:

```text
Total Weight ≤ W
```

Each item can be chosen:

```text
0 times or 1 time
```

Hence the name:

```text
0/1 Knapsack
```

---

# Example

```text
weights = [1,2,4,5]
values  = [5,4,8,6]

W = 5
```

Possible selections:

```text
{1,2}      => value = 9
{5}        => value = 6
{1,4}      => value = 13
{2,4}      => value = 12
```

Maximum:

```text
13
```

Output:

```text
13
```

---

# Intuition

For every item we have two choices:

```text
1. Take the item
2. Do not take the item
```

If we take an item:

```text
value[i] +
remaining answer
```

Otherwise:

```text
skip current item
```

Therefore:

```text
answer =
max(take, notTake)
```

---

# Recursive Formulation

Define:

```text
f(index, capacity)
```

Meaning:

```text
Maximum value obtainable
using items 0...index
with remaining capacity
```

---

## Choices

### Not Take

```text
f(index-1, capacity)
```

### Take

Allowed only when:

```text
weight[index] <= capacity
```

Then:

```text
value[index]
+
f(index-1,
  capacity-weight[index])
```

---

## Recurrence

```text
f(index, capacity) =
max(
    notTake,
    take
)
```

---

# Base Case

When:

```text
index == 0
```

Only one item remains.

```java
if(weight[0] <= capacity)
    return value[0];

return 0;
```

---

# Memoization Solution

```java
class Solution {

    static int knapsack(int[] weight,
                        int[] value,
                        int n,
                        int maxWeight) {

        int[][] dp =
            new int[n][maxWeight + 1];

        for (int[] row : dp)
            Arrays.fill(row, -1);

        return solve(
                n - 1,
                maxWeight,
                weight,
                value,
                dp);
    }

    static int solve(int index,
                     int capacity,
                     int[] weight,
                     int[] value,
                     int[][] dp) {

        if (index == 0) {

            if (weight[0] <= capacity)
                return value[0];

            return 0;
        }

        if (dp[index][capacity] != -1)
            return dp[index][capacity];

        int notTake =
            solve(index - 1,
                  capacity,
                  weight,
                  value,
                  dp);

        int take = Integer.MIN_VALUE;

        if (weight[index] <= capacity) {

            take = value[index]
                    + solve(
                        index - 1,
                        capacity -
                        weight[index],
                        weight,
                        value,
                        dp);
        }

        return dp[index][capacity]
                = Math.max(
                    take,
                    notTake);
    }
}
```

---

# Tabulation Solution

## DP State

```text
dp[i][w]
```

Meaning:

```text
Maximum value obtainable
using first (i+1) items
and capacity w
```

---

## Initialization

For first item:

```java
for(int w = weight[0];
    w <= maxWeight;
    w++) {

    dp[0][w] = value[0];
}
```

---

## Transition

```java
notTake =
dp[i-1][w]

take =
value[i]
+
dp[i-1][w-weight[i]]
```

```java
dp[i][w] =
max(take, notTake)
```

---

## Code

```java
class Solution {

    static int knapsack(int[] weight,
                        int[] value,
                        int n,
                        int maxWeight) {

        int[][] dp =
            new int[n][maxWeight + 1];

        for (int w = weight[0];
             w <= maxWeight;
             w++) {

            dp[0][w] = value[0];
        }

        for (int i = 1; i < n; i++) {

            for (int w = 0;
                 w <= maxWeight;
                 w++) {

                int notTake =
                    dp[i - 1][w];

                int take =
                    Integer.MIN_VALUE;

                if (weight[i] <= w) {

                    take =
                        value[i]
                        + dp[i - 1]
                            [w - weight[i]];
                }

                dp[i][w] =
                    Math.max(
                        take,
                        notTake);
            }
        }

        return dp[n - 1][maxWeight];
    }
}
```

---

# Space Optimized Solution

Observation:

```text
Current row depends
only on previous row.
```

Therefore:

```text
O(N × W)
↓
O(W)
```

---

## Code

```java
class Solution {

    static int knapsack(int[] weight,
                        int[] value,
                        int n,
                        int maxWeight) {

        int[] prev =
            new int[maxWeight + 1];

        for (int w = weight[0];
             w <= maxWeight;
             w++) {

            prev[w] = value[0];
        }

        for (int i = 1; i < n; i++) {

            int[] curr =
                new int[maxWeight + 1];

            for (int w = 0;
                 w <= maxWeight;
                 w++) {

                int notTake =
                    prev[w];

                int take =
                    Integer.MIN_VALUE;

                if (weight[i] <= w) {

                    take =
                        value[i]
                        + prev[w - weight[i]];
                }

                curr[w] =
                    Math.max(
                        take,
                        notTake);
            }

            prev = curr;
        }

        return prev[maxWeight];
    }
}
```

---

# Most Optimal Solution (Single Array)

Since:

```text
dp[w] depends on
dp[w - weight[i]]
from previous row
```

Traverse capacity backwards.

---

## Code

```java
class Solution {

    static int knapsack(int[] weight,
                        int[] value,
                        int n,
                        int maxWeight) {

        int[] dp =
            new int[maxWeight + 1];

        for (int w = weight[0];
             w <= maxWeight;
             w++) {

            dp[w] = value[0];
        }

        for (int i = 1; i < n; i++) {

            for (int w = maxWeight;
                 w >= weight[i];
                 w--) {

                dp[w] = Math.max(
                    dp[w],
                    value[i]
                    + dp[w - weight[i]]
                );
            }
        }

        return dp[maxWeight];
    }
}
```

---

# Why Traverse Backward?

Suppose:

```text
weight = 2
value  = 5
```

If capacity moves:

```text
left → right
```

Then:

```text
dp[2] becomes 5

dp[4] may use dp[2]
generated in same iteration
```

Meaning:

```text
Same item used twice
```

which converts the problem into:

```text
Unbounded Knapsack
```

Backward traversal ensures:

```text
Every item is used
at most once.
```

---

# Dry Run

```text
weights = [1,2,4,5]

values = [5,4,8,6]

capacity = 5
```

Best choice:

```text
Weight = 1 + 4 = 5

Value  = 5 + 8
       = 13
```

Answer:

```text
13
```

---

# Pattern Recognition

Whenever you see:

```text
Take or skip item

Maximum profit

Capacity constraint

Each item at most once
```

Think:

```text
✅ 0/1 Knapsack
```

State:

```text
(index, capacity)
```

Transition:

```text
max(take, notTake)
```

---

# Complexity Analysis

### Memoization

```text
Time  : O(N × W)
Space : O(N × W) + O(N)
```

### Tabulation

```text
Time  : O(N × W)
Space : O(N × W)
```

### Space Optimized

```text
Time  : O(N × W)
Space : O(W)
```

Where:

```text
N = number of items

W = knapsack capacity
```

---

# DP Pattern Summary

```text
Subset Sum
    ↓
Can Make Target?
    ↓
Boolean DP

Count Subsets Sum K
    ↓
Number of Ways
    ↓
Count DP

0/1 Knapsack
    ↓
Maximum Profit
    ↓
Max DP
```
