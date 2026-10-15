# Minimum Insertions to Make String a Palindrome

## Problem Statement

Given a string `s`, return the minimum number of insertions required to make the string a palindrome.

You may insert characters at any position.

---

## Example 1

```text
Input:
s = "zzazz"

Output:
0
```

Already a palindrome.

---

## Example 2

```text
Input:
s = "mbadm"

Output:
2
```

One possible palindrome:

```text
mbdadbm
```

Insertions:

```text
2
```

---

## Example 3

```text
Input:
s = "leetcode"

Output:
5
```

---

# Intuition

Instead of directly finding insertions, think:

```text
What is the largest part of the string
that is already a palindrome?
```

That is exactly:

```text
Longest Palindromic Subsequence (LPS)
```

Characters in LPS do not need any insertion.

Only the remaining characters need to be inserted appropriately.

---

# Key Observation

```text
Minimum Insertions
=
Length of String
-
Length of Longest Palindromic Subsequence
```

---

## Example

```text
s = "mbadm"
```

Length:

```text
5
```

LPS:

```text
mam
```

Length:

```text
3
```

Therefore:

```text
Minimum Insertions

= 5 - 3

= 2
```

---

# Relation with LCS

We know:

```text
LPS(s)
=
LCS(
    s,
    reverse(s)
)
```

Therefore:

```text
Minimum Insertions
=
n - LCS(s, reverse(s))
```

---

# Approach

### Step 1

Reverse the string.

```java
String rev =
    new StringBuilder(s)
        .reverse()
        .toString();
```

---

### Step 2

Find:

```text
LCS(s, rev)
```

---

### Step 3

Compute:

```java
answer = n - lps;
```

---

# Java Code

```java
public class Main {

    public static int minInsertions(String s) {

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

        int lps = dp[n][n];

        return n - lps;
    }

    public static void main(String[] args) {

        String s = "mbadm";

        System.out.println(
                minInsertions(s)
        );
    }
}
```

---

# Dry Run

## Input

```text
s = "mbadm"
```

Reverse:

```text
mdabm
```

---

### LCS

```text
mam
```

Length:

```text
3
```

---

### Minimum Insertions

```text
n - LPS

= 5 - 3

= 2
```

Answer:

```text
2
```

---

# Visualization

```text
Original:

m b a d m
|       |

LPS = "mam"
```

Characters not in LPS:

```text
b
d
```

Need insertions:

```text
2
```

---

# Space Optimized Solution

Since this is an LCS problem, we can optimize space using:

```java
int[] prev
int[] curr
```

reducing space complexity from:

```text
O(N²)
```

to:

```text
O(N)
```

---

# Time Complexity

LCS Computation:

```text
O(N²)
```

---

# Space Complexity

### DP Table

```text
O(N²)
```

### Space Optimized

```text
O(N)
```

---

# Related Problems

```text
Palindrome DP Pattern
│
├── Longest Palindromic Subsequence
├── Minimum Insertions to Make Palindrome ✅
├── Minimum Deletions to Make Palindrome
├── Palindrome Partitioning
├── Longest Palindromic Substring
└── Count Palindromic Subsequences
```

---

# Important Interview Trick

Remember these formulas:

```text
LPS = LCS(s, reverse(s))
```

```text
Minimum Deletions
=
n - LPS
```

```text
Minimum Insertions
=
n - LPS
```

Therefore both problems reduce to:

```text
Find Longest Palindromic Subsequence
```

first.
