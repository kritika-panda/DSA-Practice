# Longest Happy Prefix

## Problem Statement

A string is called a **happy prefix** if it is a **proper prefix** that is also a **suffix** (excluding the string itself).

Given a string `s`, return the **longest happy prefix** of `s`. If no such prefix exists, return an empty string `""`.

The overall run time complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "level"
```

**Output**

```java
"l"
```

**Explanation**

The proper prefixes are `["l", "le", "lev", "leve"]`.  
The suffixes are `["l", "el", "vel", "evel"]`.  
The longest happy prefix that matches both sets is `"l"`.

---

### Example 2

**Input**

```java
s = "ababab"
```

**Output**

```java
"abab"
```

**Explanation**

The proper prefix `"abab"` is also a suffix of the string. It is the longest one available.

---

### Example 3

**Input**

```java
s = "leetcode"
```

**Output**

```java
""
```

**Explanation**

There is no non-empty proper prefix that matches a suffix of the string.

---

## Brute Force Approach

Generate all prefixes and check if they are identical to their corresponding suffixes, starting from the longest possible length down to 1.

### Steps

1. Run a loop with length `l` from `n - 1` down to `1`.
2. Extract the prefix of length `l`: `prefix = s.substring(0, l)`.
3. Extract the suffix of length `l`: `suffix = s.substring(n - l, n)`.
4. If `prefix.equals(suffix)`, return this prefix immediately.
5. If the loop finishes without a match, return `""`.

### Complexity

```text
Time Complexity: O(n^2) // due to substring generation and string comparisons
Space Complexity: O(n) to store the generated substrings
```

Generating substrings repeatedly causes severe performance penalties for long or highly repetitive text blocks. We can achieve linear time by leveraging the **LPS (Longest Prefix Suffix)** precalculation array array technique from the **KMP algorithm**.

---

# Optimal Approach: KMP LPS Array Processing

## Key Idea

The **LPS array** used in the Knuth-Morris-Pratt (KMP) algorithm is explicitly designed to find the length of the longest proper prefix that is also a suffix for *every* prefix position of a string.

Instead of building out the entire structural matching pipeline, we only need to compute the **last cell value** of the string's LPS table. 

1. Initialize an `lps` table array of size `n`.
2. Use two pointers (`len` for the tracking prefix length and `i` for scanning the suffix characters) to populate the table in a single linear pass.
3. The value sitting at the very last slot of the array, `lps[n - 1]`, gives us the exact length of the longest happy prefix for the entire string.
4. Return the prefix substring from index `0` up to `lps[n - 1]`.

---

## Visual Understanding

Suppose `s = "ababab"` where `n = 6`:

Let's compute the matching states of the LPS array using our fast-moving pointer `i`:

```text
Index: 0  1  2  3  4  5
Char : a  b  a  b  a  b
LPS  : 0  0  1  2  3  4
```

- At index `2`, character `'a'` matches with index `0`, so `lps[2] = 1`.
- At index `3`, character `'b'` matches with index `1`, so `lps[3] = 2`.
- Following this expansion down to the end, the last index holds `lps[5] = 4`.

This indicates that a proper prefix of length `4` (`"abab"`) is also a valid suffix. We pull `s.substring(0, 4)` and return `"abab"` in a single pass.

---

## Partition Variables

Let:

```java
int[] lps = new int[s.length()];
int len = 0; // Length of the previous longest prefix suffix
int i = 1;
```

---

### Border Elements

When a character path mismatch occurs, the prefix tracker length drops back to its last known valid prefix state boundary using the table history:

```java
if (len != 0) {
    len = lps[len - 1];
} else {
    lps[i] = 0;
    i++;
}
```

---

## Correct Partition Condition

The value stored in the final slot of the processed table defines the boundary size used to slice our output substring:

```java
int longestLength = lps[s.length() - 1];
return s.substring(0, longestLength);
```

---

## Java Solution

```java
class Solution {

    public String longestHappyPrefix(String s) {
        if (s == null || s.length() <= 1) {
            return "";
        }

        int n = s.length();
        int[] lps = new int[n];
        
        int len = 0; // Length of the previous longest prefix suffix
        int i = 1;

        lps[0] = 0; // Base case: first cell is always 0

        // Step 1: Compute the LPS tracking values linearly
        while (i < n) {
            if (s.charAt(i) == s.charAt(len)) {
                len++;
                lps[i] = len;
                i++;
            } else {
                // If a mismatch occurs, drop back using our precalculated history
                if (len != 0) {
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }

        // Step 2: The last index in the LPS array gives the longest happy prefix length
        int longestPrefixLength = lps[n - 1];
        
        return s.substring(0, longestPrefixLength);
    }
}
```

---

## Dry Run

### Input

```java
s = "level"
```

---

### Step Execution Traversal

- **Initialization:** `n = 5`, `lps` array size = 5. `len = 0, i = 1`.
- **i = 1 ('e'):** `'e' != 'l'`. `len == 0` \(\implies\) `lps[1] = 0, i = 2`.
- **i = 2 ('v'):** `'v' != 'l'`. `len == 0` \(\implies\) `lps[2] = 0, i = 3`.
- **i = 3 ('e'):** `'e' != 'l'`. `len == 0` \(\implies\) `lps[3] = 0, i = 4`.
- **i = 4 ('l'):** `'l' == 'l'`. Match found! `len++ \implies 1`. `lps[4] = 1, i = 5`.
- **Loop Terminates:** `i` hits boundary `n`.
- **Substring Slicing:** Reads `lps[4] = 1`. Extracts `s.substring(0, 1)`, which returns `"l"`.

---

### Answer

```java
"l"
```

---

## Why Is the Time Complexity Bounded Linearly?

The algorithm uses a single loop pass where the pointer `i` moves strictly forward up to `n`. Although the `len` tracking pointer can shift backward on character mismatches, it can move backward at most as many times as it advanced forward. This mathematical invariant locks the total number of operations safely to a linear scale.

This uniform calculation timeline yields:

```text
O(n)
```

which satisfies the strict optimization constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Where `n` is the length of string `s`. Populating the prefix suffix state table runs in linear time, and extracting the final substring takes a constant O(n) memory slicing operation.

---

### Space Complexity

```text
O(n)
```

The algorithm allocates an auxiliary integer array of size `n` to maintain the prefix lookup states, plus O(n) space to slice out the final output substring.

---

## Key Insight

Isolating the last cell of an in-place LPS array transforms a complex double-ended substring comparison task into a single-pass prefix matching walk.

```text
Time  : O(n) linear pass execution
Space : O(n) table array storage footprint
```

---

## Similar Problems

1. Find the Index of the First Occurrence in a String / KMP (28)
2. Repeated Substring Pattern (459)
3. Shortest Palindrome (214)
4. Z Algorithm Implementation
5. String Hashing Introduction
