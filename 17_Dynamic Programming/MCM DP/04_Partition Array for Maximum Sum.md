# Partition Array for Maximum Sum (LeetCode 1043)

## Problem Statement

Given an integer array `arr`, partition the array into (contiguous) subarrays of length **at most** `k`. After partitioning, each subarray has their values changed to become the maximum value of that subarray.

Return *the largest sum of the given array after partitioning*.

The test cases are generated so that the answer fits in a 32-bit integer.

---

## Examples

### Example 1

```text
Input: arr = [1,15,7,9,2,5,10], k = 3
Output: 84
```

### Explanation

```text
The optimal partitioning strategy is:
- Subarray 1: [1, 15, 7]  → Max is 15. Transformed: [15, 15, 15] (Sum = 45)
- Subarray 2: [9, 2, 5]   → Max is 9.  Transformed: [9, 9, 9]    (Sum = 27)
- Subarray 3: [10]        → Max is 10. Transformed: [10]         (Sum = 10)

Total Maximized Sum = 45 + 27 + 10 = 84.
```

---

### Example 2

```text
Input: arr = [1,4,1,5,7,3,6,1,9,9,3], k = 4
Output: 83
```

---

### Example 3

```text
Input: arr = [1], k = 1
Output: 1
```

---

# Intuition

This problem can be cleanly solved using **1D Continuous Array Partitioning Dynamic Programming**.

Instead of trying to find partition splits across the entire array at once, we solve it incrementally: **What is the maximum sum we can achieve for the prefix of the array ending at index `i`?**

To determine `dp[i]` (the maximized sum for the prefix ending at `i`):
- We look back at all possible sizes `len` for the *last* partition block ending exactly at `i`. The partition size can range from `1` up to a maximum of `k`.
- As we expand the size of this last block backward from `i` down to `i - len + 1`, we track the maximum element seen inside it (`currMax`).
- If we separate this last block, its contribution to the sum will be `currMax * len`. 
- The total sum for this configuration is this block's contribution added to the optimal subproblem sum achieved *before* this block started (`dp[i - len]`).
- We greedily maximize this total value across all valid partition lengths.

---

# State Definition

Let `dp[i]` be the maximum sum obtainable after partitioning the prefix string/subarray `arr[0...i]`.
*   `i`: The index of the last element in the current prefix being evaluated (from `0` to `n - 1`).

---

# Recurrence Relation

For a prefix ending at index `i`, we test all partition blocks of length `len` (where `1 <= len <= k`):

```text
dp[i] = max( dp[i - len] + (currMax * len) )
for all len from 1 to k (where i - len + 1 >= 0)
```
*(Note: If `i - len < 0`, the block spans all the way to the beginning of the array, so `dp[i - len]` evaluates to 0).*

---

# Base Cases

A single element at index `0` can only form a single partition block of length 1, so its score is simply its own value:
```text
dp[0] = arr[0]
```

---

# Approach: Bottom-Up 1D Dynamic Programming Tabulation

## Algorithm

1. Determine the length `n` of the array `arr`. Initialize a 1D integer array `dp[n]` to `0`.
2. Loop through each index `i` from `0` up to `n - 1` to compute the optimal prefix sums sequentially:
   - Track a rolling local peak element `currMax = 0`.
   - Loop through each possible partition length `len` from `1` up to `k`:
     - Ensure the lookback boundary does not go out of bounds: `if (i - len + 1 < 0) break;`
     - Update the local peak element: `currMax = Math.max(currMax, arr[i - len + 1]);`
     - Calculate the potential sum:
       - If `i - len >= 0`, `potentialSum = dp[i - len] + (currMax * len);`
       - Otherwise, `potentialSum = currMax * len;`
     - Greedily lock down the maximum value: `dp[i] = Math.max(dp[i], potentialSum);`
3. Return `dp[n - 1]`.

---

# Java Solution

```java
class Solution {
    public int maxSumAfterPartitioning(int[] arr, int k) {
        if (arr == null || arr.length == 0) {
            return 0;
        }

        int n = arr.length;
        // dp[i] stores the maximum sum obtainable for the prefix ending at index i
        int[] dp = new int[n];

        // Build the 1D partition array minimums sequentially
        for (int i = 0; i < n; i++) {
            int currMax = 0;

            // Test every possible partition block length 'len' ending at index i
            for (int len = 1; len <= k && i - len + 1 >= 0; len++) {
                // Update the maximum value in the current partition block [i - len + 1 ... i]
                currMax = Math.max(currMax, arr[i - len + 1]);

                // Total sum is the current block's value + the optimal prefix sum before it
                int currentBlockSum = currMax * len;
                int remainingPrefixSum = (i - len >= 0) ? dp[i - len] : 0;

                int totalSum = remainingPrefixSum + currentBlockSum;

                // Greedily maximize the accumulated total sum
                dp[i] = Math.max(dp[i], totalSum);
            }
        }

        return dp[n - 1];
    }
}
```

---

## Complexity Analysis

Let:
*   N: The total length of the input array `arr`.
*   K: The maximum allowed length of a contiguous partition block.

### Time Complexity
The outer loop sweeps across all $N$ indices of the array. The inner loop checks at most $K$ lookback step options to find the optimal boundary cut. This keeps the execution timeline securely bounded by:
```text
Time Complexity: O(N × K)
```

### Space Complexity
The auxiliary storage layout is dominated entirely by the flat 1D `dp` tabulation array of size $N$ to track historical prefix optimal sums:
```text
Space Complexity: O(N)
```

---

# Key Pattern
This problem belongs to the **1D Continuous Array Partitioning / Lookback Tabulation** template inside dynamic programming.

Common indicators:
- Partitioning an array or sequence into contiguous subarrays where each block's cost is computed based on its local elements (like its max value).
- Bounded partition length constraints (`len <= k`).
- Calculating states for index `i` by running a fixed historical lookback pass to check previous optimal slices `j`.

---

# Similar Problems
1. Word Break (LeetCode 139)
2. Palindrome Partitioning II (LeetCode 132)
3. Decode Ways (LeetCode 91)
4. Greatest Sum Divisible by Three (LeetCode 1262)

---

# Takeaway
```text
Map sequential partition choices onto a linear 1D lookback tabulation table.

Expand the last partition block backward up to 'k' steps to capture local peaks on the fly.

Combine the current block's multiplied value with the cached optimal sum from the preceding prefix slice.
```

**Pattern:** 1D Continuous Array Partitioning DP

<FollowUp>
Let me know if you would like to continue with your dynamic programming sequence. We can explore the raw markdown code for **Word Break (LeetCode 139)** to see simple prefix text splitting or look at **Palindrome Partitioning II (LeetCode 132)** for structural lookups!
</FollowUp>
