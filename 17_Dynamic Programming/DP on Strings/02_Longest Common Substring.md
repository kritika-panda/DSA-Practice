# Longest Common Substring

## Problem Statement

Given two strings `s1` and `s2`, find the length of the **Longest Common Substring**.

A substring is a contiguous sequence of characters within a string.

---

## Example 1

```text
Input:
s1 = "abcdgh"
s2 = "acdghr"

Output:
4
```

### Longest Common Substring

```text
"cdgh"
```

Length:

```text
4
```

---

## Example 2

```text
Input:
s1 = "abc"
s2 = "xyz"

Output:
0
```

No common substring exists.

---

# Difference Between LCS and Longest Common Substring

### Longest Common Subsequence (LCS)

Characters may be non-contiguous.

```text
abcde
ace
```

LCS:

```text
ace
```

Length = 3

---

### Longest Common Substring

Characters must be contiguous.

```text
abcde
ace
```

Common substrings:

```text
a
c
e
```

Answer:

```text
1
```

---

# Intuition

For LCS:

```text
Mismatch
→ take max(left, up)
```

For Longest Common Substring:

```text
Mismatch
→ chain breaks
→ reset to 0
```

Because substring requires continuous matching characters.

---

# DP Definition

```java
dp[i][j]
```

represents:

```text
Length of longest common substring
ending at:

s1[i-1]
and
s2[j-1]
```

---

# Transition

## Characters Match

```java
if(s1.charAt(i-1) == s2.charAt(j-1))
```

Extend previous substring:

```java
dp[i][j] =
1 + dp[i-1][j-1];
```

---

## Characters Do Not Match

Substring breaks.

```java
dp[i][j] = 0;
```

---

# Formula

```java
if(s1[i-1] == s2[j-1])
    dp[i][j] = 1 + dp[i-1][j-1];
else
    dp[i][j] = 0;
```

---

# Java Code (Tabulation)

```java
public class Main {

    public static int longestCommonSubstring(
            String s1,
            String s2) {

        int n = s1.length();
        int m = s2.length();

        int[][] dp = new int[n + 1][m + 1];

        int ans = 0;

        for (int i = 1; i <= n; i++) {

            for (int j = 1; j <= m; j++) {

                if (s1.charAt(i - 1)
                        == s2.charAt(j - 1)) {

                    dp[i][j] =
                            1 + dp[i - 1][j - 1];

                    ans = Math.max(
                            ans,
                            dp[i][j]
                    );
                } else {

                    dp[i][j] = 0;
                }
            }
        }

        return ans;
    }

    public static void main(String[] args) {

        String s1 = "abcdgh";
        String s2 = "acdghr";

        System.out.println(
                longestCommonSubstring(s1, s2)
        );
    }
}
```

---

# Dry Run

## Input

```text
s1 = "abcdgh"
s2 = "acdghr"
```

---

## DP Table

```text
      a c d g h r
    ----------------
"" | 0 0 0 0 0 0
a  | 1 0 0 0 0 0
b  | 0 0 0 0 0 0
c  | 0 1 0 0 0 0
d  | 0 0 2 0 0 0
g  | 0 0 0 3 0 0
h  | 0 0 0 0 4 0
```

Maximum value:

```text
4
```

Substring:

```text
cdgh
```

---

# Visualization

```text
abcdgh
  ||||
  cdgh
acdghr
```

Longest Common Substring:

```text
cdgh
```

Length:

```text
4
```

---

# Space Optimization

Observe:

```java
dp[i][j]
```

depends only on:

```text
dp[i-1][j-1]
```

Therefore we can use:

```java
prev[]
curr[]
```

instead of:

```java
dp[][]
```

---

# Java Code (Space Optimized)

```java
public class Main {

    public static int longestCommonSubstring(
            String s1,
            String s2) {

        int n = s1.length();
        int m = s2.length();

        int[] prev = new int[m + 1];

        int ans = 0;

        for (int i = 1; i <= n; i++) {

            int[] curr = new int[m + 1];

            for (int j = 1; j <= m; j++) {

                if (s1.charAt(i - 1)
                        == s2.charAt(j - 1)) {

                    curr[j] =
                            1 + prev[j - 1];

                    ans = Math.max(
                            ans,
                            curr[j]
                    );
                }
            }

            prev = curr;
        }

        return ans;
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
N = length of s1
M = length of s2
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

# Key Difference: LCS vs Longest Common Substring

| Feature | LCS | Longest Common Substring |
|----------|------|------|
| Characters must be contiguous | ❌ | ✅ |
| Match | `1 + dp[i-1][j-1]` | `1 + dp[i-1][j-1]` |
| Mismatch | `max(left, up)` | `0` |
| Need global maximum | ❌ (`dp[n][m]`) | ✅ (`max(dp[i][j])`) |
| DP Type | Subsequence DP | Substring DP |

---

# Pattern Recognition

```text
String DP Pattern
│
├── Longest Common Subsequence (LCS)
├── Print LCS
├── Longest Common Substring ✅
├── Shortest Common Supersequence
├── Distinct Subsequences
├── Edit Distance
├── Wildcard Matching
├── Minimum Insertions & Deletions
└── Longest Palindromic Subsequence
```

### Interview Trick

Whenever a problem contains:

```text
Substring
Contiguous
Consecutive Characters
Continuous Match
```

Think:

```text
DP[i][j]

Match    → 1 + diagonal
Mismatch → 0
```

The moment you see **"Substring"**, remember:

```text
Mismatch = Reset to 0
```

which is the biggest difference from LCS.
