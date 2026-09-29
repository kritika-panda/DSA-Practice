# 198. House Robber

🔗 **Problem Link:**  
https://leetcode.com/problems/house-robber/?envType=problem-list-v2&envId=dynamic-programming

---

## Problem Statement

You are a professional robber planning to rob houses along a street.

Each house has a certain amount of money stashed, but adjacent houses have security systems connected. If two adjacent houses are robbed on the same night, the police will be alerted.

Return the maximum amount of money you can rob without robbing two adjacent houses.

---

# Approach 1: Memoization (Top-Down DP)

## Intuition

At every house, we have two choices:

1. **Rob the current house** → Skip the next house.
2. **Skip the current house** → Move to the next house.

We recursively explore both choices and cache results to avoid recomputation.

---

## Recursive Relation

```text
dfs(index) =
    max(
        nums[index] + dfs(index + 2), // Rob current house
        dfs(index + 1)                // Skip current house
    )
```

---

## Code

```java
class Solution {
    public int rob(int[] nums) {
        int[] dp = new int[nums.length];
        Arrays.fill(dp, -1);

        return dfs(0, nums, dp);
    }

    private int dfs(int index, int[] nums, int[] dp) {

        if (index >= nums.length)
            return 0;

        if (dp[index] != -1)
            return dp[index];

        int robCurrent = nums[index] + dfs(index + 2, nums, dp);
        int skipCurrent = dfs(index + 1, nums, dp);

        dp[index] = Math.max(robCurrent, skipCurrent);

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

Each index is computed once and stored in the memoization array.

### Space Complexity

```text
O(n)
```

- DP array: `O(n)`
- Recursion stack: `O(n)`

---

# Approach 2: Bottom-Up DP (1D DP)

## Intuition

Let:

```text
dp[i] = Maximum money that can be robbed from houses [0...i]
```

For every house:

### Option 1

Skip current house

```text
dp[i - 1]
```

### Option 2

Rob current house

```text
nums[i] + dp[i - 2]
```

Take the maximum of both.

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

        if (n == 1) {
            return nums[0];
        }

        int[] dp = new int[n];

        dp[0] = nums[0];
        dp[1] = Math.max(nums[0], nums[1]);

        for (int i = 2; i < n; i++) {
            dp[i] = Math.max(
                    dp[i - 1],
                    nums[i] + dp[i - 2]
            );
        }

        return dp[n - 1];
    }
}
```

---

## Dry Run

### Input

```text
nums = [2, 7, 9, 3, 1]
```

### DP Table

| i | nums[i] | dp[i] |
|---|----------|--------|
| 0 | 2 | 2 |
| 1 | 7 | 7 |
| 2 | 9 | 11 |
| 3 | 3 | 11 |
| 4 | 1 | 12 |

### Result

```text
12
```

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Single traversal of the array.

### Space Complexity

```text
O(n)
```

DP array of size `n`.

---

# Approach 3: Space Optimized DP

## Observation

To calculate:

```text
dp[i] = max(dp[i - 1], nums[i] + dp[i - 2])
```

We only need:

- `dp[i - 1]`
- `dp[i - 2]`

So storing the entire DP array is unnecessary.

---

## Variables

```text
prev1 = dp[i - 1]
prev2 = dp[i - 2]
```

For every house:

```text
curr = max(prev1, prev2 + nums[i])
```

Then shift values forward.

---

## Code

```java
class Solution {
    public int rob(int[] nums) {

        int prev1 = 0; // dp[i - 1]
        int prev2 = 0; // dp[i - 2]

        for (int num : nums) {

            int curr = Math.max(
                    prev1,
                    prev2 + num
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
nums = [2, 7, 9, 3, 1]
```

| House | Money | prev2 | prev1 | curr |
|---------|---------|---------|---------|---------|
| 2 | 2 | 0 | 0 | 2 |
| 7 | 7 | 0 | 2 | 7 |
| 9 | 9 | 2 | 7 | 11 |
| 3 | 3 | 7 | 11 | 11 |
| 1 | 1 | 11 | 11 | 12 |

### Result

```text
12
```

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Single pass through the array.

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

### Transition of Solutions

```text
Recursion
    ↓
Memoization
    ↓
Bottom-Up DP
    ↓
Space Optimized DP
```

✅ For interviews, the **Space Optimized DP** solution is considered the most optimal solution.
