# Valid Parentheses

## Problem Statement

Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:
1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.

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
s = "()"
```

**Output**

```java
true
```

---

### Example 2

**Input**

```java
s = "()[]{}"
```

**Output**

```java
true
```

---

### Example 3

**Input**

```java
s = "(]"
```

**Output**

```java
false
```

---

### Example 4

**Input**

```java
s = "([)]"
```

**Output**

```java
false
```

---

## Brute Force Approach

Repeatedly find and replace matching adjacent pairs of brackets (`"()"`, `"[]"`, `"{}"`) with an empty string until no more such pairs remain.

### Steps

1. Run a loop that searches for substrings `"()"`, `"[]"`, or `"{}"` in the string.
2. If any matching pair is found, replace it with `""` (empty string).
3. Repeat the cycle until the string contains no matching adjacent pairs.
4. If the final string is completely empty, return `true`; otherwise, return `false`.

### Complexity

```text
Time Complexity: O(n^2) // due to repeatedly creating new strings and shifting characters
Space Complexity: O(n) to store the modified strings
```

Repeatedly slicing and shrinking strings creates heavy performance bottlenecks for deeply nested inputs. We can optimize this process to a single pass using a stack.

---

# Optimal Approach: Stack-Based LIFO Tracking

## Key Idea

We can solve this problem in strict linear **O(n) time** by using a **Stack (LIFO)**.

Because brackets must close in the reverse order of how they were opened, the most recently opened bracket must be the first one to close. This fits a stack data structure perfectly:
1. Iterate through the string character by character.
2. **If we see an opening bracket** (`(`, `[`, `{`), we push its corresponding closing bracket onto the stack. This simplifies downstream matching logic.
3. **If we see a closing bracket**, it must match the bracket currently sitting at the top of the stack. We pop the top element from the stack and compare it:
   - If the stack is empty before popping, it means we have a closing bracket with no opening partner. Return `false`.
   - If the popped bracket does not match the current character, the order is invalid. Return `false`.
4. After scanning the entire string, the stack must be completely empty. If any elements remain, it means some opening brackets were never closed.

---

## Visual Understanding

Suppose `s = "([)]"`:

- **Character 0 `'('`:** Opening bracket. Push its closing partner `')'` onto the stack. Stack = `[')']`.
- **Character 1 `'['`:** Opening bracket. Push its closing partner `']'` onto the stack. Stack = `[')', ']']`.
- **Character 2 `')'`:** Closing bracket. Pop from the stack. Top element is `']'`.
- **Comparison:** Current character `')'` does not match the popped bracket `']'`. Order contract violated! Return `false` immediately.

The parsing stops without executing any extra redundant passes.

---

## Partition Variables

Let:

```java
Deque<Character> stack = new ArrayDeque<>();
```

*(Note: In modern Java, `ArrayDeque` is highly preferred over the legacy `Stack` class because it is faster and avoids redundant synchronization lock overhead).*

---

### Border Elements

The checking logic verifies if the stack has run completely out of elements prematurely when processing a trailing edge closing token:

```java
if (stack.isEmpty() || stack.pop() != c) {
    return false;
}
```

---

## Correct Partition Condition

The mapping routine pushes matching closing pairs during the forward sweep:

```java
if (c == '(') {
    stack.push(')');
} else if (c == '[') {
    stack.push(']');
} else if (c == '{') {
    stack.push('}');
}
```

---

## Java Solution

```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {

    public boolean isValid(String s) {
        if (s == null || s.length() == 0) {
            return true;
        }

        // Fast-fail: An odd length string can never be completely balanced
        if (s.length() % 2 != 0) {
            return false;
        }

        // Use Deque as a strict, high-performance Stack implementation
        Deque<Character> stack = new ArrayDeque<>();

        // Single pass scan through the string characters
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);

            // Step 1: If it's an opening bracket, push its expected closing partner
            if (c == '(') {
                stack.push(')');
            } else if (c == '[') {
                stack.push(']');
            } else if (c == '{') {
                stack.push('}');
            } 
            // Step 2: If it's a closing bracket, it must match the top of the stack
            else {
                if (stack.isEmpty() || stack.pop() != c) {
                    return false; // Mismatch or empty stack means invalid structure
                }
            }
        }

        // Step 3: The string is valid only if all pushed brackets were successfully closed
        return stack.isEmpty();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Stack Processing** | **O(n)** | **O(n)** | Traverses the string exactly once while allocating up to `n` characters on the stack in the worst-case scenario. |

---

## Key Insight

Pushing expected closing brackets onto a stack transforms a distributed symbol-matching problem into a series of constant-time adjacent character checks.

```text
Time  : O(n) single pass execution
Space : O(n) dynamic stack memory tracking allocation
```

---

## Similar Problems

1. Minimum Add to Make Parentheses Valid (921)
2. Generate Parentheses (22)
3. Longest Valid Parentheses (32)
4. Minimum Remove to Make Valid Parentheses (1249)
5. Check if Word Is Valid After Substitutions (1003)
