# 213. House Robber II

🔗 **Problem Link:**  
https://leetcode.com/problems/house-robber-ii/

---

# Problem Statement

You are a professional robber planning to rob houses along a street.

All houses are arranged in a **circle**, which means:

- The first house is adjacent to the second house.
- The last house is adjacent to the second-last house.
- **The first and last houses are also adjacent.**

You cannot rob two adjacent houses.

Return the maximum amount of money you can rob without alerting the police.

---

# Key Observation

In **House Robber I**, houses were arranged in a straight line.

In **House Robber II**, houses form a circle.

This creates one additional constraint:

```text
Cannot rob both:
- First house
- Last house
```

because they are adjacent.

---

# Breaking the Circular Dependency

For any valid solution:

### Case 1

Rob houses from:

```text
[0 ... n-2]
```

Exclude the last house.

### Case 2

Rob houses from:

```text
[1 ... n-1]
```

Exclude the first house.

The answer becomes:

```text
max(
    rob(0, n-2),
    rob(1, n-1)
)
```

Each of these subproblems is exactly **House Robber I**.

---

# Approach 1: Memoization (Top-Down DP)

## Intuition

Compute:

```text
max(
    rob houses [0...n-2],
    rob houses [1...n-1]
)
```

For each range, use the House Robber I memoization solution.

---

## Recursive Relation

```text
dfs(index) =
    max(
        nums[index] + dfs(index + 2),
        dfs(index + 1)
    )
```

---

## Code

```java
class Solution {

    public int rob(int[] nums) {

        int n = nums.length;

        if (n == 1)
            return nums[0];

        int[] dp1 = new int[n];
        int[] dp2 = new int[n];

        Arrays.fill(dp1, -1);
        Arrays.fill(dp2, -1);

        int excludeLast = dfs(0, n - 2, nums, dp1);
        int excludeFirst = dfs(1, n - 1, nums, dp2);

        return Math.max(excludeLast, excludeFirst);
    }

    private int dfs(int index,
                    int end,
                    int[] nums,
                    int[] dp) {

        if (index > end)
            return 0;

        if (dp[index] != -1)
            return dp[index];

        int robCurrent =
                nums[index] +
                dfs(index + 2, end, nums, dp);

        int skipCurrent =
                dfs(index + 1, end, nums, dp);

        dp[index] =
                Math.max(robCurrent, skipCurrent);

        return dp[index];
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Each state is computed once.

### Space Complexity

```text
O(n)
```

- Memoization array
- Recursion stack

---

# Approach 2: Bottom-Up DP (1D DP)

## Intuition

Run House Robber I twice:

### First Pass

```text
House 0 → House n-2
```

### Second Pass

```text
House 1 → House n-1
```

Return the larger answer.

---

## DP Relation

```text
dp[i] = max(
    dp[i - 1],
    nums[i] + dp[i - 2]
)
```

---

## Code

```java
class Solution {

    public int rob(int[] nums) {

        int n = nums.length;

        if (n == 1)
            return nums[0];

        return Math.max(
                robLinear(nums, 0, n - 2),
                robLinear(nums, 1, n - 1)
        );
    }

    private int robLinear(int[] nums,
                          int start,
                          int end) {

        int len = end - start + 1;

        if (len == 1)
            return nums[start];

        int[] dp = new int[len];

        dp[0] = nums[start];
        dp[1] = Math.max(
                nums[start],
                nums[start + 1]
        );

        for (int i = 2; i < len; i++) {

            dp[i] = Math.max(
                    dp[i - 1],
                    nums[start + i] + dp[i - 2]
            );
        }

        return dp[len - 1];
    }
}
```

---

## Dry Run

### Input

```text
nums = [2, 3, 2]
```

### Case 1

Exclude last house:

```text
[2, 3]
```

Answer:

```text
3
```

### Case 2

Exclude first house:

```text
[3, 2]
```

Answer:

```text
3
```

### Final

```text
max(3, 3) = 3
```

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Two linear traversals.

### Space Complexity

```text
O(n)
```

DP array.

---

# Approach 3: Space Optimized DP (Most Optimal)

## Intuition

House Robber I only needs:

```text
dp[i - 1]
dp[i - 2]
```

Therefore we can optimize space from:

```text
O(n)
```

to

```text
O(1)
```

---

## Algorithm

Compute:

```text
max(
    robLinear(0, n-2),
    robLinear(1, n-1)
)
```

Where each `robLinear()` uses only:

```text
prev1 = dp[i-1]
prev2 = dp[i-2]
```

---

## Code

```java
class Solution {

    public int rob(int[] nums) {

        int n = nums.length;

        if (n == 1)
            return nums[0];

        return Math.max(
                robLinear(nums, 0, n - 2),
                robLinear(nums, 1, n - 1)
        );
    }

    private int robLinear(int[] nums,
                          int start,
                          int end) {

        int prev1 = 0;
        int prev2 = 0;

        for (int i = start; i <= end; i++) {

            int curr = Math.max(
                    prev1,
                    prev2 + nums[i]
            );

            prev2 = prev1;
            prev1 = curr;
        }

        return prev1;
    }
}
```

---

## Dry Run

### Input

```text
nums = [1, 2, 3, 1]
```

### Case 1

Exclude Last House

```text
[1, 2, 3]
```

| House | Value | Profit |
|---------|---------|---------|
| 1 | 1 | 1 |
| 2 | 2 | 2 |
| 3 | 3 | 4 |

Result:

```text
4
```

---

### Case 2

Exclude First House

```text
[2, 3, 1]
```

| House | Value | Profit |
|---------|---------|---------|
| 2 | 2 | 2 |
| 3 | 3 | 3 |
| 1 | 1 | 3 |

Result:

```text
3
```

---

### Final Answer

```text
max(4, 3) = 4
```

---

## Edge Cases

### Single House

```text
nums = [5]
```

Output:

```text
5
```

---

### Two Houses

```text
nums = [2, 3]
```

Output:

```text
3
```

---

### First and Last Conflict

```text
nums = [2, 3, 2]
```

Cannot rob both 2's because they are adjacent in the circle.

Output:

```text
3
```

---

# Complexity Analysis

### Time Complexity

```text
O(n)
```

Two linear passes.

```text
O(n) + O(n) = O(n)
```

### Space Complexity

```text
O(1)
```

Only two variables are maintained.

---

# Key Takeaways

| Approach | Time | Space |
|-----------|--------|--------|
| Memoization | O(n) | O(n) |
| 1D DP | O(n) | O(n) |
| Space Optimized DP | O(n) | O(1) |

---

## Difference from House Robber I

| House Robber I | House Robber II |
|----------------|-----------------|
| Houses in a line | Houses in a circle |
| No first-last dependency | First and last are adjacent |
| Single DP run | Two DP runs |
| Direct solution | Split into two ranges |

---

# Pattern Recognition

Whenever a problem says:

```text
Array is circular
First and last elements are adjacent
```

A common trick is:

```text
Case 1 -> Include first element, exclude last

Case 2 -> Exclude first element, include last

Answer = max(case1, case2)
```

This pattern reappears in many circular DP problems.

✅ **Most Optimal Solution:** Space Optimized DP with two House Robber I runs (`O(n)` time, `O(1)` space).
