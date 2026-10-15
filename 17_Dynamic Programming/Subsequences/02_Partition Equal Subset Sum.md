# Partition Equal Subset Sum (LeetCode 416)

## Problem Statement

Given an integer array `nums`, return `true` if you can partition the array into two subsets such that the sum of elements in both subsets is equal.

### Example 1

```text
Input: nums = [1,5,11,5]
Output: true

Explanation:
Subset 1 = [1,5,5] => sum = 11
Subset 2 = [11]    => sum = 11
```

### Example 2

```text
Input: nums = [1,2,3,5]
Output: false
```

---

# Intuition

Let:

```text
totalSum = sum(nums)
```

For two subsets to have equal sum:

```text
subset1 + subset2 = totalSum

subset1 = subset2

2 × subset1 = totalSum
```

Therefore:

```text
target = totalSum / 2
```

Now the problem reduces to:

> Can we find a subset whose sum is exactly `target`?

This is a classic **Subset Sum (0/1 Knapsack)** problem.

---

# Optimal Approach (1D DP)

We maintain a DP array where:

```text
dp[j] = true
```

means:

```text
A subset exists whose sum equals j.
```

Initially:

```text
dp[0] = true
```

because sum `0` can always be formed by selecting no elements.

For every number, update DP from right to left:

```text
dp[j] = dp[j] || dp[j - num]
```

Backward traversal ensures that each element is used only once.

---

# Java Solution

```java
class Solution {

    public boolean canPartition(int[] nums) {

        int totalSum = 0;

        for (int num : nums) {
            totalSum += num;
        }

        // Odd sum can never be partitioned equally
        if ((totalSum & 1) == 1) {
            return false;
        }

        int target = totalSum / 2;

        boolean[] dp = new boolean[target + 1];

        dp[0] = true;

        for (int num : nums) {

            for (int j = target; j >= num; j--) {

                dp[j] = dp[j] || dp[j - num];
            }
        }

        return dp[target];
    }
}
```

---

# Dry Run

```text
nums = [1,5,11,5]

totalSum = 22

target = 11
```

Initial:

```text
dp = [T,F,F,F,F,F,F,F,F,F,F,F]
```

After processing `1`:

```text
dp = [T,T,F,F,F,F,F,F,F,F,F,F]
```

After processing `5`:

```text
dp = [T,T,F,F,F,T,T,F,F,F,F,F]
```

After processing `11`:

```text
dp[11] = true
```

Since:

```text
dp[target] = dp[11] = true
```

Answer:

```text
true
```

---

# Why Traverse Backward?

Suppose:

```text
num = 3
target = 6
```

If we move left → right:

```text
dp[3] becomes true

dp[6] may immediately use dp[3]
created in the same iteration
```

This effectively uses `3` twice.

Backward traversal prevents this and guarantees:

```text
Each element is used at most once.
```

---

# Complexity Analysis

### Time Complexity

```text
O(N × Target)
```

where

```text
Target = Total Sum / 2
```

### Space Complexity

```text
O(Target)
```

---

# Pattern Recognition

If the problem contains phrases like:

```text
Can we divide the array...
Can we split the array...
Can we select elements with a target sum...
Can we form a subset...
```

Think:

```text
✅ Subset Sum
✅ 0/1 Knapsack
✅ DP on Target Sum
✅ Partition Equal Subset Sum
```
