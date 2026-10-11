# Rabin-Karp Algorithm (Pattern Searching using Rolling Hash)

## Problem Statement

Given a text string `txt` and a pattern string `pat`, find all the starting indices where `pat` occurs as a substring inside `txt`.

The **Rabin-Karp Algorithm** optimizes pattern matching by converting string slices into numerical hash values. This allows us to compare the pattern against sliding windows of the text in **constant O(1) time** on average, instead of checking character-by-character linearly.

The overall run time complexity should be:

```text
O(n + m) average, O(n * m) worst case
```

*(where n is the length of txt and m is the length of pat)*

---

## Examples

### Example 1

**Input**

```java
txt = "abccabc"
pat = "abc"
```

**Output**

```java
[0, 4]
```

**Explanation**

The pattern `"abc"` appears twice in the text: starting at index `0` and index `4`.

---

### Example 2

**Input**

```java
txt = "aaaaa"
pat = "aa"
```

**Output**

```java
[0, 1, 2, 3]
```

**Explanation**

The overlapping substring matches are located at indices `0, 1, 2,` and `3`.

---

### Example 3

**Input**

```java
txt = "abcdef"
pat = "xyz"
```

**Output**

```java
[]
```

---

## Brute Force Approach

Use two nested loops to check every substring window of length `m` inside `txt` by explicitly matching characters.

### Steps

1. Run an outer loop with pointer `i` from `0` up to `n - m`.
2. Run an inner loop with pointer `j` from `0` to `m - 1` to compare `txt.charAt(i + j)` with `pat.charAt(j)`.
3. If all characters match, record index `i`.
4. If a mismatch occurs, break early and shift the outer window by 1.

### Complexity

```text
Time Complexity: O(n * m)
Space Complexity: O(1)
```

Comparing every character from scratch across overlapping windows is highly inefficient for repetitive datasets. Rabin-Karp accelerates this by keeping a rolling hash.

---

# Optimal Approach: Polynomial Rolling Hash & Sliding Window

## Key Idea

Instead of calculating a completely new hash value for every window position in `txt`, we can compute the hash of the next window in **O(1) time** from the previous window using a **Polynomial Rolling Hash**.

1. **Precalculation:** Compute the initial hash code for the pattern `pat` and the first substring window of `txt` of length `m`.
2. **Slide and Roll:** Slide the window over `txt` one character at a time. To transition from the current window hash to the next:
   - **Subtract** the leading character's hash component.
   - **Multiply** the remaining value by the base `p` to shift the polynomial powers.
   - **Add** the incoming trailing character's hash component.
3. **Collision Verification:** If the hash of a text window matches the pattern hash, we perform a strict character-by-character check. This step is necessary because different strings can occasionally generate identical hashes due to modulus limitations (known as a **hash collision**).

---

## Visual Understanding

Suppose `txt = "abcc"`, `pat = "bc"`, base `p = 31`, and modulus `m = 10^9 + 7`. Let character values be 'a'=1, 'b'=2, 'c'=3. Length `m = 2`.

- Pattern Hash (`"bc"`): \((2 \cdot 31^1 + 3 \cdot 31^0) \pmod m = 62 + 3 = 65\).
- Window 1 Hash (`"ab"`): \((1 \cdot 31^1 + 2 \cdot 31^0) \pmod m = 31 + 2 = 33\). (Mismatch with 65).

To roll from `"ab"` to `"bc"` in \(O(1)\):
- Remove leading character 'a' (value 1): \((33 - 1 \cdot 31^1) = 2\).
- Shift powers by multiplying by base 31: \(2 \cdot 31 = 62\).
- Add incoming trailing character 'c' (value 3): \(62 + 3 = 65\).

The new window hash `65` matches the pattern hash `65`. We verify characters, confirm a match, and record index `1`.

---

## Partition Variables

Let:

```java
long pHash = 0; // Hash value for pattern
long tHash = 0; // Hash value for current text window
long h = 1;     // High-order multiplier value: p^(m-1) % mod
```

---

### Border Elements

The high-order multiplier \(h = p^{m-1} \pmod{\text{mod}}\) is precalculated to quickly subtract the leading character from the rolling hash:

```java
for (int i = 0; i < m - 1; i++) {
    h = (h * p) % mod;
}
```

---

## Correct Partition Condition

The linear sliding hash transition removes the oldest character out of the left boundary and appends the newest to the right:

