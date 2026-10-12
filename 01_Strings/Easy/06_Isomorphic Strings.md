# Isomorphic Strings

## Problem Statement

Given two strings `s` and `t`, determine if they are **isomorphic**.

Two strings `s` and `t` are isomorphic if the characters in `s` can be replaced to get `t`. All occurrences of a character must be replaced with another character while preserving the character order. No two characters may map to the same character, but a character may map to itself.

The overall runtime complexity must be:

```text
O(n)
```

*(where n is the length of string s and t)*

---

## Examples

### Example 1

**Input**

```java
s = "egg"
t = "add"
```

**Output**

```java
true
```

**Explanation**

We can map `'e' -> 'a'` and `'g' -> 'd'`, which transforms `"egg"` into `"add"`.

---

### Example 2

**Input**

```java
s = "foo"
t = "bar"
```

**Output**

```java
false
```

**Explanation**

`'o'` would have to map to both `'a'` and `'r'`, violating the one-to-one mapping rule.

---

### Example 3

**Input**

```java
s = "paper"
t = "title"
```

**Output**

```java
true
```

---

## Brute Force Approach

Use two nested loops to track all occurrences of every character, checking if characters at matching relative indices in `s` correspond to the exact same tracking characters in `t`.

### Steps

1. Fast-fail if `s.length() != t.length()`.
2. For each unique character position `i`, find all indices `j` where `s.charAt(i) == s.charAt(j)`.
3. Verify that for all those matching indices, `t.charAt(i) == t.charAt(j)` holds true as well.
4. If a mismatch is discovered anywhere, return `false`.

### Complexity

```text
Time Complexity: O(n^2)
Space Complexity: O(1)
```

This quadratic approach re-evaluates indices repeatedly, leading to execution timeouts for large strings. We can optimize this process to a single pass by encoding the strings into character tracking maps.

---

# Optimal Approach: Two-Pass Position Tracking / Dual Alphabet Arrays

## Key Idea

To confirm a strict **one-to-one character mapping (bijection)**, we can keep track of the last seen positions of characters from both strings using two primitive arrays. 

Instead of saving explicit character-to-character maps, we record the **1-based index position** where each character last appeared:
1. Initialize two integer tracking arrays of size 256 (to cover standard extended ASCII character sets) initialized entirely to `0`.
2. Iterate through both strings simultaneously using a single index pointer `i`.
3. Compare the last seen position of `s.charAt(i)` inside our first array with the last seen position of `t.charAt(i)` inside our second array.
4. **The Validation Rule:** If their last recorded positions mismatch, it implies the structural relationship pattern has broken down. Return `false` immediately.
5. If they match, update both tracking slots to the current 1-based position tracker `i + 1`, and move forward.

---

## Visual Understanding

Suppose `s = "foo"`, `t = "bar"` with length 3:

- **Initialization:** `mapS = [0...0]`, `mapT = [0...0]`.
- **i = 0 (`'f'` vs `'b'`):**
  - `mapS['f']` is `0`, `mapT['b']` is `0`. They match!
  - Update states: `mapS['f'] = 1`, `mapT['b'] = 1`.
- **i = 1 (`'o'` vs `'a'`):**
  - `mapS['o']` is `0`, `mapT['a']` is `0`. They match!
  - Update states: `mapS['o'] = 2`, `mapT['a'] = 2`.
- **i = 2 (`'o'` vs `'r'`):**
  - `mapS['o']` is `2` (from index 1). `mapT['r']` is `0`.
  - Mismatch intercepted! `2 != 0`. The character layout is not isomorphic. Return `false`.

The violation is caught instantly without heavy map object tracking.

---

## Partition Variables

Let:

```java
int[] mapS = new int[256];
int[] mapT = new int[256];
```

---

### Border Elements

The preliminary size validation handles mismatched length boundary conditions instantly before initializing structural buffers:

```java
if (s.length() != t.length()) {
    return false;
}
```

---

## Correct Partition Condition

The linear pass loop evaluates the structural position history sync step at each character alignment index:

```java
if (mapS[s.charAt(i)] != mapT[t.charAt(i)]) {
    return false; // Character structural mapping pattern broken
}
mapS[s.charAt(i)] = i + 1;
mapT[t.charAt(i)] = i + 1;
```

---

## Java Solution

```java
class Solution {

    public boolean isIsomorphic(String s, String t) {
        // Fast-fail: Strings of different lengths can never be isomorphic
        if (s == null || t == null || s.length() != t.length()) {
            return false;
        }

        // Parallel tracking buffers mapping extended ASCII index footprints
        int[] mapS = new int[256];
        int[] mapT = new int[256];
        int n = s.length();

        // Single pass scanning loop through both strings simultaneously
        for (int i = 0; i < n; i++) {
            char charS = s.charAt(i);
            char charT = t.charAt(i);

            // If the last seen position indices mismatch, the mapping is broken
            if (mapS[charS] != mapT[charT]) {
                return false;
            }

            // Update both arrays to store the current 1-based position marker
            mapS[charS] = i + 1;
            mapT[charT] = i + 1;
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
| **Dual Buffer Scan** | **O(n)** | **O(1)** | Traverses the string exactly once. The auxiliary array allocation size is strictly fixed at 256 cells regardless of string length. |

---

## Key Insight

Tracking the last-seen structural positions of characters instead of mapping their values explicitly removes object allocations and reference hashing penalties, checking structural patterns in true linear time.

```text
Time  : O(n) single pass execution
Space : O(1) fixed tracking array footprint
```

---

## Similar Problems

1. Word Pattern (290)
2. Group Anagrams (49)
3. Valid Anagram (242)
4. Find the Index of the First Occurrence in a String (28)
5. Replace Words (648)
