# Infix to Postfix Conversion

## Problem Statement

Given an **infix expression** string `s` containing single-character operands (like letters `A-Z`, `a-z`, or digits `0-9`) and operators (`+`, `-`, `*`, `/`, `^`), along with grouping parentheses `(` and `)`, convert it into its equivalent **postfix expression** format.

- **Infix Expression:** Operators are written *between* operands (e.g., `A + B`).
- **Postfix Expression:** Operators are written *after* their corresponding operands (e.g., `A B +`).

The conversion must respect standard mathematical operator precedence and associativity rules:
1. Exponentiation (`^`) has the highest precedence and is **right-associative**.
2. Multiplication (`*`) and Division (`/`) have medium precedence and are **left-associative**.
3. Addition (`+`) and Subtraction (`-`) have the lowest precedence and are **left-associative**.

The overall runtime complexity must be strictly bounded by:

```text
O(N)
```

*(where N is the length of the string s)*

---

## Examples

### Example 1

```java
Input: s = "a+b*(c^d-e)^(f+g*h)-i"
Output: "abcd^e-fgh*+^*+i-"
```

---

### Example 2

```java
Input: s = "A+B*C"
Output: "ABC*+"
```

### Explanation

```text
Multiplication '*' has higher precedence than addition '+'. 
Therefore, 'B*C' is grouped first into 'BC*', and then '+' is appended to form 'ABC*+'.
```

---

# Intuition

To rearrange an expression from infix to postfix while respecting operator precedence, we need to temporarily hold onto operators until both of their operands have been fully printed out. Because operators with higher precedence must be processed before operators with lower precedence, this workflow follows a **Last-In, First-Out (LIFO)** structural pattern.

We can solve this problem optimally using a **Stack**:
1. **Stream Operands Directly:** As we scan the string from left to right, if we encounter an operand character, we append it to our output string buffer immediately.
2. **Handle Opening Parentheses:** When an opening parenthesis `(` appears, it marks the start of an isolated sub-expression. Push it onto the stack.
3. **Unwind Closing Parentheses:** When a closing parenthesis `)` appears, we must immediately pop operators off the stack and append them to our output until we hit its matching opening parenthesis `(`. Pop and discard the opening parenthesis.
4. **Enforce Operator Precedence Hierarchy:** When an operator arrives:
   - While the stack is not empty and the operator at the top of the stack has **greater than or equal precedence** to our incoming operator, we pop the top operator and append it to our output.
   - Once the stack is clear of higher-precedence blockers, we push our current incoming operator onto the stack.

---

# State Definition

Let our tracking variables be:

```text
Stack<Character> stack = new Stack<>(); // LIFO buffer holding operators and opening brackets
StringBuilder sb = new StringBuilder();  // Dynamic string buffer assembling the postfix sequence
```

---

# Precedence Mapping Logic

```text
Function precedence(char c):
    If c == '+' or c == '-' -> Return 1
    If c == '*' or c == '/' -> Return 2
    If c == '^'             -> Return 3
    Return -1
```

---

# Optimal Structural Shunting-Yard Solution

## Idea

By processing the expression in a single linear pass and utilizing a stack to enforce operator precedence bounds dynamically, character ordering is resolved efficiently. This handles complex nested sub-expressions smoothly inside a clean **O(N)** time footprint.

---

## Java Code

```java
import java.util.Stack;

class Solution {

    // Helper utility to define operator precedence hierarchies
    private int getPrecedence(char ch) {
        switch (ch) {
            case '+':
            case '-':
                return 1;
            case '*':
            case '/':
                return 2;
            case '^':
                return 3; // Highest precedence
        }
        return -1; // Brackets and invalid items return the baseline floor
    }

    public String infixToPostfix(String s) {
        if (s == null || s.length() == 0) {
            return "";
        }

        StringBuilder sb = new StringBuilder();
        Stack<Character> stack = new Stack<>();

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);

            // Case 1: If the character is an alphanumeric operand, stream it directly to output
            if (Character.isLetterOrDigit(c)) {
                sb.append(c);
            } 
            // Case 2: An opening parenthesis sets up an isolated nested scope frame
            else if (c == '(') {
                stack.push(c);
            } 
            // Case 3: A closing parenthesis unrolls all operators in the active scope frame
            else if (c == ')') {
                while (!stack.isEmpty() && stack.peek() != '(') {
                    sb.append(stack.pop());
                }
                // Guard Condition: Remove the matched opening parenthesis container
                if (!stack.isEmpty() && stack.peek() == '(') {
                    stack.pop();
                }
            } 
            // Case 4: An operator arrives
            else {
                // Enforce precedence: clear out higher or equal precedence operators first
                // Note: For right-associative operators like '^', use strict '>' instead of '>='
                while (!stack.isEmpty() && getPrecedence(c) <= getPrecedence(stack.peek())) {
                    // Exception check for right-associative operator '^'
                    if (c == '^' && stack.peek() == '^') {
                        break; 
                    }
                    sb.append(stack.pop());
                }
                // Push the current operator onto the stack
                stack.push(c);
            }
        }

        // Case 5: Empty out any leftover operators remaining on the stack frame
        while (!stack.isEmpty()) {
            sb.append(stack.pop());
        }

        return sb.toString();
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The algorithm performs a single pass over the string of length N. Each character is pushed onto and popped from the stack at most once, meaning total operations scale strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Memory allocation scales linearly to hold the operators inside the stack buffer and assemble characters inside the output `StringBuilder` container.

---

# Key Takeaways

✅ Core fundamental compiling pattern for parsing algebraic tokens.

✅ **The Value of the Shunting-Yard Template:** This procedure is a direct implementation of Edsger Dijkstra's famous **Shunting-Yard Algorithm**. It perfectly separates operand streams from operator precedence stacks, transforming human-readable infix notations into machine-executable linear tracks.

✅ Universally used inside **compiler expression parsers**, spreadsheet cell formula engines, and calculator math processing utilities.

✅ Common Architectural Paradigm:

```text
LIFO Precedence Buffering for Chronological Sequence Flattening
```

### Similar Problems

- Infix to Prefix Conversion
- Postfix to Infix Conversion
- Evaluate Reverse Polish Notation (LeetCode 150)
- Basic Calculator (LeetCode 224)
- Basic Calculator II (LeetCode 227)
