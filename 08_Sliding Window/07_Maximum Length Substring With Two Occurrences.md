# Maximum Length Substring With Two Occurrences

## Problem Statement

Given a string `s`, return the **maximum length of a substring** such that it contains at most two occurrences of each character.

---

## Examples

### Example 1

```text
Input: s = "bcbbbcba"
Output: 4
```

### Explanation

```text
The following substring has a length of 4 and contains at most two occurrences of each character: "bcbbbcba".
Other valid options of length 4 include "cbbc" and "bcba".
```

---

### Example 2

```text
Input: s = "aaaa"
Output: 2
```

### Explanation

```text
The following substring has a length of 2 and contains at most two occurrences of each character: "aaaa".
```

---

# Key Concept: Sliding Window

```text
[ Window Start (l) ... Window End (r) ]
```

Instead of generating all possible substrings, we can maintain a dynamic valid window of characters using two pointers: `l` (left boundary) and `r` (right boundary). As `r` expands the window forward, `l` adjusts to shrink the window from the left whenever any character's count breaches the permitted threshold.

---

# Intuition

We use a fixed-size frequency array `count` of size 26 to keep track of the frequency of lowercase English letters inside our current window.

At every step as `r` moves forward:

### Case 1: Character count remains valid
We increment the count of the character at `s.charAt(r)`. If `count[s.charAt(r) - 'a'] <= 2`, the window remains valid. We calculate the size of the current window as `r - l + 1`, update our maximum length (`maxi`), and move `r` forward.

---

### Case 2: Character count exceeds the limit
If incrementing the current character pushes its count to `3`, the window becomes invalid. 

To restore validity, we contract the window from the left by advancing the `l` pointer. As characters leave the window, we decrement their frequency counts (`count[s.charAt(l) - 'a']--`). We keep moving `l` forward until the count of the violating character at `r` drops back down to `2`.

---

# Visualization

Tracking pointers on `s = "aaaa"`:

```text
1. Initial State: l = 0, r = 0, maxi = 0, count = [0, 0, ...]
2. Process r = 0 ('a'): count['a'] = 1. Valid. maxi = max(0, 0-0+1) = 1. r updates to 1.
3. Process r = 1 ('a'): count['a'] = 2. Valid. maxi = max(1, 1-0+1) = 2. r updates to 2.
4. Process r = 2 ('a'): count['a'] = 3. Invalid! (count > 2)
   - Shrink from left: l is at 0 ('a'). Decrement count['a'] to 2. l increments to 1.
   - Loop exits because count['a'] is no longer > 2.
   - Window size = 2 - 1 + 1 = 2. maxi = max(2, 2) = 2. r updates to 3.

Window tracking visual sequence:
r=0: [a]aaa (len=1)
r=1: [aa]aa (len=2)
r=2: a[aa]a (len=2) -> 'a' reached 3, 'l' moved forward to index 1
```

---

# Java Implementation

```java
class Solution {
    public int maximumLengthSubstring(String s) {
        // Fixed array of size 26 for character counts (a-z)
        int[] count = new int[26];
        int maxi = 0;
        int l = 0;
        int r = 0, n = s.length();
        
        while (r < n) {
            // Include the current character in the window frequency tracking
            count[s.charAt(r) - 'a']++;
            
            // If the current character count exceeds 2, shrink the window from the left
            while (count[s.charAt(r) - 'a'] > 2) {
                count[s.charAt(l) - 'a']--;
                l++;
            }
            
            // Track the maximum valid window size found so far
            maxi = Math.max(maxi, r - l + 1);
            r++;
        }
        
        return maxi;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the length of the string `s`. Each character is processed at most twice—once by the right pointer `r` expanding the window and at most once by the left pointer `l` contracting it.
* **Space Complexity:** O(1) auxiliary space. We use a fixed-size frequency array of 26 integers to track the character counts, which remains constant regardless of how long the input string is.
