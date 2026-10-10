# Number of Substrings Containing All Three Characters

## Problem Statement

Given a string `s` consisting only of the characters `'a'`, `'b'`, and `'c'`, return the **number of substrings** that contain at least one occurrence of all these characters.

---

## Examples

### Example 1

```text
Input: s = "abcba"
Output: 5
```

### Explanation

```text
The substrings containing at least one occurrence of 'a', 'b', and 'c' are:
"abc", "abcb", "abcba", "bcba", and "cba".
```

---

### Example 2

```text
Input: s = "ccabcc"
Output: 8
```

### Explanation

```text
The valid substrings are:
"ccab", "ccabc", "ccabcc", "cab", "cabc", "cabcc", "abc", and "abcc".
```

---

# Key Property: Tracking Last-Seen Pointers

Every time you reach a position `i` in the string where all three characters have appeared at least once, you can form multiple valid substrings that end exactly at index `i`. 

The key insight is to look backward. If the closest occurrences of `'a'`, `'b'`, and `'c'` are tracked, the one that appeared the furthest back in time (the minimum index among them) dictates where a valid window can start. Any substring starting from index `0` up to that minimum index, and ending at `i`, will guarantee inclusion of all three characters.

---

# Intuition

We iterate through the string using a single loop variable `i` acting as the ending index of our substrings:
1. Maintain an array `last` of size 3 initialized to `-1` to store the most recent index where `'a'`, `'b'`, and `'c'` were spotted.
2. For each character `s.charAt(i)`, update its respective position in the `last` array.
3. Find the minimum index stored in the `last` array:
   ```text
   min_idx = min(last['a'], last['b'], last['c'])
   ```
4. If any character hasn't been seen yet, `min_idx` will remain `-1`, adding `0` valid substrings.
5. If all three have been seen (`min_idx >= 0`), then there are exactly `min_idx + 1` valid starting positions (from index `0` to `min_idx`) that form a valid substring ending at `i`. We add this to our total result count.

---

# Visualization

Tracking pointers on `s = "abcba"`:

```text
Indices:  0  1  2  3  4
String:   a  b  c  b  a
Initial: last = [-1, -1, -1], res = 0

1. At i = 0 ('a'): last = [0, -1, -1]. min(-1) -> res += 0
2. At i = 1 ('b'): last = [0, 1, -1].  min(-1) -> res += 0
3. At i = 2 ('c'): last =.   min(0, 1, 2) = 0.
   - Valid substrings ending at index 2 must start at or before index 0.
   - Valid start: index 0 ("abc").
   - res += (0 + 1) -> res = 1.

4. At i = 3 ('b'): last =.   min(0, 3, 2) = 0.
   - Valid starts: index 0 ("abcb").
   - res += (0 + 1) -> res = 2.

5. At i = 4 ('a'): last =.   min(4, 3, 2) = 2.
   - Valid starts: index 0 ("abcba"), index 1 ("bcba"), index 2 ("cba").
   - res += (2 + 1) -> res = 2 + 3 = 5.

Final Answer: 5
```

---

# Java Implementation

```java
class Solution {
    public int numberOfSubstrings(String s) {
        // Array to store the last seen indices of 'a', 'b', and 'c'
        int[] last = {-1, -1, -1};
        int res = 0;
        
        for (int i = 0; i < s.length(); i++) {
            // Update the last seen index for the current character
            last[s.charAt(i) - 'a'] = i;
            
            // The number of valid substrings ending at index i is determined 
            // by the minimum last-seen index of the three characters.
            res += Math.min(last[0], Math.min(last[1], last[2])) + 1;
        }
        
        return res;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the length of the string `s`. The algorithm scans the string in a single pass. Inside the loop, finding the minimum value among three elements takes constant O(1) time.
* **Space Complexity:** O(1) auxiliary space. The `last` array uses a fixed length of 3 integers regardless of how long the input string grows.
