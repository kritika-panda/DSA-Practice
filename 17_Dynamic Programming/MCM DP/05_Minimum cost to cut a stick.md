# Minimum Cost to Cut a Stick (LeetCode 1547)

## Problem Statement

Given a wooden stick of length `n` units. The stick is labeled from `0` to `n`. You are given an integer array `cuts` where `cuts[i]` denotes a position you should perform a cut at.

You should perform the cuts in order, you can change the order of the cuts as you wish.

The cost of one cut is the length of the stick to be cut, the total cost is the sum of costs of all cuts. When you cut a stick, it will be split into two smaller sticks (i.e. the total length of any cut is the length of the stick before the cut). Please guide the minimum total cost of the cuts.

The overall runtime complexity must be strictly bounded by:

```text
O(M³)
```

*(where M is the number of elements inside the cuts array)*

---

## Example 1

### Input

```text
n = 7
cuts = [1,3,4,5]
```

### Output

```text
16
```

### Explanation

```text
- Performing cuts in order [1, 3, 4, 5] as given:
  - First cut at 1: Stick length is 7. Cost = 7. Sticks become [0,1] and.
  - Second cut at 3: Cut stick [1,7] at 3. Stick length is 6. Cost = 6. Sticks become [1,3] and.
  - Third cut at 4: Cut stick [3,7] at 4. Stick length is 4. Cost = 4. Sticks become [3,4] and.
  - Fourth cut at 5: Cut stick [4,7] at 5. Stick length is 3. Cost = 3. Sticks become [4,5] and.
  Total Cost = 7 + 6 + 4 + 3 = 20.

- An optimal strategy is ordering cuts as:
  - Cut at 3: Stick length 7. Cost = 7. Sticks:, [3,7].
  - Cut at 5: Cut [3,7] at 5. Length 4. Cost = 4. Sticks:, [5,7].
  - Cut at 1: Cut [0,3] at 1. Length 3. Cost = 3. Sticks:, [1,3].
  - Cut at 4: Cut [3,5] at 4. Length 2. Cost = 2. Sticks:, [4,5].
  Total Cost = 7 + 4 + 3 + 2 = 16.
```

---

## Example 2

### Input

```text
n = 9
cuts = [5,6,1,4,2]
```

### Output

```text
22
```

---

# Intuition

This problem can be classified as a core **Interval Dynamic Programming** challenge, bearing an exact structural mapping to *Matrix Chain Multiplication* and *Burst Balloons*.

If we try to pick the *first* cut point inside an interval, the subproblems are difficult to isolate because they depend on the external boundaries of the stick section. 

The strategy shift that simplifies the state space is to **guess which cut point `k` is the ABSOLUTE LAST cut performed in the active interval `[i...j]`**:
1. **Padded Anchors:** Start by sorting the `cuts` array and appending the stick endpoints `0` and `n` as virtual boundaries. Let this padded array be `C`. Now, any interval `[i...j]` represents a sub-stick bounded between the physical markers `C[i-1]` and `C[j+1]`.
2. **The Independent Boundary Split:** Since `k` is the last cut remaining to be made inside `[i...j]`, all cuts between `i` and `k-1` have already split the left sub-stick, and all cuts between `k+1` and `j` have already split the right sub-stick.
3. This completely isolates our two subproblems: optimizing the left range `[i...k-1]` and the right range `[k+1...j]`.
4. The cost of making this final cut `k` is precisely the length of the parent stick segment: `C[j+1] - C[i-1]`.

---

# State Definition

Let `dp[i][j]` be the minimum cost to perform all cuts from index `i` to index `j` inside the sorted, padded array `C`.
*   `i`: Left boundary index of the active cut points (1-indexed relative to padding, from `1` to `M`).
*   `j`: Right boundary index of the active cut points (1-indexed relative to padding, from `1` to `M`).

---

# Recurrence Relation

For a cut window `[i...j]` testing an intermediate pivot point `k`:

```text
dp[i][j] = min( dp[i][k-1] + dp[k+1][j] + (C[j+1] - C[i-1]) )
for all k where i <= k <= j
```

---

# Base Cases

When `i > j`, there are no cuts left to perform inside the active window interval, costing exactly `0`:
```text
dp[i][j] = 0
```

---

