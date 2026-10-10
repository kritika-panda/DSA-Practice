# Minimum Window Subsequence

## Problem Statement

Given two strings `s1` and `s2`, find the **smallest substring** in `s1` such that `s2` appears as a **subsequence** within that substring.

### Definition

* **Subsequence:** The characters of `s2` must appear in the same relative order within the substring of `s1`, but they do not need to be contiguous.
* **Tie-Breaking Rule:** If there are multiple valid substrings of the same minimum length, return the one that appears **earliest / leftmost** in `s1`. If no such substring exists, return an empty string `""`.

---

## Examples

### Example 1

```text
Input: s1 = "geeksforgeeks", s2 = "eksrg"
Output: "eksforg"
```

### Explanation

```text
The substring "eksforg" satisfies all conditions. 
"eksrg" is a subsequence within it, and it is the smallest and leftmost option available.
```

---

### Example 2

```text
Input: s1 = "abcdebdde", s2 = "bde" 
Output: "bcde"
```

### Explanation

```text
Both "bcde" and "bdde" are valid substrings of s1 where s2 occurs as a subsequence. 
Since they both have a length of 4, "bcde" is selected because it appears earlier in s1.
```

---

### Example 3

```text
Input: s1 = "ad", s2 = "b" 
Output: ""
```

### Explanation

```text
There is no substring in s1 where 'b' occurs, so an empty string is returned.
```

---

# Key Concept: Two-Pointer Scan with Backtracking Optimization

```text
Phase 1 (Forward Scan):  s1[i] == s2[0] ---> Look for match indices forward ---> Subsequence complete
Phase 2 (Backtrack Scan): Reverse look back from end index to optimize the true window start
```

Because we are checking for a **subsequence** rather than a substring, a standard sliding window frequency counter will not work. We must enforce strict relative character ordering. We can achieve this by searching forward until a complete match is found, and then **backtracking in reverse** from the termination point to trim away any unnecessary characters from the left edge.

---

# Intuition

We iterate through `s1` using an index pointer `i`:

### Phase 1: Forward Scan
Whenever `s1.charAt(i)` matches the first character of `s2` (`s2.charAt(0)`), it represents a potential starting point. We spawn a temporary pointer `j = i` and a string `s2` tracker index `k = 0`. We advance `j` forward through `s1`, incrementing `k` only when characters match.

---

### Phase 2: Backtrack and Minimize
If `k == m`, it means the entire sequence `s2` was found within `s1[i...j-1]`. However, the window `[i, j-1]` might be wider than necessary if duplicate characters appeared early on. 

To find the true optimized start boundary:
1. We start a reverse lookup from the end position `end = j - 1` and trace `s2` backward from `k = m - 1`.
2. As we decrement `end`, whenever `s1.charAt(end) == s2.charAt(k)`, we decrement `k`.
3. The moment `k` drops below `0`, the pointer `end` rests at the **most optimal (tightest) starting index** for this specific match window.
4. We calculate its length (`j - end`), check if it's strictly shorter than our running `minLen`, and update our tracking coordinates accordingly.

---

# Java Implementation

```java
class Solution {
    public String minWindow(String s1, String s2) {
        int n = s1.length(), m = s2.length();
        int minLen = Integer.MAX_VALUE;
        int start = -1;

        // Iterate through s1 to find potential starting points
        for (int i = 0; i < n; i++) {
            // A window can only start if the character matches the beginning of s2
            if (s1.charAt(i) == s2.charAt(0)) {
                int j = i, k = 0;

                // 1. Forward scan to confirm the subsequence exists
                while (j < n && k < m) {
                    if (s1.charAt(j) == s2.charAt(k)) {
                        k++;
                    }
                    j++;
                }

                // If a full subsequence match is found
                if (k == m) {
                    int end = j - 1;
                    k = m - 1;
                    
                    // 2. Backtrack from the end position to find the tightest left boundary
                    while (end >= i) {
                        if (s1.charAt(end) == s2.charAt(k)) {
                            k--;
                            if (k < 0) {
                                break;
                            }
                        }
                        end--;
                    }

                    // 3. Update the global minimum window tracking variables
                    // Using strictly less than (<) naturally preserves the leftmost requirement
                    if (j - end < minLen) {
                        minLen = j - end;
                        start = end;
                    }
                }
            }
        }

        // Return the smallest substring, or empty string if no valid window was found
        return start == -1 ? "" : s1.substring(start, start + minLen);
    }
}
```

### Complexity Analysis

* **Time Complexity:** \(O(N \times M)\) where \(N\) is the length of string `s1` and \(M\) is the length of string `s2`. For every character in `s1` that matches `s2[0]`, the algorithm performs a linear sweep and backtrack step bounded by the length of `s2`. 
* **Space Complexity:** \(O(1)\) auxiliary space. The structural updates execute entirely in-place, using only a few primitive integer iteration pointers.
