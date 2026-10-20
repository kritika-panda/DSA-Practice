# Valid Anagram

## Problem Statement

Given two strings `s` and `t`, return `true` *if `t` is an **anagram** of `s`, and `false` otherwise*.

An **anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

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
s = "anagram"
t = "nagaram"
```

**Output**

```java
true
```

---

### Example 2

**Input**

```java
s = "rat"
t = "car"
```

**Output**

```java
false
```

---

### Example 3

**Input**

```java
s = "a"
t = "ab"
```

**Output**

```java
false
```

---

## Brute Force Approach

Convert both strings into primitive character arrays, sort them completely using a standard sorting routine, and compare if the resulting array layouts are identical.

### Steps

1. Fast-fail if `s.length() != t.length()`.
2. Convert both strings to character arrays: `s.toCharArray()` and `t.toCharArray()`.
3. Sort both arrays globally: `Arrays.sort(charArrayS)` and `Arrays.sort(charArrayT)`.
4. Return `Arrays.equals(charArrayS, charArrayT)`.

### Complexity

```text
Time Complexity: O(n * log(n)) // due to character array sorting overhead
Space Complexity: O(n) to store the character array copies
```

Global array sorting introduces an unnecessary log-linear overhead. Since the problem operates on a finite alphabet range, we can optimize this process to a single linear pass using a fixed-capacity frequency bucket table.

---

# Optimal Approach: Fixed-Size Alphabet Frequency Array

## Key Idea

If two strings are anagrams of each other, they must contain the exact same characters with the exact same frequencies. 

Instead of checking combinations or sorting characters, we can maintain an absolute tally of character distributions using a flat **frequency tracking array**:
1. Initialize an integer array `counts` of size 26 (matching the 26 lowercase English letters).
2. Iterate through both strings simultaneously using a single index pointer `i`.
3. For each position, increment the bucket cell corresponding to the character from string `s`, and decrement the bucket cell corresponding to the character from string `t`.
4. After scanning the strings completely, verify that every cell inside the `counts` array is exactly `0`. If any cell is non-zero, it means a character mismatch or an uneven count exists.

---

## Visual Understanding

Suppose `s = "anagram"` and `t = "nagaram"` with length 7:

- **i = 0 (`'a'` vs `'n'`):** Increment at `'a'` \(\implies\) `+1`. Decrement at `'n'` \(\implies\) `-1`.
- **i = 1 (`'n'` vs `'a'`):** Increment at `'n'` \(\implies\) `-1 + 1 = 0`. Decrement at `'a'` \(\implies\) `+1 - 1 = 0`.
- **Continuing the loop down to index 6:** Every unique letter increment matches an equal letter decrement from the opposite string block.

The array tracks back to all zeros, confirming a valid anagram structure instantly.

---

## Partition Variables

Let:

```java
int[] counts = new int[26];
```

---

### Border Elements

The structural size check intercepts length mismatches right at the gate before processing character mapping offsets:

```java
if (s.length() != t.length()) {
    return false;
}
```

---

## Correct Partition Condition

The single loop pass records frequency shifts relative to the base anchor character character code `'a'`:

```java
counts[s.charAt(i) - 'a']++;
counts[t.charAt(i) - 'a']--;
```

---

## Java Solution

```java
class Solution {

    public boolean isAnagram(String s, String t) {
        // Fast-fail: Strings of different lengths can never be valid anagrams
        if (s == null || t == null || s.length() != t.length()) {
            return false;
        }

        // Initialize a fixed-size frequency array for lowercase English letters
        int[] counts = new int[26];
        int n = s.length();

        // Step 1: Record character frequencies from both strings in a single pass
        for (int i = 0; i < n; i++) {
            counts[s.charAt(i) - 'a']++; // Increment for character in s
            counts[t.charAt(i) - 'a']--; // Decrement for character in t
        }

        // Step 2: Verify that all frequency buckets are exactly zero
        for (int count : counts) {
            if (count != 0) {
                // Any non-zero cell implies an uneven character distribution
                return false; 
            }
        }

        return true;
    }
}
```

If we consider 128 size of array:
``` java
class Solution {

    public boolean isAnagram(String s, String t) {

        if (s == null || t == null || s.length() != t.length()) {
            return false;
        }

        int[] counts = new int[128];

        for (int i = 0; i < s.length(); i++) {
            counts[s.charAt(i)]++;
            counts[t.charAt(i)]--;
        }

        for (int count : counts) {
            if (count != 0) {
                return false;
            }
        }

        return true;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Frequency Scan** | **O(n)** | **O(1)** | Traverses the string inputs exactly once. The character array tracking allocation is strictly fixed at 26 cells regardless of string length. |

---

## Key Insight

Mapping character frequencies onto a single fixed-size array instead of sorting objects eliminates log-linear sorting overhead, checking character distribution balance in true linear time.

```text
Time  : O(n) single pass forward execution
Space : O(1) absolute constant memory footprint
```

---

## Similar Problems

1. Group Anagrams (49)
2. Find All Anagrams in a String (438)
3. Isomorphic Strings (205)
4. Word Pattern (290)
5. Palindrome Permutation (266)
