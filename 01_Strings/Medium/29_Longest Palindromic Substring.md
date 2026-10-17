# Longest Palindromic Substring

**LeetCode 5 - Longest Palindromic Substring**

## Problem Statement

Given a string `s`, return the **longest palindromic substring** in `s`.

A **string** is a palindrome if it reads the same backward as forward. A **substring** is a contiguous sequence of characters within a string.

---

## Example

```java
String s = "babad";

Solution sol = new Solution();
sol.longestPalindrome(s); // Returns "bab" (or "aba" is also valid)
```

**Explanation:** Both `"bab"` and `"aba"` are valid palindromic substrings of length 3. The algorithm returns either of them.

---

# Intuition

A naive brute force approach checks every possible substring to see if it is a palindrome. With O(N²) substrings and an O(N) validation check, this takes O(N³) time — completely unviable for long strings.

We can optimize this by reversing our point of view: instead of picking two boundaries and checking the middle, we pick a **center** point and **expand outwards** as long as the matching characters on the left and right remain identical. 

The main catch is handling structural symmetry:
1. **Odd-length Palindromes:** Center is a single character (e.g., `"aba"`, center is `'b'`).
2. **Even-length Palindromes:** Center is the gap between two identical adjacent characters (e.g., `"cbbc"`, center is between the two `'b'`s).

By checking both odd and even center configurations across all characters in the string, we find the longest palindrome in linear-quadratic time without needing any secondary memory arrays.

---

# Approach: Expand Around Center Optimization

## Data Structure

```java
int start, int end
```
Two global or structural tracking boundaries used to capture the absolute window coordinates of the longest palindromic substring found so far.

---

## Algorithm Steps

1. Create a helper function `expandFromCenter(s, left, right)` that returns the length of the palindrome formed by expanding outward from the given boundary indices.
2. Inside `expandFromCenter`:
   - While `left >= 0` and `right < s.length()` and `s.charAt(left) == s.charAt(right)`:
     - Decrement `left` and increment `right`.
   - Once the loop breaks, return the length of the valid segment: `right - left - 1`.
3. In the main function, iterate through every index `i` from `0` to `N - 1`:
   - Calculate odd-length palindrome length: `len1 = expandFromCenter(s, i, i)`.
   - Calculate even-length palindrome length: `len2 = expandFromCenter(s, i, i + 1)`.
   - Take the maximum of both lengths: `len = Math.max(len1, len2)`.
4. If `len` is greater than the current recorded window size (`end - start`), recalculate the new `start` and `end` indices using midpoints.
5. Return `s.substring(start, end + 1)`.

---

# Dry Run

### Input
```java
s = "cabad"
```

### Execution Trace

- **Initialization:** `start = 0`, `end = 0`

| Index `i` | Odd Center (`i, i`) Expansion | Even Center (`i, i+1`) Expansion | Max Length `len` | Condition (`len > end - start + 1`)? | Boundary Update (`start`, `end`) |
|---|---|---|---|---|---|
| `0` (`c`) | `"c"` (len 1) | `""` (len 0) | `1` | No (`1 > 1` is false) | `(0, 0)` |
| `1` (`a`) | `"aba"` (len 3) | `""` (len 0) | `3` | ✅ Yes (`3 > 1`) | `start = 1 - (3-1)/2 = 0`<br>`end = 1 + 3/2 = 2` `-> (0, 2)` |
| `2` (`b`) | `"aba"` (len 3) | `""` (len 0) | `3` | No (`3 > 3` is false) | `(0, 2)` |
| `3` (`a`) | `"bad"` fails → len 1 | `""` (len 0) | `1` | No | `(0, 2)` |
| `4` (`d`) | `"d"` (len 1) | `""` (len 0) | `1` | No | `(0, 2)` |

### Result
```java
s.substring(0, 3) -> "aba"
```

---

# Java Solution

```java
class Solution {
    private int start = 0;
    private int maxLength = 0;

    public String longestPalindrome(String s) {
        if (s == null || s.length() < 1) {
            return "";
        }

        int n = s.length();
        for (int i = 0; i < n; i++) {
            // Case 1: Odd length palindromes (e.g., "aba", center is 'b')
            expandFromCenter(s, i, i);
            
            // Case 2: Even length palindromes (e.g., "cbbc", center is between 'b' and 'b')
            expandFromCenter(s, i, i + 1);
        }

        return s.substring(start, start + maxLength);
    }

    private void expandFromCenter(String s, int left, int right) {
        // Expand outward as long as it's a valid palindrome boundary
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            left--;
            right++;
        }

        // The loop terminates after boundaries step one index past the valid palindrome string
        // Actual length = (right - 1) - (left + 1) + 1 = right - left - 1
        int currentLength = right - left - 1;

        if (currentLength > maxLength) {
            maxLength = currentLength;
            // The first valid palindrome index is left + 1
            start = left + 1;
        }
    }
}
```

---

# Complexity Analysis

### Time Complexity

```java
O(N²)
```
There are 2N - 1 possible center points across a string of length N. For each center point, the algorithm expands outward, doing at most O(N) operations in the worst case (e.g., a string consisting entirely of the same characters like `"aaaaa"`). This gives a tight upper bound of \(O(N \cdot N) = O(N^2)\).

### Space Complexity

```java
O(1)
```
Unlike Dynamic Programming versions of this solution that create an O(N²) tracking grid matrix, this simulation relies entirely on boundary index math pointers, maintaining a purely constant footprint.

---

# Comparison of Approaches

| Approach | Time Complexity | Space Complexity | Pros / Cons |
|---|---|---|---|
| **Brute Force** | O(N³) | O(1) | Simple, but completely unusable for large strings. |
| **Dynamic Programming** | O(N²) | O(N²) | Avoids redundant inner checks, but consumes massive memory overhead. |
| **Expand Around Center** | O(N²) | O(1) | Highly optimized time with **zero auxiliary memory** allocation. |
| **Manacher's Algorithm** | O(N) | O(N) | **Linear time complexity**, but highly complex to implement in standard interviews. |

---

# Key Takeaways

- **Dual-Center Exploration:** Never forget to run center checks for **both** `(i, i)` and `(i, i + 1)` combinations to handle alternating parity constraints seamlessly.
- **Post-Loop Arithmetic:** After a symmetric expansion loop ends, the pointers reside exactly one step outside the structural match boundary. The valid window length formula translates directly to `right - left - 1`.
- Core concepts:
  - Symmetric String Inversion
  - Center Expansion Strategy
  - Window Slice Index Arithmetic
- Expected interview preference: **Expand Around Center Approach** (or Manacher's Algorithm for hard-level target positions).
