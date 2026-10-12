# Boyer-Moore Algorithm (Efficient Pattern Searching)

## Problem Statement

Given a text string `txt` and a pattern string `pat`, find all the starting indices where `pat` occurs as a substring inside `txt`.

The **Boyer-Moore Algorithm** is one of the most efficient string-searching algorithms used in standard library search functions. It optimizes pattern matching by processing the pattern from **right to left** (backward) while sliding the pattern from **left to right** (forward) across the text. 

By utilizing structural analysis of the pattern, it can skip entire blocks of text characters completely on a mismatch, yielding sub-linear performance in many real-world scenarios.

The overall run time complexity is:

```text
O(n / m) best case, O(n + m) average, O(n * m) worst case
```

*(where n is the length of txt and m is the length of pat)*

---

## The Core Heuristics

Boyer-Moore achieves its high-speed text skipping by combining two independent mismatch rules:

### 1. The Bad Character Heuristic
When a character mismatch occurs at `txt.charAt(i + j)` with `pat.charAt(j)`:
- Look up the mismatched text character inside a precalculated **Bad Character Table** (which stores the rightmost occurrence index of every alphabet character in the pattern).
- **Case A (Character in pattern):** Shift the pattern forward until that rightmost character aligns perfectly under the mismatched text character.
- **Case B (Character not in pattern):** Shift the pattern completely past the mismatched text character position, skipping the entire window.

### 2. The Good Suffix Heuristic
When a suffix segment of the pattern matches the text window successfully before a mismatch is hit, the algorithm shifts the pattern forward to align with the next matching instance of that suffix (or a prefix of that suffix) elsewhere inside the pattern.

*(Note: In most standard technical interview contexts, implementing the full Good Suffix Heuristic adds significant structural complexity. A lightweight version utilizing only the **Bad Character Heuristic** is heavily preferred, highly performant, and known colloquially as the **Boyer-Moore-Horspool** variation).*

---

## Visual Understanding (Bad Character Shift)

Suppose `txt = "abXcdabc"` and `pat = "abc"`. Length `m = 3`.

- **Alignment 1:** Align `pat` at text index `0`.
  ```text
  txt: a b X c d a b c
  pat: a b c
  ```
  - Scan right to left starting from the end of `pat` (`j = 2`): `pat.charAt(2) = 'c'` mismatches `txt.charAt(2) = 'X'`.
  - Look up `'X'` in our Bad Character Table. `'X'` does not exist in `pat` (represented as `-1`).
  - **The Skip:** We shift the pattern completely past index 2, jumping the window straight forward by 3 slots!

- **Alignment 2:** New shift position:
  ```text
  txt: a b X c d a b c
  pat:       a b c
  ```
  - Scan right to left: `pat.charAt(2) = 'c'` matches `txt.charAt(5) = 'b'`? Mismatch.
  - Look up `'b'` in the table. The rightmost index of `'b'` in `pat` is `1`.
  - Shift pattern to align `'b'`.

---

## Partition Variables

Let:

```java
int[] badChar = new int; // Maps extended ASCII space characters to rightmost indices
int shift = 0;              // Tracks the active sliding window index offset inside txt
```

---

### Border Elements

The precalculation table initializes all cells to `-1` (indicating non-existence) before recording the inner character coordinates of the pattern string:

```java
for (int i = 0; i < 256; i++) {
    badChar[i] = -1;
}
for (int i = 0; i < m; i++) {
    badChar[pat.charAt(i)] = i;
}
```

---

## Correct Partition Condition

When a mismatch is intercepted, the window shift size is determined by taking the max between a safe 1-slot progression and the bad character formula step:

```java
shift += Math.max(1, j - badChar[txt.charAt(shift + j)]);
```

---

## Java Solution (Boyer-Moore-Horspool Variant)

```java
import java.util.ArrayList;
import java.util.List;

class BoyerMoore {

    public List<Integer> search(String txt, String pat) {
        List<Integer> result = new ArrayList<>();
        if (txt == null || pat == null || txt.length() < pat.length()) {
            return result;
        }

        int n = txt.length();
        int m = pat.length();

        // Step 1: Precalculate the Bad Character Table for extended ASCII
        int[] badChar = new int;
        
        // Initialize all characters to -1 (indicating they do not exist in the pattern)
        for (int i = 0; i < 256; i++) {
            badChar[i] = -1;
        }
        
        // Record the rightmost index position of each character inside the pattern
        for (int i = 0; i < m; i++) {
            badChar[pat.charAt(i)] = i;
        }

        int shift = 0; // Tracks the starting alignment offset of the pattern inside txt

        // Step 2: Slide the pattern across the text window boundary lines
        while (shift <= (n - m)) {
            int j = m - 1; // Start scanning from the rightmost character of the pattern

            // Step backward while characters match perfectly
            while (j >= 0 && pat.charAt(j) == txt.charAt(shift + j)) {
                j--;
            }

            // Match Condition: Entire pattern length successfully verified
            if (j < 0) {
                result.add(shift); // Record starting index match location
                
                // Shift the pattern forward to catch subsequent overlapping occurrences
                // If there are more characters left in txt, align with the matching token
                shift += (shift + m < n) ? m - badChar[txt.charAt(shift + m)] : 1;
            } 
            // Mismatch Condition: Apply the Bad Character Shift Rule
            else {
                // Shift the pattern forward so the text character aligns with its rightmost match inside pat.
                // Math.max guarantees the shift moves forward even if the rightmost match sits to the right of j.
                shift += Math.max(1, j - badChar[txt.charAt(shift + j)]);
            }
        }

        return result;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Best-Case : O(n / m)
Average   : O(n + m)
Worst-Case: O(n * m)
```

- **Best Case:** Occurs on random text streams with a large alphabet pool where character mismatches trigger giant shifts of size `m` repeatedly.
- **Worst Case:** Triggered on highly repetitive structures (e.g., `txt = "aaaaaaaa"` and `pat = "baaa"`), where the algorithm runs full right-to-left verification scans before shifting forward by a constant step of 1 slot.

---

### Space Complexity

```text
O(1) // Relative to text/pattern length
```

The algorithm allocates a fixed-size table array of 256 cells to store alphabet indices, maintaining an absolute constant operational footprint.

---

## Key Insight

Scanning characters backward from right to left allows mismatches at the tail of the pattern to skip past whole chunks of non-matching text characters instantly, providing exceptional search performance on real-world text bodies.

```text
Time  : O(n + m) average runtime timeline
Space : O(1) fixed auxiliary table memory footprint
```

---

## Similar Problems

1. Find the Index of the First Occurrence in a String / KMP (28)
2. Find the Index of the First Occurrence in a String / Rabin-Karp (28)
3. Z Algorithm Implementation
4. Repeated Substring Pattern (459)
5. Longest Happy Prefix (1392)
