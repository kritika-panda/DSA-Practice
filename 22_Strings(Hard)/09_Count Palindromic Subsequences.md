# Count Palindromic Subsequences

## Problem Statement

Given a string `s`, return the **total number of palindromic subsequences** (modulo 10⁹ + 7 or simply the absolute count depending on the variation, typically modulo 10⁹ + 7 is used for large constraints). 

A **subsequence** is a string that can be derived from another string by deleting some or no characters without changing the order of the remaining characters. A **palindromic subsequence** is a subsequence that reads the same forward and backward. Two subsequences are considered different if they use different indices of the original string.

The overall run time complexity should be:

```text
O(n^2)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "aba"
```

**Output**

```java
5
```

**Explanation**

The 5 distinct palindromic subsequences based on unique index selections are:
- `"a"` (at index 0)
- `"b"` (at index 1)
- `"a"` (at index 2)
- `"aa"` (at indices {0, 2})
- `"aba"` (at indices {0, 1, 2})

---

### Example 2

**Input**

```java
s = "abcd"
```

**Output**

```java
4
```

**Explanation**

The only valid palindromic subsequences are the single-character selections: `"a"`, `"b"`, `"c"`, and `"d"`. No multi-character selection forms a palindrome.

---

### Example 3

**Input**

```java
s = "a"
```

**Output**

```java
1
```

---

## Brute Force Approach

Generate all \(2^n - 1\) non-empty subsequences of the string, check each one to see if it reads the same forward and backward, and count the valid ones.

### Steps

1. Use backtracking or bit manipulation to generate all possible subsequences.
2. For each subsequence, use a two-pointer approach to verify if it is a palindrome.
3. If it is a palindrome, increment the counter.

### Complexity

```text
Time Complexity: O(2^n * n)
Space Complexity: O(n) for the recursion stack
```

This exponential strategy triggers a Time Limit Exceeded (TLE) error for strings longer than 20 characters. The problem requires a more optimal dynamic programming approach.

---

# Optimal Approach: Interval Dynamic Programming

## Key Idea

We can solve this problem in O(n²) time using **Interval Dynamic Programming**. Let \(DP[i][j]\) represent the total number of palindromic subsequences within the substring \(s[i \dots j]\).

For any interval \([i, j]\), we can derive the count by breaking it down into smaller subproblems:
1. **Case 1: Characters at boundaries do not match (\(s[i] \neq s[j]\))**
   The palindromic subsequences can be found in the range \([i, j-1]\) or the range \([i+1, j]\). However, the overlapping range \([i+1, j-1]\) is counted twice in both subproblems, so we must subtract it:
   \[DP[i][j] = DP[i][j-1] + DP[i+1][j] - DP[i+1][j-1]\]

2. **Case 2: Characters at boundaries match (\(s[i] == s[j]\))**
   In addition to all the palindromic subsequences found when they don't match, the matching boundaries can enclose *every* palindromic subsequence found in the inner range \([i+1, j-1]\) to form a new, longer palindrome. Plus, the boundary pair itself forms a new palindrome (e.g., `""` wrapped by `a...a` becomes `"aa"`). Therefore, we add \((DP[i+1][j-1] + 1)\) to our overlapping calculation, which simplifies nicely to:
   \[DP[i][j] = DP[i][j-1] + DP[i+1][j] + 1\]

---

## Visual Understanding

Suppose `s = "aba"` with length 3:

We build the DP table from smaller lengths up to the full string length:
- **Length 1:** \(DP[0][0] = 1\), \(DP[1][1] = 1\), \(DP[2][2] = 1\) (Every single character is a palindrome).
- **Length 2 (`"ab"` at [0,1]):** \(s[0] \neq s[1] \implies DP[0][1] = DP[0][0] + DP[1][1] - DP[1][0] = 1 + 1 - 0 = 2\).
- **Length 2 (`"ba"` at [1,2]):** \(s[1] \neq s[2] \implies DP[1][2] = DP[1][1] + DP[2][2] - DP[2][1] = 1 + 1 - 0 = 2\).
- **Length 3 (`"aba"` at [0,2]):** \(s[0] == s[2] == 'a'\). 
  \[DP[0][2] = DP[0][1] + DP[1][2] + 1 = 2 + 2 + 1 = 5\]

The final cell \(DP[0][2]\) safely stores the total count `5`.

---

## Partition Variables

Let:

```java
long[][] dp = new long[n][n];
long MOD = 1000000007; // Standard modulus wrapper to prevent integer overflow
```

---

### Border Elements

The base cases populate the main diagonal of our interval matrix where the substring length is exactly 1:

```java
for (int i = 0; i < n; i++) {
    dp[i][i] = 1;
}
```

---

## Correct Partition Condition

When calculating intervals where the outer character ends match up, the inner segment count maps into the transition equation directly:

```java
if (s.charAt(i) == s.charAt(j)) {
    dp[i][j] = (dp[i][j - 1] + dp[i + 1][j] + 1) % MOD;
} else {
    dp[i][j] = (dp[i][j - 1] + dp[i + 1][j] - dp[i + 1][j - 1] + MOD) % MOD;
}
```

---

## Java Solution

```java
class Solution {

    public int countPalindromicSubsequences(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        int n = s.length();
        long[][] dp = new long[n][n];
        long MOD = 1000000007;

        // Step 1: Base Case - Every single character is a valid palindrome of length 1
        for (int i = 0; i < n; i++) {
            dp[i][i] = 1;
        }

        // Step 2: Build the table dynamically for substring intervals of increasing lengths
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;

                // Condition A: Boundary characters match
                if (s.charAt(i) == s.charAt(j)) {
                    dp[i][j] = (dp[i][j - 1] + dp[i + 1][j] + 1) % MOD;
                } 
                // Condition B: Boundary characters mismatch
                else {
                    dp[i][j] = (dp[i][j - 1] + dp[i + 1][j] - dp[i + 1][j - 1] + MOD) % MOD;
                }
            }
        }

        // The top-right cell contains the result for the entire string range [0...n-1]
        return (int) dp[0][n - 1];
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
O(n^2)
```

Where `n` is the length of the string `s`. The algorithm utilizes nested loops to fill the upper-triangular half of the n × n matrix table, computing each state transition in O(1) constant time.

---

### Space Complexity

```text
O(n^2)
```

The algorithm allocates a 2D matrix array of dimensions n × n to store the precalculated interval counts.

---

## Key Insight

By breaking down a global string sequence search into localized nested interval states, we can reuse smaller overlapping substring properties to avoid exponential backtracking entirely.

```text
Time  : O(n^2) interval matrix pass
Space : O(n^2) auxiliary DP storage footprint
```

---

## Similar Problems

1. Longest Palindromic Subsequence (516)
2. Palindromic Substrings / Count Substrings (647)
3. Longest Palindromic Substring (5)
4. Distinct Palindromic Subsequences (730)
5. Minimum Insertions to Make a String Palindrome (1312)
