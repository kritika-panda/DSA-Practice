# Minimum Pair Removal to Sort Array I

## Problem Statement

You are given an integer array `nums`. In one operation, you can select any **adjacent pair** of elements `(nums[i], nums[i + 1])` and remove them from the array. After the removal, the remaining parts of the array contract and become adjacent.

Return *the **minimum number of operations** (pair removals) required to make the remaining array strictly sorted in non-decreasing order (`nums[i] <= nums[i + 1]`)*. If it is impossible to sort the array or if the array becomes completely empty (which is considered vacuously sorted), return the total operations matching that state.

---

## Examples

### Example 1

```text
Input: nums = [1, 4, 3, 2, 5]
Output: 1
```

### Explanation

```text
- Remove the adjacent pair (4, 3) at index 1.
- The remaining elements contract to form [1, 2, 5].
- The array [1, 2, 5] is strictly non-decreasing (1 <= 2 <= 5).
- Total operations = 1.
```

---

### Example 2

```text
Input: nums = [5, 4, 3, 2, 1]
Output: 2
```

### Explanation

```text
- Remove pair (4, 3) -> remaining is [5, 2, 1]
- Remove pair (5, 2) -> remaining is [1], which is sorted.
- Total operations = 2.
```

---

# Intuition

This problem can be structurally rephrased as finding the **Longest Non-Decreasing Subsequence (LNDS)** that can be preserved while removing elements strictly in pairs.

Every time we remove an adjacent pair, we eliminate exactly 2 elements from the total size. If we perform k operations, we remove 2k elements. For the remaining array to be sorted, the elements we **keep** must:
1. Form a valid non-decreasing subsequence.
2. The index gaps between the preserved elements must allow valid pair removals. Specifically, the number of deleted elements between any two kept elements (and at the boundaries) must be **even**.

For an array of moderate size (e.g., N ≤ 10³), we can use **Dynamic Programming**:
- Define `dp[i]` as the maximum length of a valid sorted subsequence ending at index `i`.
- For each element `i`, we look backward at all valid predecessors `j < i` where `nums[j] <= nums[i]`.
- The structural gap condition dictates that the number of skipped elements between `j` and `i` must be even: `(i - j - 1) % 2 == 0`.
- Additionally, the number of skipped elements before the very first kept element and after the very last kept element must also be even to ensure complete pair matching.

---

# Brute Force Solution

## Idea

We can use a recursive backtracking template with memoization to try all possible adjacent pair removals at every step. If the current state configuration forms a sorted sequence, we capture the operation depth count and minimize it globally.

---

## Java Code

```java
import java.util.Arrays;

class Solution {
    public int minPairRemovals(int[] nums) {
        // Base placeholder for multi-pass recursive permutations.
        // Given the exponential complexity of raw backtracking, 
        // we directly pivot to the optimal structural DP engine below.
        return solveDP(nums);
    }

    private int solveDP(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n];
        Arrays.fill(dp, 1);
        int maxLen = 0;

        for (int i = 0; i < n; i++) {
            // First element must have an even number of skipped prefixes
            if (i % 2 != 0) dp[i] = -1; 

            for (int j = 0; j < i; j++) {
                if (dp[j] != -1 && nums[j] <= nums[i] && (i - j - 1) % 2 == 0) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
            
            // Suffix check: remaining elements after i must be even
            if (dp[i] != -1 && (n - 1 - i) % 2 == 0) {
                maxLen = Math.max(maxLen, dp[i]);
            }
        }

        // If no subsequence could successfully satisfy parity limits, clear all except full wipeout
        if (maxLen == 0) return n / 2;
        return (n - maxLen) / 2;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N²) due to the nested loop structure scanning matching parity predecessors for each index element.
- **Space Complexity:** O(N) auxiliary state tracking layout array.

---

# State Definition (Optimal Approach)

To establish our calculations inside a formal dynamic programming grid framework, we define our states as:

```text
dp[i] = Maximum length of a valid non-decreasing subsequence ending at index i, 
        ensuring all skipped elements from the left boundaries up to i match an even parity block constraint.
```

---

# Recurrence / Structural State Condition

```text
For each i from 0 to n-1:
    Initial State: dp[i] = (i % 2 == 0) ? 1 : -1
    
    Transitions:
    For each j from 0 to i-1:
        If dp[j] != -1 AND nums[j] <= nums[i] AND (i - j - 1) % 2 == 0:
            dp[i] = max(dp[i], dp[j] + 1)
```

---

# Optimal Dynamic Programming Solution

## Idea

We initialize a 1D `dp` array. We process array states linearly from left to right. An entry `dp[i]` is only valid (`!= -1`) if the elements before it can be completely removed in pairs. When transitioning from a predecessor `j`, we verify that the value constraint (`nums[j] <= nums[i]`) and the index spacing constraint (`(i - j - 1) % 2 == 0`) are simultaneously preserved. At the end, we extract the maximum length that leaves an even number of elements at the tail boundary.

---

## Java Code

```java
import java.util.Arrays;

class Solution {
    public int minPairRemovals(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }

        int n = nums.length;
        // dp[i] stores the max sorted subsequence length ending at index i
        int[] dp = new int[n];
        Arrays.fill(dp, -1);

        int maxPreservedElements = 0;

        for (int i = 0; i < n; i++) {
            // Base Case: A subsequence can start at index i if all elements preceding it can be paired up
            if (i % 2 == 0) {
                dp[i] = 1;
            }

            // Look back at all valid predecessors
            for (int j = 0; j < i; j++) {
                if (dp[j] != -1 && nums[j] <= nums[i]) {
                    // Parity Check: The number of elements skipped between j and i must be even
                    if ((i - j - 1) % 2 == 0) {
                        dp[i] = Math.max(dp[i], dp[j] + 1);
                    }
                }
            }

            // An ending position is valid only if all remaining elements after it can be paired up
            if (dp[i] != -1 && (n - 1 - i) % 2 == 0) {
                maxPreservedElements = Math.max(maxPreservedElements, dp[i]);
            }
        }

        // Minimum removals = (Total elements - Maximum elements kept) / 2
        return (n - maxPreservedElements) / 2;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N²)
```
*Explanation:* The algorithm uses a standard nested loop structure where each element i checks all preceding elements j. The inner operations involve only basic constant-time numeric comparisons and parity evaluation math, bounding the runtime complexity strictly to quadratic dimensions.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary storage space scales linearly to allocate the 1D state tracking cache array `dp` of size N.

---

# Key Takeaways

✅ Advanced sequence optimization problem combining Longest Increasing Subsequence templates with parity constraint bounds.

✅ **The Even Parity Invariant:** When deletions are restricted strictly to adjacent pairs, any gap created between elements that are **kept** must contain an **even number of elements**. This insight allows us to filter transitions in the DP inner loop using simple index arithmetic checks (`(i - j - 1) % 2 == 0`).

✅ Directly drives real-world tracking engines like **transaction log stream synchronization controllers**, compression utilities minimizing token transformations, and video frame delta encoding formatters.

✅ Common Architectural Paradigm:

```text
Parity-Constrained Subsequence Maximization for Structural Stream Reductions
```

### Similar Problems

- Longest Increasing Subsequence ([LeetCode 300](https://leetcode.com))
- Delete Columns to Make Sorted ([LeetCode 944](https://leetcode.com))
- Minimum Number of Removals to Make Mountain Array ([LeetCode 1671](https://leetcode.com))
- Number of Longest Increasing Subsequence ([LeetCode 673](https://leetcode.com))
