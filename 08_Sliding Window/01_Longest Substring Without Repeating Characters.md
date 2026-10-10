# Longest Substring Without Repeating Characters

## Problem Statement

Given a string `s`, find the length of the **longest substring** without repeating characters.

### Definition

A **substring** is a contiguous sequence of characters within a string. This is distinct from a **subsequence**, which can be formed by deleting some or no characters from the original string without changing the order of the remaining characters.

---

## Examples

### Example 1

```text
Input: s = "abcabcbb"
Output: 3
```

Because:

```text
The answer is "abc", with a length of 3. 
Note that "bca" and "cab" are also valid substrings of length 3.
```

---

### Example 2

```text
Input: s = "bbbbb"
Output: 1
```

Because:

```text
The answer is "b", with a length of 1.
```

---

### Example 3

```text
Input: s = "pwwkew"
Output: 3
```

Because:

```text
The answer is "wke", with a length of 3. 
Notice that "pwke" is a subsequence and not a substring because its characters are not contiguous.
```

---

# Key Concept: Sliding Window

```text
[ Window Start (l) ... Window End (r) ]
```

Instead of generating every possible substring, we can track a dynamic valid window of unique characters using two pointers: `l` (left boundary) and `r` (right boundary). As `r` expands the window forward, `l` adjusts to shrink the window whenever a duplicate character is encountered.

---

# Intuition

We can optimize the sliding window approach by using a **HashMap** to cache the *most recent index* where each character was seen. 

At every step as `r` moves forward:

### Case 1: New character or unique inside current window
If the character at `r` has not been seen before, or its last seen index is outside the current window boundary (`index < l`), the window remains valid. We calculate the size of the window as `r - l + 1` and update our maximum length.

---

### Case 2: Duplicate character detected inside current window
If the character at `r` matches a character already tracked inside our current window boundary, it creates a duplication error. 

Instead of moving `l` forward step-by-step, we can jump `l` instantly to the right of the old duplicate character's index position:
```text
l = Math.max(l, last_seen_index + 1)
```
*(Note: Using `Math.max` ensures that `l` never moves backward when reading cached historical values from outside the active window, such as in strings like `"abba"`).*

---

# Visualization

Tracking pointers on `s = "abcabcbb"`:

```text
1. Initial State: l = 0, r = 0, maxi = 0, hm = {}
2. Process 'a': Unique. maxi = max(0, 0-0+1) = 1. hm = {'a': 0}. r updates to 1.
3. Process 'b': Unique. maxi = max(1, 1-0+1) = 2. hm = {'a': 0, 'b': 1}. r updates to 2.
4. Process 'c': Unique. maxi = max(2, 2-0+1) = 3. hm = {'a': 0, 'b': 1, 'c': 2}. r updates to 3.

5. Process 'a' at index 3: Duplicate!
   - Cached index for 'a' is 0. 
   - Shift left bound: l = max(0, 0 + 1) = 1.
   - Update map: hm = {'a': 3, 'b': 1, 'c': 2}.
   - Window size = 3 - 1 + 1 = 3. maxi = max(3, 3) = 3. r updates to 4.

Window positions visual sequence:
r=0: [a]bcabcbb  (len=1)
r=1: [ab]cabcbb (len=2)
r=2: [abc]abcbb (len=3)
r=3: a[bca]bcbb (len=3) -> 'a' repeated, 'l' jumped to index 1
```

---

# Java Implementation

```java
import java.util.HashMap;

class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length();
        int l = 0, r = 0;
        int maxi = 0;
        
        // HashMap to store the most recent index of each character
        HashMap<Character, Integer> hm = new HashMap<>();
        
        while (r < n) {
            char m = s.charAt(r);
            
            // If the character is already present in the map, 
            // move the left pointer to the right of the previous occurrence
            if (hm.containsKey(m)) {
                l = Math.max(l, hm.get(m) + 1); // Math.max handles cases like "abba"
            }
            
            // Update the maximum length found so far
            maxi = Math.max(maxi, r - l + 1);
            
            // Update or insert the current character's index position
            hm.put(m, r);
            r++;
        }
        
        return maxi;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the length of the string `s`. The right pointer `r` traverses the string exactly once from beginning to end. Map lookups and updates run in O(1) time complexity.
* **Space Complexity:** O(min(M, N)) auxiliary space where N is the size of the string and M is the size of the charset/vocabulary. The HashMap stores up to a maximum number of unique characters encountered.
