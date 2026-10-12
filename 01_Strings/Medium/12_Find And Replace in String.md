# Find and Replace in String

## Problem Statement

You are given a 0-indexed string `s` that you must perform `k` replacement operations on. You are also given three arrays: `indices`, `sources`, and `targets`, all of length `k`.

A replacement operation is described as follows:
* Check whether the substring `sources[i]` occurs at index `indices[i]` in the original string `s`.
* If it does not occur, **do nothing** for this specific operation.
* If it does occur, **replace** that substring with `targets[i]`.

All replacement operations happen **simultaneously**, meaning the replacement indices refer to the original string configuration before any operations take place. The test cases are generated such that the replacements **will not overlap** in memory.

Return *the resulting string after applying all valid replacement operations.*

The overall runtime complexity must be strictly bounded by:

```text
O(n + m)
```

*(where n is the length of string s and m is the total character payload across all sources and targets)*

---

## Examples

### Example 1

**Input**

```java
s = "abcd"
indices = [0, 2]
sources = ["a", "cd"]
targets = ["eee", "ffff"]
```

**Output**

```java
"eeebffff"
```

**Explanation**

- `"a"` occurs at index 0 in `"abcd"`, so it is replaced by `"eee"`.
- `"cd"` occurs at index 2 in `"abcd"`, so it is replaced by `"ffff"`.

---

### Example 2

**Input**

```java
s = "abcd"
indices = [0, 2]
sources = ["ab", "ec"]
targets = ["eee", "ffff"]
```

**Output**

```java
"eeecd"
```

**Explanation**

- `"ab"` occurs at index 0 in `"abcd"`, so it is replaced by `"eee"`.
- `"ec"` does not occur at index 2 in `"abcd"`, so nothing happens for this operation.

---

## Brute Force Approach

Apply modifications directly to a mutable string buffer one by one while adjusting index tracking pointers to account for character expansion or contraction after every replacement step.

### Steps

1. Pair up `indices[i]`, `sources[i]`, and `targets[i]` into an object list.
2. Sort the list of operations in descending order based on their index positions. Sorting backward ensures that modifying the trailing sections of the string will not alter or corrupt the index offsets of upcoming operations further to the left.
3. For each operation, use `.startsWith()` to check if the source substring matches the text.
4. If it matches, use `.replace()` or `.delete()` and `.insert()` on a `StringBuilder` to update the text.

### Complexity

```text
Time Complexity: O(k * log(k) + k * n) // sorting plus repeatedly shifting characters inside the builder
Space Complexity: O(n + m) to hold the structural operation objects and string states
```

Repeatedly shifting characters inside a text buffer during dynamic insertion passes creates a heavy quadratic time bottleneck. We can optimize this process down to a single clean pass by building the output string from left to right using a **precomputed mapping array**.

---

# Optimal Approach: Precalculated Matching Index Table

## Key Idea

Because the problem specifies that none of the valid replacements will ever overlap with each other, we can track all operational checkpoints inside a single linear array. This allows us to construct our output string from left to right in a **single pass** without shifting any characters or managing offset math.

1. **Build a Checkpoint Map:** Create an integer array `matchMap` initialized entirely to `-1`, with a size matching the length of string `s`. 
2. **Validate Operations:** Iterate through the `indices` array. For each operation `i`, check if the string `s` contains `sources[i]` starting precisely at `indices[i]` using `s.startsWith(sources[i], indices[i])`. 
   - If it matches, log the operation index `i` inside our map at that starting coordinate: `matchMap[indices[i]] = i`.
3. **Single Pass Construction:** Initialize a new `StringBuilder`. Start an index pointer `bucketIdx = 0` and walk forward to `s.length() - 1`:
   - **Case A (No Valid Replacement):** If `matchMap[bucketIdx] == -1`, no operation begins at this coordinate. Append the raw character `s.charAt(bucketIdx)` to our builder, and advance `bucketIdx` by 1.
   - **Case B (Valid Replacement Found):** If `matchMap[bucketIdx] != -1`, an operation index `opIdx` is triggered! Append the entire text block `targets[opIdx]` straight into our builder. Then, skip past the entire original matched source block by advancing our pointer forward by the length of the source substring: `bucketIdx += sources[opIdx].length()`.

