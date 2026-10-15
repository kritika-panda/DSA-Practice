# Palindrome Partitioning II (LeetCode 132)

## Problem Statement

Given a string `s`, partition `s` such that every substring of the partition is a **palindrome**.

Return *the **minimum cuts** needed for a palindrome partitioning of `s`*.

---

## Examples

### Example 1

```text
Input: s = "aab"
Output: 1
```

### Explanation

```text
The palindrome partitioning ["aa","b"] could be produced using 1 cut.
```

---

### Example 2

```text
Input: s = "a"
Output: 0
```

---

### Example 3

```text
Input: s = "ab"
Output: 1
```

---

# Intuition

This problem can be cleanly solved by combining two classic dynamic programming paradigms:
1. **2D Palindrome Substring Precomputation:** To verify if a substring `s[j...i]` is a palindrome in $O(1)$ constant time, we can precompute a 2D boolean lookup table `isPal[j][i]`.
2. **1D Continuous Array Partitioning:** We solve the minimum cut requirements incrementally: **What is the minimum number of cuts needed to validly partition the prefix string up to index `i`?**

To find the minimum cuts for `dp[i]` (the prefix of length `i + 1` from index `0` to `i`):
- If the entire substring `s[0...i]` is already a palindrome (`isPal[0][i] == true`), then **0 cuts** are needed.
- Otherwise, we look back at all possible split positions `j` (where `1 <= j <= i`). If the remaining trailing chunk `s[j...i]` is a palindrome (`isPal[j][i] == true`), we can cut the string right before index `j`. The total cuts would be `1 + dp[j - 1]`.
- We greedily minimize this value across all valid choices of `j`.

---

# State Definition

Let:
*   `isPal[j][i]`: A boolean matrix tracking whether the substring `s[j...i]` is a valid palindrome.
*   `dp[i]`: An integer tracking the minimum cuts needed to partition the prefix string `s[0...i]`.

---

# Recurrence Relation

For a prefix ending at index `i`:

```text
If isPal[0][i] == true:
    dp[i] = 0
Else:
    dp[i] = min( 1 + dp[j - 1] ) for all j where 1 <= j <= i AND isPal[j][i] == true
```

---

# Approach: Double DP Tabulation (2D + 1D combo)

## Algorithm

1. Initialize a 2D boolean array `isPal[n][n]` with `false`.
2. Populate `isPal` matrix by expanding interval window lengths from `1` up to `n`:
   - A single character is always a palindrome (`isPal[i][i] = true`).
   - Two adjacent identical characters form a palindrome (`isPal[i][i+1] = true` if characters match).
   - For larger strings, `isPal[j][i] = true` if `s[j] == s[i]` AND `isPal[j+1][i-1] == true`.
3. Initialize a 1D integer array `dp[n]`. For each index `i`, set its worst-case initial boundary to its max cuts value: `dp[i] = i`.
4. Loop through `i` from `0` up to `n - 1`:
   - If `isPal[0][i]` is `true`, `dp[i] = 0`.
   - Otherwise, loop through all potential partition cuts `j` from `1` up to `i`:
     - If `isPal[j][i]` is `true`, update: `dp[i] = Math.min(dp[i], 1 + dp[j - 1])`.
5. Return `dp[n - 1]`.

---

# Java Solution

```java
class Solution {
    public int minCut(String s) {
        if (s == null || s.length() <= 1) {
            return 0;
        }

        int n = s.length();
        boolean[][] isPal = new boolean[n][n];

        // Step 1: Precompute the 2D palindrome lookup matrix table
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (s.charAt(i) == s.charAt(j)) {
                    // Substrings of length 1, 2 or 3 only require matching endpoints
                    if (j - i <= 2) {
                        isPal[i][j] = true;
                    } else {
                        // Inner content must also be palindromic
                        isPal[i][j] = isPal[i + 1][j - 1];
                    }
                }
            }
        }

        // dp[i] tracks the minimum cuts needed for the prefix s[0...i]
        int[] dp = new int[n];

        // Step 2: Build the 1D partition array minimums sequentially
        for (int i = 0; i < n; i++) {
            // Worst-case baseline scenario: cut every single letter separately
            dp[i] = i; 

            if (isPal[0][i]) {
                // No cuts required if the entire prefix is already a palindrome
                dp[i] = 0;
            } else {
                // Test every possible cut boundary point 'j'
                for (int j = 1; j <= i; j++) {
                    if (isPal[j][i]) {
                        dp[i] = Math.min(dp[i], 1 + dp[j - 1]);
                    }
                }
            }
        }

        return dp[n - 1];
    }
}
```

---

## Complexity Analysis

Let:
*   N: The total length of the string `s`.

### Time Complexity
Precomputing the 2D palindrome matrix takes a two-tier nested loop over $N$ items ($O(N^2)$). The subsequent minimum-cut 1D tabulation also runs a two-tier nested lookup over $N$ items ($O(N^2)$). This keeps the runtime strictly bounded quadratically:
```text
Time Complexity: O(N²)
```

### Space Complexity
The auxiliary storage footprint is dominated by the 2D boolean `isPal` matrix layout of size $N \times N$:
```text
Space Complexity: O(N²)
```

---

# Key Pattern
This problem belongs to the **1D Continuous Partitioning with 2D Structural Constraint Precomputation** template.

Common indicators:
- Slicing a sequence into continuous segments where each chunk must fulfill an explicit verification constraint (e.g., palindrome).
- Evaluating a cost function over a 1D timeline where current indices look back across historical cut positions `j`.
- Combining two decoupled DP tracking grids to manage operations cleanly inside a $O(N^2)$ limit.

---

# Similar Problems
1. Palindrome Partitioning I (Print all path combinations - LeetCode 131)
2. Word Break (LeetCode 139)
3. Decode Ways (LeetCode 91)
4. Longest Palindromic Substring (LeetCode 5)

---

# Takeaway
```text
Decouple structural pattern validation from the chronological optimization pass.

Precalculate 2D matrix lookups to eliminate inner slice traversal overhead.

Reset cut totals to 0 immediately if the entire active prefix satisfies the target condition.
```

**Pattern:** 1D Partitioning with 2D Structural Constraint Precomputation DP

<FollowUp>
We can continue reviewing high-frequency dynamic programming templates from your track. If you'd like, let me know if we should generate the raw markdown for **Word Break (LeetCode 139)** or explore the path reconstruction mechanics of **Palindrome Partitioning I (LeetCode 131)**!
</FollowUp>
