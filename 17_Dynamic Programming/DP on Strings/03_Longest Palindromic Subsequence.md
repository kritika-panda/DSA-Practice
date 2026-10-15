# Longest Palindromic Subsequence (LPS)

## Problem Statement

Given a string `s`, return the length of the **Longest Palindromic Subsequence**.

A subsequence can be formed by deleting some or no characters without changing the relative order of the remaining characters.

A palindrome reads the same forward and backward.

---

## Example 1

```text
Input:
s = "bbbab"

Output:
4
```

### Longest Palindromic Subsequence

```text
bbbb
```

Length:

```text
4
```

---

## Example 2

```text
Input:
s = "cbbd"

Output:
2
```

### Longest Palindromic Subsequence

```text
bb
```

Length:

```text
2
```

---

# Intuition

A palindrome remains unchanged when reversed.

Example:

```text
agbcba
```

Reverse:

```text
abcbga
```

Notice:

```text
abcba
```

appears in both strings.

Thus:

```text
Longest Palindromic Subsequence
=
Longest Common Subsequence
between
String and Reverse(String)
```

---

# Key Observation

Let:

```text
s = "bbbab"
```

Reverse:

```text
rev = "babbb"
```

LCS between:

```text
bbbab
babbb
```

gives:

```text
bbbb
```

Length:

```text
4
```

which is exactly the LPS.

---

# Reduction to LCS

```java
LPS(s)
=
LCS(
    s,
    reverse(s)
)
```

Thus we can directly apply the LCS algorithm.

---

# DP Definition

```java
dp[i][j]
```

represents:

```text
LCS length between

s[0...i-1]
and
rev[0...j-1]
```

---

# Transition

## Characters Match

```java
if(s.charAt(i-1) == rev.charAt(j-1))
```

```java
dp[i][j] =
1 + dp[i-1][j-1];
```

---

## Characters Do Not Match

```java
dp[i][j] =
Math.max(
    dp[i-1][j],
    dp[i][j-1]
);
```

---

# Java Code (LCS Approach)

```java
public class Main {

    public static int longestPalindromeSubseq(String s) {

        String rev =
                new StringBuilder(s)
                        .reverse()
                        .toString();

        int n = s.length();

        int[][] dp =
                new int[n + 1][n + 1];

        for (int i = 1; i <= n; i++) {

            for (int j = 1; j <= n; j++) {

                if (s.charAt(i - 1)
                        == rev.charAt(j - 1)) {

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

        return dp[n][n];
    }

    public static void main(String[] args) {

        String s = "bbbab";

        System.out.println(
                longestPalindromeSubseq(s)
        );
    }
}
```

---

# Dry Run

## Input

```text
s = "bbbab"
```

Reverse:

```text
babbb
```

---

### LCS

```text
bbbab
babbb
```

Common subsequence:

```text
bbbb
```

Length:

```text
4
```

Answer:

```text
4
```

---

# Visualization

```text
Original : b b b a b
Reverse  : b a b b b

LCS      : b b b b
```

Thus:

```text
LPS Length = 4
```

---

# Direct DP Approach

Instead of converting to LCS:

Define:

```java
dp[i][j]
```

as:

```text
Longest Palindromic Subsequence
inside substring s[i...j]
```

---

## Cases

### Single Character

```java
dp[i][i] = 1
```

---

### Characters Match

```java
s.charAt(i) == s.charAt(j)
```

Then:

```java
dp[i][j] =
2 + dp[i+1][j-1]
```

---

### Characters Don't Match

```java
dp[i][j] =
Math.max(
    dp[i+1][j],
    dp[i][j-1]
)
```

---

# Java Code (Direct DP)

```java
public class Main {

    public static int longestPalindromeSubseq(
            String s) {

        int n = s.length();

        int[][] dp =
                new int[n][n];

        for (int i = 0; i < n; i++) {
            dp[i][i] = 1;
        }

        for (int len = 2; len <= n; len++) {

            for (int i = 0;
                 i + len - 1 < n;
                 i++) {

                int j = i + len - 1;

                if (s.charAt(i)
                        == s.charAt(j)) {

                    if (len == 2)
                        dp[i][j] = 2;
                    else
                        dp[i][j] =
                                2 +
                                dp[i + 1][j - 1];
                } else {

                    dp[i][j] =
                            Math.max(
                                    dp[i + 1][j],
                                    dp[i][j - 1]
                            );
                }
            }
        }

        return dp[0][n - 1];
    }
}
```

---

# Time Complexity

### LCS Approach

```text
O(N²)
```

---

### Direct DP Approach

```text
O(N²)
```

---

# Space Complexity

```text
O(N²)
```

---

# Relationship with Other Problems

```text
String DP Pattern
│
├── Longest Common Subsequence
├── Print LCS
├── Longest Common Substring
├── Longest Palindromic Subsequence ✅
├── Minimum Insertions to Make Palindrome
├── Minimum Deletions to Make Palindrome
├── Shortest Common Supersequence
├── Edit Distance
└── Distinct Subsequences
```

---

# Important Interview Observation

Since:

```text
LPS
=
LCS(String, Reverse(String))
```

many palindrome DP questions become easy.

Examples:

```text
Minimum Deletions to Make Palindrome
= n - LPS

Minimum Insertions to Make Palindrome
= n - LPS
```

So once you know **LCS**, you automatically unlock several palindrome-based DP problems.
