# Edit Distance (LeetCode 72)

## Problem Statement

Given two strings `word1` and `word2`, return *the **minimum number of operations** required to convert `word1` to `word2`*.

You have the following three operations permitted on a word:
1. **Insert** a character
2. **Delete** a character
3. **Replace** a character

---

## Examples

### Example 1

```text
Input: word1 = "horse", word2 = "ros"
Output: 3
```

### Explanation

```text
horse -> rorse (replace 'h' with 'r')
rorse -> rose  (remove 'r')
rose  -> ros   (remove 'e')
Total operations = 3.
```

---

### Example 2

```text
Input: word1 = "intention", word2 = "execution"
Output: 5
```

### Explanation

```text
intention -> inention  (remove 't')
inention  -> enention  (replace 'i' with 'e')
enention  -> exention  (replace 'n' with 'x')
exention  -> exection  (replace 'n' with 'c')
exection  -> execution (insert 'u')
Total operations = 5.
```

---

# Intuition

This problem is a classic **2D String Alignment Dynamic Programming** challenge. We compare prefixes of `word1` and `word2` and figure out how to match them with the absolute minimal penalty cost.

When analyzing the characters `word1[i - 1]` and `word2[j - 1]`, we evaluate two branches:
1. **The Cost-Free Match:** If the two characters are identical (`word1[i - 1] == word2[j - 1]`), we don't need any new operations. The problem cost safely reduces to whatever it took to match the previous prefixes: `dp[i - 1][j - 1]`.
2. **The 3-Way Operation Split:** If the characters mismatch, we must pay a penalty of **1 operation**. We greedily evaluate which of our three valid actions results in the lowest subproblem cost:
   * **Replace:** Reduces the problem to `dp[i - 1][j - 1]` (change `word1[i-1]` to match `word2[j-1]`).
   * **Delete:** Reduces the problem to `dp[i - 1][j]` (wipe `word1[i-1]` completely out of the active window).
   * **Insert:** Reduces the problem to `dp[i][j - 1]` (add a matching character onto `word1`'s tail layout).

---

# State Definition

Let `dp[i][j]` be the minimum edit distance between the prefix `word1[0...i-1]` and `word2[0...j-1]`.
*   `i`: Length of the active prefix from `word1` (from `0` to `m`).
*   `j`: Length of the active prefix from `word2` (from `0` to `n`).

---

# Recurrence Relation

```text
If word1[i - 1] == word2[j - 1]:
    dp[i][j] = dp[i - 1][j - 1]
Else:
    dp[i][j] = 1 + min(
        dp[i - 1][j - 1], // Replace
        dp[i - 1][j],     // Delete
        dp[i][j - 1]      // Insert
    )
```

---

# Base Cases

1. **Empty `word2` (`j == 0`):** Converting a prefix of `word1` of length `i` to an empty string requires exactly `i` deletions.
   ```text
   dp[i][0] = i for all 0 <= i <= m
   ```
2. **Empty `word1` (`i == 0`):** Converting an empty string to a prefix of `word2` of length `j` requires exactly `j` insertions.
   ```text
   dp[0][j] = j for all 0 <= j <= n
   ```

---

# Approach: Bottom-Up 2D Dynamic Programming

## Algorithm

1. Initialize a 2D matrix `dp[m + 1][n + 1]`.
2. Seed the base cases: set `dp[i][0] = i` and `dp[0][j] = j` across the boundary tracks.
3. Run a two-tier nested loop over `i` (from `1` to `m`) and `j` (from `1` to `n`):
   - If `word1.charAt(i - 1) == word2.charAt(j - 1)`, set `dp[i][j] = dp[i - 1][j - 1]`.
   - Otherwise, set `dp[i][j] = 1 + Math.min(dp[i - 1][j - 1], Math.min(dp[i - 1][j], dp[i][j - 1]))`.
4. Return `dp[m][n]`.

---

# Java Solution

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length();
        int n = word2.length();

        int[][] dp = new int[m + 1][n + 1];

        // Base case setup: convert prefixes to/from an empty string
        for (int i = 0; i <= m; i++) {
            dp[i][0] = i; // i deletions needed
        }
        for (int j = 0; j <= n; j++) {
            dp[0][j] = j; // j insertions needed
        }

        // Build the state grid systematically using bottom-up tabulation
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    // Characters match -> no new operations required
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    // Characters mismatch -> pay 1 penalty and pick the optimal move
                    int replaceOp = dp[i - 1][j - 1];
                    int deleteOp  = dp[i - 1][j];
                    int insertOp  = dp[i][j - 1];

                    dp[i][j] = 1 + Math.min(replaceOp, Math.min(deleteOp, insertOp));
                }
            }
        }

        return dp[m][n];
    }
}
```

---

# Space Optimization to O(N)

Since `dp[i][j]` only reads from the current row and the previous row (`dp[i - 1]`), we can optimize the space down to a **1D array** of size `n + 1`. We use a temporary variable `pre` to preserve the top-left diagonal cell value (`dp[i-1][j-1]`) before it gets overwritten during the column sweep.

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int m = word1.length();
        int n = word2.length();
        
        int[] dp = new int[n + 1];
        for (int j = 0; j <= n; j++) {
            dp[j] = j;
        }

        for (int i = 1; i <= m; i++) {
            int pre = dp[0]; // Caches top-left diagonal dp[i-1][j-1]
            dp[0] = i;       // Base case: i deletions for empty word2
            
            for (int j = 1; j <= n; j++) {
                int temp = dp[j]; // Store old value before it gets overwritten
                
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[j] = pre;
                } else {
                    dp[j] = 1 + Math.min(pre, Math.min(dp[j], dp[j - 1]));
                }
                pre = temp; // Update the diagonal tracker for the next iteration step
            }
        }

        return dp[n];
    }
}
```

---

# Complexity Analysis

Let:
*   M: Length of string `word1`.
*   N: Length of string `word2`.

### Time Complexity
The nested loops evaluate a full tabulation matrix grid of size M × N exactly once:
```text
Time Complexity: O(M × N)
```

### Space Complexity
- Standard approach: Matrix layout space of \(O(M \times N)\).
- Optimized approach: Compressed single-row buffer space of:
```text
Space Complexity: O(N)
```

---

# Key Pattern
This problem belongs to the **String Alignment / Matrix Levenshtein Distance** dynamic programming template.

Common indicators:
- Finding the minimum cost to transform one string into another.
- Operations allow localized changes (Insert, Delete, Replace).
- 2D grid matrix states tracking subproblem prefixes concurrently.

---

# Similar Problems
1. Longest Common Subsequence (LeetCode 1143)
2. Distinct Subsequences (LeetCode 115)
3. Wildcard Matching (LeetCode 44)
4. Regular Expression Matching (LeetCode 10)
5. Delete Operation for Two Strings (LeetCode 583)

---

# Takeaway
```text
Map out character adjustments inside a Levenshtein grid matrix.

Differentiate cost-free matching cells from three-way operational penalty splits.

Cache the top-left diagonal value when optimizing the grid into a single 1D row array.
```

**Pattern:** String Alignment / Matrix Levenshtein Distance DP

<FollowUp>
We can continue mapping out advanced string alignment or pattern-matching matrices. Let me know if you want to explore the raw markdown code for **Wildcard Matching (LeetCode 44)** or check out **Regular Expression Matching (LeetCode 10)**!
</FollowUp>
