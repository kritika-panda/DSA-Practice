# 357. Count Numbers with Unique Digits

🔗 **Problem Link:**  
https://leetcode.com/problems/count-numbers-with-unique-digits/

---

# Problem Statement

Given an integer `n`, return the count of all numbers with unique digits `x` where:

```text
0 ≤ x < 10^n
```

A number has **unique digits** if no digit appears more than once.

---

# Examples

## Example 1

```text
Input: n = 2
```

Numbers range from:

```text
0 to 99
```

Numbers with repeated digits:

```text
11, 22, 33, 44, 55, 66, 77, 88, 99
```

Total numbers:

```text
100
```

Repeated-digit numbers:

```text
9
```

Unique-digit numbers:

```text
100 - 9 = 91
```

Output:

```text
91
```

---

## Example 2

```text
Input: n = 0
```

Only number:

```text
0
```

Output:

```text
1
```

---

# Key Observation

Instead of counting:

```text
Numbers with repeated digits
```

directly, we count:

```text
Numbers with unique digits
```

for each possible digit length.

---

# Understanding the Pattern

## 1-Digit Numbers

Possible numbers:

```text
0,1,2,3,4,5,6,7,8,9
```

Count:

```text
10
```

---

## 2-Digit Numbers

First digit:

```text
1-9
```

Choices:

```text
9
```

Second digit:

```text
0-9 excluding first digit
```

Choices:

```text
9
```

Total:

```text
9 × 9 = 81
```

---

## 3-Digit Numbers

First digit:

```text
9 choices
```

Second digit:

```text
9 choices
```

Third digit:

```text
8 choices
```

Total:

```text
9 × 9 × 8 = 648
```

---

## 4-Digit Numbers

```text
9 × 9 × 8 × 7
```

---

# General Formula

For numbers with exactly:

```text
k digits
```

Count:

```text
9 × 9 × 8 × 7 × ...
```

For every additional digit:

```text
Available choices decrease by 1
```

because digits cannot repeat.

---

# Building the Answer Incrementally

For:

```text
n = 3
```

We count:

### Length 1

```text
10
```

### Length 2

```text
81
```

### Length 3

```text
648
```

Total:

```text
10 + 81 + 648
=
739
```

---

# Variables Used

## result

Stores cumulative answer.

```java
result = 10;
```

Initially counts:

```text
All 1-digit numbers
```

---

## unique

Stores count of current digit length.

Example:

```text
2 digits → 81

3 digits → 648

4 digits → 4536
```

---

## available

Remaining digits available to use.

Initially:

```java
available = 9;
```

because after choosing the first digit:

```text
9 digits remain
```

---

# Code

```java
class Solution {

    public int countNumbersWithUniqueDigits(int n) {

        if (n == 0)
            return 1;

        int result = 10;
        int unique = 9;
        int available = 9;

        for (int i = 2; i <= n && available > 0; i++) {

            unique *= available;

            result += unique;

            available--;
        }

        return result;
    }
}
```

---

# Dry Run

## Input

```text
n = 2
```

---

### Initial Values

```text
result = 10
unique = 9
available = 9
```

---

### i = 2

```text
unique = 9 × 9
       = 81
```

Add to answer:

```text
result = 10 + 81
       = 91
```

Decrease available digits:

```text
available = 8
```

---

### End

```text
91
```

Output:

```text
91
```

---

# Dry Run

## Input

```text
n = 3
```

---

### Initial

```text
result = 10
unique = 9
available = 9
```

---

### i = 2

```text
unique = 81
result = 91
available = 8
```

---

### i = 3

```text
unique = 81 × 8
       = 648

result = 91 + 648
       = 739
```

---

### Final Answer

```text
739
```

---

# Why Stop at 10 Digits?

There are only:

```text
10 digits
```

available:

```text
0-9
```

After using all ten digits:

```text
No additional unique-digit numbers can be formed.
```

Therefore:

```java
available > 0
```

acts as a stopping condition.

---

# Mathematical Interpretation

For each length:

```text
Length 1:
10

Length 2:
9 × 9

Length 3:
9 × 9 × 8

Length 4:
9 × 9 × 8 × 7

...

Length k:
9 × P(9, k-1)
```

where

```text
P(n,r)
=
n! / (n-r)!
```

---

# Visualization

```text
1 Digit

0 1 2 3 4 5 6 7 8 9
↑
10 choices
```

```text
2 Digits

_ _
↑
9 choices (1-9)

_ _
  ↑
9 remaining choices
```

```text
3 Digits

_ _ _
↑
9 choices

_ _ _
  ↑
9 choices

_ _ _
    ↑
8 choices
```

---

# Greedy Counting Idea

For each new digit position:

```text
Choose any digit
that has not been used before.
```

Number of choices:

```text
9
9
8
7
6
...
```

This naturally produces the permutation count.

---

# Complexity Analysis

### Time Complexity

Loop runs at most:

```text
10 times
```

because there are only 10 digits.

```text
O(10)
=
O(1)
```

---

### Space Complexity

Only a few variables are used.

```text
O(1)
```

---

# Pattern Recognition

This problem belongs to:

```text
Combinatorics
+
Permutation Counting
```

Common clues:

✅ Count valid numbers

✅ Digits cannot repeat

✅ Unique arrangements

✅ Small digit domain (0-9)

✅ Count instead of generate

---

# Similar Problems

| Problem | Pattern |
|----------|----------|
| 357. Count Numbers with Unique Digits | Combinatorics |
| 2376. Count Special Integers | Digit DP |
| 1012. Numbers With Repeated Digits | Digit DP |
| 902. Numbers At Most N Given Digit Set | Digit DP |
| Permutations of Digits | Counting |

---

# Key Takeaways

| Observation | Benefit |
|------------|----------|
| Digits cannot repeat | Use permutations |
| First digit cannot be 0 | 9 choices initially |
| Remaining digits decrease | 9,8,7,... |
| Sum counts for all lengths | Final answer |
| Maximum 10 unique digits exist | O(1) solution |

---

# Complexity Summary

| Approach | Time | Space |
|-----------|--------|--------|
| Combinatorial Counting | O(1) | O(1) |

✅ **Optimal Approach:** Count unique-digit numbers of each length using permutation logic and accumulate the results.
