# Number of Segments in a String

## Problem Statement

Given a string `s`, return *the number of segments in the string*.

A **segment** is defined as a maximal substring of non-space characters.

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "Hello, my name is John"
```

**Output**

```java
5
```

**Explanation**

The five segments are `["Hello,", "my", "name", "is", "John"]`.

---

### Example 2

**Input**

```java
s = "Hello"
```

**Output**

```java
1
```

---

### Example 3

**Input**

```java
s = "        "
```

**Output**

```java
0
```

**Explanation**

The string contains only spaces, so there are no valid non-space segments.

---

## Brute Force Approach

Split the string into an array of sub-tokens using a space regular expression delimiter, filter out any empty array cells, and count the total number of valid elements.

### Steps

1. Trim leading and trailing spaces from the string to clean up edges.
2. If the trimmed string is completely empty, return `0`.
3. Split the string into a collection of words using a space regular expression: `s.trim().split("\\s+")`.
4. Return the length of the resulting array.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // Allocates array memory proportional to the string length
```

While this linear pass is conceptually straightforward, calling `.split()` inside memory-constrained systems forces the runtime engine to instantiate multiple temporary substring object blocks and array allocations. We can optimize this down to an absolute **zero-allocation footprint** by counting segments on the fly.

---

# Optimal Approach: Single-Pass In-Place Counter

## Key Idea

We can count the total number of segments in a **single forward pass** with **O(1) auxiliary space** by tracking the boundaries where a new segment begins.

A character marks the **start of a new segment** if and only if:
1. The current character is **not a space** (`s.charAt(i) != ' '`).
2. It is either the **very first character** of the string (`i == 0`), OR the character immediately preceding it was a **space** (`s.charAt(i - 1) == ' '`).

By scanning the string from index `0` up to `n - 1` and checking this edge condition at each character, we can increment our counter precisely when a segment opens, skipping multi-space gaps automatically.

---

## Visual Understanding

Suppose `s = "  foo   bar "` with length 11:

- **i = 0 (' '):** Space character. Condition failed.
- **i = 1 (' '):** Space character. Condition failed.
- **i = 2 ('f'):** Non-space character AND previous character `s[1]` is a space. **Segment 1 detected!** `count = 1`.
- **i = 3 ('o'):** Non-space character, but previous character `s[2]` is not a space. Skip.
- **i = 4 ('o'):** Non-space character, but previous character `s[3]` is not a space. Skip.
- **i = 5 (' '):** Space character. Condition failed.
- **i = 6 (' '):** Space character. Condition failed.
- **i = 7 (' '):** Space character. Condition failed.
- **i = 8 ('b'):** Non-space character AND previous character `s[7]` is a space. **Segment 2 detected!** `count = 2`.
- **i = 9 ('a'):** Non-space character, but previous character `s[8]` is not a space. Skip.
- **i = 10 ('r'):** Non-space character, but previous character `s[9]` is not a space. Skip.

The loop completes with a total count of `2` without creating intermediate string partitions.

---

## Partition Variables

Let:

```java
int segmentCount = 0;
int n = s.length();
```

---

### Border Elements

The lookback condition safely checks index `0` as a starting boundary gate without triggering an out-of-bounds error:

```java
if (s.charAt(i) != ' ' && (i == 0 || s.charAt(i - 1) == ' ')) {
    segmentCount++;
}
```

---

## Correct Partition Condition

The linear forward pass scans through all characters within a single tracking loop:

```java
for (int i = 0; i < s.length(); i++) {
    if (s.charAt(i) != ' ' && (i == 0 || s.charAt(i - 1) == ' ')) {
        segmentCount++;
    }
}
return segmentCount;
```

---

## Java Solution

```java
class Solution {

    public int countSegments(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        int segmentCount = 0;
        int n = s.length();

        // Step 1: Scan the string linearly from left to right
        for (int i = 0; i < n; i++) {
            
            // Step 2: Check if the current character marks the beginning of a segment
            if (s.charAt(i) != ' ' && (i == 0 || s.charAt(i - 1) == ' ')) {
                segmentCount++;
            }
        }

        return segmentCount;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Single-Pass Scan** | **O(n)** | **O(1)** | Processes each character in the string exactly once. Runs entirely in-place with zero auxiliary memory allocations. |

---

## Key Insight

Identifying the structural opening boundaries of token segments eliminates the performance and memory penalties of standard regular expression string splitting.

```text
Time  : O(n) single pass forward execution
Space : O(1) absolute constant memory footprint
```

---

## Similar Problems

1. Length of Last Word (58)
2. Reverse Words in a String (151)
3. Count and Say (38)
4. Word Pattern (290)
5. String Compression (443)
