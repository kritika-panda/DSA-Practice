# Longest Common Subsequence (LCS)

## Problem Statement

Given two strings `text1` and `text2`, return the length of their **Longest Common Subsequence (LCS)**.

A subsequence is a sequence that can be derived from a string by deleting some or no characters without changing the relative order of the remaining characters.

---

## Example 1

```text
Input:
text1 = "abcde"
text2 = "ace"

Output:
3
```

### LCS

```text
ace
```

Length:

```text
3
```

---

## Example 2

```text
Input:
text1 = "abc"
text2 = "abc"

Output:
3
```

LCS:

```text
abc
```

---

## Example 3

```text
Input:
text1 = "abc"
text2 = "def"

Output:
0
```

No common subsequence exists.

---

# Intuition

For every character, we have two choices:

```text
Take it
OR
Skip it
```

Suppose:

```text
text1 = abcde
text2 = ace
```

Compare characters from the end.

---

### If Characters Match

```text
e == e
```

Then this character must be part of LCS.

```java
1 + LCS(i-1, j-1)
```

---

### If Characters Do Not Match

```text
d != c
```

We try both possibilities:

```java
LCS(i-1, j)
```

Skip from string 1

OR

```java
LCS(i, j-1)
```

Skip from string 2

Take the maximum.

---

# Recursive Relation

## Match

```java
if(s1.charAt(i) == s2.charAt(j))
    return 1 + f(i-1,j-1);
```

---

## Not Match

```java
return Math.max(
        f(i-1,j),
        f(i,j-1)
);
```

---

# Memoization Solution

```java
import java.util.Arrays;

public class Main {

    static int lcs(int i, int j,
                   String s1,
                   String s2,
                   int[][] dp) {

        if (i < 0 || j < 0)
            return 0;

        if (dp[i][j] != -1)
            return dp[i][j];

        if (s1.charAt(i) == s2.charAt(j)) {

            return dp[i][j] =
                    1 + lcs(
                            i - 1,
                            j - 1,
                            s1,
                            s2,
                            dp
                    );
        }

        return dp[i][j] =
                Math.max(
                        lcs(i - 1, j, s1, s2, dp),
                        lcs(i, j - 1, s1, s2, dp)
                );
    }

    public static int longestCommonSubsequence(
            String s1,
            String s2) {

        int n = s1.length();
        int m = s2.length();

        int[][] dp = new int[n][m];

        for (int[] row : dp)
            Arrays.fill(row, -1);

        return lcs(
                n - 1,
                m - 1,
                s1,
                s2,
                dp
        );
    }
}
```

---

# Tabulation Solution

## DP Definition

```java
dp[i][j]
```

represents:

```text
LCS length between

s1[0...i-1]
and
s2[0...j-1]
```

---

## Base Case

```java
dp[0][j] = 0
dp[i][0] = 0
```

Empty string has no common subsequence.

---

## Transition

### Match

```java
dp[i][j] =
1 + dp[i-1][j-1]
```

---

### Not Match

```java
dp[i][j] =
Math.max(
    dp[i-1][j],
    dp[i][j-1]
)
```

---

# Java Code (Tabulation)

```java
public class Main {

    public static int longestCommonSubsequence(
            String s1,
            String s2) {

        int n = s1.length();
        int m = s2.length();

        int[][] dp = new int[n + 1][m + 1];

        for (int i = 1; i <= n; i++) {

            for (int j = 1; j <= m; j++) {

                if (s1.charAt(i - 1)
                        == s2.charAt(j - 1)) {

                    dp[i][j] =
                            1 + dp[i - 1][j - 1];
                } else {

                    dp[i][j] =
                            Math.max(
                                    dp[i - 1][j],
                                    dp[i][j - 1]
                            );
                }
            }
        }

        return dp[n][m];
    }
}
```

---

# Dry Run

## Input

```text
s1 = "abcde"
s2 = "ace"
```

---

## DP Table

```text
      "" a c e
   ----------------
"" | 0  0 0 0
a  | 0  1 1 1
b  | 0  1 1 1
c  | 0  1 2 2
d  | 0  1 2 2
e  | 0  1 2 3
```

Final Answer:

```text
dp[5][3] = 3
```

---

# Space Optimization

Observe:

```java
dp[i][j]
```

depends only on:

```text
Previous Row
Current Row
```

So we can use:

```java
int[] prev
int[] curr
```

instead of:

```java
int[][] dp
```

---

# Java Code (Space Optimized)

```java
public class Main {

    public static int longestCommonSubsequence(
            String s1,
            String s2) {

        int n = s1.length();
        int m = s2.length();

        int[] prev = new int[m + 1];

        for (int i = 1; i <= n; i++) {

            int[] curr = new int[m + 1];

            for (int j = 1; j <= m; j++) {

                if (s1.charAt(i - 1)
                        == s2.charAt(j - 1)) {

                    curr[j] =
                            1 + prev[j - 1];
                } else {

                    curr[j] =
                            Math.max(
                                    prev[j],
                                    curr[j - 1]
                            );
                }
            }

            prev = curr;
        }

        return prev[m];
    }
}
```

---

# Time Complexity

```text
O(N × M)
```

Where:

```text
N = length of text1
M = length of text2
```

---

# Space Complexity

### Tabulation

```text
O(N × M)
```

### Space Optimized

```text
O(M)
```

---

# Visualization

```text
abcde
| | |
a c e
```

Common subsequence:

```text
ace
```

LCS Length:

```text
3
```

---

# Related Problems (LCS Pattern)

```text
LCS Pattern
│
├── Longest Common Subsequence ✅
├── Print LCS
├── Longest Common Substring
├── Shortest Common Supersequence
├── Minimum Insertions & Deletions
├── Distinct Subsequences
├── Edit Distance
├── Wildcard Matching
└── Palindromic Subsequence
```

### Interview Trick

Whenever you see:

```text
Two Strings
Longest/Common/Match/Subsequence
```

Think immediately:

```text
2D DP

dp[i][j]
=
Answer using prefixes of both strings
```

LCS is the foundation of nearly all string DP problems.
