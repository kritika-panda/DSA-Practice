# Wildcard Matching

## Problem Statement

Given an input string `s` and a pattern `p`, implement wildcard pattern matching with support for `'?'` and `'*'`.

* `'?'` Matches any single character.
* `'*'` Matches any sequence of characters (including the empty sequence).

The matching should cover the **entire** input string (not partial).

The overall runtime complexity must be:

```text
O(n * m)
```

*(where n is the length of string s and m is the length of pattern p)*

---

## Examples

### Example 1

**Input**

```java
s = "aa", p = "a"
```

**Output**

```java
false
```

**Explanation**

`"a"` does not match the entire string `"aa"`.

---

### Example 2

**Input**

```java
s = "aa", p = "*"
```

**Output**

```java
true
```

**Explanation**

`'*'` matches any sequence of characters, including `"aa"`.

---

### Example 3

**Input**

```java
s = "cb", p = "?a"
```

**Output**

```java
false
```

**Explanation**

`'?'` matches `'c'`, but the second character `'a'` mismatches `'b'`.

---

## Brute Force Approach

Use recursive backtracking without caching to explore choices whenever a `'*'` is encountered: treating it as matching zero elements or matching one element from `s` and staying on the same pattern index.

### Steps

1. If the pattern `p` is empty, return whether the string `s` is also empty.
2. If `p` starts with `'*'`, branch into two recursive choices:
   - Path A: Skip the `'*'` in the pattern and match 0 characters from `s` (`isMatch(s, p.substring(1))`).
   - Path B: If `s` is not empty, consume 1 character from `s` and keep the `'*'` (`isMatch(s.substring(1), p)`).
3. If the first character matches (`!s.isEmpty() && (p.charAt(0) == s.charAt(0) || p.charAt(0) == '?')`), advance both pointers: `isMatch(s.substring(1), p.substring(1))`.
4. If none of these match, return `false`.

### Complexity

```text
Time Complexity: O(2^(n + m)) // Exponential worst-case branching depth
Space Complexity: O(n + m) for the recursion call stack depth
```

Without caching, this approach repeatedly evaluates overlapping subproblem suffix combinations, resulting in severe Time Limit Exceeded (TLE) timeouts. We can optimize this using **2D Dynamic Programming (Tabulation)**.

---

# Optimal Approach: 2D Dynamic Programming (Tabulation)

## Key Idea

We can solve this problem in **quadratic time** using a 2D boolean grid `dp[i][j]`, which represents whether `s.substring(0, i)` matches `p.substring(0, j)`.

1. **DP Grid Setup:** Initialize a boolean grid `dp` of size `(n + 1) * (m + 1)`.
2. **Base Case Configuration:** `dp[0][0] = true` because an empty pattern matches an empty string.
3. **Empty String Matches (First Row):** An empty string can match a pattern only if the pattern consists exclusively of `'*'`. For `j` from 1 up to `m`:
   - If `p.charAt(j - 1) == '*'`, then `dp[0][j] = dp[0][j - 1]`.
4. **Grid Transitions:** For every cell `(i, j)` where `i > 0` and `j > 0`:
   - **Case 1 (Literal or '?'):** If `p.charAt(j - 1) == s.charAt(i - 1) || p.charAt(j - 1) == '?'`, then the current character position matches. The result depends on the diagonal subproblem: `dp[i][j] = dp[i - 1][j - 1]`.
   - **Case 2 (Wildcard '*'):** If `p.charAt(j - 1) == '*'`, we evaluate the wildcard branch options:
     - **Path A (Zero characters matched):** Treat `'*'` as empty. Look 1 cell back horizontally: `dp[i][j - 1]`.
     - **Path B (One or more characters matched):** Consume the current character of `s`. Look 1 cell up vertically: `dp[i - 1][j]`.
     - Combined logic: `dp[i][j] = dp[i][j - 1] || dp[i - 1][j]`.
5. Return the final cell value `dp[n][m]`.

---

## Visual Understanding

Suppose `s = "ad"`, `p = "*d"`:

- **Grid Sizing:** `3 * 3` matrix. `dp[0][0] = true`.
- **First Row Population:** `p.charAt(0) == '*'` \(\implies\) `dp[0][1] = dp[0][0] = true`. (Meaning `"*"` can match `""`).
- **Cell (1, 1) (`s = "a"`, `p = "*"`):** Wildcard checked.
  - `dp[1][1] = dp[1][0] (false) || dp[0][1] (true) = true`. (Meaning `"*"` matches `"a"`).
- **Cell (2, 1) (`s = "ad"`, `p = "*"`):** Wildcard checked.
  - `dp[2][1] = dp[2][0] (false) || dp[1][1] (true) = true`. (Meaning `"*"` matches `"ad"`).
- **Cell (2, 2) (`s = "ad"`, `p = "*d"`):** `p.charAt(1) ('d') == s.charAt(1) ('d')`.
  - Matches! Inherit diagonally from `dp[1][1]` which is `true`. `dp[2][2] = true`.

`dp` resolves to `true`, confirming a valid matching path.

---

## Partition Variables

Let:

```java
int n = s.length();
int m = p.length();
boolean[][] dp = new boolean[n + 1][m + 1];
```

---

### Border Elements

The algorithm handles structural base properties dynamically inside its row initialization bounds, ensuring patterns starting with `***` reduce smoothly down to empty string states:

```java
for (int j = 1; j <= m; j++) {
    if (p.charAt(j - 1) == '*') {
        dp[0][j] = dp[0][j - 1];
    }
}
```

---

## Correct Partition Condition

The internal state machine transitions handle regular text characters and wildcard sequences using clean logical routing:

```java
if (p.charAt(j - 1) == '*') {
    dp[i][j] = dp[i][j - 1] || dp[i - 1][j];
} else if (p.charAt(j - 1) == '?' || p.charAt(j - 1) == s.charAt(i - 1)) {
    dp[i][j] = dp[i - 1][j - 1];
}
```

---

## Java Solution

```java
class Solution {

    public boolean isMatch(String s, String p) {
        if (s == null || p == null) {
            return false;
        }

        int n = s.length();
        int m = p.length();

        // dp[i][j] tracks if s[0...i-1] matches pattern p[0...j-1]
        boolean[][] dp = new boolean[n + 1][m + 1];
        
        // Base case: Empty string matches empty pattern
        dp[0][0] = true;

        // Step 1: Initialize the first row (handling patterns starting with '*')
        for (int j = 1; j <= m; j++) {
            if (p.charAt(j - 1) == '*') {
                dp[0][j] = dp[0][j - 1]; // Inherit match state from one step back
            }
        }

        // Step 2: Populate the remaining inner grid cells using state transitions
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                char charP = p.charAt(j - 1);

                // Condition A: Active pattern token is a wildcard '*'
                if (charP == '*') {
                    // dp[i][j-1] represents matching 0 characters
                    // dp[i-1][j] represents matching 1 or more characters
                    dp[i][j] = dp[i][j - 1] || dp[i - 1][j];
                } 
                // Condition B: Active pattern token is a literal character or '?'
                else if (charP == '?' || charP == s.charAt(i - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
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

Formulating the matching rules as cell updates in a 2D matrix caches subproblem prefix results, avoiding duplicate recursion tracks and solving the wildcard parsing sequence in optimal quadratic space.

```text
Time  : O(n * m) stable quadratic timeline
Space : O(n * m) structural grid tabulation layout
```

---

## Similar Problems

1. Regular Expression Matching (10)
2. Interleaving String (97)
3. Edit Distance (72)
4. Longest Common Subsequence (1143)
5. Word Break (139)
