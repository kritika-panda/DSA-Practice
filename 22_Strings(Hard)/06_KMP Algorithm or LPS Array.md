# KMP Algorithm & Lowest Pattern Substring (LPS) Array

## Problem Statement

Given a text string `txt` and a pattern string `pat`, find all the starting indices where `pat` occurs as a substring inside `txt`.

The **Knuth-Morris-Pratt (KMP) Algorithm** solves this problem by optimizing the pattern-matching process. When a character mismatch occurs after some matches, the algorithm uses precalculated structural data to skip redundant comparisons. Instead of rewinding the text pointer backward, KMP shifts the pattern smartly using the **LPS (Longest Prefix Suffix) array**.

The overall run time complexity must be strictly bounded by:

```text
O(n + m)
```

*(where n is the length of txt and m is the length of pat)*

---

## The LPS Array (Longest Prefix Suffix)

The backbone of the KMP algorithm is the **LPS array**. For a pattern string of size `m`, `lps[i]` stores the length of the longest proper prefix of the substring `pat[0...i]` that is also a suffix of `pat[0...i]`.

A **proper prefix** is a prefix that is not equal to the entire string itself.

### Visualizing LPS Generation

Suppose `pat = "abab"`.
- `lps[0] = 0` (A single character has no proper prefix)
- For `pat[0...1] = "ab"`: Proper prefixes: `["a"]`, Suffixes: `["b"]`. Match length = `0`.
- For `pat[0...2] = "aba"`: Proper prefixes: `["a", "ab"]`, Suffixes: `["a", "ba"]`. The longest matching shared token is `"a"`. Match length = `1`.
- For `pat[0...3] = "abab"`: Proper prefixes: `["a", "ab", "aba"]`, Suffixes: `["b", "ab", "bab"]`. The longest matching shared token is `"ab"`. Match length = `2`.

The compiled array states look like this:
```text
Index: 0  1  2  3
Char : a  b  a  b
LPS  : 0  0  1  2
```

---

## Visual Understanding of KMP Matching

Suppose we match `txt = "abaxab"` with `pat = "abab"` using the precalculated LPS above:

- At `i = 0` to `2`, characters match up to `"aba"`. Pattern pointer `j` reaches index `3`.
- At `i = 3`, `txt[3] = 'x'` mismatches with `pat[3] = 'b'`.

Instead of resetting `i` back to `1` and `j` to `0` (brute-force rewind), KMP keeps `i` locked at `3` and adjusts `j` using the LPS table:
- Read `lps[j - 1] = lps[2] = 1`.
- Move `j` to `1`. The pattern effectively shifts over, keeping the matched prefix `"a"` aligned with the text suffix `"a"` right before `'x'`.

```text
txt: a b a x a b
pat: a b a b      (j = 3 mismatches on 'x')
     
      Shift using lps[2] = 1:
txt: a b a x a b
pat:     a b a b  (j = 1 now compares 'b' with 'x')
```

---

## Partition Variables

Let:

```java
int[] lps = new int[m];
int i = 0; // Pointer for txt
int j = 0; // Pointer for pat
```

---

### Border Elements

When character matching paths fail, pattern pointer `j` falls back to its previous longest prefix state boundary recursively until it hits index 0:

```java
if (j != 0) {
    j = lps[j - 1];
} else {
    i++;
}
```

---

## Correct Partition Condition

A full pattern match boundary is intercepted the moment the pattern lookup pointer `j` tracks up to length `m`:

```java
if (j == m) {
    result.add(i - j);
    j = lps[j - 1]; // Reset j using the table to catch overlapping matches
}
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class KMPAlgorithm {

    public List<Integer> search(String txt, String pat) {
        List<Integer> result = new ArrayList<>();
        if (txt == null || pat == null || txt.length() < pat.length()) {
            return result;
        }

        int n = txt.length();
        int m = pat.length();

        // Step 1: Precalculate the LPS array
        int[] lps = computeLPSArray(pat, m);

        int i = 0; // Index pointer for txt
        int j = 0; // Index pointer for pat

        // Step 2: Traverse the text using a single forward pass
        while (i < n) {
            if (txt.charAt(i) == pat.charAt(j)) {
                i++;
                j++;
            }

            // Match condition: Entire pattern length successfully verified
            if (j == m) {
                result.add(i - j); // Record starting index match location
                j = lps[j - 1];    // Fallback using table to track overlapping occurrences
            } 
            // Mismatch condition
            else if (i < n && txt.charAt(i) != pat.charAt(j)) {
                // Smart fallback: shift the pattern pointer j using precalculated history
                if (j != 0) {
                    j = lps[j - 1];
                } else {
                    i++; // If j is already at root, advance text window forward
                }
            }
        }

        return result;
    }

    private int[] computeLPSArray(String pat, int m) {
        int[] lps = new int[m];
        int len = 0; // Length of the previous longest prefix suffix
        int i = 1;

        lps[0] = 0; // Base case: lps[0] is always 0

        // Build the lookup table in a single loop
        while (i < m) {
            if (pat.charAt(i) == pat.charAt(len)) {
                len++;
                lps[i] = len;
                i++;
            } else {
                if (len != 0) {
                    len = lps[len - 1]; // Fallback to check smaller prefix matches
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }
        return lps;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
O(n + m)
```

Building the LPS array table takes `O(m)` steps. During the pattern searching phase, the text pointer `i` never moves backward and increases strictly up to `n`. While the pattern pointer `j` can move backward on mismatches, it can move backward at most as many times as it moved forward, bounding total operations linearly to `O(n)`.

---

### Space Complexity

```text
O(m)
```

The algorithm allocates an auxiliary integer array of size `m` to maintain the precalculated LPS structural lookup table.

---

## Key Insight

By mapping the repeat patterns inside the pattern string itself into an LPS array, KMP eliminates the need to rewind the text pointer on character mismatches, securing a guaranteed linear-time search path.

```text
Time  : O(n + m) linear execution pass
Space : O(m) table array storage footprint
```

---

## Similar Problems

1. Find the Index of the First Occurrence in a String (28)
2. Longest Happy Prefix (1392)
3. Z Algorithm Implementation
4. Repeated Substring Pattern (459)
5. Shortest Palindrome (214)
