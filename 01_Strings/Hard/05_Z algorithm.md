# Z Algorithm (Linear Time Pattern Searching)

## Problem Statement

Given a text string `txt` and a pattern string `pat`, find all the starting indices where `pat` occurs as a substring inside `txt`.

The **Z Algorithm** solves this problem by computing an array called the **Z-array** for a concatenated string `S = pat + '$' + txt` (where `$` is a special separator character not present in either string). 

For an array of length `N`, `Z[i]` stores the length of the **longest common prefix (LCP)** between the string starting at index `i` and the entire string itself. Once the Z-array is built, any index `i` where `Z[i] == pat.length()` indicates a valid pattern match position in the text.

The overall run time complexity must be strictly bounded by:

```text
O(n + m)
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

The concatenated string is `S = "abc$abccabc"`.  
The pattern length is 3. The Z-array values corresponding to the `txt` segment match the pattern length at text offsets 0 and 4.

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

The pattern `"aa"` matches overlapping positions at indices `0, 1, 2,` and `3` inside the text.

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

Compute the longest common prefix for every index position from scratch by starting a separate nested character loop at each index.

### Steps

1. Construct the concatenated string `S = pat + '$' + txt`.
2. Initialize an integer array `Z` of size `S.length()`.
3. Run an outer loop with index `i` from `1` to `S.length() - 1`.
4. Run an inner loop to match characters starting at `S.charAt(i)` with `S.charAt(0)`, incrementing the count until a mismatch occurs.
5. Assign this count to `Z[i]`.

### Complexity

```text
Time Complexity: O(N^2) // where N = n + m + 1
Space Complexity: O(N) to store the Z-array
```

Re-evaluating characters from scratch repeatedly causes severe performance penalties on highly repetitive datasets. The optimal Z algorithm eliminates this by using a sliding window to reuse previously calculated values.

---

# Optimal Approach: Sliding Z-Box Framework

## Key Idea

We can achieve strict linear time complexity by maintaining a sliding window `[L, R]`, known as the **Z-box**, which represents the rightmost substring that is also a prefix of the entire string `S`.

As the index `i` walks forward from left to right:
- **Case 1 (Outside the Z-box):** If `i > R`, the current index has no precalculated history. We manually expand the window to the right by comparing characters starting from `S.charAt(i)` and `S.charAt(0)`. Once a mismatch is hit, we establish a new Z-box boundary: `L = i` and `R = current_match_end`.
- **Case 2 (Inside the Z-box):** If `i <= R`, the current index sits inside our pre-mapped prefix domain. This means its position corresponds directly to a prefix position index `k = i - L`. 
  - If `Z[k] < R - i + 1`, then the prefix match terminates *inside* the current Z-box. We can instantly copy the value: `Z[i] = Z[k]` in **O(1) constant time**.
  - If `Z[k] >= R - i + 1`, the prefix match extends *beyond* our known Z-box boundary. We initialize `Z[i] = R - i + 1` and continue manual character comparisons starting strictly from `R + 1` to expand our Z-box further right.

Because the right boundary `R` never moves backward, every character comparison either progresses `R` forward or resolves in constant time, locking the total runtime bounds linearly.

---

## Visual Understanding

Suppose `S = "aaaa"` and we are calculating `Z[2]` inside a Z-box:

- At `i = 1`, characters match out to the end. Z-box updates to `L = 1, R = 3`. `Z[1] = 3`.
- At `i = 2`, `i <= R (3)` holds true. We map the corresponding inside prefix position index: `k = 2 - 1 = 1`.
- We check `Z[1]`, which is `3`. 
- The remaining room inside our current Z-box boundary is `R - i + 1 = 3 - 2 + 1 = 2`.
- Since `Z[1] (3) >= 2`, the match path can potentially leak past `R`. We copy the safe boundary limit `Z[2] = 2` and try to expand further right starting from index `R + 1 = 4` (out of bounds here). 

The calculation finishes efficiently without rewinding or re-scanning indices.

---

## Partition Variables

Let:

```java
int[] Z = new int[S.length()];
int L = 0;
int R = 0;
```

---

### Border Elements

The character comparison loop expands the right pointer `R` forward, cleanly guarding string capacity boundaries:

```java
while (R < n && S.charAt(R) == S.charAt(R - L)) {
    R++;
}
```

---

## Correct Partition Condition

When a pattern match is confirmed, the Z-value at that index matches the exact length of the pattern prefix string segment:

```java
if (Z[i] == m) {
    result.add(i - m - 1); // Extract and map the real starting index offset inside txt
}
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class ZAlgorithm {

    public List<Integer> search(String txt, String pat) {
        List<Integer> result = new ArrayList<>();
        if (txt == null || pat == null || txt.length() < pat.length()) {
            return result;
        }

        int m = pat.length();
        // Step 1: Construct the concatenated string S = pat + '$' + txt
        String S = pat + "$" + txt;
        int n = S.length();
        
        int[] Z = new int[n];
        int L = 0;
        int R = 0;

        // Step 2: Build the Z-array in a single linear pass
        for (int i = 1; i < n; i++) {
            // Scenario A: i is inside the current active Z-box boundary
            if (i <= R) {
                int k = i - L;
                
                // If the prefix value fits completely within the Z-box, copy it
                if (Z[k] < R - i + 1) {
                    Z[i] = Z[k];
                } 
                // Otherwise, initialize to the Z-box border limit and try to expand
                else {
                    L = i;
                    while (R < n && S.charAt(R) == S.charAt(R - L)) {
                        R++;
                    }
                    Z[i] = R - L;
                    R--;
                }
            } 
            // Scenario B: i is outside the current active Z-box boundary
            else {
                L = i;
                R = i;
                while (R < n && S.charAt(R) == S.charAt(R - L)) {
                    R++;
                }
                Z[i] = R - L;
                R--;
            }
        }

        // Step 3: Scan the Z-array to identify matches that match the pattern length
        for (int i = m + 1; i < n; i++) {
            if (Z[i] == m) {
                result.add(i - m - 1); // Convert index back to original txt coordinates
            }
        }

        return result;
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

- **Concatenation:** `S = "abc$abccabc"`. Length `n = 11`. Pattern length `m = 3`.
- **Z-Array Generation:**
  - `i = 1 to 3`: Hashes and characters do not match the prefix. `Z[1]=0, Z[2]=0, Z[3]=0`.
  - `i = 4 ("a...")`: Outside window (`4 > 0`). Character loops expand `R` to index 7 (`'c' == 'c'`), mismatching at `'$'`. `Z[4] = 3`. Z-box updates to `L = 4, R = 6`.
  - `i = 5 ("b...")`: Inside window (`5 <= 6`). `k = 5 - 4 = 1`. `Z[1] (0) < 6 - 5 + 1 (2)` \(\implies\) `Z[5] = Z[1] = 0`.
  - `i = 6 ("c...")`: Inside window (`6 <= 6`). `k = 6 - 4 = 2`. `Z[2] (0) < 6 - 6 + 1 (1)` \(\implies\) `Z[6] = Z[2] = 0`.
  - `i = 8 ("a...")`: Outside window (`8 > 6`). Character loops expand `R` to index 11 (end of string). `Z[8] = 3`. Z-box updates to `L = 8, R = 10`.
- **Parsing Phase:** Matches filtered from index `m + 1 = 4` to 10. `Z[4] == 3` \(\implies\) adds `4 - 3 - 1 = 0`. `Z[8] == 3` \(\implies\) adds `8 - 3 - 1 = 4`.

Returns `[0, 4]`.

---

## Why Is the Time Complexity Bounded Linearly?

The linear execution guarantee lies in the behavior of the right boundary pointer `R`. Characters are explicitly compared only when extending the right edge of the Z-box further down the timeline. Since `R` starts at 0 and increments at most `n + m + 1` times without ever moving backward, the total number of character comparisons throughout the entire execution is strictly bounded.

This continuous forward-sliding timeline yields:

```text
O(n + m)
```

which satisfies the optimal time complexity constraint perfectly.

---

## Complexity Analysis

### Time Complexity

```text
O(n + m)
```

Where `n` is the length of `txt` and `m` is the length of `pat`. Constructing the string takes linear time, building the Z-box table runs in \(O(n + m)\) time, and a final linear sweep filters the results.

---

### Space Complexity

```text
O(n + m)
```

The algorithm allocates an integer array of size `n + m + 1` to maintain the precalculated prefix length lookup table.

---

## Key Insight

By maintaining a rightmost matching window boundary, we can reuse previously calculated prefix patterns to solve internal substring matches in constant time, ensuring the overall search progresses without costly index regressions.
```text 
Time  : O(n + m) linear execution pass Space : O(n + m) lookup array storage footprint
```