```java
tHash = (p * (tHash - txt.charAt(i) * h) + txt.charAt(i + m)) % mod;
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class RabinKarp {

    public List<Integer> search(String txt, String pat) {
        List<Integer> indices = new ArrayList<>();
        if (txt == null || pat == null || txt.length() < pat.length()) {
            return indices;
        }

        int n = txt.length();
        int m = pat.length();
        long p = 31; // Prime base for lowercase alphabet
        long mod = 1000000007; // Large prime modulus

        long pHash = 0;
        long tHash = 0;
        long h = 1;

        // Step 1: Precalculate the high-order multiplier h = p^(m-1) % mod
        for (int i = 0; i < m - 1; i++) {
            h = (h * p) % mod;
        }

        // Step 2: Calculate the initial hash values for pat and the first window of txt
        for (int i = 0; i < m; i++) {
            pHash = (p * pHash + (pat.charAt(i) - 'a' + 1)) % mod;
            tHash = (p * tHash + (txt.charAt(i) - 'a' + 1)) % mod;
        }

        // Step 3: Slide the window over the text
        for (int i = 0; i <= n - m; i++) {
            // If the hash values match, verify characters to protect against collisions
            if (pHash == tHash) {
                if (checkEqual(txt, pat, i, m)) {
                    indices.add(i);
                }
            }

            // Calculate the hash value for the next window if within bounds
            if (i < n - m) {
                long leadingChar = txt.charAt(i) - 'a' + 1;
                long trailingChar = txt.charAt(i + m) - 'a' + 1;

                // Roll the hash: remove leading character, multiply by p, add trailing character
                tHash = (p * (tHash - leadingChar * h) + trailingChar) % mod;

                // Handle negative values in Java modulo operations gracefully
                if (tHash < 0) {
                    tHash += mod;
                }
            }
        }

        return indices;
    }

    private boolean checkEqual(String txt, String pat, int start, int m) {
        for (int j = 0; j < m; j++) {
            if (txt.charAt(start + j) != pat.charAt(j)) {
                return false;
            }
        }
        return true;
    }
}
```

---

## Dry Run

### Input

```java
txt = "abccabc";
pat = "abc";
```

---

### Step Execution Traversal

- **Initialization:** `n = 7`, `m = 3`. Computes `pHash` for `"abc"` and initial `tHash` for `txt[0...2]` (`"abc"`). Both hash values evaluate to identical numbers.
- **i = 0:** `pHash == tHash` is true. `checkEqual` confirms a full match. `0` is added to results.
- **Rolling Step:** Rolling hash shifts left character `'a'` out and takes trailing character `'c'` in. `tHash` recalculates to represent `"bcc"`.
- **i = 1 ("bcc"):** Hashes mismatch. Loop shifts.
- **i = 2 ("cca"):** Hashes mismatch. Loop shifts.
- **i = 3 ("cab"):** Hashes mismatch. Loop shifts.
- **i = 4 ("abc"):** Hashes match perfectly. `checkEqual` confirms characters. `4` is added to results. 

Loop finishes. Returns `[0, 4]`.

---

## Why Is the Average Time Complexity Linear?

By applying rolling math updates, calculating the next window's hash skips character iteration loops entirely, dropping window transitions to \(O(1)\) constant time. Since collisions are extremely rare with a large prime modulus, the character verification code rarely executes, keeping the overall runtime bounded linearly.

This sliding hash timeline yields:

```text
O(n + m) average time complexity
```

*(Worst-case performance lands at O(n * m) if malicious data causes a hash collision at every single index position).*

---

## Complexity Analysis

### Time Complexity

```text
Average: O(n + m)
Worst-Case: O(n * m)
```

Precalculating the initial hashes takes \(O(m)\) steps. The window slides across \(n - m\) iterations, and each rolling hash shift takes \(O(1)\) time.

---

### Space Complexity

```text
O(1)
```

The algorithm runs entirely in-place. No extra auxiliary storage lookup maps, trees, or recursive stack frames are allocated.

---

## Key Insight

Using modular arithmetic to drop the leading character and pull in the trailing character transforms an expensive string slice scanning routine into a simple constant-time math calculation.

```text
Time  : O(n + m) average runtime
Space : O(1) constant operational space footprint
```

---

## Similar Problems

1. Find the Index of the First Occurrence in a String / KMP (28)
2. Longest Duplicate Substring (1044)
3. Repeated String Match (686)
4. String Hashing Introduction
5. Shortest Palindrome (214)
