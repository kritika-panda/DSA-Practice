# Compare Version Numbers

## Problem Statement

Given two version strings, `version1` and `version2`, compare them. A version string consists of revisions separated by dots `'.'`. Each revision contains digits and may contain leading zeros.

To compare version strings, compare their revisions in left-to-right order. Revisions are compared as their **integer values**, ignoring any leading zeros. If a version string does not specify a revision at an index, then treat that revision as `0`.

Return:
* `1` if `version1 > version2`
* `-1` if `version1 < version2`
* `0` if `version1 == version2`

The overall runtime complexity must be:

```text
O(n + m)
```

*(where n is the length of version1 and m is the length of version2)*

---

## Examples

### Example 1

**Input**

```java
version1 = "1.01", version2 = "1.001"
```

**Output**

```java
0
```

**Explanation**

Ignoring leading zeros, both `"01"` and `"001"` evaluate to the integer `1`. `1.1 == 1.1`.

---

### Example 2

**Input**

```java
version1 = "1.0", version2 = "1.0.0.0"
```

**Output**

```java
0
```

**Explanation**

Missing revisions in `version1` are treated as `0`. Therefore, `1.0.0.0` is structurally equal to `1.0.0.0`.

---

### Example 3

**Input**

```java
version1 = "0.1", version2 = "1.1"
```

**Output**

```java
-1
```

**Explanation**

The first revision of `version1` is `0`, while the first revision of `version2` is `1`. Since `0 < 1`, return `-1`.

---

## Brute Force Approach

Split both strings into token arrays using the dot character `\\.` as a regular expression delimiter, parse chunks using `Integer.parseInt()`, and handle mismatched array lengths by dynamically padding indices with zeros.

### Steps

1. Split both strings: `String[] levels1 = version1.split("\\.")`.
2. Determine the maximum length between the two resulting arrays: `maxLen = Math.max(levels1.length, levels2.length)`.
3. Loop from `0` up to `maxLen - 1`.
4. Parse the level chunk token to an integer if it falls within array limits; otherwise, fall back to `0`.
5. Compare the values. If a difference is found, return `1` or `-1`.
6. If the loop completes with all values equal, return `0`.

### Complexity

```text
Time Complexity: O(n + m)
Space Complexity: O(n + m) // Fails to maximize performance due to regex token string array allocations
```

While linear, calling `.split()` inside performance loops forces the engine to instantiate multiple temporary token string objects and memory arrays on the heap. We can optimize this down to an absolute **zero-allocation footprint** by parsing integers in-place from left to right.

---

# Optimal Approach: Two-Pointer In-Place Chunk Parsing

## Key Idea

We can compute the comparison in **linear time** and **constant extra space** by utilizing a two-pointer numerical scan strategy, extracting sub-revision integers on the fly.

1. Initialize two pointers, `i = 0` for scanning `version1` and `j = 0` for scanning `version2`.
2. Run a loop that continues as long as at least one pointer has not crossed its respective string boundary limit.
3. For each revision segment, accumulate characters into an integer register dynamically:
   - **For version1:** Move `i` forward while `i < version1.length()` and the character is not `'.'`. Accumulate the value via `num1 = num1 * 10 + (char - '0')`. Skip the trailing dot separator by incrementing `i`.
   - **For version2:** Move `j` forward similarly to calculate `num2`.
4. Compare `num1` against `num2` immediately at the end of that revision track. If they differ, return `1` or `-1` right away.
5. If they are equal, reset the registers to `0` and continue to the next revision segment. If the loop runs completely out of string characters with no inequalities discovered, return `0`.

---

## Visual Understanding

Suppose `version1 = "1.01"` and `version2 = "1.001"`:

- **Initialization:** `i = 0`, `j = 0`.
- **Revision Block 1:**
  - Pointer `i` parses up to `'.'`: `num1 = 1`. Pointer `i` moves past `'.'`.
  - Pointer `j` parses up to `'.'`: `num2 = 1`. Pointer `j` moves past `'.'`.
  - Compare: `1 == 1`. Loop continues.
- **Revision Block 2:**
  - Pointer `i` parses `"01"` to the end: `num1 = 0 * 10 + 0 = 0`, then `0 * 10 + 1 = 1`.
  - Pointer `j` parses `"001"` to the end: `num2 = 0 \to 0 \to 1`.
  - Compare: `1 == 1`. Loop ends as both strings are fully processed.

Returns `0` without creating any sub-token list arrays.

---

## Partition Variables

Let:

```java
int i = 0;
int j = 0;
int n1 = version1.length();
int n2 = version2.length();
```

---

### Border Elements

The parsing condition gracefully processes asymmetric version tracks by defaulting missing components smoothly to zero:

```java
while (i < n1 || j < n2) {
    int num1 = 0;
    int num2 = 0;
    // ... accumulate digits if pointers are within string limits ...
}
```

---

## Correct Partition Condition

The internal accumulation loops build up integers and skip dot markers dynamically in-place:

```java
while (i < n1 && version1.charAt(i) != '.') {
    num1 = num1 * 10 + (version1.charAt(i) - '0');
    i++;
}
i++; // Skip the dot '.' separator
```

---

## Java Solution

```java
class Solution {

    public int compareVersion(String version1, String version2) {
        if (version1 == null || version2 == null) {
            return 0;
        }

        int i = 0;
        int j = 0;
        int n1 = version1.length();
        int n2 = version2.length();

        // Step 1: Scan both version strings from left to right
        while (i < n1 || j < n2) {
            int num1 = 0;
            int num2 = 0;

            // Step 2: Extract the sub-revision integer for version1 in-place
            while (i < n1 && version1.charAt(i) != '.') {
                num1 = num1 * 10 + (version1.charAt(i) - '0');
                i++;
            }

            // Step 3: Extract the sub-revision integer for version2 in-place
            while (j < n2 && version2.charAt(j) != '.') {
                num2 = num2 * 10 + (version2.charAt(j) - '0');
                j++;
            }

            // Step 4: Compare the evaluated revision blocks instantly
            if (num1 < num2) {
                return -1;
            } else if (num1 > num2) {
                return 1;
            }

            // Move pointers past the dot '.' separator to prepare for the next revision
            i++;
            j++;
        }

        return 0;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Two-Pointer Scan** | **O(n + m)** | **O(1)** | Traverses both strings linearly from left to right exactly once. Runs entirely in-place inside local primitive tracking registers with absolute zero memory allocation. |

---

## Key Insight

Parsing the character chunks directly into primitive integers on the fly bypasses the allocation overhead of array splitting libraries, enabling version checks with absolute zero auxiliary space.

```text
Time  : O(n + m) linear tracking pass
Space : O(1) absolute constant extra space
```

---

## Similar Problems

1. String to Integer / atoi (8)
2. Valid Palindrome (125)
3. Length of Last Word (58)
4. License Key Formatting (482)
5. Backspace String Compare (844)
