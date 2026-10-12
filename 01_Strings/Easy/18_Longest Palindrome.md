# Longest Palindrome

## Problem Statement

Given a string `s` which consists of lowercase or uppercase letters, return *the length of the **longest palindrome** that can be built with those letters*.

Letters are **case sensitive**, for example, `"Aa"` is not considered a palindrome here.

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
s = "abccccdd"
```

**Output**

```java
7
```

**Explanation**

One longest palindrome that can be built is `"dccaccd"`, whose length is 7.

---

### Example 2

**Input**

```java
s = "a"
```

**Output**

```java
1
```

**Explanation**

The longest palindrome that can be built is `"a"`, whose length is 1.

---

### Example 3

**Input**

```java
s = "AAbb"
```

**Output**

```java
4
```

**Explanation**

We can build `"bAAb"` or `"AbbA"`, both of which have a length of 4.

---

## Brute Force Approach

Generate all possible unique permutations of the given characters, check which permutations form valid palindromes, and return the maximum length found.

### Steps

1. Use backtracking to find every unique character arrangement permutation.
2. For each arrangement, use a two-pointer technique to check if it reads the same forward and backward.
3. Track and return the maximum length among the valid palindromes.

### Complexity

```text
Time Complexity: O(n!) // Due to checking every factorial combination
Space Complexity: O(n) for the recursion stack depth
```

This combinatorial search scales exponentially, hitting a Time Limit Exceeded (TLE) error almost immediately. The problem does not ask us to *build* the string, only to find its maximum potential *length*. We can solve this cleanly in a single pass by analyzing character counts.

---

# Optimal Approach: Greedy Frequency Pairing

## Key Idea

A palindrome reads identically from left to right because its characters are symmetrically mirrored around a central axis. This means every character in a palindrome must appear in **matching pairs** (an even frequency). The only exception is the absolute center of the string, which can host **at most one single unpaired character** (an odd frequency).

We can determine the maximum length dynamically using a greedy frequency tracking strategy:
1. **Tally Letter Frequencies:** Count the occurrences of each letter. Since the string is case-sensitive and contains both uppercase and lowercase English characters, we can use an integer tracking array of size 128 (covering the standard ASCII spectrum) or a `HashSet` to catch mismatched characters.
2. **Greedy Collection Pass:** Walk through the characters of the string.
   - If a character is not in our `HashSet`, add it. This means we are looking for a matching partner.
   - If the character is *already* in our `HashSet`, we have successfully found a pair! We increment our palindrome length counter by `2`, and remove the character from the set.
3. **The Center Odd-Element Bonus:** After checking the entire string, if the `HashSet` is not empty, it means there are some characters left over without a matching partner. We can greedily take exactly **one** of these unpaired characters to sit at the absolute center of our palindrome, adding `1` to our total length.

---

## Visual Understanding

Suppose `s = "abccccdd"`:

Initialize `length = 0`, `HashSet set = []`. Let's process characters from left to right:
- `'a'` \(\implies\) Not in set. Add it. `set = ['a']`.
- `'b'` \(\implies\) Not in set. Add it. `set = ['a', 'b']`.
- `'c'` \(\implies\) Not in set. Add it. `set = ['a', 'b', 'c']`.
- `'c'` \(\implies\) Already in set! Pair found. `length += 2` (2). Remove `'c'`. `set = ['a', 'b']`.
- `'c'` \(\implies\) Not in set. Add it. `set = ['a', 'b', 'c']`.
- `'c'` \(\implies\) Already in set! Pair found. `length += 2` (4). Remove `'c'`. `set = ['a', 'b']`.
- `'d'` \(\implies\) Not in set. Add it. `set = ['a', 'b', 'd']`.
- `'d'` \(\implies\) Already in set! Pair found. `length += 2` (6). Remove `'d'`. `set = ['a', 'b']`.

**Final check:** Set is not empty (`['a', 'b']` are left over). We add `1` as a central axis bonus. Total length = `6 + 1 = 7`.

---

## Partition Variables

Let:

```java
Set<Character> set = new HashSet<>();
int length = 0;
```

---

### Border Elements

The character stream loops linearly through all elements from index 0 to `s.length() - 1` with zero overflow risks:

```java
for (int i = 0; i < s.length(); i++)
```

---

## Correct Partition Condition

The twin tracking condition adds length points and clears elements instantly upon discovering any balanced duplicate pair:

```java
if (set.contains(c)) {
    length += 2;
    set.remove(c);
} else {
    set.add(c);
}
```

---

## Java Solution

```java
import java.util.HashSet;
import java.util.Set;

class Solution {

    public int longestPalindrome(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        Set<Character> unmatchedChars = new HashSet<>();
        int length = 0;

        // Step 1: Single pass to extract all matched character pairs greedily
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);

            // If we find a matching character partner, add 2 to our length
            if (unmatchedChars.contains(c)) {
                length += 2;
                unmatchedChars.remove(c); // Clear the pair from our tracker
            } 
            // Otherwise, hold onto this character until its twin is found
            else {
                unmatchedChars.add(c);
            }
        }

        // Step 2: If any unmatched characters remain, one can sit in the center
        if (!unmatchedChars.isEmpty()) {
            length += 1;
        }

        return length;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Greedy Pairing Scan** | **O(n)** | **O(1)** | Traverses the string exactly once. The auxiliary space is bounded by the alphabet set size (at most 52 unique English characters), which represents a constant O(1) memory footprint. |

---

## Key Insight

Focusing on character pair combinations rather than string order requirements converts a complex permutation puzzle into a clean, single-pass frequency collection tracking loop.

```text
Time  : O(n) single pass forward execution
Space : O(1) absolute constant auxiliary tracking memory
```

---

## Similar Problems

1. Valid Palindrome (125)
2. Longest Palindromic Substring (5)
3. Palindrome Permutation (266)
4. Longest Palindromic Subsequence (516)
5. Group Anagrams (49)
