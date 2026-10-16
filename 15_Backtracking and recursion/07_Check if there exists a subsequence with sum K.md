# Check If There Exists a Subsequence with Sum K ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given an array of non-negative integers `arr` and a target value `K`, determine if there is a **subsequence** of the given array whose elements sum up to exactly `K`.

Return `true` if such a subsequence exists, otherwise return `false`.

---

## Examples

### Example 1

```text
Input: arr = [3, 34, 4, 12, 5, 2], K = 9
Output: true
```

### Explanation

```text
The subsequence [4, 5] has a sum of 4 + 5 = 9, which matches K perfectly.
```

---

### Example 2

```text
Input: arr = [3, 34, 4, 12, 5, 2], K = 30
Output: false
```

### Explanation

```text
No combination of elements can be summed to equal 30.
```

---

# Intuition

This challenge is a classic variation of the **Subset Sum Problem**, which natively maps to a **0/1 Knapsack Problem** configuration. For each number in the array, we face a binary decision: **either we include it in our running sum pool, or we exclude it**.

- **The Exponential Backtracking Trap:** A naive recursive choice tree explores both paths for every item. For an array of size `N`, this creates a state-space branch size of \(O(2^N)\), triggering Time Limit Exceeded (TLE) exceptions for larger sets.
- **Overlapping Subproblems:** If we are at index `i` and our remaining target sum is `remK`, it doesn't matter *which* preceding elements were picked to reach this state. We only care about the pair `(i, remK)`.
- **Dynamic Programming Optimization:** By tracking states inside a boolean grid cache, we drop execution latency from an exponential track straight down to a fast **\(O(N \times K)\)** polynomial track. 
- **Space Optimization (1D Array Rolling Slide):** Since computing row `i` only requires values from row `i-1`, we can completely eliminate a 2D matrix overhead. We can maintain a single 1D boolean array of size `K + 1` and slide values **backward from right to left** to compute updates in-place without overwriting active prerequisites.

---

# Brute Force Solution

## Idea

We use a simple recursive backtracking sequence. At each index, we evaluate the inclusion branch (`target - arr[index]`) and the exclusion branch (`target`), returning `true` the moment a short-circuit success condition matches.

---

## Java Code

```java
class Solution {
    public boolean checkSubsequenceSum(int[] arr, int K) {
        return backtrack(0, arr, K);
    }

    private boolean backtrack(int index, int[] arr, int target) {
        // Base Case 1: Target sum achieved
        if (target == 0) return true;
        // Base Case 2: Target breached or array completely exhausted
        if (target < 0 || index == arr.length) return false;

        // Try including the item OR excluding the item
        return backtrack(index + 1, arr, target - arr[index]) || 
               backtracking(index + 1, arr, target);
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** \(O(2^N)\) where N is the length of the array.
- **Space Complexity:** O(N) auxiliary recursion call stack memory depth allocation.

---

# State Definition (Optimal 1D DP Approach)

To formalise our dynamic knapsack updates inside a optimized space structure, we initialize a single tracking array cache:

```text
dp[j] = A boolean flag indicating whether a subsequence sum of exactly j is achievable 
        using the subset of elements processed up to the current iteration step.
```

---

# Recurrence / Structural State Condition

```text
Initial State:
dp[0] = true (A sum of 0 is always achievable via the empty subsequence [])
All other dp[j] = false

Transitions for each element 'num' inside 'arr':
For j from K down to num:
    dp[j] = dp[j] OR dp[j - num]
```

---

# Optimal Space-Optimized 1D Dynamic Programming Solution

## Idea

We initialize a boolean array `dp` of size `K + 1` set to `false`, seeding `dp[0] = true`. We loop through each integer `num` in the array. For each number, we loop **backwards from `K` down to `num`** to update the array. This backward scan prevents a number from being reused multiple times in the same step, enforcing the 0/1 selection rule using only O(K) space.

---

## Java Code

```java
class Solution {
    public boolean checkSubsequenceSum(int[] arr, int K) {
        // Guard Condition: If target is 0, the empty subsequence satisfies it instantly
        if (K == 0) {
            return true;
        }
        if (arr == null || arr.length == 0) {
            return false;
        }

        // dp[j] tracks if a subsequence sum equal to j can be formed
        boolean[] dp = new boolean[K + 1];
        
        // Base Case: A target sum of 0 is vacuously true (empty subset choice)
        dp[0] = true;

        // Step 1: Iterate through each available integer element
        for (int num : arr) {
            
            // Step 2: Update the target sum availability cache BACKWARDS
            // Scanning backward protects historical state flags from premature overwrites
            for (int j = K; j >= num; j--) {
                
                // If target 'j' is already achievable, or can be achieved by including 'num'
                if (dp[j - num]) {
                    dp[j] = true;
                }
            }
            
            // Optimization Short-Circuit: If the final target K is already unlocked, exit early
            if (dp[K]) {
                return true;
            }
        }

        return dp[K];
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N × K)
```
*Explanation:* The outer loop runs exactly N times (for each item in the array). The inner transition loops backward at most K times. Inside the loop, it performs basic constant-time logical OR operations, keeping the time complexity bounded by polynomial limits.

---

### Space Complexity

```text
Space Complexity: O(K)
```
*Explanation:* Highly optimized. By compressing a standard 2D grid matrix into a single rolling 1D state array cache, the auxiliary memory footprint remains completely independent of the array size N, scaling strictly with the magnitude of the target sum value `K`.

---

# Key Takeaways

✅ Core fundamental paradigm for processing subset sums and capacity allocation paths.

✅ **The Backward Scan Invariant:** When squashing a 2D 0/1 Knapsack problem into a 1D row array cache, you **must loop the sum variable backwards** (`for (int j = K; j >= num; j--)`). If you scan forward from left to right, you will inadvertently allow the same element to be included multiple times across the same row pass, corrupting the code into a *Unbounded Knapsack* layout.

✅ Directly drives real-world microarchitectures like **cryptographic subset sum knapsack decoders**, inventory budget allocation schedulers, financial portfolio risk balance matchers, and bin-packing optimization engines.

✅ Common Architectural Paradigm:

```text
Space-Optimized Bitwise Knapsack Sweeps for Sum Target Reachability
```

### Similar Problems

- Partition Equal Subset Sum ([LeetCode 416](https://leetcode.com))
- Combination Sum ([LeetCode 39](https://leetcode.com))
- Target Sum ([LeetCode 494](https://leetcode.com))
- Ones and Zeroes ([LeetCode 474](https://leetcode.com))
