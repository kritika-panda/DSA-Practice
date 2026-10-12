# Remove Outermost Parentheses

## Problem Statement

A valid parentheses string is either empty `""`, `"(" + A + ")"` , or `A + B`, where `A` and `B` are valid parentheses strings, and `+` represents string concatenation.
- For example, `""`, `"()"`, `"(())()"`, and `"(()(()))"` are all valid parentheses strings.

A valid parentheses string `s` is primitive if it is non-empty, and there does not exist a way to split it into `s = A + B`, with `A` and `B` being non-empty valid parentheses strings.

Given a valid parentheses string `s`, consider its decomposition into primitive substrings: `s = P_1 + P_2 + ... + P_k`, where `P_i` are primitive valid parentheses strings.

Return *the string `s` after removing the outermost parentheses of every primitive substring in the primitive decomposition of `s`*.

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
s = "(()())(())"
```

**Output**

```java
"()()()"
```

**Explanation**

The input string is `"(()())(())"`, with primitive decomposition `"(()())" + "(())"`.
After removing outer parentheses of each part, this becomes `"()()"` + `"()"` = `"()()()"`.

---

### Example 2

**Input**

```java
s = "(()())(())(()(()))"
```

**Output**

```java
"()()()()(())"
```

**Explanation**

The input string is `"(()())(())(()(()))"`, with primitive decomposition `"(()())" + "(())" + "(()(()))"`.
After removing outer parentheses of each part, this becomes `"()()"` + `"()"` + `"()(())"` = `"()()()()(())"`.

---

### Example 3

**Input**

```java
s = "()()"
```

**Output**

```java
""
```

**Explanation**

The input string is `"()()"`, with primitive decomposition `"()" + "()"`.
After removing outer parentheses of each part, this becomes `""` + `""` = `""`.

---

## Brute Force Approach

Simulate the decomposition by maintaining an active character track via a stack to identify the precise boundaries of each primitive substring. Slice out each block, strip the first and last characters, and concatenate the inner frames.

### Steps

1. Maintain an empty `Stack<Character>` and an integer pointer `start = 0`.
2. Iterate forward through the string tracking index `i`.
3. If `s.charAt(i) == '('`, push it onto the stack. If it is `')'`, pop from the stack.
4. When the stack becomes completely empty, a primitive substring block boundary has been successfully isolated between indices `start` and `i`.
5. Extract the inner portion using `s.substring(start + 1, i)` and append it to a result buffer.
6. Update `start = i + 1` and repeat.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // Allocates memory frames for characters on the stack heap and generates multiple substring slices
```

While linear, pushing objects onto a heap collection and generating multiple intermediate substring slices creates heavy memory allocation footprints. We can optimize this process down to an absolute **zero-allocation tracking loop** using a single primitive primitive balance counter.

---

# Optimal Approach: Single-Pass In-Place Balance Counter

## Key Idea

Instead of tracking character symbols using a full stack object, we can maintain an active integer variable `opened` to represent the number of open parentheses that have not yet been closed.

An opening parenthesis `(` is **not** an outermost character if it opens a nested layer within a primitive block. This is true if `opened > 0`. 
Similarly, a closing parenthesis `)` is **not** an outermost character if it closes a nested layer within a primitive block. This is true if `opened > 1`.

1. Initialize a mutable `StringBuilder` and a primitive integer counter `opened = 0`.
2. Iterate through the string character by character from left to right:
   - **Case 1 (Opening Parenthesis `'('`):** If the character is `'('`, we check if `opened > 0`. If true, it is an inner bracket—append it to our builder. Then, increment `opened++`.
   - **Case 2 (Closing Parenthesis `')'`):** If the character is `')'`, we decrement `opened--` first. Then, we check if `opened > 0`. If true, it is an inner bracket—append it to our builder.
3. Returning the built string string sequence handles the stripping process natively in a single forward pass.

---

## Visual Understanding

Suppose `s = "(()())"`:

- **Initialization:** `opened = 0`, `StringBuilder sb = ""`. Scan from left to right:
- **i = 0 `'('`:** `opened` is 0. Condition `opened > 0` fails (this is the absolute outermost bracket!). Skip appending. Increment `opened` to 1.
- **i = 1 `'('`:** `opened` is 1. Condition `opened > 0` is true (inner layer!). Append it \(\implies\) `sb = "("`. Increment `opened` to 2.
- **i = 2 `')'`:** Decrement `opened` to 1 first. Condition `opened > 0` is true (inner layer!). Append it \(\implies\) `sb = "()"`.
- **i = 3 `'('`:** `opened` is 1. Condition `opened > 0` is true. Append it \(\implies\) `sb = "()("`. Increment `opened` to 2.
- **i = 4 `')'`:** Decrement `opened` to 1 first. Condition `opened > 0` is true. Append it \(\implies\) `sb = "()()"`.
- **i = 5 `')'`:** Decrement `opened` to 0 first. Condition `opened > 0` fails (outermost closing bracket!). Skip appending.

The final string `"()()"` is extracted cleanly with zero sub-slicing or stack allocation overhead.

---

## Partition Variables

Let:

```java
StringBuilder sb = new StringBuilder();
int opened = 0;
```

---

### Border Elements

The pre-decrement step for closing parentheses ensures that the terminal closing bracket of a primitive block (where `opened` falls back down to 0) is correctly recognized and ignored:

```java
if (c == ')') {
    opened--;
    if (opened > 0) sb.append(c);
}
```

---

## Correct Partition Condition

The counter tracking checks filter out the absolute outermost shell elements inside a flat linear loop sweep:

```java
for (char c : s.toCharArray()) {
    if (c == '(') {
        if (opened > 0) sb.append(c);
        opened++;
    } else if (c == ')') {
        opened--;
        if (opened > 0) sb.append(c);
    }
}
```

---

## Java Solution

```java
class Solution {

    public String removeOuterParentheses(String s) {
        if (s == null || s.length() == 0) {
            return "";
        }

        StringBuilder sb = new StringBuilder();
        int opened = 0;

        // Step 1: Scan each parenthesis character sequentially
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);

            if (c == '(') {
                // If opened > 0, this parenthesis is nested inside a primitive block
                if (opened > 0) {
                    sb.append(c);
                }
                opened++;
            } else if (c == ')') {
                opened--;
                // If opened > 0 after decrementing, this is an inner closing parenthesis
                if (opened > 0) {
                    sb.append(c);
                }
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
| **Balance Counter Scan** | **O(n)** | **O(n)** | Traverses the string exactly once. The `StringBuilder` allocates memory cells matching the size of the filtered output text with zero stack overhead. |

---

## Key Insight

Using an in-place nesting balance counter transforms an explicit structural string decomposition and slicing workflow into a simple, constant-space stream filtering pass.

```text
Time  : O(n) single pass forward execution
Space : O(n) dynamic string output builder allocation
```

---

## Similar Problems

1. Valid Parentheses (20)
2. Score of Parentheses (856)
3. Minimum Add to Make Parentheses Valid (921)
4. Decode String (394)
5. Crawler Log Folder (1598)
