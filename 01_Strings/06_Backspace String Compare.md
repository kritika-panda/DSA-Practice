# Backspace String Compare

## Problem Statement

Given two strings `s` and `t`, return `true` *if they are equal when both are typed into empty text editors*. `'#'` means a backspace character.

Note that after backspacing an empty text, the text will continue to remain empty.

The overall runtime complexity must be:

```text
O(n + m)
```

*(where n is the length of string s and m is the length of string t)*

And the space complexity must be strictly bounded by:

```text
O(1)
```

---

## Examples

### Example 1

**Input**

```java
s = "ab#c", t = "ad#c"
```

**Output**

```java
true
```

**Explanation**

Both `s` and `t` become `"ac"` after processing backspaces.

---

### Example 2

**Input**

```java
s = "ab##", t = "c#d#"
```

**Output**

```java
true
```

**Explanation**

Both `s` and `t` become `""` (empty string).

---

### Example 3

**Input**

```java
s = "a#c", t = "b"
```

**Output**

```java
false
```

**Explanation**

`s` becomes `"c"` while `t` becomes `"b"`.

---

## Brute Force Approach

Rebuild both strings explicitly from scratch by using a stack or a dynamic array buffer to push characters when they are normal letters and pop them when encountering a `'#'` character.

### Steps

1. Create a helper function that takes a string and builds the final result using a `StringBuilder` or a stack.
2. Iterate through each character:
   - If it is a letter, append it to the buffer.
   - If it is a `'#'` and the buffer is not empty, delete the last appended character.
3. Apply this helper function to both `s` and `t`.
4. Return whether the two built strings are equal.

### Complexity

```text
Time Complexity: O(n + m)
Space Complexity: O(n + m) // Fails the strict O(1) space constraint
```

Building full string buffers copies data paths into the heap memory. To satisfy the optimal space requirement, we must evaluate characters **in-place from right to left** without saving their states.

---

# Optimal Approach: Two-Pointer Backward Skip Counting

## Key Idea

When we process a string from left to right, a backspace character affects characters that came *before* it, making lookaheads highly complex. However, if we scan the string **backward from right to left**, a backspace character affects characters that appear *after* it in our traversal loop.

This insight allows us to skip deleted characters dynamically using a simple backspace counter:
1. Initialize two pointers, `i = s.length() - 1` and `j = t.length() - 1`.
2. Maintain two skip variables, `skipS = 0` and `skipT = 0`, to keep track of active backspace counts.
3. For each string, resolve backspaces moving backward:
   - If the current character is `'#'`, increment the skip counter and decrement the pointer.
   - If the current character is a normal letter but the skip counter is strictly greater than `0`, decrement the skip counter and skip this character by decrementing the pointer.
4. Once both pointers are resting on valid, non-deleted letters, compare them:
   - If one pointer is valid and the other has run out of bounds, the strings mismatch. Return `false`.
   - If both characters mismatch, return `false`.
5. Decrement both pointers and repeat until the strings are completely exhausted.

---

## Visual Understanding

Suppose `s = "ab#c"` and `t = "ad#c"`:

- **Initialization:** `i = 3`, `j = 3`, `skipS = 0`, `skipT = 0`.
- **First Iteration:**
  - String `s`: `s.charAt(3) = 'c'`. Valid character. Pointer holds at `i = 3`.
  - String `t`: `t.charAt(3) = 'c'`. Valid character. Pointer holds at `j = 3`.
  - Compare: `s[3] == t[3]` (`'c' == 'c'`). Move backward: `i = 2, j = 2`.
- **Second Iteration:**
  - String `s`: `s.charAt(2) = '#'`. Increment `skipS = 1`, move to `i = 1`. Next character is `'b'`. Since `skipS > 0`, clear it: `skipS = 0`, move to `i = 0`. Character at `i = 0` is `'a'`. Valid!
  - String `t`: `t.charAt(2) = '#'`. Increment `skipT = 1`, move to `j = 1`. Next character is `'d'`. Since `skipT > 0`, clear it: `skipT = 0`, move to `j = 0`. Character at `j = 0` is `'a'`. Valid!
- **Comparison:** Compare `s[0] == t[0]` (`'a' == 'a'`). Move backward: `i = -1, j = -1`.

Both pointers drop out of bounds simultaneously. The strings are verified as equal with zero extra allocations.

---

## Partition Variables

Let:

```java
int i = s.length() - 1;
int j = t.length() - 1;
int skipS = 0;
int skipT = 0;
```

---

### Border Elements

The internal skip loops must continually guard pointer limits to ensure they do not cross out of bounds during deletion skips:

```java
while (i >= 0) {
    if (s.charAt(i) == '#') { skipS++; i--; }
    else if (skipS > 0) { skipS--; i--; }
    else break;
}
```

---

## Correct Partition Condition

After evaluating the backspace skip boundaries for both text tracks, verify structural cross-alignment rules directly:

```java
if (i >= 0 && j >= 0 && s.charAt(i) != t.charAt(j)) {
    return false;
}
if ((i >= 0) != (j >= 0)) {
    return false; // One string ran out of characters prematurely
}
```

---

## Java Solution

```java
class Solution {

    public boolean backspaceCompare(String s, String t) {
        int i = s.length() - 1;
        int j = t.length() - 1;

        int skipS = 0;
        int skipT = 0;

        // Traverse both strings backward simultaneously
        while (i >= 0 || j >= 0) {
            
            // Step 1: Resolve all backspaces for string s to find the next valid character
            while (i >= 0) {
                if (s.charAt(i) == '#') {
                    skipS++;
                    i--;
                } else if (skipS > 0) {
                    skipS--;
                    i--;
                } else {
                    break; // Settled on a valid character position
                }
            }

            // Step 2: Resolve all backspaces for string t to find the next valid character
            while (j >= 0) {
                if (t.charAt(j) == '#') {
                    skipT++;
                    j--;
                } else if (skipT > 0) {
                    skipT--;
                    j--;
                } else {
                    break; // Settled on a valid character position
                }
            }

            // Step 3: Compare the isolated valid characters
            if (i >= 0 && j >= 0 && s.charAt(i) != t.charAt(j)) {
                return false; // Direct character mismatch
            }

            // Step 4: Verify if one string exhausted characters before the other
            if ((i >= 0) != (j >= 0)) {
                return false;
            }

            // Move inward for the next comparison track
            i--;
            j--;
        }

        return true;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Backward Double Scan** | **O(n + m)** | **O(1)** | Traverses both strings backward from the end at most once. Runs entirely in-place inside local register pointers with absolute zero auxiliary memory allocation. |

---

## Key Insight

Processing character modifications backward turns deletion tracks into simple addition/subtraction skip counters, eliminating the memory and tracking penalties of stack buffers.

```text
Time  : O(n + m) linear tracking pass
Space : O(1) absolute constant extra space
```

---

## Similar Problems

1. Longest Common Prefix (14)
2. Valid Palindrome (125)
3. Length of Last Word (58)
4. License Key Formatting (482)
5. Remove All Adjacent Duplicates In String (1047)
