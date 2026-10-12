# Interleaving String

## Problem Statement

Given strings `s1`, `s2`, and `s3`, find whether `s3` is formed by an **interleaving** of `s1` and `s2`.

An **interleaving** of two strings `s` and `t` is a configuration where they are divided into non-empty substrings such that:
* `s = s1 + s2 + ... + sn`
* `t = t1 + t2 + ... + tm`
* `|n - m| <= 1`
* The interleaving is `s1 + t1 + s2 + t2 + s3 + t3 + ...` or `t1 + s1 + t2 + s2 + t3 + s3 + ...`

Note that `a + b` is the concatenation of strings `a` and `b`. The characters within each string must preserve their original relative order.

The overall runtime complexity must be:

```text
O(n * m)
```

*(where n is the length of string s1 and m is the length of string s2)*

---

## Examples

### Example 1

**Input**

```java
s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
```

**Output**

```java
true
```

**Explanation**

One way to obtain `s3` by interleaving `s1` and `s2` is:
- Split `s1` into: `"aa"`, `"bc"`, `"c"`
- Split `s2` into: `"dbbc"`, `"a"`
- Interleave: `"aa"` + `"dbbc"` + `"bc"` + `"a"` + `"c"` = `"aadbbcbcac"`.

---

### Example 2

**Input**

```java
s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc"
```

**Output**

```java
false
```

**Explanation**

It is impossible to get `s3` from interleaving `s1` and `s2` without breaking relative character ordering contracts.

---

### Example 3

**Input**

```java
s1 = "", s2 = "", s3 = ""
```

**Output**

```java
true
```

---

## Brute Force Approach

Use recursive backtracking to test choices at each step, matching the current character of `s3` with either `s1` or `s2` whenever possible.

### Steps

1. Start with three pointers: `i = 0` (for `s1`), `j = 0` (for `s2`), and `k = 0` (for `s3`).
2. If `k == s3.length()`, check if `i == s1.length()` and `j == s2.length()`.
3. If `s1.charAt(i) == s3.charAt(k)`, recursively branch forward by incrementing `i` and `k`.
4. If `s2.charAt(j) == s3.charAt(k)`, recursively branch forward by incrementing `j` and `k`.
5. Return `true` if either branch achieves full string completion.

### Complexity

```text
Time Complexity: O(2^(n + m)) // Exponential branching depth
Space Complexity: O(n + m) for the recursion call stack depth
```

Without caching, this approach repeatedly evaluates overlapping subproblem combinations, resulting in severe Time Limit Exceeded (TLE) timeouts. We can optimize this using **2D Dynamic Programming (Tabulation)**.

---

# Optimal Approach: 2D Dynamic Programming

## Key Idea

We can solve this problem in **quadratic time** using a 2D boolean grid `dp[i][j]`, which represents whether `s3.substring(0, i + j)` can be formed by interleaving `s1.substring(0, i)` and `s2.substring(0, j)`.

1. **Length Validation Guard:** If `s1.length() + s2.length() != s3.length()`, it is mathematically impossible to interleave them. Return `false` instantly.
2. **DP Grid Setup:** Initialize a boolean grid `dp` of size `(n + 1) * (m + 1)`.
3. **Base Case Configuration:** `dp[0][0] = true` because an empty `s3` can be formed from an empty `s1` and an empty `s2`.
4. **First Row Population (s2 only):** Loop through the first row (`i = 0`). `dp[0][j]` is true if the previous cell `dp[0][j - 1]` was true and `s2.charAt(j - 1) == s3.charAt(j - 1)`.
5. **First Column Population (s1 only):** Loop through the first column (`j = 0`). `dp[i][0]` is true if `dp[i - 1][0]` was true and `s1.charAt(i - 1) == s3.charAt(i - 1)`.
6. **Inner Grid Transitions:** For every cell `(i, j)` where `i > 0` and `j > 0`:
   - `dp[i][j]` becomes `true` if it can continue a matching streak from the top neighbor (`dp[i - 1][j]` is true AND `s1.charAt(i - 1) == s3.charAt(i + j - 1)`).
   - OR it can continue a matching streak from the left neighbor (`dp[i][j - 1]` is true AND `s2.charAt(j - 1) == s3.charAt(i + j - 1)`).
