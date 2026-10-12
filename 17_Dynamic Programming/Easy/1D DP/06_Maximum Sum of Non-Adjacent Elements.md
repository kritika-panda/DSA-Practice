# Maximum Sum of Non-Adjacent Elements

## Problem Statement

Given an array of positive integers `nums`, find the maximum sum of a subsequence such that no two elements in the subsequence are adjacent in the array. 

In other words, if you pick an element at index `i`, you cannot pick elements at index `i - 1` or `i + 1`.

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the total number of elements inside the nums array)*

---

## Example 1

### Input

```text
nums = [2, 1, 4, 9]
```

### Output

```text
11
```

### Explanation

Optimal choice: Pick `2` (index 0) and `9` (index 3).  
`Cost = 2 + 9 = 11`. Note that `2` and `4` would yield `6`, while `1` and `9` would yield `10`.

---

## Example 2

### Input

```text
nums = [1, 2, 4]
```

### Output

```text
5
```

---

# Intuition

At any given element `i`, you have two distinct choices:
1. **Pick the element:** If you pick `nums[i]`, you cannot take the immediate previous element. Thus, you add `nums[i]` to the best possible result from index `i - 2`.
2. **Skip the element:** If you skip `nums[i]`, your maximum value up to this point will simply be the best possible result from index `i - 1`.

Therefore,

```text
Maximum Sum(i)
=
max(
    pick nums[i] + optimal from i-2,
    skip nums[i] + optimal from i-1
)
```

This naturally maps to a Dynamic Programming structural optimization problem.

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
        // Base case: If we fall below index 0, no sum can be accumulated
        if (index < 0) {
            return 0;
        }
        
        // Base case: If we are at the very first element, we must pick it
        if (index == 0) {
            return nums[0];
        }

        // Choice 1: Pick the current element and recurse from index - 2
        int pick = nums[index] + solve(index - 2, nums);

        // Choice 2: Skip the current element and recurse from index - 1
        int skip = solve(index - 1, nums);

        return Math.max(pick, skip);
    }

    public int maximumNonAdjacentSum(int[] nums) {
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

        int pick = nums[index] + solve(index - 2, nums, dp);
        int skip = solve(index - 1, nums, dp);

        return dp[index] = Math.max(pick, skip);
    }

    public int maximumNonAdjacentSum(int[] nums) {
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
dp[i] = maximum non-adjacent sum possible from elements up to index i
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

    public int maximumNonAdjacentSum(int[] nums) {
        int n = nums.length;
        if (n == 0) return 0;
        if (n == 1) return nums[0];

        int[] dp = new int[n];
        
        // Base case setups
        dp[0] = nums[0];

        for (int i = 1; i < n; i++) {
            int pick = nums[i];
            if (i > 1) {
                pick += dp[i - 2];
            }
            
            int skip = dp[i - 1];

            dp[i] = Math.max(pick, skip);
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

we only ever read values from:

```text
dp[i-1]
dp[i-2]
```

Therefore, maintaining the entire tracking array is unnecessary. We can collapse our space by using two rolling primitive variables.

---

## Java Code

```java
class Solution {

    public int maximumNonAdjacentSum(int[] nums) {
        int n = nums.length;
        if (n == 0) return 0;

        int prev = nums[0]; // Represents dp[i-1]
        int prev2 = 0;      // Represents dp[i-2]

        for (int i = 1; i < n; i++) {
            // Pick current element and link to two slots back
            int pick = nums[i] + prev2;
            
            // Skip current element and inherit from one slot back
            int skip = prev;

            int current = Math.max(pick, skip);

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
nums = [2, 1, 4, 9]
```

### Initial

```text
prev  = 2
prev2 = 0
```

### i = 1 (num = 1)

```text
pick = 1 + 0 = 1
skip = 2

current = max(1, 2) = 2

prev2 = 2
prev = 2
```

### i = 2 (num = 4)

```text
pick = 4 + 2 = 6
skip = 2

current = max(6, 2) = 6

prev2 = 2
prev = 6
```

### i = 3 (num = 9)

```text
pick = 9 + 2 = 11
skip = 6

current = max(11, 6) = 11
```

### Answer

```text
11
```

---

# DP Pattern

## Identify State

```text
f(i)
=
Maximum non-adjacent sum achievable up to index i
```

## Choices

```text
For position i:
1. Pick element (add nums[i] and bridge to f(i-2))
2. Skip element (inherit directly from f(i-1))
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
