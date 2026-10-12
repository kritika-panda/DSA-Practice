# House Robber

## Problem Statement

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that **adjacent houses have security systems connected** and it will automatically contact the police if two adjacent houses were broken into on the same night.

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
nums = [1,2,3,1]
```

### Output

```text
4
```

### Explanation

Rob house 1 (money = 1) and then rob house 3 (money = 3).  
Total amount you can rob = `1 + 3 = 4`.

---

## Example 2

### Input

```text
nums = [2,7,9,3,1]
```

### Output

```text
12
```

### Explanation

Rob house 1 (money = 2), rob house 3 (money = 9), and rob house 5 (money = 1).  
Total amount you can rob = `2 + 9 + 1 = 12`.

---

# Intuition

At any given house `i`, you have two distinct choices:
1. **Rob the house:** If you rob `nums[i]`, you cannot rob the immediate previous house (`i - 1`). Thus, you add `nums[i]` to the best possible result from two houses back (`i - 2`).
2. **Skip the house:** If you skip `nums[i]`, your maximum value up to this point will simply be the best possible result from the previous house (`i - 1`).

Therefore,

```text
Maximum Money(i)
=
max(
    rob nums[i] + optimal from i-2,
    skip nums[i] + optimal from i-1
)
```

This structural configuration directly tracks the maximum sum of non-adjacent elements.

---

# Recursive Solution

## Recurrence

```text
f(i) =
max(
    nums[i] + f(i-2),
    f(i-1)
)
```

### Base Case

```text
f(0) = nums[0]
f(i < 0) = 0
```

---

## Java Code (Recursion)

```java
class Solution {

    private int solve(int index, int[] nums) {
        // Base case: If we move past the starting house, no money can be collected
        if (index < 0) {
            return 0;
        }
        
        // Base case: If there is only one house, we must rob it
        if (index == 0) {
            return nums[0];
        }

        // Choice 1: Rob the current house and link to index - 2
        int rob = nums[index] + solve(index - 2, nums);

        // Choice 2: Skip the current house and inherit from index - 1
        int skip = solve(index - 1, nums);

        return Math.max(rob, skip);
    }

    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        return solve(nums.length - 1, nums);
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

Store already computed subproblem values in a 1D DP table.  
Avoid expanding duplicate branch subproblems repeatedly.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int index, int[] nums, int[] dp) {
        if (index < 0) {
            return 0;
        }

        if (index == 0) {
            return nums[0];
        }

        // Return cached state instantly if previously evaluated
        if (dp[index] != -1) {
            return dp[index];
        }

        int rob = nums[index] + solve(index - 2, nums, dp);
        int skip = solve(index - 1, nums, dp);

        return dp[index] = Math.max(rob, skip);
    }

    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        
        int n = nums.length;
        int[] dp = new int[n];
        Arrays.fill(dp, -1);

        return solve(n - 1, nums, dp);
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

Build optimal subproblems progressively from left to right.

---

## DP Definition

```text
dp[i] = maximum money possible to rob from houses up to index i
```

### Base Case

```text
dp[0] = nums[0]
```

### Transition

```text
dp[i] =
max(
    nums[i] + (i > 1 ? dp[i-2] : 0),
    dp[i-1]
)
```

---

## Java Code

```java
class Solution {

    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        
        int n = nums.length;
        if (n == 1) {
            return nums[0];
        }

        int[] dp = new int[n];
        
        // Base case setups
        dp[0] = nums[0];
        dp[1] = Math.max(nums[0], nums[1]);

        for (int i = 2; i < n; i++) {
            int rob = nums[i] + dp[i - 2];
            int skip = dp[i - 1];

            dp[i] = Math.max(rob, skip);
        }

        return dp[n - 1];
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

For computing the state:

```text
dp[i]
```

we only ever read values from the immediate two preceding variables:

```text
dp[i-1]
dp[i-2]
```

Therefore, maintaining the entire tracking array is unnecessary. We can collapse our space down to absolute constant overhead by using two rolling primitive variables.

---

## Java Code

```java
class Solution {

    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        
        int n = nums.length;
        if (n == 1) {
            return nums[0];
        }

        int prev2 = 0;       // Represents dp[i-2]
        int prev = nums[0];  // Represents dp[i-1]

        for (int i = 1; i < n; i++) {
            // Rob current house and link to two slots back
            int rob = nums[i] + prev2;
            
            // Skip current house and inherit from one slot back
            int skip = prev;

            int current = Math.max(rob, skip);

            // Shift registers forward for the next loop cycle
            prev2 = prev;
            prev = current;
        }

        return prev;
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
nums = [2,7,9,3,1]
```

### Initial

```text
prev  = 2
prev2 = 0
```

### i = 1 (num = 7)

```text
rob  = 7 + 0 = 7
skip = 2

current = max(7, 2) = 7

prev2 = 2
prev = 7
```

### i = 2 (num = 9)

```text
rob  = 9 + 2 = 11
skip = 7

current = max(11, 7) = 11

prev2 = 7
prev = 11
```

### i = 3 (num = 3)

```text
rob  = 3 + 7 = 10
skip = 11

current = max(10, 11) = 11

prev2 = 11
prev = 11
```

### i = 4 (num = 1)

```text
rob  = 1 + 11 = 12
skip = 11

current = max(12, 11) = 12
```

### Answer

```text
12
```

---

# DP Pattern

## Identify State

```text
f(i)
=
Maximum money achievable up to house i
```

## Choices

```text
For house i:
1. Rob house (add nums[i] and bridge to f(i-2))
2. Skip house (inherit directly from f(i-1))
```

## Recurrence

```text
f(i) = max(nums[i] + f(i-2), f(i-1))
```

## Base Case

```text
f(0) = nums[0]
f(i < 0) = 0
```
