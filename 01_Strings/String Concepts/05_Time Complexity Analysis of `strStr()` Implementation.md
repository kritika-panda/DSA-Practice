# Time Complexity Analysis of `strStr()` Implementation

## Problem Statement

Implement the function:

```java
int strStr(String haystack, String needle)
```

Return the index of the first occurrence of `needle` in `haystack`.

If `needle` does not occur in `haystack`, return:

```java
-1
```

---

## Naive Implementation

```java
class Solution {

    public int strStr(String haystack,
                      String needle) {

        int n = haystack.length();
        int m = needle.length();

        for (int i = 0; i <= n - m; i++) {

            int j = 0;

            while (j < m &&
                   haystack.charAt(i + j)
                           == needle.charAt(j)) {

                j++;
            }

            if (j == m) {
                return i;
            }
        }

        return -1;
    }
}
```

---

# Variables

Let:

```java
n = haystack.length()
m = needle.length()
```

Example:

```java
haystack = "mississippi"
needle = "iss"
```

then:

```text
n = 11
m = 3
```

---

# Outer Loop Analysis

```java
for (int i = 0; i <= n - m; i++)
```

The loop can run:

```text
(n - m + 1)
```

times.

Example:

```java
haystack = "abcdef"

needle = "cd"
```

Possible starting positions:

```text
0
1
2
3
4
```

Total:

```text
6 - 2 + 1
=
5
```

positions.

Thus:

```text
Outer Loop = O(n - m + 1)
≈ O(n)
```

---

# Inner Loop Analysis

```java
while (j < m)
```

The inner loop compares characters of:

```java
needle
```

with characters of:

```java
haystack
```

In the worst case, it compares:

```text
m characters
```

for every starting position.

Therefore:

```text
Inner Loop = O(m)
```

---

# Overall Time Complexity

Total work:

```text
Outer Loop × Inner Loop
```

```text
=
O((n - m + 1) × m)
```

Simplifying:

```text
O(n × m)
```

---

# Best Case

Suppose:

```java
haystack = "abcdef"
needle   = "abc"
```

At:

```text
i = 0
```

the entire pattern matches immediately.

Only:

```text
m comparisons
```

are performed.

Complexity:

```text
O(m)
```

---

# Average Case

For random strings:

```text
Most mismatches happen early.
```

The inner loop usually exits quickly.

Average complexity is often close to:

```text
O(n)
```

but formally remains:

```text
O(n × m)
```

---

# Worst Case

The worst case occurs when almost all characters match repeatedly before failing.

Example:

```java
haystack = "aaaaaaaaaa"
needle   = "aaaaab"
```

---

## Comparison Process

### Start at Index 0

```text
aaaaaa
aaaaab
```

Comparisons:

```text
a == a
a == a
a == a
a == a
a == a
a != b
```

Total:

```text
6 comparisons
```

---

### Start at Index 1

```text
 aaaaaa
 aaaaab
```

Again:

```text
6 comparisons
```

---

### Start at Index 2

Again:

```text
6 comparisons
```

---

This continues for:

```text
(n - m + 1)
```

positions.

Total work:

```text
(n - m + 1) × m
```

which becomes:

```text
O(n × m)
```

---

# Mathematical Proof

Suppose:

```java
n = 10000
m = 5000
```

Then:

```text
(n - m + 1)
=
5001
```

At every position:

```text
5000 comparisons
```

Total:

```text
5001 × 5000
=
25,005,000
```

operations.

Complexity:

```text
O(n × m)
```

---

# Space Complexity

Extra variables used:

```java
i
j
n
m
```

Only constant extra memory.

Therefore:

```text
Space Complexity = O(1)
```

---

# Why Is Naive Search Slow?

Because after a mismatch occurs:

```text
Previously matched characters
are checked again.
```

Example:

```java
aaaaaaaaaa
aaaaab
```

The same characters are repeatedly revisited.

This creates redundant work.

---

# Visualization

```text
Haystack: aaaaaaaaaa

Needle:   aaaaab
```

Attempt 1:

```text
aaaaaa
aaaaab
```

Fail at last character.

---

Attempt 2:

```text
 aaaaaa
 aaaaab
```

Fail at last character.

---

Attempt 3:

```text
  aaaaaa
  aaaaab
```

Fail again.

---

Many repeated comparisons occur.

---

# Comparison with KMP

## Naive Approach

```text
Time  : O(n × m)
Space : O(1)
```

---

## KMP Algorithm

```text
Time  : O(n + m)
Space : O(m)
```

KMP avoids rechecking already matched characters by using:

```text
LPS (Longest Prefix Suffix) Array
```

---

# Complexity Summary

| Case | Time Complexity |
|--------|--------|
| Best Case | O(m) |
| Average Case | O(n) (practical) |
| Worst Case | O(n × m) |

---

# Space Complexity Summary

| Extra Space | Complexity |
|------------|------------|
| Variables Only | O(1) |

---

# Interview Follow-Up

### If interviewer asks:

> Can we do better than O(n × m)?

Answer:

```text
Yes.
```

Use:

```text
KMP (Knuth-Morris-Pratt)
```

Complexity:

```text
Time  : O(n + m)
Space : O(m)
```

Other advanced algorithms:

```text
Rabin-Karp
Z Algorithm
Boyer-Moore
KMP
```

---

# Key Takeaways

For the standard naive implementation of `strStr()`:

```java
for each starting position
    compare entire pattern
```

Outer loop:

```text
O(n)
```

Inner loop:

```text
O(m)
```

Overall:

```text
Time  : O(n × m)
Space : O(1)
```

The worst case occurs when many prefixes match before failing, causing repeated comparisons.

```text
Example:
haystack = "aaaaaaaaaa"
needle   = "aaaaab"
```

This is the primary motivation for learning:

```text
KMP String Matching Algorithm
```
