# Valid Parentheses (LeetCode 20)

## Problem Statement

Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:
1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.

---

## Examples

### Example 1

```java
Input: s = "()"
Output: true
```

### Explanation

```text
The open bracket '(' is immediately closed by its matching pair ')'.
```

---

### Example 2

```java
Input: s = "()[]{}"
Output: true
```

### Explanation

```text
Every type of bracket opens and closes in the correct sequence.
```

---

### Example 3

```java
Input: s = "(]"
Output: false
```

### Explanation

```text
The open bracket '(' is incorrectly matched with a closing square bracket ']'.
```

---

# Intuition

To validate bracket ordering:

- The last open bracket encountered must be the very first one to close when a closing bracket appears.
- This behavior follows a **Last-In, First-Out (LIFO)** structural pattern.
- We can track this pattern using a **Stack**:
  1. When we see an opening bracket (`(`, `{`, `[`), we push it onto the stack.
  2. When we see a closing bracket (`)`, `}`, `]`), we check the top of the stack. If the stack is empty, or the top element doesn't match our closing bracket type, the order is broken.
  3. If it matches, we pop the open bracket off the stack and continue.

---

# Greedy Observation

Consider the string:

```text
s = "{ [ ] }"
```

Let's track the required matches layer by layer:

```text
Char '{' → Pushed onto stack.
Char '[' → Pushed onto stack.
Char ']' → Top of stack is '['. Matches perfectly! Pop '['.
Char '}' → Top of stack is '{'. Matches perfectly! Pop '{'.
```

By ensuring that each closing brace strictly targets the most recent unclosed matching entity, nested scopes validate cleanly without tracking historical mismatches.

---

# Approach: Stack Data Structure

## Algorithm

1. Initialize an empty character `Stack`.
2. Iterate through each character `c` in the string `s`:
   - If `c` is an opening bracket (`(`, `{`, `[`), push it onto the stack.
   - Otherwise, `c` is a closing bracket:
     - If the stack is empty, there is an unmatched closing bracket. Return `false`.
     - Pop the top character from the stack.
     - Verify if the popped opening bracket matches the current closing bracket `c`. If not, return `false`.
3. After exploring the entire string, check if the stack is completely empty. If it is empty, all brackets were resolved correctly. Return `true`. Otherwise, return `false`.

---

# Dry Run

```java
s = "([)]"
```

### Step 1 (c = '(')

```text
Opening bracket. Push '(' onto stack.
Stack = ['(']
```

---

### Step 2 (c = '[')

```text
Opening bracket. Push '[' onto stack.
Stack = ['(', '[']
```

---

### Step 3 (c = ')')

```text
Closing bracket. Stack is not empty.
Pop top element: '['.

Does '(' match ')'? ❌ (Popped '[' does not match closing ')')
Return false.
```

---

# Java Solution

```java
import java.util.Stack;

class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();

        for (char c : s.toCharArray()) {
            // Push opening brackets onto the stack
            if (c == '(' || c == '{' || c == '[') {
                stack.push(c);
            } 
            // Evaluate closing brackets
            else {
                // If stack is empty, we have an unmatched closing bracket
                if (stack.isEmpty()) {
                    return false;
                }
                
                char top = stack.pop();
                
                // Check for bracket mismatch
                if (c == ')' && top != '(') return false;
                if (c == '}' && top != '{') return false;
                if (c == ']' && top != '[') return false;
            }
        }

        // The string is valid only if all opening brackets have been closed
        return stack.isEmpty();
    }
}
```

---

# Optimized Understanding

Using a raw hash map or a helper conditional block map can make your code look even cleaner. Instead of pushing the opening bracket, we can push the **expected closing bracket** onto the stack.

```java
if (c == '(') stack.push(')');
else if (c == '{') stack.push('}');
else if (c == '[') stack.push(']');
else if (stack.isEmpty() || stack.pop() != c) return false;
```

This structural shift transforms your bracket validation comparison from a variable multi-state tree down to a simple equality check: `stack.pop() != c`.

---

# Correctness Proof

### Greedy Choice

We always evaluate a closing bracket against the absolute closest opening bracket sitting at the top of the stack.

Suppose we attempted to bypass the top element and match the closing bracket with an earlier, deeper open element down the stack instead.

```text
Stack : [ '{', '[' ] -> Incoming character: '}'
Match attempt: '{' with '}'
Leftover stack element: '[' (now permanently trapped)
```

This leaves the intermediate brackets stranded without an escape path, violating the nested scope requirement. Therefore, matching strictly with the top of the stack is always optimal.

---

### Optimal Substructure

Once a valid inner bracket pair is matched and removed via `stack.pop()`, the remaining outer bracket elements and string components form an identical valid parenthesis problem space.

---

# Complexity Analysis

Let:

```text
n = s.length()
```

### Time Complexity

Single-pass loop:

```text
O(n)
```

Overall:

```text
O(n)
```

---

### Space Complexity

```text
O(n)
```

In the worst-case scenario where the entire string consists of open brackets (e.g., `((((((( `), the stack stores all n characters.

---

# Key Pattern

This problem belongs to:

```text
Stack + Balanced LIFO Parsing
```

Common indicators:

- Checking balanced nested architectures
- String parsing with matching structural pairs
- Evaluating recursive scopes, code compilation parameters, or tag markers (HTML/XML)

---

# Similar Problems

1. Generate Parentheses (LeetCode 22)
2. Longest Valid Parentheses (LeetCode 32)
3. Remove Outermost Parentheses (LeetCode 1021)
4. Minimum Add to Make Parentheses Valid (LeetCode 921)
5. Check if a Parentheses String Can Be Valid (LeetCode 2116)

---

# Takeaway

```text
Use a LIFO Stack to process bracket pairs.

Push opening elements (or their expected closing inverses).

Pop and validate equality the moment a closing boundary is crossed, 
ensuring the stack ends completely empty.
```

**Pattern:** Stack + Balanced LIFO Parsing