---

## Visual Understanding

Suppose `s = "abcd"`, `indices = [0, 2]`, `sources = ["a", "cd"]`, and `targets = ["eee", "ffff"]`:

- **Step 1 (Validate and Map Operations):**
  - Index 0: `"abcd".startsWith("a", 0)` is true \(\implies\) `matchMap[0] = 0`.
  - Index 2: `"abcd".startsWith("cd", 2)` is true \(\implies\) `matchMap[2] = 1`.
  - Map configuration: `[0, -1, 1, -1]`.

- **Step 2 (Construct Left to Right):**
  - `bucketIdx = 0`: `matchMap[0] = 0`. Replacement triggered! Append `targets[0]` (`"eee"`). Jump pointer forward: `bucketIdx += sources[0].length()` \(\implies\) `0 + 1 = 1`. `sb = "eee"`.
  - `bucketIdx = 1`: `matchMap[1] = -1`. Append character `s.charAt(1)` (`'b'`). Advance: `bucketIdx = 2`. `sb = "eeeb"`.
  - `bucketIdx = 2`: `matchMap[2] = 1`. Replacement triggered! Append `targets[1]` (`"ffff"`). Jump pointer forward: `bucketIdx += sources[1].length()` \(\implies\) `2 + 2 = 4`. `sb = "eeebffff"`.
  - `bucketIdx = 4`: Exits loop.

The output string `"eeebffff"` is built efficiently with zero array shifts.

---

## Partition Variables

Let:

```java
int n = s.length();
int[] matchMap = new int[n];
Arrays.fill(matchMap, -1);
StringBuilder sb = new StringBuilder();
```

---

### Border Elements

The construction loop uses a flexible pointer step mutation line to handle variable jump lengths seamlessly:

```java
int bucketIdx = 0;
while (bucketIdx < n) {
    // ... processing logic ...
}
```

---

## Correct Partition Condition

The pointer leaps forward dynamically to skip replaced text blocks completely:

```java
int opIdx = matchMap[bucketIdx];
sb.append(targets[opIdx]);
bucketIdx += sources[opIdx].length(); // Fast forward past the original text segment
```

---

## Java Solution

```java
import java.util.Arrays;

class Solution {

    public String findReplaceString(String s, int[] indices, String[] sources, String[] targets) {
        if (s == null || s.length() == 0 || indices == null || indices.length == 0) {
            return s;
        }

        int n = s.length();
        // Create an index map to log which operation index triggers at each string position
        int[] matchMap = new int[n];
        Arrays.fill(matchMap, -1);

        // Step 1: Validate and register all valid non-overlapping operations
        for (int i = 0; i < indices.length; i++) {
            int startIdx = indices[i];
            
            // If the source substring matches the text at the target index, log the operation index
            if (s.startsWith(sources[i], startIdx)) {
                matchMap[startIdx] = i;
            }
        }

        StringBuilder sb = new StringBuilder();
        int bucketIdx = 0;

        // Step 2: Build the resulting string from left to right in a single pass
        while (bucketIdx < n) {
            // If a valid replacement operation starts at the current index, apply it
            if (matchMap[bucketIdx] != -1) {
                int opIdx = matchMap[bucketIdx];
                sb.append(targets[opIdx]);
                
                // Fast-forward past the original replaced source segment
                bucketIdx += sources[opIdx].length();
            } 
            // Otherwise, copy the original character over unchanged
            else {
                sb.append(s.charAt(bucketIdx));
                bucketIdx++;
            }
        }

        return sb.toString();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Precalculated Map Scan** | **O(n + m)** | **O(n + m)** | Validating operations and building the final text takes a linear amount of time proportional to the total character payload. The map array scales exactly with the size of string `s`. |

---

## Key Insight

Pre-mapping operation tokens into a localized coordinate index array enables simultaneous substitutions to be compiled from left to right in a flat linear pass, completely eliminating performance-killing text shifting loops.

```text
Time  : O(n + m) optimal linear performance timeline
Space : O(n + m) structural index array and output buffer allocations
```

---

## Similar Problems

1. Replace Words (648)
2. String Compression (443)
3. Zigzag Conversion (6)
4. Decode String (394)
5. Find All Anagrams in a String (438)
