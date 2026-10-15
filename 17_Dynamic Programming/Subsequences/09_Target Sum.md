# Target Sum (LeetCode 494)

## Problem Statement

You are given an integer array `nums` and an integer `target`.

You need to assign either:

```text
+
or
-
```

before every number such that the resulting expression evaluates to `target`.

Return the number of different expressions possible.

---

## Example 1

```text
Input:
nums = [1,1,1,1,1]
target = 3

Output:
5
```

Explanation:

```text
-1 +1 +1 +1 +1 = 3
+1 -1 +1 +1 +1 = 3
+1 +1 -1 +1 +1 = 3
+1 +1 +1 -1 +1 = 3
+1 +1 +1 +1 -1 = 3
```

Total ways:

```text
5
```

---

## Example 2

```text
Input:
nums = [1]
target = 1

Output:
1
```

---

# Key Observation

Let:

```text
P = sum of numbers assigned '+'

N = sum of numbers assigned '-'
```

Then:

```text
P - N = target
```

Also:

```text
P + N = totalSum
```

Adding both equations:

```text
2P = totalSum + target
```

```text
P = (totalSum + target) / 2
```

Similarly:

```text
2N = totalSum - target
```

```text
N = (totalSum - target)/2
```

So the problem becomes:

> Count the number of subsets whose sum equals

```text
(totalSum - target)/2
```

This is exactly:

```text
Count Partitions With Given Difference
```

or

```text
Count Subsets With Sum K
```

---

# Invalid Cases

No solution exists when:

```java
totalSum - target < 0
```

or

```java
(totalSum - target) is odd
```

```java
if(totalSum - target < 0 ||
   (totalSum - target) % 2 != 0)
    return 0;
```

---

# DP Reduction

Target becomes:

```java
reqSum =
(totalSum - target)/2;
```

Now count subsets having sum:

```text
reqSum
```

---

# Memoization Solution

```java
class Solution {

    public int findTargetSumWays(
            int[] nums,
            int target) {

        int totalSum = 0;

        for (int num : nums)
            totalSum += num;

        if (totalSum - target < 0 ||
           (totalSum - target) % 2 != 0)
            return 0;

        int reqSum =
            (totalSum - target) / 2;

        int[][] dp =
            new int[nums.length]
                   [reqSum + 1];

        for (int[] row : dp)
            Arrays.fill(row, -1);

        return solve(
                nums.length - 1,
                reqSum,
                nums,
                dp);
    }

    private int solve(
            int index,
            int target,
            int[] nums,
            int[][] dp) {

        if (index == 0) {

            if (target == 0 &&
                nums[0] == 0)
                return 2;

            if (target == 0 ||
                target == nums[0])
                return 1;

            return 0;
        }

        if (dp[index][target] != -1)
            return dp[index][target];

        int notTake =
            solve(index - 1,
                  target,
                  nums,
                  dp);

        int take = 0;

        if (nums[index] <= target) {

            take =
                solve(index - 1,
                      target - nums[index],
                      nums,
                      dp);
        }

        return dp[index][target]
                = take + notTake;
    }
}
```

---

# Tabulation Solution

```java
class Solution {

    public int findTargetSumWays(
            int[] nums,
            int target) {

        int totalSum = 0;

        for (int num : nums)
            totalSum += num;

        if (totalSum - target < 0 ||
            (totalSum - target) % 2 != 0)
            return 0;

        int reqSum =
            (totalSum - target) / 2;

        int n = nums.length;

        int[][] dp =
            new int[n][reqSum + 1];

        if (nums[0] == 0)
            dp[0][0] = 2;
        else
            dp[0][0] = 1;

        if (nums[0] != 0 &&
            nums[0] <= reqSum)
            dp[0][nums[0]] = 1;

        for (int i = 1; i < n; i++) {

            for (int sum = 0;
                 sum <= reqSum;
                 sum++) {

                int notTake =
                    dp[i - 1][sum];

                int take = 0;

                if (nums[i] <= sum)
                    take =
                        dp[i - 1]
                          [sum - nums[i]];

                dp[i][sum] =
                    take + notTake;
            }
        }

        return dp[n - 1][reqSum];
    }
}
```

---

# Space Optimized Solution

```java
class Solution {

    public int findTargetSumWays(
            int[] nums,
            int target) {

        int totalSum = 0;

        for (int num : nums)
            totalSum += num;

        if (totalSum - target < 0 ||
            (totalSum - target) % 2 != 0)
            return 0;

        int reqSum =
            (totalSum - target) / 2;

        int[] prev =
            new int[reqSum + 1];

        if (nums[0] == 0)
            prev[0] = 2;
        else
            prev[0] = 1;

        if (nums[0] != 0 &&
            nums[0] <= reqSum)
            prev[nums[0]] = 1;

        for (int i = 1; i < nums.length; i++) {

            int[] curr =
                new int[reqSum + 1];

            for (int sum = 0;
                 sum <= reqSum;
                 sum++) {

                int notTake =
                    prev[sum];

                int take = 0;

                if (nums[i] <= sum)
                    take =
                        prev[sum - nums[i]];

                curr[sum] =
                    take + notTake;
            }

            prev = curr;
        }

        return prev[reqSum];
    }
}
```

---

# Dry Run

```text
nums = [1,1,1,1,1]
target = 3
```

Total Sum:

```text
5
```

Required subset sum:

```text
(5 - 3)/2

= 1
```

Count subsets whose sum is:

```text
1
```

Possible subsets:

```text
{1} from position 1
{1} from position 2
{1} from position 3
{1} from position 4
{1} from position 5
```

Count:

```text
5
```

Answer:

```text
5
```

---

# Pattern Recognition

Whenever you see:

```text
Assign + and -

Reach target value

Expression evaluation count

Target Sum
```

Think:

```text
P - N = Target
P + N = TotalSum
```

Reduction:

```text
Count Subsets Sum K

K =
(TotalSum - Target)/2
```

---

# Relationship with Previous Problems

```text
Count Subsets Sum K
        ↑

Count Partitions With Difference
        ↑

Target Sum
```

All three problems eventually reduce to:

```text
Count subsets having a specific sum.
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
K = (TotalSum - Target) / 2
```