7. Return the final cell value `dp[n][m]`.

---

## Visual Understanding

Suppose `s1 = "a"`, `s2 = "b"`, `s3 = "ab"`:

- **Grid Sizing:** `2 * 2` matrix. `dp[0][0] = true`.
- **First Row (`i = 0`):** `dp[0][1]` checks if `s2` can form `s3`. `s2.charAt(0) ('b') != s3.charAt(0) ('a')` \(\implies\) `false`.
- **First Column (`j = 0`):** `dp[1][0]` checks if `s1` can form `s3`. `s1.charAt(0) ('a') == s3.charAt(0) ('a')` \(\implies\) `true`.
- **Cell (1, 1):** Validates interleaving matching index `s3.charAt(1) ('b')`.
  - From top neighbor (`dp[0][1]`): false.
  - From left neighbor (`dp[1][0]`): True, and `s2.charAt(0) ('b') == s3.charAt(1) ('b')` \(\implies\) `true`.

`dp[1][1]` resolves to `true`, confirming a valid interleaving path.

---

## Partition Variables

Let:

```java
int n = s1.length();
int m = s2.length();
boolean[][] dp = new boolean[n + 1][m + 1];
```

---

### Border Elements

The preliminary grid size safety guard blocks mismatched lengths right at the gate:

```java
if (s1.length() + s2.length() != s3.length()) {
    return false;
}
```

---

## Correct Partition Condition

The internal transition states combine historical matching paths using clean conditional boolean routing gates:

```java
dp[i][j] = (dp[i - 1][j] && s1.charAt(i - 1) == s3.charAt(i + j - 1))
        || (dp[i][j - 1] && s2.charAt(j - 1) == s3.charAt(i + j - 1));
```

---

## Java Solution

```java
class Solution {

    public boolean isInterleave(String s1, String s2, String s3) {
        // Fast-fail: Character counts must match exactly to allow interleaving
        if (s1 == null || s2 == null || s3 == null || s1.length() + s2.length() != s3.length()) {
            return false;
        }

        int n = s1.length();
        int m = s2.length();

        // dp[i][j] tracks if s3[0...i+j-1] is formed by interleaving s1[0...i-1] and s2[0...j-1]
        boolean[][] dp = new boolean[n + 1][m + 1];
        
        // Base case: Empty strings interleave to form an empty string
        dp[0][0] = true;

        // Step 1: Initialize the first column (matching s1 exclusively against s3)
        for (int i = 1; i <= n; i++) {
            dp[i][0] = dp[i - 1][0] && s1.charAt(i - 1) == s3.charAt(i - 1);
        }

        // Step 2: Initialize the first row (matching s2 exclusively against s3)
        for (int j = 1; j <= m; j++) {
            dp[0][j] = dp[0][j - 1] && s2.charAt(j - 1) == s3.charAt(j - 1);
        }

        // Step 3: Populate the remaining inner grid cells using state transitions
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                char charS3 = s3.charAt(i + j - 1);
                
                // Cell becomes true if it satisfies matching extensions from top or left paths
                dp[i][j] = (dp[i - 1][j] && s1.charAt(i - 1) == charS3)
                        || (dp[i][j - 1] && s2.charAt(j - 1) == charS3);
            }
        }

        return dp[n][m];
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **2D Matrix Tabulation** | **O(n * m)** | **O(n * m)** | Uses a nested coordinate loop of dimensions `n` by `m`. The boolean array stores calculation frames matching those grid sizes explicitly. |

---

## Key Insight

Formulating the interleaving alignment paths as a grid transition dependencies system caches historical prefix truths, cutting exponential backtracking branches down to an optimal quadratic workspace.

```text
Time  : O(n * m) stable quadratic timeline
Space : O(n * m) structural grid tabulation layout
```

---

## Similar Problems

1. Longest Common Subsequence (1143)
2. Edit Distance (72)
3. Word Break (139)
4. Is Subsequence (392)
5. Wildcard Matching (44)
