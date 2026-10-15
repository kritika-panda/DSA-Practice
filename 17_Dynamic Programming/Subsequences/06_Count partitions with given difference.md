# Count Partitions with Given Difference

## Problem Statement

Given an array `arr[]` and an integer `D`, count the number of ways to partition the array into two subsets `S1` and `S2` such that:

```text
S1 - S2 = D
```

and

```text
S1 >= S2
```

Return the number of such partitions.

### Example 1

```text
Input:
arr = [5,2,6,4]
D = 3

Output:
1
```

### Example 2

```text
Input:
arr = [1,1,1,1]
D = 0

Output:
6
```

---

# Intuition

Let:

```text
sum(S1) = s1
sum(S2) = s2
```

Given:

```text
s1 - s2 = D
```

Also:

```text
s1 + s2 = totalSum
```

Adding both equations:

```text
2*s1 = totalSum + D
```

Therefore:

```text
s1 = (totalSum + D) / 2
```

Similarly:

```text
2*s2 = totalSum - D
```

```text
s2 = (totalSum - D) / 2
```

Since counting subsets with sum `s2` is easier:

```text
Target = (totalSum - D) / 2
```

Thus the problem reduces to:

> Count the number of subsets whose sum is exactly `Target`.

This is exactly **Count Subsets with Sum K**.

---

# Edge Cases

Partition is impossible if:

```text
totalSum - D < 0
```

or

```text
(totalSum - D) is odd
```

Because target must be a non-negative integer.

```java
if (totalSum - D < 0 ||
    (totalSum - D) % 2 != 0)
    return 0;
```

---

# DP State

```text
dp[i][target]
```

Meaning:

```text
Number of subsets from 0...i
having sum = target
```

---

# Memoization Solution

```java
class Solution {

    public int countPartitions(int n,
                               int d,
                               int[] arr) {

        int totalSum = 0;

        for (int num : arr)
            totalSum += num;

        if (totalSum - d < 0 ||
            (totalSum - d) % 2 != 0)
            return 0;

        int target =
                (totalSum - d) / 2;

        int[][] dp =
                new int[n][target + 1];

        for (int[] row : dp)
            Arrays.fill(row, -1);

        return solve(n - 1,
                     target,
                     arr,
                     dp);
    }

    private int solve(int index,
                      int target,
                      int[] arr,
                      int[][] dp) {

        if (index == 0) {

            if (target == 0 &&
                arr[0] == 0)
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

            pick =
                solve(index - 1,
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

```java
class Solution {

    public int countPartitions(int n,
                               int d,
                               int[] arr) {

        int totalSum = 0;

        for (int num : arr)
            totalSum += num;

        if (totalSum - d < 0 ||
            (totalSum - d) % 2 != 0)
            return 0;

        int target =
                (totalSum - d) / 2;

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

                if (arr[i] <= sum)
                    pick =
                        dp[i - 1][sum - arr[i]];

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

```java
class Solution {

    public int countPartitions(int n,
                               int d,
                               int[] arr) {

        int totalSum = 0;

        for (int num : arr)
            totalSum += num;

        if (totalSum - d < 0 ||
            (totalSum - d) % 2 != 0)
            return 0;

        int target =
                (totalSum - d) / 2;

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

                if (arr[i] <= sum)
                    pick =
                       prev[sum - arr[i]];

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

# Most Common Interview Reduction

Remember:

```text
s1 - s2 = D
s1 + s2 = TotalSum
```

Subtract:

```text
2*s2 = TotalSum - D
```

Therefore:

```text
Count Partitions with Given Difference
           ↓
Count Subsets with Sum
(Target = (TotalSum - D)/2)
```

This reduction is the entire trick of the problem.

---

# Dry Run

```text
arr = [5,2,6,4]
D = 3

TotalSum = 17

Target =
(17 - 3)/2
= 7
```

Count subsets having sum:

```text
7
```

Possible subset:

```text
{5,2}
```

Count:

```text
1
```

Answer:

```text
1
```

---

# Pattern Recognition

Whenever you see:

```text
Count partitions
Difference between subsets
Given difference D
Number of ways to divide array
```

Think:

```text
✅ Count Subsets Sum K

Target =
(TotalSum - Difference) / 2
```

---

# Complexity Analysis

### Time Complexity

```text
O(N × Target)
```

### Space Complexity

```text
O(Target)
```

where

```text
Target = (TotalSum - D) / 2
```
