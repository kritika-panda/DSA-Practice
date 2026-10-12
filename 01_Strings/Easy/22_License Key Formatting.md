# License Key Formatting

## Problem Statement

You are given a license key represented as a string `s` that consists of English letters, digits, and dashes. The string is separated into `n + 1` groups by `n` dashes. You are also given an integer `k`.

We want to reformat the string `s` such that each group contains exactly `k` characters, except for the first group, which could be shorter than `k` but must still contain at least one character. Furthermore, there must be a dash inserted between two consecutive groups, and you must convert all lowercase letters to uppercase.

Return *the reformatted license key*.

The overall runtime complexity must be:

```text
O(n)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "5F3Z-2e-9-w"
k = 4
```

**Output**

```java
"5F3Z-2E9W"
```

**Explanation**

The string `s` has been split into two groups. The first group has 4 characters, and the second group has 4 characters. Note that the lowercase letters have been converted to uppercase.

---

### Example 2

**Input**

```java
s = "2-5g-3-J"
k = 2
```

**Output**

```java
"2-5G-3J"
```

**Explanation**

The string `s` has been split into three groups. The first group has 1 character, and the next two groups have 2 characters each.

---

## Brute Force Approach

Clean the string of all dashes, convert it to uppercase, and calculate the size of the first group before chunking the remainder from left to right.

### Steps

1. Remove all existing dashes and convert the entire string to uppercase.
2. Calculate the length of the cleaned string `L`.
3. The first group's size will be `L % k`. If `L % k == 0`, the first group will have exactly `k` characters.
4. Iterate from left to right, copying characters over and inserting dashes after the first group and every subsequent block of `k` characters.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // Allocates multiple intermediate string buffers
```

While this left-to-right pass works, manually managing the variable-length remainder of the first group introduces complex conditional branching. We can optimize this process down to a clean, single pass with minimal overhead by processing the string **backward from the end**.

---

# Optimal Approach: Backward Single-Pass Accumulation

## Key Idea

The problem specifies that *only* the first group can have a length shorter than `k`. Every subsequent group moving toward the right must have exactly `k` characters. 

Instead of dealing with remainder math for the first group upfront, we can scan the string **backward from right to left** using an efficient, mutable `StringBuilder`:
1. Start a loop at the final index of the string `s.length() - 1` and walk backward to `0`.
2. Ignore all existing dashes (`'-'`).
3. For every valid character or digit encountered, count it using a local tracking variable `count`.
4. Convert lowercase characters to uppercase on the fly by utilizing `Character.toUpperCase()`.
5. Append the valid uppercase character to our `StringBuilder`.
6. **The Dash Rule:** If `count % k == 0`, we have just completed a full group of size `k`. If there are still more characters left to process in the string, append a dash `'-'` to the builder immediately before collecting the next character.
7. Since we collected the characters from right to left, reverse the compiled `StringBuilder` before returning the final text.

---

## Visual Understanding

Suppose `s = "2-5g-3-J"` and `k = 2`:

- **Initialization:** `count = 0`, `StringBuilder sb = ""`. Scan backward:
- **Index 6 (`'J'`):** Valid character. `sb.append('J')` \(\implies\) `sb = "J"`. `count = 1`.
- **Index 5 (`'-'`):** Dash found. Skip it.
- **Index 4 (`'3'`):** Valid character. `sb.append('3')` \(\implies\) `sb = "J3"`. `count = 2`.
  - Check: `count % k == 0` (`2 % 2 == 0`). A group of 2 is complete! Append a dash safely because more characters remain. `sb = "J3-"`.
- **Index 3 (`'-'`):** Dash found. Skip it.
- **Index 2 (`'g'`):** Valid character. Convert to uppercase `'G'`. `sb.append('G')` \(\implies\) `sb = "J3-G"`. `count = 3`.
- **Index 1 (`'-'`):** Dash found. Skip it.
- **Index 0 (`'2'`):** Valid character. `sb.append('2')` \(\implies\) `sb = "J3-G2"`. `count = 4`.
  - Check: `count % 2 == 0`. Group complete, but no more characters left in the text stream, so we do not append a trailing dash.

**Termination:** Reverse `sb` \(\implies\) `"2-5G-3J"`. The first group naturally gets the leftover size `1` automatically.

---

## Partition Variables

Let:

```java
StringBuilder sb = new StringBuilder();
int count = 0;
```

---

### Border Elements

The dash insertion rule must protect against placing a trailing dash at the absolute end of our backward generation pass (which corresponds to the front of the final string):

```java
if (count > 0 && count % k == 0 && i > 0) // Ensure we only append a dash if more characters are incoming
```

---

## Correct Partition Condition

The internal loop filters out dashes and updates block sizing counters dynamically in-place:

```java
char c = s.charAt(i);
if (c != '-') {
    if (count > 0 && count % k == 0) {
        sb.append('-');
    }
    sb.append(Character.toUpperCase(c));
    count++;
}
```

---

## Java Solution

```java
class Solution {

    public String licenseKeyFormatting(String s, int k) {
        if (s == null || s.length() == 0) {
            return "";
        }

        StringBuilder sb = new StringBuilder();
        int count = 0;

        // Step 1: Scan the string backward from right to left
        for (int i = s.length() - 1; i >= 0; i--) {
            char c = s.charAt(i);

            // Ignore all existing dashes completely
            if (c != '-') {
                
                // If a group of size k is complete, append a delimiter dash
                if (count > 0 && count % k == 0) {
                    sb.append('-');
                }
                
                // Convert lowercase letters to uppercase on the fly and append
                sb.append(Character.toUpperCase(c));
                count++;
            }
        }

        // Step 2: Reverse the builder since characters were collected backward
        return sb.reverse().toString();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Backward Pass** | **O(n)** | **O(n)** | Traverses each character in the string exactly once. The `StringBuilder` allocates memory proportional to the size of the cleaned output text. |

---

## Key Insight

Processing variable-chunk sequences backward forces the layout remainder to settle automatically into the first group, removing the need for complex offset logic loops.

```text
Time  : O(n) single pass backward execution
Space : O(n) dynamic string output builder allocation
```

---

## Similar Problems

1. Length of Last Word (58)
2. Number of Segments in a String (434)
3. Reverse String (344)
4. String Compression (443)
5. Repeated Substring Pattern (459)
