# Shortest Palindrome

## Problem Statement

You are given a string `s`. You can convert `s` to a **palindrome** by adding characters in front of it.

Return *the shortest palindrome you can find by performing this transformation*.

The overall run time complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "aacecaaa"
```

**Output**

```java
"aaacecaaa"
```

**Explanation**

We can append a single `'a'` in front of `s` to form `"aaacecaaa"`, which reads the exact same forward and backward.

---

### Example 2

**Input**

```java
s = "abcd"
```

**Output**

```java
"dcbabcd"
```

**Explanation**

The shortest valid extension requires adding the reversed remaining suffix `"dcb"` to the front.

---

### Example 3

**Input**

```java
s = ""
```

**Output**

```java
""
```

---

## Brute Force Approach

Reverse the string `s`, and check every possible prefix slice of the reversed string to see if appending it to the front of `s` forms a valid palindrome.

### Steps

1. Create a reversed copy of the string: `rev = new StringBuilder(s).reverse().toString()`.
2. Run a loop with an index pointer `i` from `0` up to `n`.
3. Check if `s` starts with the suffix substring of `rev` from index `i`. 
4. The first position `i` where `s.startsWith(rev.substring(i))` evaluates to true tells us that the remaining characters of `s` form the longest palindromic prefix.
5. Take the non-matching prefix part of `rev` from index `0` to `i`, append it to the front of `s`, and return the result.

### Complexity

```text
Time Complexity: O(n^2) // due to repetitive substring extraction and string matches
Space Complexity: O(n) to store the reversed string copies
```

This quadratic check becomes highly inefficient for large strings. We can optimize it to a pure linear time approach by adapting the **LPS (Longest Prefix Suffix) array** generation technique from the **KMP algorithm**.

---

## Optimal Approach: KMP LPS Array Processing

## Key Idea

The problem can be rephrased as finding the **longest palindromic prefix** of `s`. Once we find this longest palindromic prefix, we can take the remaining suffix, reverse it, and prepend it to the front of `s`.

We can find the longest palindromic prefix in linear time by utilizing the **LPS array** lookup properties:
1. Construct a temporary combined string `temp = s + "#" + reverse(s)`. The special separator character `#` ensures that the prefix pattern matching does not leak past the boundary length of `s`.
2. Compute the LPS array for this combined string `temp`. 
3. The value sitting at the very last cell of this array, `lps[temp.length() - 1]`, gives us the length of the longest proper prefix of `temp` that is also a suffix of `temp`. Because the second half of `temp` is the reversed string, this suffix match corresponds exactly to the **longest palindromic prefix** of `s`.
4. Extract the remaining non-palindromic suffix of `s`, reverse it, and prepend it to the front of `s`.

---

## Visual Understanding

Suppose `s = "aacecaaa"` where `n = 8`:

1. **Construct Combined Pattern String:**
   - `reverse(s) = "aaacecaa"`
   - `temp = "aacecaaa#aaacecaa"`

2. **Compute the LPS Array for `temp`:**
   The suffix matching checks step forward, tracking overlapping prefix characters:
   ```text
   String: a a c e c a a a # a a a c e c a a
   LPS Value at the absolute final index settles to: 7
   ```

3. **Reconstruct Palindrome:**
   - Longest palindromic prefix length = `7` (`"aacecaa"`).
   - Remaining non-palindromic suffix of `s` = `s.substring(7)` = `"a"`.
   - Reverse the remaining suffix = `"a"`.
   - Prepend it to `s`: `"a" + "aacecaaa"` = `"aaacecaaa"`.

The shortest palindrome is found in a single forward pass.

---

## Partition Variables

Let:

```java
String reversed = new StringBuilder(s).reverse().toString();
String temp = s + "#" + reversed;
int[] lps = new int[temp.length()];
```

---

### Border Elements

When a character mismatch occurs during the LPS array generation, the prefix length pointer drops back recursively using the table history to preserve sub-prefix matches:

```java
if (len != 0) {
    len = lps[len - 1];
} else {
    lps[i] = 0;
    i++;
}
```

