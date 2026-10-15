# Prefix to Postfix Conversion

## Problem Statement

Given a **prefix expression** string `s` containing single-character operands (like letters `A-Z`, `a-z`, or digits `0-9`) and operators (`+`, `-`, `*`, `/`, `^`), convert it into its equivalent **postfix expression** format.

- **Prefix Expression:** Operators are written *before* their corresponding operands (e.g., `+ A B`).
- **Postfix Expression:** Operators are written *after* their corresponding operands (e.g., `A B +`).

The overall runtime complexity must be strictly bounded by:

```text
O(N)
```

*(where N is the length of the string s)*

---

## Examples

### Example 1

```java
Input: s = "*+ABC"
Output: "AB+C*"
```

### Explanation

```text
1. The rightmost operator '+' takes operands 'A' and 'B' -> "AB+"
2. The operator '*' takes sub-expression "AB+" and operand 'C' -> "AB+C*"
```

---

### Example 2

```java
Input: s = "+A*BC"
Output: "ABC*+"
```

### Explanation

```text
1. The rightmost operator '*' takes operands 'B' and 'C' -> "BC*"
2. The operator '+' takes operand 'A' and sub-expression "BC*" -> "ABC*+"
```

---

# Intuition

Similar to the **Prefix to Infix** conversion, the cleanest way to decode a **prefix** string is to read it **backward (from right to left)**.

When scanning from right to left, we build our operands from the bottom up before evaluating their trailing operators. This allows us to use a **Stack** to buffer the changing string expressions dynamically:
1. **Push Operands:** If we encounter an alphanumeric operand character, we push it onto our stack as a base string.
2. **Combine Sub-Expressions:** If we encounter an operator character, it means this operator must join the two most recently resolved sub-expressions sitting at the top of the stack.
3. We pop the first element (let this be `operand1`) and the second element (let this be `operand2`).
4. We then concatenate them in **postfix order** by placing the operator at the absolute end of the string block:
   ```text
   combinedString = operand1 + operand2 + operator
   ```
5. We push this new `combinedString` back onto the stack.
6. Once the backward scan completes, the final remaining element on the stack represents the fully assembled, equivalent postfix expression.

---

# State Definition

The tracking data structure uses a single stack container to hold compiled intermediate strings:

```text
Stack<String> stack = new Stack<>(); // LIFO buffer combining postfix sub-expressions
```

---

# Operational Re-indexing Logic

```text
Scan s from right to left (i = n-1 down to 0):
    If s[i] is operand -> stack.push(s[i].toString())
    If s[i] is operator -> 
        op1 = stack.pop()
        op2 = stack.pop()
        stack.push(op1 + op2 + s[i])
```

---

# Optimal Structural Backward Stack Solution

## Idea

By processing the prefix expression from right to left, operand dependencies are established before operators are evaluated. Utilizing a stack to arrange substrings in an `operand1 + operand2 + operator` layout completely eliminates the need for expensive multi-pass grammar rewriting or lookahead syntax checks, completing the transformation inside a fast **O(N)** time footprint.

---

## Java Code

```java
import java.util.Stack;

class Solution {

    // Helper utility to verify if a character is a mathematical operator
    private boolean isOperator(char c) {
        return c == '+' || c == '-' || c == '*' || c == '/' || c == '^';
    }

    public String prefixToPostfix(String prefix) {
        if (prefix == null || prefix.length() == 0) {
            return "";
        }

        Stack<String> stack = new Stack<>();
        int n = prefix.length();

        // Core Step: Scan the prefix expression backward (from right to left)
        for (int i = n - 1; i >= 0; i--) {
            char c = prefix.charAt(i);

            // Case 1: If the character is an operator, pop and combine sub-expressions
            if (isOperator(c)) {
                // Guard Condition: Ensure stack has enough components to resolve
                if (stack.size() < 2) {
                    throw new IllegalArgumentException("Invalid prefix expression format.");
                }

                // In a right-to-left scan, the first popped item is the left operand
                String op1 = stack.pop();
                String op2 = stack.pop();

                // Assemble the postfix string layout: operand1 + operand2 + operator
                String combined = op1 + op2 + c;
                
                // Push the combined postfix sub-expression back onto the stack
                stack.push(combined);
            } 
            // Case 2: If the character is an operand, push it as a base string
            else if (Character.isLetterOrDigit(c)) {
                stack.push(String.valueOf(c));
            }
        }

        // The final remaining element on the stack is our fully assembled postfix expression
        if (stack.size() != 1) {
            throw new IllegalArgumentException("Invalid prefix expression format.");
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
*Explanation:* The algorithm performs a single pass over the string of length N from right to left. String concatenations take time proportional to the length of the substrings, but since each character is processed a constant number of times, the overall time complexity remains strictly linear.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary space scales linearly to hold the generated string segments inside the stack frames.

---

# Key Takeaways

✅ Core fundamental compiler parsing technique for mathematical tokens.

✅ **The Value of Directional Inversion:** Just like Prefix to Infix, scanning prefix expressions from right to left completely eliminates the need to recursively look ahead for operand pairs. It turns a complex syntax parsing tree problem into a clean, simple stack reduction loop.

✅ Directly maps to real-world software utilities like **compiler syntax tree translators**, execution bytecode formatters, and mathematical engine translators.

✅ Common Architectural Paradigm:

```text
Right-to-Left Stack Reduction for Operator-Preceding To Operator-Succeeding Layouts
```

### Similar Problems

- Infix to Postfix Conversion
- Infix to Prefix Conversion
- Prefix to Infix Conversion
- Postfix to Infix Conversion
- Postfix to Prefix Conversion
- Evaluate Reverse Polish Notation (LeetCode 150)
