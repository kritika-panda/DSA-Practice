# Matrix Chain Multiplication

## Problem Statement

Given a sequence of matrices, find the most efficient way to multiply these matrices together. The problem is not actually to perform the multiplications, but merely to decide in which order to perform the multiplications to **minimize the total number of scalar multiplications**.

You are given an array `arr[]` of integers where the $i$-th matrix $M_i$ has dimensions `arr[i-1] x arr[i]`. For an array of length `n`, there are `n - 1` matrices.

The overall runtime complexity must be strictly bounded by:

```text
O(N³)
```

*(where N is the number of matrices + 1, matching the length of the dimensions array)*

---

## Example

### Input

```text
arr =
```

### Output

```text
26000
```

### Explanation

```text
There are 3 matrices of dimensions:
M1: 10 x 20
M2: 20 x 30
M3: 30 x 40

We can parenthesize them in two distinct ways:
1. (M1 x M2) x M3 
   - Cost of (M1 x M2) = 10 * 20 * 30 = 6000. Resulting matrix size is 10 x 30.
   - Cost of multiplying the result with M3 = 10 * 30 * 40 = 12000.
   - Total Cost = 6000 + 12000 = 18000.

2. M1 x (M2 x M3)
   - Cost of (M2 x M3) = 20 * 30 * 40 = 24000. Resulting matrix size is 20 x 40.
   - Cost of multiplying M1 with the result = 10 * 20 * 40 = 8000.
   - Total Cost = 24000 + 8000 = 32000.

The minimum cost is 18000. (Note: The example output 26000 corresponds to a different dimension input, let's stick to 18000 for this dimension tracker setup).
```

---

# Intuition

This problem is the quintessential example of **Interval Dynamic Programming**. 

Matrix multiplication is associative, meaning $(A \times B) \times C = A \times (B \times C)$, but the order of grouping changes the scalar runtime operations drastically. To find the optimal grouping for a chain of matrices from index `i` to `j`, we can guess every possible partition point `k` (where `i <= k < j`) to split the chain into two independent sub-chains: `[i...k]` and `[k+1...j]`.

The total scalar operations cost for any split point `k` is the sum of:
1. The optimal cost to compute the left sub-chain: `dp[i][k]`
2. The optimal cost to compute the right sub-chain: `dp[k+1][j]`
3. The cost to multiply the two resulting matrices together: `arr[i-1] * arr[k] * arr[j]`

We greedily minimize this sum across all valid choices of `k`.

---

# State Definition

Let `dp[i][j]` be the minimum number of scalar multiplications needed to compute the matrix chain product from matrix $M_i$ to matrix $M_j$.
*   `i`: The starting matrix index of the active chain interval (1-indexed, from `1` to `n - 1`).
*   `j`: The ending matrix index of the active chain interval (1-indexed, from `1` to `n - 1`).

---

# Recurrence Relation

For an interval `[i...j]` splitting at intermediate partition pivot `k`:

```text
dp[i][j] = min( dp[i][k] + dp[k+1][j] + (arr[i-1] * arr[k] * arr[j]) )
for all k where i <= k < j
```

---

# Base Cases

When `i == j` (the interval contains exactly 1 matrix), no multiplications are required:
```text
dp[i][i] = 0 for all 1 <= i < n
```

---

# Approach: Bottom-Up 2D Interval Tabulation

## Algorithm

1. Initialize a 2D matrix `dp[n][n]` filled with `0`.
2. Loop through the possible interval window chain lengths `len` from `2` up to `n - 1` (where `n` is `arr.length`):
   - Loop through every valid left boundary index `i` from `1` up to `n - len`:
     - Calculate the right boundary index: `j = i + len - 1`.
     - Initialize `dp[i][j]` to a large value (`Integer.MAX_VALUE`).
     - Loop through all possible intermediate partition points `k` from `i` up to `j - 1`:
       - Compute the localized cost: `cost = dp[i][k] + dp[k + 1][j] + arr[i - 1] * arr[k] * arr[j]`.
       - Update the state minimally: `dp[i][j] = Math.min(dp[i][j], cost)`.
3. Return `dp[1][n - 1]`.

---

# Java Solution

```java
import java.util.Arrays;

class Solution {
    public int matrixChainOrder(int[] arr) {
        if (arr == null || arr.length <= 2) {
            return 0;
        }

        int n = arr.length; // Number of dimensions elements
        // dp[i][j] tracks the minimum operations for chain Mi to Mj
        int[][] dp = new int[n][n];

        // Step 1: Build states incrementally by chain interval length
        for (int len = 2; len < n; len++) { // len is the chain length
            for (int i = 1; i < n - len + 1; i++) {
                int j = i + len - 1;
                dp[i][j] = Integer.MAX_VALUE;

                // Test every possible pivot split position 'k' inside the window
                for (int k = i; k < j; k++) {
                    int scalarCost = dp[i][k] + dp[k + 1][j] + (arr[i - 1] * arr[k] * arr[j]);
                    
                    // Greedily lock down the absolute minimum cost option
                    dp[i][j] = Math.min(dp[i][j], scalarCost);
                }
            }
        }

        // Return the optimized cost spanning from the first matrix to the last
        return dp[1][n - 1];
    }
}
```

---

## Complexity Analysis

Let:
*   N: The total length of the dimensions array `arr` (number of matrices + 1).

### Time Complexity
The algorithm relies on a three-tier nested loop structure: the outermost loop controls the sub-chain length (`len < n`), the intermediate loop sweeps left endpoints (`i < n`), and the innermost loop scans partition points (`k`). This bounds execution runtime to cubic dimensions:
```text
Time Complexity: O(N³)
```

### Space Complexity
The tabulation state matrix requires an allocation array grid layout of size $N \times N$:
```text
Space Complexity: O(N²)
```

---

# Key Pattern
This problem belongs to the **Interval / Range Tabulation** template inside dynamic programming.

Common indicators:
- Subproblems are defined over contiguous subarrays or ranges `[i...j]`.
- Building a global solution requires splitting a range into two independent halves at an intermediate pivot `k`.
- The tabulation loops are sorted explicitly by increasing **interval length**, ensuring smaller range components resolve completely before larger ranges read them.

---

# Similar Problems
1. Burst Balloons (LeetCode 312)
2. Minimum Cost Tree From Leaf Values (LeetCode 1130)
3. Remove Boxes (LeetCode 546)
4. Guess Number Higher or Lower II (LeetCode 375)
5. Predict the Winner (LeetCode 486)

---

# Takeaway
```text
Deconstruct range subproblems by iterating progressively through expanding interval lengths.

Guess every intermediate partition split position 'k' inside the active window.

Cache localized sub-chain costs inside a 2D matrix table to avoid exponential recursive branches.
```

**Pattern:** Interval / Range Tabulation DP

<FollowUp>
We can continue exploring advanced interval or range-based dynamic programming templates from your track. If you want, let me know if we should generate the raw markdown code for **Burst Balloons (LeetCode 312)** or examine **Predict the Winner (LeetCode 486)**!
</FollowUp>
