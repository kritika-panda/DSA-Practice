# Postfix to Prefix Conversion

## Problem Statement

Given a **postfix expression** string `s` containing single-character operands (like letters `A-Z`, `a-z`, or digits `0-9`) and operators (`+`, `-`, `*`, `/`, `^`), convert it into its equivalent **prefix expression** format.

- **Postfix Expression:** Operators are written *after* their corresponding operands (e.g., `A B +`).
- **Prefix Expression:** Operators are written *before* their corresponding operands (e.g., `+ A B`).

The overall runtime complexity must be strictly bounded by:

```text
O(N)
```

*(where N is the length of the string s)*

---

## Examples

### Example 1

```java
Input: s = "ab+c*"
Output: "*+abc"
```

### Explanation

```text
1. The first operator '+' encountered takes operands 'a' and 'b' -> "+ab"
2. The next operator '*' takes sub-expression "+ab" and operand 'c' -> "*+abc"
```

---

### Example 2

```java
Input: s = "abc*+"
Output: "+a*bc"
```

### Explanation

```text
1. The first operator '*' encountered takes the two most recent operands 'b' and 'c' -> "*bc"
2. The operator '+' takes operand 'a' and sub-expression "*bc" -> "+a*bc"
```

---

# Intuition

The standard way to decode a **postfix** string is to read it **forward (from left to right)**.

When scanning from left to right, we buffer operands until an operator arrives to bind them. This sequential chronological evaluation allows us to use a **Stack** to hold intermediate strings dynamically:
1. **Push Operands:** If we encounter an alphanumeric operand character, we push it onto our stack as a base string.
2. **Combine Sub-Expressions:** If we encounter an operator character, it means this operator must combine the two most recently resolved sub-expressions sitting at the top of the stack.
3. We pop the first element (let this be `operand2`, as it was pushed last) and the second element (let this be `operand1`).
4. We then concatenate them in **prefix order** by placing the operator at the absolute beginning of the string block:
   ```text
   combinedString = operator + operand1 + operand2
   ```
5. We push this new `combinedString` back onto the stack.
6. Once the forward scan completes, the final remaining element on the stack represents the fully assembled, equivalent prefix expression.

---

# State Definition

The tracking data structure uses a single stack container to hold compiled intermediate strings:

```text
Stack<String> stack = new Stack<>(); // LIFO buffer combining prefix sub-expressions
```

---

# Operational Re-indexing Logic

```text
Scan s from left to right (i = 0 to n-1):
    If s[i] is operand -> stack.push(s[i].toString())
    If s[i] is operator -> 
        op2 = stack.pop()
        op1 = stack.pop()
        stack.push(s[i] + op1 + op2)
```

---

# Optimal Structural Forward Stack Solution

## Idea

By processing the postfix expression from left to right, operand variables are collected until an operator triggers their structural realignment. Utilizing a stack to arrange substrings in an `operator + operand1 + operand2` layout completely eliminates the need for manual recursive tree generation or multi-pass string parsing, completing the transformation inside a fast **O(N)** time footprint.

---

## Java Code

```java
import java.util.Stack;

class Solution {

    // Helper utility to verify if a character is a mathematical operator
    private boolean isOperator(char c) {
        return c == '+' || c == '-' || c == '*' || c == '/' || c == '^';
    }

    public String postfixToPrefix(String postfix) {
        if (postfix == null || postfix.length() == 0) {
            return "";
        }

        Stack<String> stack = new Stack<>();
        int n = postfix.length();

        // Core Step: Scan the postfix expression forward (from left to right)
        for (int i = 0; i < n; i++) {
            char c = postfix.charAt(i);

            // Case 1: If the character is an operand, push it as a base string
            if (Character.isLetterOrDigit(c)) {
                stack.push(String.valueOf(c));
            }
            // Case 2: If the character is an operator, pop and combine sub-expressions
            else if (isOperator(c)) {
                // Guard Condition: Ensure stack has enough components to resolve
                if (stack.size() < 2) {
                    throw new IllegalArgumentException("Invalid postfix expression format.");
                }

                // In a left-to-right scan, the first popped item is the right operand
                String op2 = stack.pop();
                String op1 = stack.pop();

                // Assemble the prefix string layout: operator + operand1 + operand2
                String combined = c + op1 + op2;
                
                // Push the combined prefix sub-expression back onto the stack
                stack.push(combined);
            }
        }

        // The final remaining element on the stack is our fully assembled prefix expression
        if (stack.size() != 1) {
            throw new IllegalArgumentException("Invalid postfix expression format.");
        }

        return stack.pop();
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The algorithm performs a single pass over the string of length N from left to right. String concatenations take time proportional to the length of the substrings, but since each character is processed a constant number of times, the overall time complexity remains strictly linear.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary space scales linearly to hold the generated string segments inside the stack frames.

---

# Key Takeaways

✅ Core fundamental compiler parsing technique for transforming formal grammar tokens.

✅ **The Logic of Operand Extraction Order:** In a forward left-to-right postfix sweep, the operator immediately succeeds its operands. Because of the LIFO nature of the stack, the first item popped is always the **right operand (`op2`)**, and the second item popped is the **left operand (`op1`)**. Preserving this relative ordering during concatenation is mandatory to ensure non-commutative operations like subtraction (`-`) and division (`/`) map correctly.

✅ Directly maps to real-world software utilities like **compiler syntax tree translators**, intermediate language interpreters, and mathematical layout formatters.

✅ Common Architectural Paradigm:

```text
Left-to-Right Stack Reduction for Operator-Succeeding To Operator-Preceding Layouts
```

### Similar Problems

- Infix to Postfix Conversion
- Infix to Prefix Conversion
- Prefix to Infix Conversion
- Prefix to Postfix Conversion
- Postfix to Infix Conversion
- Evaluate Reverse Polish Notation (LeetCode 150)
