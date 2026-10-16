# Count All Subsequences with Sum K ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given an array `arr` of non-negative integers and an integer `K`, find the **total number of subsequences** of the given array whose elements sum up to exactly `K`.

Since the answer can be very large, return it **modulo \(10^9 + 7\)**.

---

## Examples

### Example 1

```text
Input: arr =, K = 10
Output: 3
```

### Explanation

```text
The distinct subsequences that sum up to 10 are:
1. [5, 2, 3]  -> 5 + 2 + 3 = 10
2. [10]       -> 10
3. [2, 8]     -> 2 + 8 = 10
Total count = 3.
```

---

### Example 2

```text
Input: arr =, K = 10
Output: 3
```

### Explanation

```text
The distinct subsequences that sum up to 10 are:
1. [2, 5, 3] -> 2 + 5 + 3 = 10
2. [5, 1, 4] -> 5 + 1 + 4 = 10
3. [2, 1, 4, 3] -> 2 + 1 + 4 + 3 = 10
Total count = 3.
```

---

### Example 3 (The Zero Invariant Case)

```text
Input: arr =, K = 3
Output: 4
```

### Explanation

```text
The distinct subsequences that sum up to 3 are:
1. [3]       -> 3
2. [0, 3]    -> 0 + 3 = 3 (using the first 0)
3. [0, 3]    -> 0 + 3 = 3 (using the second 0)
4. [0, 0, 3] -> 0 + 0 + 3 = 3
Total count = 4.
```

---

# Intuition

This problem structurally expands on the **Subset Sum Check** problem. Instead of verifying mere *reachability* (using a logical `OR` operation), we need to **accumulate the total combinations** of paths leading to each target value (using a numeric `SUM` operation).

- **The Zero Traversal Boundary Risk:** If an array contains `0` elements, they do not change the sum value mathematically, but they **double the total combination count** for that sum state. This is because any valid subset can either include the `0` or exclude the `0`. 
- **Dynamic Programming Compression:** We can construct a space-optimized 1D integer tracking cache array of size `K + 1`. We process the data stream row by row, rolling states **backwards from `K` down to `num`** to ensure that each array element is evaluated exactly once per selection layer (enforcing the 0/1 selection restriction).

---

# State Definition (Optimal 1D DP Approach)

To compile the numerical path combinations efficiently using a flat, constant storage allocation envelope:

```text
dp[j] = The total number of valid subsequences processed so far whose elements sum up to exactly j.
```

---

# Recurrence / Structural State Condition

```text
Initial State:
dp[0] = 1 (There is exactly 1 way to form a sum of 0: the empty subsequence [])
All other dp[j] = 0

Transitions for each element 'num' inside 'arr':
For j from K down to num:
    dp[j] = (dp[j] + dp[j - num]) % MOD
```

---

# Optimal Space-Optimized Dynamic Programming Solution

## Idea

We allocate an integer array cache `dp` seeded with `dp[0] = 1`. We process each element `num` from the array sequentially, running a backward iteration pass from `K` down to `num`. By computing transitions in reverse, we safely integrate `0` elements natively without needing complex pre-sorting rules or exponential post-loop math multiplications.

---

## Java Code

```java
class Solution {
    public int perfectSum(int[] arr, int K) {
        if (arr == null) {
            return 0;
        }

        int MOD = 1_000_000_007;
        
        // dp[j] stores the number of subsequences that sum up to j
        int[] dp = new int[K + 1];
        
        // Base Case: There is exactly 1 way to form a sum of 0 (using the empty set [])
        dp[0] = 1;

        // Step 1: Iterate through each available integer element
        for (int num : arr) {
            
            // Step 2: Accumulate path counts BACKWARDS from K down to num
            // Processing in reverse protects historical indices from premature row overwrites
            for (int j = K; j >= num; j--) {
                dp[j] = (dp[j] + dp[j - num]) % MOD;
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
*Explanation:* The outer processing loop loops exactly N times (the size of the array). The inner accumulation loop steps backward at most K times per item. All inner calculations resolve via constant-time primitive modular additions, locking total runtime complexity to polynomial limits.

---

### Space Complexity

```text
Space Complexity: O(K)
```
*Explanation:* Highly optimal auxiliary memory footprint. By flattening a standard 2D combinatorial matrix grid into a single rolling 1D state cache array, space requirements scale strictly with the target parameter `K`, remaining completely independent of the array length N.

---

# Key Takeaways

✅ Core baseline template for counting combination sums and matching paths within capacity constraints.

✅ **The Modulo Addition Rule:** When adding combinations inside a dynamic programming track, always execute the modulo operator right at the point of addition (`(dp[j] + dp[j - num]) % MOD`). Waiting until the end of the script will cause standard primitive data types to overflow, corrupting the calculation logs.

✅ Directly drives real-world microarchitectures like **asymmetric cryptography path counting engines**, network bandwidth packet multiplexers, financial risk portfolio asset allocator paths, and combinatorics solvers.

✅ Common Architectural Paradigm:

```text
Space-Compressed Accumulative Knapsack Sweeps for Modular Combination Counts
```

### Similar Problems

- Check If There Exists a Subsequence with Sum K ([GeeksforGeeks](https://geeksforgeeks.com))
- Combination Sum IV ([LeetCode 377](https://leetcode.com))
- Partition Equal Subset Sum ([LeetCode 416](https://leetcode.com))
- Target Sum ([LeetCode 494](https://leetcode.com))
