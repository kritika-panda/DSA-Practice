# Count Subsets with Sum K

## Problem Statement

Given an array `arr[]` and an integer `K`, count the number of subsets whose sum is exactly `K`.

### Example 1

```text
Input:
arr = [1,2,2,3]
K = 3

Output:
3

Explanation:

Subsets are:
{1,2}  -> using first 2
{1,2}  -> using second 2
{3}

Total = 3
```

### Example 2

```text
Input:
arr = [1,1,1,1]
K = 2

Output:
6
```

---

# Intuition

At every index we have two choices:

```text
1. Pick the current element
2. Do not pick the current element
```

Unlike Subset Sum where we return:

```text
true / false
```

Here we need:

```text
number of ways
```

Therefore:

```text
ways =
pick + notPick
```

---

# Recursive Formulation

Define:

```text
f(index, target)
```

Meaning:

```text
Number of subsets from 0...index
having sum = target
```

Recurrence:

```text
pick =
f(index - 1, target - arr[index])

notPick =
f(index - 1, target)

answer =
pick + notPick
```

---

# Special Base Case for Zeroes

Suppose:

```text
arr = [0]
target = 0
```

Possible subsets:

```text
{}
{0}
```

Count:

```text
2
```

So the base condition becomes:

```java
if (index == 0) {

    if (target == 0 && arr[0] == 0)
        return 2;

    if (target == 0 || target == arr[0])
        return 1;

    return 0;
}
```

This is the most important part of the problem.

---

# Memoization Solution

```java
class Solution {

    public int perfectSum(int[] arr, int target) {

        int n = arr.length;

        int[][] dp =
                new int[n][target + 1];

        for (int[] row : dp) {
            Arrays.fill(row, -1);
        }

        return solve(n - 1, target, arr, dp);
    }

    private int solve(int index,
                      int target,
                      int[] arr,
                      int[][] dp) {

        if (index == 0) {

            if (target == 0 && arr[0] == 0)
                return 2;

            if (target == 0 ||
                target == arr[0])
                return 1;

            return 0;
        }

        if (dp[index][target] != -1)
            return dp[index][target];

        int notPick =
                solve(index - 1,
                      target,
                      arr,
                      dp);

        int pick = 0;

        if (arr[index] <= target) {

            pick = solve(
                    index - 1,
                    target - arr[index],
                    arr,
                    dp);
        }

        return dp[index][target]
                = pick + notPick;
    }
}
```

---

# Tabulation Solution

## DP State

```text
dp[i][j]
```

Meaning:

```text
Number of subsets using
first (i+1) elements
whose sum = j
```

---

## Initialization

For first element:

```java
if (arr[0] == 0)
    dp[0][0] = 2;
else
    dp[0][0] = 1;

if (arr[0] != 0 &&
    arr[0] <= target)
    dp[0][arr[0]] = 1;
```

---

## Transition

```java
dp[i][sum] =
pick + notPick
```

---

## Code

```java
class Solution {

    public int perfectSum(int[] arr, int target) {

        int n = arr.length;

        int[][] dp =
                new int[n][target + 1];

        if (arr[0] == 0)
            dp[0][0] = 2;
        else
            dp[0][0] = 1;

        if (arr[0] != 0 &&
            arr[0] <= target)
            dp[0][arr[0]] = 1;

        for (int i = 1; i < n; i++) {

            for (int sum = 0;
                 sum <= target;
                 sum++) {

                int notPick =
                        dp[i - 1][sum];

                int pick = 0;

                if (arr[i] <= sum) {

                    pick =
                        dp[i - 1]
                          [sum - arr[i]];
                }

                dp[i][sum] =
                        pick + notPick;
            }
        }

        return dp[n - 1][target];
    }
}
```

---

# Space Optimized Solution

Since each row depends only on the previous row:

```java
class Solution {

    public int perfectSum(int[] arr, int target) {

        int n = arr.length;

        int[] prev =
                new int[target + 1];

        if (arr[0] == 0)
            prev[0] = 2;
        else
            prev[0] = 1;

        if (arr[0] != 0 &&
            arr[0] <= target)
            prev[arr[0]] = 1;

        for (int i = 1; i < n; i++) {

            int[] curr =
                    new int[target + 1];

            for (int sum = 0;
                 sum <= target;
                 sum++) {

                int notPick =
                        prev[sum];

                int pick = 0;

                if (arr[i] <= sum) {

                    pick =
                       prev[sum - arr[i]];
                }

                curr[sum] =
                        pick + notPick;
            }

            prev = curr;
        }

        return prev[target];
    }
}
```

---

# Dry Run

```text
arr = [1,2,2,3]
target = 3
```

Possible subsets:

```text
{3}

{1,2} using first 2

{1,2} using second 2
```

Count:

```text
3
```

Output:

```text
3
```

---

# Relation with Subset Sum

### Subset Sum

```text
Can we make target?

Answer:
true / false
```

Transition:

```text
take OR notTake
```

### Count Subsets Sum K

```text
How many ways can we make target?
```

Transition:

```text
take + notTake
```

This is the only major difference.

---

# Pattern Recognition

Whenever the problem asks:

```text
Count subsets
Number of ways
Total combinations
Ways to achieve target
```

Think:

```text
✅ DP Counting Problem

State:
(index, target)

Transition:
pick + notPick
```

---

# Complexity Analysis

### Time Complexity

```text
O(N × K)
```

### Space Complexity

```text
O(K)
```

where

```text
N = size of array
K = target sum
```
