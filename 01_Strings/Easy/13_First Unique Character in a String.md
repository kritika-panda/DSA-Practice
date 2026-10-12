# First Unique Character in a String

## Problem Statement

Given a string `s`, *find the first non-repeating character in it and return its index*. If it does not exist, return `-1`.

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "leetcode"
```

**Output**

```java
0
```

**Explanation**

The character `'l'` occurs only once in the string and appears at index `0`.

---

### Example 2

**Input**

```java
s = "loveleetcode"
```

**Output**

```java
2
```

**Explanation**

The character `'v'` occurs only once in the string and appears at index `2`.

---

### Example 3

**Input**

```java
s = "aabb"
```

**Output**

```java
-1
```

**Explanation**

All characters in the string repeat, so there is no unique character.

---

## Brute Force Approach

Compare each character against all other characters in the string using nested loops to verify if it appears anywhere else.

### Steps

1. Run an outer loop with pointer `i` from `0` to `n - 1`.
2. Initialize a flag `isUnique = true`.
3. Run an inner loop with pointer `j` from `0` to `n - 1`.
4. If `i != j` and `s.charAt(i) == s.charAt(j)`, set `isUnique = false` and break the inner loop.
5. If `isUnique` remains `true` after the inner loop finishes, return index `i`.
6. If the outer loop finishes without finding any unique character, return `-1`.

### Complexity

```text
Time Complexity: O(n^2)
Space Complexity: O(1)
```

Scanning the entire string repeatedly for every character results in a quadratic bottleneck. We can optimize this process to two quick linear passes using a fixed-size frequency array.

---

# Optimal Approach: Two-Pass Frequency Array Tally

## Key Idea

We can identify the first unique character in **linear time** by breaking the problem down into two simple passes: counting and searching.

Since the problem usually handles a fixed, lowercase English alphabet range, we can build a highly optimized frequency look-up table using a simple array of size 26 instead of a heavy object map:
1. **Pass 1 (Count):** Traverse the string from left to right. For each character, calculate its alphabet index position (`c - 'a'`) and increment its total tally inside a fixed-size frequency array.
2. **Pass 2 (Search):** Traverse the string from left to right a second time. For each character, look up its total count from our precalculated frequency array. 
3. **The Target Condition:** The first character we encounter whose frequency count is exactly `1` is guaranteed to be the first unique character in the string. Return its index immediately.

---

## Visual Understanding

Suppose `s = "leetcode"` with length 8:

- **Pass 1 (Frequency Mapping):**
  - Loop populates the array buckets: `counts['l'-'a'] = 1`, `counts['e'-'a'] = 3`, `counts['t'-'a'] = 1`, `counts['c'-'a'] = 1`, `counts['o'-'a'] = 1`, `counts['d'-'a'] = 1`.
- **Pass 2 (Sequential Index Inspection):**
  - **i = 0 (`'l'`):** Read `counts['l'-'a']`. The count is `1`.
  - Condition met! Return index `0` instantly.

The search finishes early without ever validating the remaining character nodes.

---

## Partition Variables

Let:

```java
int[] counts = new int;
```

---

### Border Elements

The character mapping code handles standard boundaries efficiently by utilizing character unicode calculations relative to the anchor character `'a'`:

```java
counts[s.charAt(i) - 'a']++;
```

---

## Correct Partition Condition

The second verification pass sweeps from left to right to guarantee that the absolute first index matching the unique property is returned:

```java
if (counts[s.charAt(i) - 'a'] == 1) {
    return i;
}
```

---

## Java Solution

```java
class Solution {

    public int firstUniqChar(String s) {
        if (s == null || s.length() == 0) {
            return -1;
        }

        // Fixed-size frequency bucket array for lowercase English letters
        int[] counts = new int;
        int n = s.length();

        // Step 1: First pass - Tally character frequencies
        for (int i = 0; i < n; i++) {
            counts[s.charAt(i) - 'a']++;
        }

        // Step 2: Second pass - Find the first character with a frequency count of 1
        for (int i = 0; i < n; i++) {
            if (counts[s.charAt(i) - 'a'] == 1) {
                return i; // Found the absolute first non-repeating character index
            }
        }

        return -1;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Two-Pass Tally** | **O(n)** | **O(1)** | The algorithm executes exactly two linear passes through the string. The tracking array allocation size is strictly fixed at 26 cells regardless of string length. |

---

## Key Insight

Tallying character counts first and checking the values sequentially in a second forward pass avoids nested tracking logic, isolating unique indices in true linear time.

```text
Time  : O(n) two-pass forward execution
Space : O(1) absolute constant tracking array footprint
```

---

## Similar Problems

1. Valid Anagram (242)
2. Isomorphic Strings (205)
3. Find All Anagrams in a String (438)
4. Sort Characters By Frequency (451)
5. Non-Repeating Character in a Stream
