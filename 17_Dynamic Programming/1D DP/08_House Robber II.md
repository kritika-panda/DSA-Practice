# House Robber II

## Problem Statement

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses at this place are **arranged in a circle.** That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have a security system connected, and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given an integer array `nums` representing the amount of money of each house, return *the maximum amount of money you can rob tonight **without alerting the police***.

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the total number of houses inside the nums array)*

---

## Example 1

### Input

```text
nums = [2,3,2]
```

### Output

```text
3
```

### Explanation

You cannot rob house 0 (money = 2) and then rob house 2 (money = 2), because they are adjacent neighbors in a circular street configuration. Your optimal choice is to rob house 1 (money = 3).

---

## Example 2

### Input

```text
nums = [1,2,3,1]
```

### Output

```text
4
```

### Explanation

Rob house 0 (money = 1) and then rob house 2 (money = 3). Total amount you can rob = `1 + 3 = 4`.

---

# Intuition

The circular arrangement introduces a crucial conflict: **you cannot rob both the first house (`index 0`) and the last house (`index n - 1`) on the same night.** 

This circular constraint can be broken down into two simpler, linear subproblems that we already know how to solve using the standard *House Robber* logic:
1. **Case 1 (Rob the first house):** If you choose to consider robbing the first house, you must strictly exclude the last house from your options. This means you run a linear robbery optimization over the slice range `[0, n - 2]`.
2. **Case 2 (Rob the last house):** If you choose to consider robbing the last house, you must strictly exclude the first house from your options. This means you run a linear robbery optimization over the slice range `[1, n - 1]`.

The absolute maximum money achievable tonight is simply the maximum value returned between these two independent choices.

```text
Maximum Money(Circular)
=
max(
    HouseRobberLinear(slice from 0 to n-2),
    HouseRobberLinear(slice from 1 to n-1)
)
```

---

# Recursive Solution

## Recurrence

```text
f(i) = max(nums[i] + f(i-2), f(i-1))

Evaluated across two separate boundaries:
1. range from 0 to n-2
2. range from 1 to n-1
```

### Base Case

```text
f(start) = nums[start]
f(i < start) = 0
```

---

## Java Code (Recursion)

```java
class Solution {

    private int solveLinear(int index, int start, int[] nums) {
        // Base case: If we fall below our starting boundary slice, no money remains
        if (index < start) {
            return 0;
        }
        
        // Base case: If we are at the initial house of the slice range
        if (index == start) {
            return nums[start];
        }

        int rob = nums[index] + solveLinear(index - 2, start, nums);
        int skip = solveLinear(index - 1, start, nums);

        return Math.max(rob, skip);
    }

    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int n = nums.length;
        if (n == 1) return nums[0];

        // Combine the results of both mutually exclusive linear paths
        int opt1 = solveLinear(n - 2, 0, nums); // Skip last house
        int opt2 = solveLinear(n - 1, 1, nums); // Skip first house

        return Math.max(opt1, opt2);
    }
}
```

### Complexity Analysis

```text
Time Complexity : O(2^N)
Space Complexity: O(N)
```

---

# Memoization (Top-Down DP)

## Idea

Apply a 1D DP lookup table array to cache intermediate states for both independent linear passes, clearing or maintaining distinct cache boundaries.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solveLinear(int index, int start, int[] nums, int[] dp) {
        if (index < start) {
            return 0;
        }

        if (index == start) {
            return nums[start];
        }

        if (dp[index] != -1) {
            return dp[index];
        }

        int rob = nums[index] + solveLinear(index - 2, start, nums, dp);
        int skip = solveLinear(index - 1, start, nums, dp);

        return dp[index] = Math.max(rob, skip);
    }

    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int n = nums.length;
        if (n == 1) return nums[0];

        int[] dp1 = new int[n];
        int[] dp2 = new int[n];
        Arrays.fill(dp1, -1);
        Arrays.fill(dp2, -1);

        int opt1 = solveLinear(n - 2, 0, nums, dp1);
        int opt2 = solveLinear(n - 1, 1, nums, dp2);

        return Math.max(opt1, opt2);
    }
}
```

### Complexity Analysis

```text
Time Complexity : O(N)
Space Complexity: O(N) + O(N) recursion stack
```

---

# Tabulation (Bottom-Up DP)

## Idea

Implement a dedicated helper routine that builds optimal non-adjacent answers using standard 1D iteration sweeps across defined array bounds.

---

## DP Definition

```text
dp[i] = maximum money possible to rob from the active sub-segment slice up to index i
```

---

## Java Code

```java
class Solution {

