# Ransom Note

## Problem Statement

Given two strings `ransomNote` and `magazine`, return `true` *if `ransomNote` can be constructed by using the letters from `magazine` and `false` otherwise*.

Each letter in `magazine` can only be used once in `ransomNote`.

The overall runtime complexity must be strictly bounded by:

```text
O(n + m)
```

*(where n is the length of ransomNote and m is the length of magazine)*

---

## Examples

### Example 1

**Input**

```java
ransomNote = "a"
magazine = "b"
```

**Output**

```java
false
```

---

### Example 2

**Input**

```java
ransomNote = "aa"
magazine = "ab"
```

**Output**

```java
false
```

**Explanation**

`magazine` only contains one `'a'`, but `ransomNote` requires two.

---

### Example 3

**Input**

```java
ransomNote = "aa"
magazine = "aab"
```

**Output**

```java
true
```

---

## Brute Force Approach

For every character in `ransomNote`, scan `magazine` linearly to see if it exists. If found, remove it from `magazine` so it cannot be reused.

### Steps

1. Convert `magazine` into a mutable structure like a `StringBuilder`.
2. Iterate through each character `c` of `ransomNote`.
3. Search for `c` inside the `magazine` buffer using `.indexOf()`.
4. If the character is missing, return `false`.
5. If found, delete that specific character from the buffer and continue.
6. If the loop completes successfully, return `true`.

### Complexity

```text
Time Complexity: O(n * m) // due to searching and shrinking characters repeatedly
Space Complexity: O(m) to store the mutable string copy
```

Repeatedly searching and modifying string buffers introduces an unnecessary quadratic runtime bottleneck. Since the problem contains only lowercase English letters, we can optimize this to a pure linear scan using a fixed-size character frequency map.

---

# Optimal Approach: Fixed-Capacity Alphabet Frequency Array

## Key Idea

To verify if `magazine` has enough characters to construct `ransomNote`, we must ensure that the frequency of each unique character required by the note does not exceed the total available supply in the magazine.

We can track this resource allocation efficiently using an integer array of size 26:
1. **Inventory Collection:** Iterate through the `magazine` string from left to right. For each character, calculate its offset relative to `'a'` and increment its corresponding bucket value in our frequency array.
2. **Resource Consumption:** Iterate through the `ransomNote` string. For each character, decrement its corresponding bucket tally inside the frequency array.
3. **The Shortage Condition:** If a bucket count drops **strictly below `0`**, it means `ransomNote` requested more instances of that character than `magazine` supplied. We intercept this immediately and return `false`.

---

## Visual Understanding

Suppose `ransomNote = "aa"` and `magazine = "aab"`:

- **Step 1 (Populate Inventory from `magazine`):**
  - Array logs: `counts['a'-'a'] = 2`, `counts['b'-'a'] = 1`.
- **Step 2 (Consume Inventory for `ransomNote`):**
  - **i = 0 (`'a'`):** Decrement `counts['a'-'a']` from 2 to 1. Check: `1 >= 0` (Valid).
  - **i = 1 (`'a'`):** Decrement `counts['a'-'a']` from 1 to 0. Check: `0 >= 0` (Valid).

The loop finishes without hitting a deficit, validating the request as `true`.

---

## Partition Variables

Let:

```java
int[] counts = new int;
```

---

### Border Elements

An early optimization boundary checks whether the note's absolute length exceeds the magazine's supply capacity, returning a fast fail instantly if triggered:

```java
if (ransomNote.length() > magazine.length()) {
    return false;
}
```

---

## Correct Partition Condition

The consumption pass decreases structural cell counts, flagging character deficits the instant a bucket value falls below zero:

```java
counts[ransomNote.charAt(i) - 'a']--;
if (counts[ransomNote.charAt(i) - 'a'] < 0) {
    return false; 
}
```

---

## Java Solution

```java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        if (ransomNote == null || magazine == null || ransomNote.length() > magazine.length()) {
            return false;
        }
        int[] freq=new int[26];
        for(char c:magazine.toCharArray())
        {
            freq[c-'a']++;
        }
        for(char c:ransomNote.toCharArray())
        {
            freq[c-'a']--;
            if(freq[c-'a'] < 0)
                return false;
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
| **Frequency Tally** | **O(n + m)** | **O(1)** | Processes each string linearly. The tracking array allocation size is strictly fixed at 26 cells regardless of string length. |

---

## Key Insight

Mapping characters onto a fixed-size frequency index avoids the overhead of mutable string lookups, checking inventory distribution boundaries in pure linear time.

```text
Time  : O(n + m) linear execution pass
Space : O(1) absolute constant memory tracking footprint
```

---

## Similar Problems

1. Valid Anagram (242)
2. Group Anagrams (49)
3. Isomorphic Strings (205)
4. Word Pattern (290)
5. Find All Anagrams in a String (438)
