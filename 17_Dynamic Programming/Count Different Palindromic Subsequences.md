# 730. Count Different Palindromic Subsequences

🔗 **Problem Link:**  
https://leetcode.com/problems/count-different-palindromic-subsequences/

---

# Problem Statement

Given a string `s` consisting only of the characters:

```text
'a', 'b', 'c', 'd'
```

Return the number of **different non-empty palindromic subsequences** in `s`.

Since the answer can be very large, return it modulo:

```text
10^9 + 7
```

---

# What is a Palindromic Subsequence?

A subsequence is formed by deleting zero or more characters without changing the order of the remaining characters.

A palindrome reads the same forwards and backwards.

---

## Example

```text
s = "bccb"
```

Different palindromic subsequences:

```text
"b"
"c"
"bb"
"cc"
"bcb"
"bccb"
```

Answer:

```text
6
```

---

# Why This Problem is Hard

We are asked to count:

```text
Distinct palindromic subsequences
```

NOT:

```text
Total palindromic subsequences
```

Duplicates must only be counted once.

---

## Example

```text
s = "aaa"
```

Palindromic subsequences:

```text
"a"
"a"
"a"
"aa"
"aa"
"aa"
"aaa"
```

Distinct palindromes:

```text
"a"
"aa"
"aaa"
```

Answer:

```text
3
```

---

# Key Observation

The string contains only:

```text
a, b, c, d
```

Only 4 possible starting/ending characters.

This allows us to define a DP state based on:

```text
Starting and ending character.
```

---

# DP State

## Definition

```text
dp[i][j][k]
```

represents:

```text
Number of distinct palindromic subsequences

inside substring:

s[i...j]

that start and end with:

char('a' + k)
```

where:

```text
k = 0 → 'a'
k = 1 → 'b'
k = 2 → 'c'
k = 3 → 'd'
```

---

## Example

```text
dp[i][j][0]
```

counts all distinct palindromes in:

```text
s[i...j]
```

that begin and end with:

```text
'a'
```

---

# Base Case

A single character is itself a palindrome.

For each position:

```java
dp[i][i][s.charAt(i) - 'a'] = 1;
```

Example:

```text
s[i] = 'c'
```

Then:

```text
dp[i][i]['c'] = 1
```

representing:

```text
"c"
```

---

# Core Transition

For every character:

```text
c = 'a' + k
```

we consider how it relates to:

```text
s[i]
and
s[j]
```

---

# Case 1

## Both Ends Match Character c

```text
s[i] == c
AND
s[j] == c
```

Example:

```text
a .... a
```

---

### New Palindromes Created

At minimum:

```text
"a"
"aa"
```

Contributes:

```text
2
```

---

### Extend Existing Palindromes

Every palindrome inside:

```text
s[i+1...j-1]
```

can be wrapped:

```text
c + palindrome + c
```

Example:

```text
inside = "bcb"

wrap with 'a'

abcba
```

---

### Formula

```text
dp[i][j][k]

=

2

+

Σ dp[i+1][j-1][m]

for all m in {a,b,c,d}
```

---

## Code

```java
int sum = 0;

for (int m = 0; m < 4; m++) {
    sum = (sum + dp[i + 1][j - 1][m]) % MOD;
}

dp[i][j][k] = (2 + sum) % MOD;
```

---

# Case 2

## Left Matches, Right Doesn't

```text
s[i] == c
s[j] != c
```

Since the right boundary contributes nothing:

```text
Ignore s[j]
```

---

### Formula

```text
dp[i][j][k]
=
dp[i][j-1][k]
```

---

## Code

```java
dp[i][j][k] = dp[i][j - 1][k];
```

---

# Case 3

## Right Matches, Left Doesn't

```text
s[i] != c
s[j] == c
```

Ignore left boundary.

---

### Formula

```text
dp[i][j][k]
=
dp[i+1][j][k]
```

---

## Code

```java
dp[i][j][k] = dp[i + 1][j][k];
```

---

# Case 4

## Neither Side Matches

```text
s[i] != c
s[j] != c
```

Current boundary characters cannot participate.

Shrink both sides.

---

### Formula

```text
dp[i][j][k]
=
dp[i+1][j-1][k]
```

---

## Code

```java
dp[i][j][k] = dp[i + 1][j - 1][k];
```

---

# Bottom-Up DP Order

We process substrings by increasing length.

```java
for (int len = 2; len <= n; len++)
```

because:

```text
dp[i][j]
depends on

dp[i+1][j]
dp[i][j-1]
dp[i+1][j-1]
```

