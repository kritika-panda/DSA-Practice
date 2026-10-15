# Distinct Subsequences (LeetCode 115)

## Problem Statement

Given two strings `s` and `t`, return *the number of distinct **subsequences** of `s` which equals `t`*.

The test cases are generated so that the answer fits in a 32-bit signed integer.

---

## Examples

### Example 1

```java
Input: s = "rabbbit", t = "rabbit"
Output: 3
```

### Explanation

```text
There are 3 distinct ways to form "rabbit" from "rabbbit":
1. ra_b_bbit
2. rab_b_bit
3. rabb_b_it
(where '_' represents the deleted character)
```

---

### Example 2

```java
Input: s = "babgbag", t = "bag"
Output: 5
```

### Explanation

```text
There are 5 distinct ways to form "bag" from "babgbag":
1. ba_g___
2. ba____g
3. _abg___
4. _ab___g
5. ____bag
```

---

# Intuition

This problem can be effectively solved using **2D Dynamic Programming**. We want to align characters of string `s` to match characters of string `t`.

When comparing character `s[i - 1]` with `t[j - 1]`, we have two architectural options:
1. **Always available option (Delete/Ignore character in `s`):** We can choose to ignore `s[i - 1]` and see how many times `t[0...j-1]` can be formed using only the prefix `s[0...i-2]`.
2. **Conditional option (Match characters):** If `s[i - 1] == t[j - 1]`, we can *also* choose to pair these two characters together. This means the problem reduces to finding how many times the prefix `t[0...j-2]` can be formed by the prefix `s[0...i-2]`.

---

# State Definition

Let `dp[i][j]` be the number of distinct subsequences of `s[0...i-1]` that equal `t[0...j-1]`.
*   `i`: Length of the active prefix from string `s` (from `0` to `m`).
*   `j`: Length of the active prefix from string `t` (from `0` to `n`).

---

# Recurrence Relation

```text
If s[i - 1] == t[j - 1]:
    dp[i][j] = dp[i - 1][j] (Ignore s[i-1]) + dp[i - 1][j - 1] (Match s[i-1] and t[j-1])
Else:
    dp[i][j] = dp[i - 1][j] (Ignore s[i-1])
```

---

# Base Cases

1. **Empty String `t` (`j == 0`):** An empty string can always be formed from any prefix of `s` exactly **1** way (by deleting all characters of `s`).
   ```text
   dp[i][0] = 1 for all 0 <= i <= m
   ```
2. **Empty String `s` but Non-empty `t` (`i == 0` and `j > 0`):** An empty string cannot form a non-empty string `t`.
   ```text
   dp[0][j] = 0 for all 1 <= j <= n
   ```

---

# Approach: Bottom-Up 2D Dynamic Programming

## Algorithm

1. Initialize a 2D matrix `dp[m + 1][n + 1]` with `0`.
2. Seed the base cases: set `dp[i][0] = 1` for all `0 <= i <= m`.
3. Run a two-tier nested loop over `i` (from `1` to `m`) and `j` (from `1` to `n`):
   - If `s.charAt(i - 1) == t.charAt(j - 1)`, set `dp[i][j] = dp[i - 1][j] + dp[i - 1][j - 1]`.
   - Otherwise, set `dp[i][j] = dp[i - 1][j]`.
4. Return `dp[m][n]`.

---

# Dry Run

```java
s = "bab", t = "ba"
```

### Initial State

```text
Matrix size: 4 x 3
Row 0 (s=""):   [1, 0, 0]
Row 1 (s="b"):  [1, 0, 0]
Row 2 (s="ba"): [1, 0, 0]
Row 3 (s="bab"): [1, 0, 0]
```

---

### Step 1 (i=1, s='b')
- `j=1` (t='b'): Matches! `dp[1][1] = dp[0][1] + dp[0][0] = 0 + 1 = 1`
- `j=2` (t='a'): Mismatch. `dp[1][2] = dp[0][2] = 0`

---

### Step 2 (i=2, s='a')
- `j=1` (t='b'): Mismatch. `dp[2][1] = dp[1][1] = 1`
- `j=2` (t='a'): Matches! `dp[2][2] = dp[1][2] + dp[1][1] = 0 + 1 = 1`

---

### Step 3 (i=3, s='b')
- `j=1` (t='b'): Matches! `dp[3][1] = dp[2][1] + dp[2][0] = 1 + 1 = 2`
- `j=2` (t='a'): Mismatch. `dp[3][2] = dp[2][2] = 1`

Answer: `dp[3][2] = 1`

---

# Java Solution

```java
class Solution {
    public int numDistinct(String s, String t) {
        int m = s.length();
        int n = t.length();

        // If s is shorter than t, it's impossible to form t
        if (m < n) {
            return 0;
        }

        int[][] dp = new int[m + 1][n + 1];

        // Base case: An empty target string t can always be formed 1 way
        for (int i = 0; i <= m; i++) {
            dp[i][0] = 1;
        }

        // Build states using the bottom-up 2D tabulation grid
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (s.charAt(i - 1) == t.charAt(j - 1)) {
                    // Option 1: Ignore current character s[i-1]
                    // Option 2: Match current character s[i-1] with t[j-1]
                    dp[i][j] = dp[i - 1][j] + dp[i - 1][j - 1];
                } else {
                    // Option 1 only: Must ignore s[i-1]
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }

        return dp[m][n];
    }
}
```

---

# Space Optimization to O(N)

Since `dp[i][j]` depends only on the current row and values from the *previous* row (`dp[i - 1]`), we can optimize the space from a 2D matrix down to a **1D array** of size `n + 1`. 

By running the inner loop for `j` **backward** (from `n` down to `1`), we ensure that `dp[j - 1]` represents the value from the previous row layer before it gets overwritten, matching the 2D requirement perfectly.

```java
class Solution {
    public int numDistinct(String s, String t) {
        int m = s.length();
        int n = t.length();
        if (m < n) return 0;

        int[] dp = new int[n + 1];
        dp[0] = 1; // Base case: empty t can be formed 1 way

        for (int i = 1; i <= m; i++) {
            // Loop backwards to preserve older layer dependencies safely
            for (int j = n; j >= 1; j--) {
                if (s.charAt(i - 1) == t.charAt(j - 1)) {
                    dp[j] = dp[j] + dp[j - 1];
                }
            }
        }

        return dp[n];
    }
}
```

---

# Complexity Analysis

Let:
*   M: Length of string `s`.
*   N: Length of string `t`.

### Time Complexity
The nested loop infrastructure evaluates a grid of size M × N:
```text
Time Complexity: O(M × N)
```

### Space Complexity
- Standard approach: Matrix layout space of \(O(M \times N)\).
- Optimized approach: Flat single-row array buffer space of:
```text
Space Complexity: O(N)
```

---

# Key Pattern
This problem belongs to the **String Alignment / Matrix Edit Distance** dynamic programming template.

Common indicators:
- Comparing prefixes of two strings concurrently.
- Branching based on matching vs. mismatching character conditions.
- Cumulative combination tracking over state options.

---

# Similar Problems
1. Edit Distance (LeetCode 72)
2. Longest Common Subsequence (LeetCode 1143)
3. Interleaving String (LeetCode 97)
4. Delete Operation for Two Strings (LeetCode 583)

---

# Takeaway
```text
Map string alignments onto a 2D matrix grid layout.

Always preserve the "ignore option" value from the preceding row layer.

Loop the inner layer backward to compress the space matrix safely into a 1D row buffer.
```

**Pattern:** String Alignment / Matrix Edit Distance DP
