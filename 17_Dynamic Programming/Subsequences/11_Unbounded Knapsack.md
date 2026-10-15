# Unbounded Knapsack

## Problem Statement

You are given:

```text
N items

weight[i] = weight of ith item
profit[i] = profit of ith item

Knapsack Capacity = W
```

Find the maximum profit obtainable such that:

```text
Total Weight ≤ W
```

Each item can be chosen:

```text
Unlimited number of times
```

Unlike 0/1 Knapsack:

```text
An item can be reused infinitely.
```

---

## Example

```text
weights = [2,4,6]
profits = [5,11,13]

W = 10
```

Possible selections:

```text
2 + 2 + 2 + 2 + 2 => profit = 25

4 + 4 + 2         => profit = 27

6 + 2 + 2         => profit = 23
```

Maximum:

```text
27
```

Output:

```text
27
```

---

# Intuition

For every item we have two choices:

```text
1. Take it
2. Skip it
```

The key difference from 0/1 Knapsack:

### 0/1 Knapsack

```text
Take item

Move to index - 1
```

because item can be used once.

### Unbounded Knapsack

```text
Take item

Stay at same index
```

because item can be used again.

---

# Recursive Formulation

Define:

```text
f(index, capacity)
```

Meaning:

```text
Maximum profit obtainable
using items[0...index]
with remaining capacity
```

---

## Not Take

```text
f(index - 1, capacity)
```

---

## Take

Possible only if:

```text
weight[index] <= capacity
```

Then:

```text
profit[index]
+
f(index,
  capacity - weight[index])
```

Notice:

```text
index remains same
```

because item can be reused.

---

## Recurrence

```text
f(index, capacity) =
max(
    take,
    notTake
)
```

---

# Base Case

When:

```text
index == 0
```

Only first item remains.

Since it can be used infinitely:

```java
(capacity / weight[0]) * profit[0]
```

```java
if(index == 0) {

    return (capacity / weight[0])
           * profit[0];
}
```

---

# Memoization Solution

```java
class Solution {

    static int knapSack(int N,
                        int W,
                        int[] profit,
                        int[] weight) {

        int[][] dp =
                new int[N][W + 1];

        for (int[] row : dp)
            Arrays.fill(row, -1);

        return solve(
                N - 1,
                W,
                profit,
                weight,
                dp);
    }

    static int solve(int index,
                     int capacity,
                     int[] profit,
                     int[] weight,
                     int[][] dp) {

        if (index == 0) {

            return (capacity /
                    weight[0])
                    * profit[0];
        }

        if (dp[index][capacity] != -1)
            return dp[index][capacity];

        int notTake =
                solve(index - 1,
                      capacity,
                      profit,
                      weight,
                      dp);

        int take = Integer.MIN_VALUE;

        if (weight[index] <= capacity) {

            take =
                profit[index]
                +
                solve(index,
                      capacity -
                      weight[index],
                      profit,
                      weight,
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
Maximum profit using
items[0...i]
with capacity w
```

---

## Initialization

```java
for (int w = 0;
     w <= W;
     w++) {

    dp[0][w] =
        (w / weight[0])
        * profit[0];
}
```

---

## Transition

```java
notTake =
dp[i - 1][w]
```

```java
take =
profit[i]
+
dp[i][w - weight[i]]
```

Notice:

```text
Same row
```

which makes it unbounded.

---

## Code

```java
class Solution {

    static int knapSack(int N,
                        int W,
                        int[] profit,
                        int[] weight) {

        int[][] dp =
                new int[N][W + 1];

        for (int w = 0;
             w <= W;
             w++) {

            dp[0][w] =
                (w / weight[0])
                * profit[0];
        }

        for (int i = 1; i < N; i++) {

            for (int w = 0;
                 w <= W;
                 w++) {

                int notTake =
                        dp[i - 1][w];

                int take =
                        Integer.MIN_VALUE;

                if (weight[i] <= w) {

                    take =
                        profit[i]
                        +
                        dp[i]
                          [w - weight[i]];
                }

                dp[i][w] =
                    Math.max(
                        take,
                        notTake);
            }
        }

        return dp[N - 1][W];
    }
}
```

---

# Space Optimized Solution

```java
class Solution {

    static int knapSack(int N,
                        int W,
                        int[] profit,
                        int[] weight) {

        int[] dp =
                new int[W + 1];

        for (int w = 0;
             w <= W;
             w++) {

            dp[w] =
                (w / weight[0])
                * profit[0];
        }

        for (int i = 1; i < N; i++) {

            for (int w = 0;
                 w <= W;
                 w++) {

                int notTake = dp[w];

                int take =
                    Integer.MIN_VALUE;

                if (weight[i] <= w) {

                    take =
                        profit[i]
                        +
                        dp[w - weight[i]];
                }

                dp[w] =
                    Math.max(
                        take,
                        notTake);
            }
        }

        return dp[W];
    }
}
```

---

# Most Optimal 1D DP

```java
class Solution {

    static int knapSack(int N,
                        int W,
                        int[] profit,
                        int[] weight) {

        int[] dp =
                new int[W + 1];

        for (int w = 0;
             w <= W;
             w++) {

            dp[w] =
                (w / weight[0])
                * profit[0];
        }

        for (int i = 1; i < N; i++) {

            for (int w = weight[i];
                 w <= W;
                 w++) {

                dp[w] = Math.max(
                        dp[w],
                        profit[i]
                        + dp[w - weight[i]]);
            }
        }

        return dp[W];
    }
}
```

---

# Why Capacity Traverses Forward?

### 0/1 Knapsack

```text
Capacity moves backward
```

```java
for(w = W; w >= weight[i]; w--)
```

Reason:

```text
Prevent reusing same item.
```

---

### Unbounded Knapsack

```text
Capacity moves forward
```

```java
for(w = weight[i]; w <= W; w++)
```

Reason:

```text
Allow reuse of same item.
```

When computing:

```text
dp[w]
```

we want:

```text
dp[w - weight[i]]
```

from the current iteration so that the same item can be picked repeatedly.

---

# Dry Run

```text
weights = [2,4,6]

profits = [5,11,13]

capacity = 10
```

Best choice:

```text
4 + 4 + 2
```

Weight:

```text
10
```

Profit:

```text
11 + 11 + 5
= 27
```

Answer:

```text
27
```

---

# Pattern Recognition

Whenever you see:

```text
Maximum profit

Infinite supply of items

Can reuse items

Rod Cutting

Coin Change (Maximum/Minimum)

Unbounded selection
```

Think:

```text
✅ Unbounded Knapsack
```

State:

```text
(index, capacity)
```

Take:

```text
stay at same index
```

Transition:

```text
max(take, notTake)
```

---

# Relation with Previous Problems

```text
Subset Sum
        ↓
OR

Count Subsets
        ↓
+

0/1 Knapsack
        ↓
max()
take -> index-1

Minimum Coins
        ↓
min()
take -> same index

Coin Change II
        ↓
+
take -> same index

Unbounded Knapsack
        ↓
max()
take -> same index
```

---

# Complexity Analysis

### Memoization

```text
Time  : O(N × W)

Space : O(N × W)
         + O(N)
```

### Tabulation

```text
Time  : O(N × W)

Space : O(N × W)
```

### Optimal 1D DP

```text
Time  : O(N × W)

Space : O(W)
```

Where:

```text
N = number of items
W = knapsack capacity
```