# Approach: Bottom-Up 2D Interval Tabulation

## Algorithm

1. Sort the original `cuts` array.
2. Build the padded array `C` of size `M + 2` where `C[0] = 0`, `C[M+1] = n`, and populate the middle with the sorted elements.
3. Initialize a 2D matrix `dp[M + 2][M + 2]` with `0`.
4. Loop through the possible interval window lengths `len` from `1` up to `M`:
   - Loop through every valid left boundary index `i` from `1` up to `M - len + 1`:
     - Calculate the right boundary index: `j = i + len - 1`.
     - Initialize `dp[i][j]` to a maximum value (`Integer.MAX_VALUE`).
     - Loop through all possible intermediate cut choices `k` from `i` up to `j`:
       - Compute the localized cost: `cost = dp[i][k-1] + dp[k+1][j] + C[j+1] - C[i-1]`.
       - Update the state minimally: `dp[i][j] = Math.min(dp[i][j], cost)`.
5. Return `dp[1][M]`.

---

# Java Solution

```java
import java.util.Arrays;

class Solution {
    public int minCost(int n, int[] cuts) {
        int m = cuts.length;
        
        // Step 1: Sort the cuts array to establish a linear grid layout
        Arrays.sort(cuts);

        // Step 2: Create a padded array embedding the stick boundaries [0] and [n]
        int[] c = new int[m + 2];
        c[0] = 0;
        c[m + 1] = n;
        System.arraycopy(cuts, 0, c, 1, m);

        // dp[i][j] tracks the minimum cost to execute all cuts from index i to j
        int[][] dp = new int[m + 2][m + 2];

        // Step 3: Build state layers incrementally by sub-interval window length
        for (int len = 1; len <= m; len++) {
            for (int i = 1; i <= m - len + 1; i++) {
                int j = i + len - 1;
                dp[i][j] = Integer.MAX_VALUE;

                // Test every possible cut 'k' acting as the last cut in the interval [i...j]
                for (int k = i; k <= j; k++) {
                    int currentCutCost = (c[j + 1] - c[i - 1]) + dp[i][k - 1] + dp[k + 1][j];
                    
                    // Greedily lock down the absolute minimum cost option
                    dp[i][j] = Math.min(dp[i][j], currentCutCost);
                }
            }
        }

        // Return the optimized cost spanning from the first cut to the last cut
        return dp[1][m];
    }
}
```

---

## Complexity Analysis

Let:
*   M: The total number of custom elements inside the `cuts` array.

### Time Complexity
Sorting the cuts array costs $O(M \log M)$ operations. The subsequent tabulation step relies on a three-tier nested loop framework: the outermost loop controls the sub-interval range length (`len`), the intermediate loop sweeps left endpoints (`i`), and the innermost loop scans pivot points (`k`). This bounds execution runtime to cubic dimensions:
```text
Time Complexity: O(M³)
```

### Space Complexity
The tabulation state matrix requires an allocation array grid layout of size $(M + 2) \times (M + 2)$:
```text
Space Complexity: O(M²)
```

---

# Key Pattern
This problem belongs to the **Interval / Range Tabulation** template inside dynamic programming.

Common indicators:
- Problems involving cutting, breaking, parsing, or merging linear structures.
- Cost of an operation depends directly on the current overall length/boundaries of the active subset partition.
- Iterative loop blocks are sorted explicitly by increasing **interval length**, ensuring smaller range components resolve completely before larger ranges read them.

---

# Similar Problems
1. Matrix Chain Multiplication
2. Burst Balloons (LeetCode 312)
3. Minimum Cost Tree From Leaf Values (LeetCode 1130)
4. Predict the Winner (LeetCode 486)

---

# Takeaway
```text
Reframe cut-ordering problems backward: guess the absolute LAST operation performed.

Pad array boundaries with the starting and terminal endpoints (0 and n) upfront.

Advance iteration tabs outward by increasing interval window lengths sequentially.
```

**Pattern:** Interval / Range Tabulation DP

<FollowUp>
Let me know which advanced interval, partitioning, or string dynamic programming template you want to configure next! We can prepare the raw markdown code for **Burst Balloons (LeetCode 312)** or dive into **Palindrome Partitioning II (LeetCode 132)**.
</FollowUp>
