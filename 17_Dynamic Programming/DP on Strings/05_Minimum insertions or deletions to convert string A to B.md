# Minimum Insertions or Deletions to Convert String A to B

## Problem Statement

Given two strings `str1` and `str2`, return the minimum number of insertions and deletions required to convert `str1` into `str2`.

You can delete a character from `str1` or insert a character into `str1` at any position.

---

## Example 1

```text
Input:
str1 = "heap", str2 = "pea"

Output:
3
```

Deletions from `str1`: 2 ('h', 'p'), Insertion to `str1`: 1 ('p').

---

## Example 2

```text
Input:
str1 = "geeksforgeeks", str2 = "geeks"

Output:
8
```

Deletions from `str1`: 8 ("forgeeks"), Insertions to `str1`: 0.

---

## Example 3

```text
Input:
str1 = "abcd", str2 = "anc"

Output:
3
```

---

# Intuition

Instead of directly finding operations, think:

```text
What is the largest part of the strings 
that is already common and preserves order?
```

That is exactly:

```text
Longest Common Subsequence (LCS)
```

Characters in the LCS do not need to be deleted from `str1` nor inserted into `str2`.

Only the remaining non-LCS characters need to be managed through operations.

---

# Key Observation

```text
Total Operations 
= 
Deletions from str1 + Insertions to str1
```

```text
Deletions = Length of str1 - Length of LCS
Insertions = Length of str2 - Length of LCS
```

Therefore:

```text
Minimum Operations 
= 
(n + m) - 2 * Length of LCS
```

---

## Example

```text
str1 = "heap", str2 = "pea"
```

Lengths:

```text
n = 4, m = 3
```

LCS:

```text
ea
```

Length:

```text
2
```

Therefore:

```text
Minimum Deletions = 4 - 2 = 2
Minimum Insertions = 3 - 2 = 1

Total Operations = 2 + 1 = 3
```

---

# Relation with LCS

We know:

```text
LCS = LCS(str1, str2)
```

Therefore:

```text
Minimum Operations = n + m - 2 * LCS(str1, str2)
```

---

# Approach

### Step 1

Determine lengths `n` and `m` of both strings.

```java
int n = str1.length();
int m = str2.length();
```

---

### Step 2

Find:

```text
LCS(str1, str2)
```

---

### Step 3

Compute:

```java
answer = (n - lcs) + (m - lcs);
```

---

# Java Code

```java
public class Main {

    public static int minOperations(String str1, String str2) {
        int n = str1.length();
        int m = str2.length();

        int[][] dp = new int[n + 1][m + 1];

        // Build the bottom-up 2D tabulation table for LCS
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (str1.charAt(i - 1) == str2.charAt(j - 1)) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        int lcs = dp[n][m];

        int deletions = n - lcs;
        int insertions = m - lcs;

        return deletions + insertions;
    }

    public static void main(String[] args) {
        String str1 = "heap";
        String str2 = "pea";

        System.out.println(
                minOperations(str1, str2)
        );
    }
}
```

---

# Dry Run

## Input

```text
str1 = "heap"
str2 = "pea"
```

---

### LCS

```text
ea
```

Length:

```text
2
```

---

### Minimum Operations

```text
Deletions = 4 - 2 = 2
Insertions = 3 - 2 = 1

Total = 2 + 1 = 3
```

Answer:

```text
3
```

---

# Visualization

```text
str1:  h  e  a  p

          |  |
str2:     p  e  a

LCS = "ea"
```

Characters not in LCS:

```text
Deletions (from str1): 'h', 'p'
Insertions (into str2): 'p'
```

Total operations required:

```text
3
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
O(N * M)
```

to:

```text
O(M)
```

---

# Time Complexity

LCS Computation Matrix:

```text
O(N * M)
```

---

# Space Complexity

### DP Table

```text
O(N * M)
```

### Space Optimized

```text
O(M)
```

---

# Related Problems

```text
LCS DP Pattern
│
├── Longest Common Subsequence
├── Shortest Common Supersequence
├── Minimum Insertions or Deletions to Convert String ✅
├── Edit Distance
├── Delete Operation for Two Strings
└── Longest Repeating Subsequence
```

---

# Important Interview Trick

Remember these formulas:

```text
LCS = Dynamic programming grid of matching characters
```

```text
Minimum Deletions
=
n - LCS
```

```text
Minimum Insertions
=
m - LCS
```

Therefore both problems reduce to:

```text
Find Longest Common Subsequence
```

first.

