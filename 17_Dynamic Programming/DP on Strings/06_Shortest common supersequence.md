# Shortest Common Supersequence (LeetCode 1092)

## Problem Statement

Given two strings `str1` and `str2`, return *the **shortest string** that has both `str1` and `str2` as subsequences*. If there are multiple valid shortest common supersequences, return **any** of them.

A string `s` is a subsequence of string `t` if deleting some number of characters from `t` (possibly zero) results in the string `s`.

---

## Example 1

```text
Input:
str1 = "abac", str2 = "cab"

Output:
"cabac"
```

### Explanation

```text
"cabac" contains "abac" (c_a_b_a_c) and "cab" (_c_a_b_ac) as subsequences.
It is the shortest possible common supersequence with a length of 5.
```

---

## Example 2

```text
Input:
str1 = "aaaaaaaa", str2 = "aaaaaaaa"

Output:
"aaaaaaaa"
```

---

# Intuition

Instead of building a supersequence completely from scratch, think:

```text
What is the largest block of characters that both strings 
already share in the exact same chronological order?
```

That is exactly:

```text
Longest Common Subsequence (LCS)
```

Characters that belong to the LCS are redundant across both strings. To make the supersequence as short as possible, we should **only include LCS characters once**, while printing all other non-matching unique elements from both strings around them.

---

# Key Observation

The length of the shortest common supersequence follows this exact relationship:

```text
Length of Shortest Common Supersequence
= 
Length of str1 + Length of str2 - Length of LCS(str1, str2)
```

To reconstruct the actual string, we can compute the standard 2D LCS tabulation matrix first, and then backtrack from the bottom-right corner `dp[n][m]` up to `dp[0][0]` to construct our result string.

---

## Example

```text
str1 = "abac", str2 = "cab"
```

Lengths:

```text
n = 4, m = 3
```

LCS:

```text
ab
```

Length:

```text
2
```

Therefore:

```text
Shortest Supersequence Length = 4 + 3 - 2 = 5
```

---

# Approach

### Step 1

Build a standard bottom-up 2D tabulation grid `dp[n+1][m+1]` to solve for `LCS(str1, str2)`.

---

### Step 2

Initialize two pointers `i = n` and `j = m` at the bottom-right corner of the matrix. Track backward up the cells using a `StringBuilder`:
- If characters match (`str1[i-1] == str2[j-1]`), this character is part of the common subsequence. Append it **once** and decrement both pointers (`i--, j--`).
- If they don't match, look at the adjacent DP cells (`dp[i-1][j]` vs `dp[i][j-1]`) to determine which direction provided the maximum path score:
  - If `dp[i-1][j] >= dp[i][j-1]`, append `str1[i-1]` and move up (`i--`).
  - Otherwise, append `str2[j-1]` and move left (`j--`).

---

### Step 3

If one string is completely exhausted before the other (`i > 0` or `j > 0`), append all remaining characters from the leftover string sequentially. Reverse the compiled buffer to output the forward chronological string path.

---

# Java Code

```java
public class Solution {

    public String shortestCommonSupersequence(String str1, String str2) {
        int n = str1.length();
        int m = str2.length();

        int[][] dp = new int[n + 1][m + 1];

        // Step 1: Compute the standard 2D LCS tabulation grid matrix
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (str1.charAt(i - 1) == str2.charAt(j - 1)) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        StringBuilder sb = new StringBuilder();
        int i = n, j = m;

        // Step 2: Backtrack from the bottom-right cell corner up to dp[0][0]
        while (i > 0 && j > 0) {
            if (str1.charAt(i - 1) == str2.charAt(j - 1)) {
                // Characters match -> Include the common character once
                sb.append(str1.charAt(i - 1));
                i--;
                j--;
            } else if (dp[i - 1][j] >= dp[i][j - 1]) {
                // Character from str1 is not part of the active LCS step
                sb.append(str1.charAt(i - 1));
                i--;
            } else {
                // Character from str2 is not part of the active LCS step
                sb.append(str2.charAt(j - 1));
                j--;
            }
        }

        // Step 3: Append any leftover non-matching characters from remaining prefixes
        while (i > 0) {
            sb.append(str1.charAt(i - 1));
            i--;
        }
        while (j > 0) {
            sb.append(str2.charAt(j - 1));
            j--;
        }

        // Invert the backward collected sequence buffer to form chronological order
        return sb.reverse().toString();
    }

    public static void main(String[] args) {
        Solution solver = new Solution();
        String str1 = "abac";
        String str2 = "cab";
        System.out.println(solver.shortestCommonSupersequence(str1, str2));
    }
}
```

---

# Dry Run

## Input

```text
str1 = "abac"
str2 = "cab"
```

---

### Matrix Backtracking Trace

```text
Start at i = 4 ('c'), j = 3 ('b'). Mismatch.
- dp[3][3] (LCS of "aba", "cab" = 2) vs dp[4][2] (LCS of "abac", "ca" = 1)
- dp[3][3] >= dp[4][2] -> Append str1[3] ('c'). Dec i to 3. sb = "c"

At i = 3 ('a'), j = 3 ('b'). Mismatch.
- dp[2][3] ("ab" vs "cab" = 2) vs dp[3][2] ("aba" vs "ca" = 1)
- dp[2][3] >= dp[3][2] -> Append str1[2] ('a'). Dec i to 2. sb = "ca"

At i = 2 ('b'), j = 3 ('b'). MATCH FOUND!
- Append 'b'. Dec i to 1, j to 2. sb = "cab"

At i = 1 ('a'), j = 2 ('a'). MATCH FOUND!
- Append 'a'. Dec i to 0, j to 1. sb = "caba"

Loop breaks since i == 0. Remaining j = 1 processes leftover str2[0] ('c').
- Append 'c'. sb = "cabac"

Reverse: "cabac"
```

Answer:

```text
"cabac"
```

---

# Visualization

```text
str1:     a  b  a  c
          │  │  │  │
LCS:      _  a  b  _
          │  │  │  │
str2:  c  _  a  b  _

Supersequence Matrix Layout:  c -> a -> b -> a -> c
```

---

# Complexity Analysis

### Time Complexity

Matrix Tabulation and Backtracking Path Traversal:

```text
O(N * M)
```

---

### Space Complexity

2D DP Table Memory Allocation Grid:

```text
O(N * M)
```

---

# Related Problems

```text
LCS DP Pattern
│
├── Longest Common Subsequence
├── Shortest Common Supersequence ✅
├── Minimum Insertions or Deletions to Convert String
├── Edit Distance
├── Delete Operation for Two Strings
└── Wildcard Matching
```

---

# Important Interview Trick

Remember these formulas:

```text
Shortest Supersequence Length = n + m - LCS(str1, str2)
```

```text
When backtracking through the grid to reconstruct strings:
- Matching characters: Consume both string indices and append once.
- Mismatching characters: Consume the index from the path offering the maximum DP cell value.
```

<FollowUp>
Let me know if you want to target another advanced string optimization or grid matrix challenge. We can check out **Edit Distance (LeetCode 72)** to see multi-operation branching weights, or dive into **Wildcard Matching (LeetCode 44)** next!
</FollowUp>
