# Prefix to Infix Conversion

## Problem Statement

Given a **prefix expression** string `s` containing single-character operands (like letters `A-Z`, `a-z`, or digits `0-9`) and operators (`+`, `-`, `*`, `/`, `^`), convert it into its equivalent **infix expression** format.

- **Prefix Expression:** Operators are written *before* their corresponding operands (e.g., `+ A B`).
- **Infix Expression:** Operators are written *between* operands (e.g., `(A + B)`).

Your conversion must ensure that the output infix expression preserves the correct operational precedence. To guarantee correctness and eliminate ambiguity without tracking manual precedence tables during the assembly step, wrap every sub-expression string inside a bounding parenthesis pair `( )`.

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
Output: "((A+B)*C)"
```

### Explanation

```text
1. The rightmost operator '+' takes operands 'A' and 'B' -> "(A+B)"
2. The operator '*' takes sub-expression "(A+B)" and operand 'C' -> "((A+B)*C)"
```

---

### Example 2

```java
Input: s = "+-*ABC/D" // Wait, let's track valid symbols: "+ - * A B C / D" -> No, let's check basic prefix: "+A*BC"
Input: s = "+A*BC"
Output: "(A+(B*C))"
```

### Explanation

```text
1. The rightmost operator '*' takes operands 'B' and 'C' -> "(B*C)"
2. The operator '+' takes operand 'A' and sub-expression "(B*C)" -> "(A+(B*C))"
```

---

# Intuition

The standard way to decode a **prefix** string is to read it **backward (from right to left)**. 

When scanning from right to left, we encounter the operands first, followed by the operators that act on them. This right-to-left chronological evaluation allows us to use a **Stack** to buffer the assembled string expressions dynamically:
1. **Push Operands:** If we encounter an alphanumeric operand character, we convert it into a string and push it directly onto our stack.
2. **Combine Sub-Expressions:** If we encounter an operator character, it means this operator must combine the two most recently resolved sub-expressions sitting at the top of the stack.
3. We pop the first element (let this be `operand1`) and the second element (let this be `operand2`). 
4. We then concatenate them with the operator placed right in the middle, wrapped securely inside a pair of parentheses to lock its precedence:
   ```text
   combinedString = "(" + operand1 + operator + operand2 + ")"
   ```
5. We push this new `combinedString` back onto the stack.
6. Once the backward scan completes, the final remaining element on the stack represents the fully assembled, equivalent infix expression.

---

# State Definition

The tracking data structure uses a single stack container to hold compiled intermediate strings:

```text
Stack<String> stack = new Stack<>(); // LIFO buffer combining parenthesized sub-expressions
```

---

# Operational Re-indexing Logic

```text
Scan s from right to left (i = n-1 down to 0):
    If s[i] is operand -> stack.push(s[i].toString())
    If s[i] is operator -> 
        op1 = stack.pop()
        op2 = stack.pop()
        stack.push("(" + op1 + s[i] + op2 + ")")
```

---

# Optimal Structural Backward Stack Solution

## Idea

By reversing the token evaluation order and scanning the prefix string from right to left, operand dependencies are established before operators are executed. Utilizing a stack to wrap string segments inside strict parenthesis enclosures eliminates any lookahead syntax ambiguity, completing the rewrite inside an optimal **O(N)** time footprint.

---

## Java Code

```java
import java.util.Stack;

class Solution {

    // Helper utility to verify if a character is a mathematical operator
    private boolean isOperator(char c) {
        return c == '+' || c == '-' || c == '*' || c == '/' || c == '^';
    }

    public String prefixToInfix(String prefix) {
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

                // Assemble the infix string wrapped inside parenthetical boundaries
                String combined = "(" + op1 + c + op2 + ")";
                
                // Push the combined sub-expression back onto the stack
                stack.push(combined);
            } 
            // Case 2: If the character is an operand, push it as a base string
            else if (Character.isLetterOrDigit(c)) {
                stack.push(String.valueOf(c));
            }
        }

        // The final remaining element on the stack is our fully assembled infix expression
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

✅ Core fundamental compiler parsing technique for transforming formal grammar tokens.

✅ **The Value of Directional Inversion:** Scanning prefix expressions from right to left completely eliminates the need to recursively look ahead for operand pairs. This turns a complex syntax parsing tree problem into a clean, simple stack reduction loop.

✅ Directly maps to real-world software utilities like **compiler syntax tree builders**, code formatting tools, and math notation translators.

✅ Common Architectural Paradigm:

```text
Right-to-Left Stack Reduction for Operator-Preceding Grammars
```

### Similar Problems

- Infix to Postfix Conversion
- Infix to Prefix Conversion
- Prefix to Postfix Conversion
- Postfix to Infix Conversion
- Evaluate Reverse Polish Notation (LeetCode 150)