which are smaller ranges.

---

# Code

```java
class Solution {

    public int countPalindromicSubsequences(String s) {

        int MOD = 1_000_000_007;

        int n = s.length();

        int[][][] dp = new int[n][n][4];

        for (int i = 0; i < n; i++) {
            dp[i][i][s.charAt(i) - 'a'] = 1;
        }

        for (int len = 2; len <= n; len++) {

            for (int i = 0; i + len <= n; i++) {

                int j = i + len - 1;

                for (int k = 0; k < 4; k++) {

                    char c = (char) ('a' + k);

                    if (s.charAt(i) == c &&
                        s.charAt(j) == c) {

                        int sum = 0;

                        for (int m = 0; m < 4; m++) {

                            sum = (
                                sum +
                                dp[i + 1][j - 1][m]
                            ) % MOD;
                        }

                        dp[i][j][k] =
                                (2 + sum) % MOD;

                    } else if (s.charAt(i) == c) {

                        dp[i][j][k] =
                                dp[i][j - 1][k];

                    } else if (s.charAt(j) == c) {

                        dp[i][j][k] =
                                dp[i + 1][j][k];

                    } else {

                        dp[i][j][k] =
                                dp[i + 1][j - 1][k];
                    }
                }
            }
        }

        int result = 0;

        for (int k = 0; k < 4; k++) {

            result = (
                result +
                dp[0][n - 1][k]
            ) % MOD;
        }

        return result;
    }
}
```

---

# Dry Run

## Input

```text
s = "bccb"
```

---

### Length = 1

```text
b
c
c
b
```

Each contributes:

```text
1 palindrome
```

---

### Length = 2

```text
bc
cc
cb
```

For:

```text
cc
```

New palindromes:

```text
"c"
"cc"
```

---

### Length = 4

Substring:

```text
bccb
```

For character:

```text
'b'
```

Both ends match.

We create:

```text
"b"
"bb"
```

and wrap interior palindromes:

```text
c
cc

=>

bcb
bccb
```

---

### Final Distinct Palindromes

```text
b
c
bb
cc
bcb
bccb
```

Answer:

```text
6
```

---

# Why 3rd Dimension is Needed

Standard interval DP:

```text
dp[i][j]
```

cannot distinguish:

```text
Palindromes starting with 'a'

vs

Palindromes starting with 'b'
```

which causes duplicate counting.

Adding:

```text
k
```

ensures every palindrome is grouped by:

```text
starting/ending character
```

and counted exactly once.

---

# DP Visualization

```text
dp[i][j][k]

k = a
k = b
k = c
k = d
```

For every substring:

```text
s[i...j]
```

we maintain:

```text
4 independent counts
```

one for each possible boundary character.

---

# Complexity Analysis

### DP States

```text
i → n choices
j → n choices
k → 4 choices
```

Total:

```text
O(4 × n²)
=
O(n²)
```

---

### Transition Cost

For matching boundary case:

```text
sum over 4 characters
```

which is constant.

```text
O(4)
```

---

### Time Complexity

```text
O(n²)
```

---

### Space Complexity

```text
O(4 × n²)
=
O(n²)
```

---

# Pattern Recognition

This problem belongs to:

```text
Interval DP
```

with an additional dimension.

Common clues:

✅ Substring DP

✅ Count distinct structures

✅ Depends on both ends

✅ Duplicate elimination

✅ Build answer from smaller intervals

---

# Similar Problems

| Problem | Pattern |
|----------|----------|
| 516. Longest Palindromic Subsequence | Interval DP |
| 647. Palindromic Substrings | Interval DP |
| 1312. Minimum Insertions to Make Palindrome | Interval DP |
| 730. Count Different Palindromic Subsequences | 3D Interval DP |
| 664. Strange Printer | Interval DP |

---

# Key Takeaways

| Observation | Benefit |
|------------|----------|
| Only 4 characters exist | Add fixed 3rd dimension |
| Track boundary character explicitly | Avoid duplicates |
| Use interval DP | Build larger substrings from smaller ones |
| Matching ends create new palindromes | Core recurrence |
| Bottom-up traversal by length | Ensures dependencies are ready |

---

# Complexity Summary

| Approach | Time | Space |
|-----------|--------|--------|
| 3D Interval DP | O(n²) | O(n²) |

✅ **Optimal Approach:** 3D Interval DP where `dp[i][j][k]` stores the count of distinct palindromic subsequences in `s[i...j]` starting and ending with character `k`.
