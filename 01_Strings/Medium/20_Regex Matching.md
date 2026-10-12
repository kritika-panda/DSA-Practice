# Regular Expression Matching

## Problem Statement

Given an input string `s` and a pattern `p`, implement regular expression matching with support for `'.'` and `'*'`.

* `'.'` Matches any single character.
* `'*'` Matches zero or more of the preceding element.

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
s = "aa", p = "a*"
```

**Output**

```java
true
```

**Explanation**

`'*'` means zero or more of the preceding element, `'a'`. Therefore, `"a*"` can repeat `'a'` twice to match `"aa"`.

---

### Example 3

**Input**

```java
s = "ab", p = ".*"
```

**Output**

```java
true
```

**Explanation**

`".*"` means "zero or more of any character", which can match any string pattern like `"ab"`.

---

## Brute Force Approach

Use recursive backtracking without caching to explore both choices whenever a `'*'` is encountered: treating it as matching zero elements or matching one element and staying on the same pattern index.

### Steps

1. If the pattern `p` is empty, return whether the string `s` is also empty.
2. Check if the first character matches: `firstMatch = (!s.isEmpty() && (p.charAt(0) == s.charAt(0) || p.charAt(0) == '.'))`.
3. If the second character of `p` is `'*'`, explore two paths:
   - Path A (Zero occurrences): Recurse on `s` and `p.substring(2)`.
   - Path B (One or more occurrences): If `firstMatch` is true, recurse on `s.substring(1)` and `p`.
4. If no `'*'` is next, check `firstMatch` and recurse on `s.substring(1)` and `p.substring(1)`.

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
3. **Empty String Matches (First Row):** Patterns with a wildcard `'*'` can match an empty string by choosing zero occurrences of its preceding character. For `j` from 2 up to `m`:
   - If `p.charAt(j - 1) == '*'`, then `dp[0][j] = dp[0][j - 2]`.
4. **Grid Transitions:** For every cell `(i, j)` where `i > 0` and `j > 0`:
   - **Case 1 (No Wildcard):** If `p.charAt(j - 1) != '*'`, the current characters must match. `dp[i][j] = dp[i - 1][j - 1] && (p.charAt(j - 1) == s.charAt(i - 1) || p.charAt(j - 1) == '.')`.
   - **Case 2 (Wildcard Check):** If `p.charAt(j - 1) == '*'`, we evaluate the wildcard branch options:
     - **Sub-case A (Zero occurrences):** Look 2 cells back horizontally: `dp[i][j] = dp[i][j - 2]`.
     - **Sub-case B (One or more occurrences):** If the preceding pattern character matches the current string character (or is `'.'`), we check if the previous string match is valid: `dp[i][j] = dp[i][j] || (dp[i - 1][j] && (p.charAt(j - 2) == s.charAt(i - 1) || p.charAt(j - 2) == '.'))`.
5. Return the final cell value `dp[n][m]`.

---

## Visual Understanding

Suppose `s = "aa"`, `p = "a*"`:

- **Grid Sizing:** `3 * 3` matrix. `dp[0][0] = true`.
- **First Row Population:** `p.charAt(1) == '*'` \(\implies\) `dp[0][2] = dp[0][0] = true`. (Meaning `"a*"` can match `""` as zero occurrences).
- **Cell (1, 2) (`s = "a"`, `p = "a*"`):** Wildcard checked.
  - Zero occurrences: `dp[1][0] = false`.
  - Multiple occurrences: `p.charAt(0) ('a') == s.charAt(0) ('a')` \(\implies\) Check top neighbor `dp[0][2]` which is `true`. Result becomes `true`.
- **Cell (2, 2) (`s = "aa"`, `p = "a*"`):** Wildcard checked.
  - Multiple occurrences: `p.charAt(0) ('a') == s.charAt(1) ('a')` \(\implies\) Check top neighbor `dp[1][2]` which is `true`. Result becomes `true`.

`dp[2][2]` resolves to `true`, confirming a valid matching path.

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

The algorithm handles structural base properties dynamically inside its row initialization bounds, ensuring patterns like `a*b*c*` reduce smoothly down to empty string states:

```java
for (int j = 2; j <= m; j++) {
    if (p.charAt(j - 1) == '*') {
        dp[0][j] = dp[0][j - 2];
    }
}
```

---

## Correct Partition Condition

The internal state machine transitions handle regular text characters and wildcard sequences using clean logical routing:

```java
if (p.charAt(j - 1) == '*') {
    dp[i][j] = dp[i][j - 2];
    if (p.charAt(j - 2) == s.charAt(i - 1) || p.charAt(j - 2) == '.') {
        dp[i][j] = dp[i][j] || dp[i - 1][j];
    }
} else {
    dp[i][j] = dp[i - 1][j - 1] && (p.charAt(j - 1) == s.charAt(i - 1) || p.charAt(j - 1) == '.');
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

        // Step 1: Initialize the first row (handling patterns that can match an empty string)
        for (int j = 2; j <= m; j++) {
            if (p.charAt(j - 1) == '*') {
                dp[0][j] = dp[0][j - 2]; // Inherit match state from two steps back
            }
        }

        // Step 2: Populate the remaining inner grid cells using state transitions
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                char charP = p.charAt(j - 1);
                char charS = s.charAt(i - 1);

                // Condition A: Active pattern token is a wildcard '*'
                if (charP == '*') {
                    // Path 1: Match zero occurrences of the preceding element
                    dp[i][j] = dp[i][j - 2];
                    
                    // Path 2: Match one or more occurrences if preceding char matches
                    if (p.charAt(j - 2) == charS || p.charAt(j - 2) == '.') {
                        dp[i][j] = dp[i][j] || dp[i - 1][j];
                    }
                } 
                // Condition B: Active pattern token is a literal character or '.'
                else {
                    dp[i][j] = dp[i - 1][j - 1] && (charP == charS || charP == '.');
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

Formulating the matching rules as cell updates in a 2D matrix caches subproblem prefix results, avoiding duplicate recursion tracks and solving the regex parsing sequence in optimal quadratic space.

```text
Time  : O(n * m) stable quadratic timeline
Space : O(n * m) structural grid tabulation layout
```

---

## Similar Problems

1. Wildcard Matching (44)
2. Interleaving String (97)
3. Edit Distance (72)
4. Longest Common Subsequence (1143)
5. Word Break (139)
