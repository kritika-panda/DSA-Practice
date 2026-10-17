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
There are 2N - 1 possible center points across a string of length N. For each center point, the algorithm expands outward, doing at most O(N) operations in the worst case (e.g., a string consisting entirely of the same characters like `"aaaaa"`). This gives a tight upper bound of (O(N cdot N) = O(N^2)).

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

# Intuition

The "Expand Around Center" approach takes (O(N^2)) because it treats every center independently, completely discarding information gained from previously discovered palindromes. 

To achieve (O(N)) time, we use **Manacher's Algorithm**. The core idea is to mirror symmetry. If a large palindrome is centered at index `C`, the property of any substring inside its left wing is mirrored exactly on its right wing. If we already know the palindrome radius at a left-wing index `i_mirror`, we can use it to instantly compute or bound the palindrome radius at the corresponding right-wing index `i`.

To bypass the edge cases caused by odd and even lengths, we transform the string first by interleaving it with a special delimiter character (e.g., `#`). For example, `"aba"` becomes `"#a#b#a#"`. Every palindrome in this modified string has an odd length, allowing us to evaluate all centers uniformly.

---

# Approach: Manacher's Algorithm

## Data Structures

```java
int[] P
```
An array where `P[i]` stores the radius of the longest palindrome centered at index `i` in the modified string.

```java
int C, int R
```
Trackers for the **Center** (`C`) and the **Right Boundary** (`R`) of the furthest-reaching palindrome found so far (`R = C + P[C]`).

---

## Algorithm Steps

1. **Preprocess String:** Insert `#` between characters and pad with special boundary markers `^` and `$` to avoid manual bounds checking (e.g., `"aba"` → `"^#a#b#a#$"`).
2. Initialize `P` array, `C = 0`, and `R = 0`.
3. Iterate through each index `i` from `1` to `N - 2`:
   - Find the mirror of `i` relative to `C`: `i_mirror = 2*C - i`.
   - If `i < R` (inside the current boundary), initialize `P[i] = Math.min(R - i, P[i_mirror])`.
   - Else, initialize `P[i] = 0`.
   - Attempt to expand the palindrome centered at `i` further outward.
   - If the newly expanded palindrome extends past `R`, update our trackers: `C = i` and `R = i + P[i]`.
4. Find the maximum value in `P`. Map its index back to the original string coordinates and extract the substring.

---

# Dry Run

### Input
Original: `"aba"` → Transformed: `"^#a#b#a#$"`

### Execution Trace (Key Iterations)

- **Initial State:** `C = 0`, `R = 0`, `P = [0, 0, 0, ...]`

| Index `i` | Char | `i < R`? | Initial `P[i]` | Expansion Loop Result (`P[i]`) | Boundary Check / Update |
|---|---|---|---|---|---|
| `1` | `#` | No | `0` | `0` (`^ != a`) | No update |
| `2` | `a` | No | `0` | `1` (`# == #`) | `R` extends to `3`. `C = 2`, `R = 3` |
| `3` | `#` | Yes | `min(3-3, P[1]) = 0` | `0` (`a != b`) | No update |
| `4` | `b` | No | `0` | `3` (`#==#, a==a, #==#`) | `R` extends to `7`. `C = 4`, `R = 7` |
| `5` | `#` | Yes | `min(7-5, P[3]) = 0` | `0` (`b != a`) | No update |
| `6` | `a` | Yes | `min(7-6, P[2]) = 1` | `1` (stops at boundary) | No update |

### Result
Max radius found is `3` at index `4` (`b`). Max length in original string = `3`. Substring extracted: `"aba"`.

---

# Java Solution

```java
class Solution {
    public String longestPalindrome(String s) {
        if (s == null || s.length() == 0) return "";

        // Step 1: Preprocess string to handle odd/even lengths uniformly
        StringBuilder sb = new StringBuilder();
        sb.append("^");
        for (int i = 0; i < s.length(); i++) {
            sb.append("#").append(s.charAt(i));
        }
        sb.append("#$");
        String T = sb.toString();

        int n = T.length();
        int[] P = new int[n];
        int C = 0, R = 0;

        for (int i = 1; i < n - 1; i++) {
            int iMirror = 2 * C - i; // Mirror of i relative to Center C

            // If within right boundary, mirror radius can be reused
            if (R > i) {
                P[i] = Math.min(R - i, P[iMirror]);
            } else {
                P[i] = 0;
            }

            // Attempt to expand palindrome centered at i
            while (T.charAt(i + 1 + P[i]) == T.charAt(i - 1 - P[i])) {
                P[i]++;
            }

            // If expanded past R, adjust center and right boundary
            if (i + P[i] > R) {
                C = i;
                R = i + P[i];
            }
        }

        // Step 2: Find the maximum radius and its center index
        int maxLen = 0;
        int centerIndex = 0;
        for (int i = 1; i < n - 1; i++) {
            if (P[i] > maxLen) {
                maxLen = P[i];
                centerIndex = i;
            }
        }

        // Step 3: Map back to the original string coordinates
        int start = (centerIndex - 1 - maxLen) / 2;
        return s.substring(start, start + maxLen);
    }
}
```

---

# Complexity Analysis

### Time Complexity

```java
O(N)
```
Although there is a nested loop execution, the inner `while` loop only increments when a palindrome expands beyond the current right boundary `R`. Because `R` moves monotonically from `0` to `2N` and never decreases, the total number of expanding checks across the entire runtime is strictly bounded by 2N. This results in a guaranteed linear time analysis.

### Space Complexity

```java
O(N)
```
Auxiliary space is required to store the modified string array configuration and the integer radius tracker array `P`, both of which scale linearly with the length of the string input.

---

# Comparison of Approaches

| Approach | Time Complexity | Space Complexity | Pros / Cons |
|---|---|---|---|
| **Expand Around Center** | (O(N^2)) | (O(1)) | Simple to write, but hits performance barriers on very large strings. |
| **Manacher's Algorithm** | (O(N)) | (O(N)) | **Strictly optimal linear runtime**, but conceptually advanced and hard to debug under stress. |

---

# Key Takeaways

- **Symmetric Repurposing:** Manacher's Algorithm is the ultimate example of skipping computations by tracking mirrored coordinates inside validated dynamic programming bounds.
- **Unified String Padding:** Inserting interleaving delimiters transforms complex dual-parity conditions into a single odd-length structural sequence problem.
- Core concepts:
  - String Interleaving Transformations
  - Boundary-Guided Mirroring
  - Monotonic Pointer Bounds Checking