---

## Correct Partition Condition

The value stored at the very last slot of the computed table array isolates the maximum substring boundary length that forms a prefix palindrome:

```java
int longestPalindromicPrefixLength = lps[temp.length() - 1];
String suffixToReverse = s.substring(longestPalindromicPrefixLength);
return new StringBuilder(suffixToReverse).reverse().toString() + s;
```

---

## Java Solution

```java
class Solution {

    public String shortestPalindrome(String s) {
        if (s == null || s.length() <= 1) {
            return s;
        }

        // Step 1: Create the reversed string copy
        String reversed = new StringBuilder(s).reverse().toString();
        
        // Step 2: Combine strings with a unique separator character '#'
        String temp = s + "#" + reversed;
        int n = temp.length();
        int[] lps = new int[n];
        
        int len = 0; // Length of the previous longest prefix suffix
        int i = 1;

        lps[0] = 0; // Base case: first cell is always 0

        // Step 3: Compute the LPS array for the combined string
        while (i < n) {
            if (temp.charAt(i) == temp.charAt(len)) {
                len++;
                lps[i] = len;
                i++;
            } else {
                // If a mismatch occurs, drop back using our precalculated history
                if (len != 0) {
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }

        // Step 4: The final cell of the LPS array holds the longest palindromic prefix length
        int longestPalindromicPrefixLength = lps[n - 1];
        
        // Step 5: Extract the remaining suffix, reverse it, and prepend it to the front
        String remainingSuffix = s.substring(longestPalindromicPrefixLength);
        String prefixToPrepend = new StringBuilder(remainingSuffix).reverse().toString();

        return prefixToPrepend + s;
    }
}
```

---

## Dry Run

### Input

```java
s = "abcd"
```

---

### Step Execution Traversal

- **Combined String Setup:** `reversed = "dcba"`. `temp = "abcd#dcba"`. Length `n = 9`.
- **LPS Evaluation Loop:**
  - Standard iterations look for prefix matches. Since `"abcd"` and `"dcba"` share no prefix-suffix overlap except the character `'a'`, the character loop evaluates the matching boundaries.
  - The absolute final slot of the array resolves to `lps[8] = 1` (matching character `'a'`).
- **Reconstruction Phase:**
  - `longestPalindromicPrefixLength = 1`.
  - `remainingSuffix = s.substring(1)` \(\implies\) `"bcd"`.
  - `prefixToPrepend = reverse("bcd")` \(\implies\) `"dcb"`.
  - Returns `"dcb" + "abcd"` = `"dcbabcd"`.

---

### Answer

```java
"dcbabcd"
```

---

## Why Is the Time Complexity Bounded Linearly?

The algorithm combines the strings and builds the LPS table array in a single loop pass where the pointer `i` moves strictly forward up to `2n + 1`. Although the `len` tracking pointer can shift backward on character mismatches, it can move backward at most as many times as it advanced forward. This ensures that the overall runtime scales linearly.

This sliding window timeline yields:

```text
O(n)
```

which satisfies the strict optimization constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Where `n` is the length of string `s`. Building the reversed string and the combined string takes `O(n)` time. Computing the LPS array table runs in linear `O(n)` time relative to the combined string length, and assembling the final string takes another `O(n)` step.

---

### Space Complexity

```text
O(n)
```

The algorithm allocates an auxiliary integer array of size `2n + 1` to store the KMP lookup states, along with extra memory allocations to hold the string builders and reversed sequences.

---

## Key Insight

By embedding a string and its reversed copy together with a unique separator, finding the longest palindromic prefix transforms from a nested search into a simple, single-pass KMP lookup calculation.

```text
Time  : O(n) linear execution pass
Space : O(n) table array storage footprint
```

---

## Similar Problems

1. Longest Happy Prefix (1392)
2. Find the Index of the First Occurrence in a String / KMP (28)
3. Valid Palindrome (125)
4. Longest Palindromic Substring (5)
5. Palindrome Pairs (336)