    private int robLinear(int[] nums, int start, int end) {
        int n = nums.length;
        int[] dp = new int[n];
        
        dp[start] = nums[start];
        dp[start + 1] = Math.max(nums[start], nums[start + 1]);

        for (int i = start + 2; i <= end; i++) {
            int rob = nums[i] + dp[i - 2];
            int skip = dp[i - 1];
            dp[i] = Math.max(rob, skip);
        }

        return dp[end];
    }

    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int n = nums.length;
        if (n == 1) return nums[0];
        if (n == 2) return Math.max(nums[0], nums[1]);

        int opt1 = robLinear(nums, 0, n - 2);
        int opt2 = robLinear(nums, 1, n - 1);

        return Math.max(opt1, opt2);
    }
}
```

### Complexity Analysis

```text
Time Complexity : O(N)
Space Complexity: O(N)
```

---

# Space Optimization

## Observation

Since each linear segment calculation only relies on its direct previous two steps (`prev` and `prev2`), we can eliminate the tracking arrays completely, calculating both ranges using absolute constant space.

---

## Java Code

```java
class Solution {

    private int robLinear(int[] nums, int start, int end) {
        int prev2 = 0; // Represents dp[i-2]
        int prev = 0;  // Represents dp[i-1]

        for (int i = start; i <= end; i++) {
            int rob = nums[i] + prev2;
            int skip = prev;
            
            int current = Math.max(rob, skip);
            
            prev2 = prev;
            prev = current;
        }

        return prev;
    }

    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int n = nums.length;
        if (n == 1) return nums[0];

        // Extract maximums from both independent, space-optimized loops
        int opt1 = robLinear(nums, 0, n - 2);
        int opt2 = robLinear(nums, 1, n - 1);

        return Math.max(opt1, opt2);
    }
}
```

### Complexity Analysis

```text
Time Complexity : O(N)
Space Complexity: O(1)
```

---

# Dry Run

```text
nums = [1, 2, 3, 1]
```

### Subproblem 1: Range [0, 2] (Slicing elements: 1, 2, 3)

```text
Initial: prev2 = 0, prev = 0

i = 0 (num = 1): current = max(1 + 0, 0) = 1 -> prev2 = 0, prev = 1
i = 1 (num = 2): current = max(2 + 0, 1) = 2 -> prev2 = 1, prev = 2
i = 2 (num = 3): current = max(3 + 1, 2) = 4 -> prev2 = 2, prev = 4

Result 1 = 4
```

### Subproblem 2: Range [1, 3] (Slicing elements: 2, 3, 1)

```text
Initial: prev2 = 0, prev = 0

i = 1 (num = 2): current = max(2 + 0, 0) = 2 -> prev2 = 0, prev = 2
i = 2 (num = 3): current = max(3 + 0, 2) = 3 -> prev2 = 2, prev = 3
i = 3 (num = 1): current = max(1 + 2, 3) = 3 -> prev2 = 3, prev = 3

Result 2 = 3
```

### Final Answer

```text
max(Result 1, Result 2) = max(4, 3) = 4
```

---

# DP Pattern

## Identify State

```text
f(i, start)
=
Maximum money achievable from a linear subarray starting at 'start' up to index i
```

## Choices

```text
Break the circular ring by executing two separate linear tracking operations:
1. Include element 0, exclude element n-1 -> Range [0, n-2]
2. Exclude element 0, include element n-1 -> Range [1, n-1]
```

## Recurrence

```text
Result = max( robLinear(0, n-2), robLinear(1, n-1) )
```

## Base Case

```text
f(start, start) = nums[start]
f(i < start, start) = 0
```
