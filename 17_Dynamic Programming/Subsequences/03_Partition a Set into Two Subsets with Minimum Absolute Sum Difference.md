# Partition a Set into Two Subsets with Minimum Absolute Sum Difference

## Problem Statement

Given an array `arr[]` of size `N`, partition it into two subsets such that the absolute difference between their sums is minimized.

Return the minimum possible absolute difference.

### Example 1

```text
Input:
arr = [1, 2, 3, 4]

Output:
0

Explanation:
Subset 1 = {1,4} => sum = 5
Subset 2 = {2,3} => sum = 5

Difference = |5 - 5| = 0
```

### Example 2

```text
Input:
arr = [1, 6, 11, 5]

Output:
1

Explanation:
Subset 1 = {1,5,6} => sum = 12
Subset 2 = {11} => sum = 11

Difference = |12 - 11| = 1
```

---

# Intuition

Let:

```text
totalSum = sum(arr)

subset1 = s1
subset2 = s2
```

We know:

```text
s1 + s2 = totalSum
```

Required:

```text
|s1 - s2|
```

Substituting:

```text
|s1 - (totalSum - s1)|

= |2*s1 - totalSum|
```

So the problem becomes:

> Find all possible subset sums and choose the one that minimizes

```text
|totalSum - 2*s1|
```

---

# DP Observation

This is a variation of the **Subset Sum Problem**.

We find all achievable subset sums from:

```text
0 → totalSum
```

Then check every achievable sum and compute:

```text
difference = |totalSum - 2*sum|
```

The minimum among them is the answer.

---

# DP State

```text
dp[i][j]
```

Meaning:

```text
Using first i elements,
can we make sum j ?
```

---

# Tabulation Solution

```java
class Solution {

    public int minDifference(int[] arr) {

        int n = arr.length;

        int totalSum = 0;

        for (int num : arr) {
            totalSum += num;
        }

        boolean[][] dp =
                new boolean[n][totalSum + 1];

        for (int i = 0; i < n; i++) {
            dp[i][0] = true;
        }

        if (arr[0] <= totalSum) {
            dp[0][arr[0]] = true;
        }

        for (int i = 1; i < n; i++) {

            for (int target = 1;
                 target <= totalSum;
                 target++) {

                boolean notTake =
                        dp[i - 1][target];

                boolean take = false;

                if (arr[i] <= target) {
                    take =
                        dp[i - 1][target - arr[i]];
                }

                dp[i][target] =
                        take || notTake;
            }
        }

        int minDiff = Integer.MAX_VALUE;

        for (int s1 = 0;
             s1 <= totalSum;
             s1++) {

            if (dp[n - 1][s1]) {

                int diff =
                    Math.abs(totalSum - 2 * s1);

                minDiff =
                    Math.min(minDiff, diff);
            }
        }

        return minDiff;
    }
}
```

---

# Space Optimized Solution

Since each row depends only on the previous row:

```java
class Solution {

    public int minDifference(int[] arr) {

        int totalSum = 0;

        for (int num : arr) {
            totalSum += num;
        }

        boolean[] dp =
                new boolean[totalSum + 1];

        dp[0] = true;

        for (int num : arr) {

            for (int sum = totalSum;
                 sum >= num;
                 sum--) {

                dp[sum] =
                    dp[sum] || dp[sum - num];
            }
        }

        int minDiff = Integer.MAX_VALUE;

        for (int s1 = 0;
             s1 <= totalSum;
             s1++) {

            if (dp[s1]) {

                minDiff = Math.min(
                    minDiff,
                    Math.abs(totalSum - 2 * s1)
                );
            }
        }

        return minDiff;
    }
}
```

---

# Further Optimization

Observe:

```text
Difference = |totalSum - 2*s1|
```

For every subset sum:

```text
s2 = totalSum - s1
```

Checking sums beyond:

```text
totalSum / 2
```

is redundant because they produce symmetric answers.

Therefore iterate only till:

```text
totalSum / 2
```

---

# Optimized Solution

```java
class Solution {

    public int minDifference(int[] arr) {

        int totalSum = 0;

        for (int num : arr) {
            totalSum += num;
        }

        boolean[] dp =
                new boolean[totalSum + 1];

        dp[0] = true;

        for (int num : arr) {

            for (int sum = totalSum;
                 sum >= num;
                 sum--) {

                dp[sum] =
                    dp[sum] || dp[sum - num];
            }
        }

        int minDiff = Integer.MAX_VALUE;

        for (int s1 = 0;
             s1 <= totalSum / 2;
             s1++) {

            if (dp[s1]) {

                minDiff = Math.min(
                    minDiff,
                    totalSum - 2 * s1
                );
            }
        }

        return minDiff;
    }
}
```

---

# Dry Run

```text
arr = [1,6,11,5]

totalSum = 23
```

Possible subset sums:

```text
0,1,5,6,7,11,12,16,17,18,22,23
```

Check only up to:

```text
23 / 2 = 11
```

Largest achievable sum ≤ 11:

```text
s1 = 11
```

Then:

```text
s2 = 23 - 11 = 12

Difference =
|12 - 11|
= 1
```

Answer:

```text
1
```

---

# Pattern Recognition

Whenever you see:

```text
Partition into two subsets
Minimum subset difference
Balance two groups
Closest equal partition
```

Think:

```text
✅ Subset Sum DP

Find all achievable sums

Answer =
min(|totalSum - 2*subsetSum|)
```

---

# Complexity Analysis

### Time Complexity

```text
O(N × TotalSum)
```

### Space Complexity

```text
O(TotalSum)
```

Where:

```text
TotalSum = sum of all elements
```
