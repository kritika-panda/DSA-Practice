# Infix to Prefix Conversion

## Problem Statement

Given an **infix expression** string `s` containing single-character operands (like letters `A-Z`, `a-z`, or digits `0-9`) and operators (`+`, `-`, `*`, `/`, `^`), along with grouping parentheses `(` and `)`, convert it into its equivalent **prefix expression** format.

- **Infix Expression:** Operators are written *between* operands (e.g., `A + B`).
- **Prefix Expression:** Operators are written *before* their corresponding operands (e.g., `+ A B`).

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
Input: s = "x+y*z/w"
Output: "+x/*yzw"
```

### Explanation

```text
Multiplication and division take precedence over addition. 
1. "y * z" becomes "*yz"
2. "(*yz) / w" becomes "/*yzw"
3. "x + (/*yzw)" becomes "+x/*yzw"
```

---

### Example 2

```java
Input: s = "(A+B)*C"
Output: "*+ABC"
```

### Explanation

```text
Parentheses override normal precedence. 
"(A+B)" is grouped first into "+AB", which is then multiplied by C to form "*+ABC".
```

---

# Intuition

The **Shunting-Yard Algorithm** is uniquely designed to produce *postfix* expressions when scanning from left to right. However, we can adapt it to produce a **prefix** expression by utilizing string inversion properties:

1. **Invert the Input:** If we reverse the infix string, it changes the processing direction from right to left.
2. **Flip Parentheses:** Reversing the string turns opening brackets `(` into closing brackets `)` and vice versa. We must swap them back to preserve correct grouping boundaries.
3. **Run a Modified Shunting-Yard Pass:** Run the standard operator stack algorithm on this reversed string. The only adjustment is handling **operator associativity**:
   - In postfix (left-to-right), an incoming left-associative operator pops equal-precedence operators off the stack (`>=`).
   - In prefix (right-to-left), we want to preserve the relative order of left-associative operators, so we only pop strictly higher precedence operators (`>`). For right-associative operators (`^`), we pop equal precedence as well (`>=`).
4. **Invert the Output:** Reversing the resulting string fixes the placement, turning the modified postfix order into a valid forward **prefix expression**.

---

# State Definition

Let our tracking structures be:

```text
Stack<Character> stack = new Stack<>(); // LIFO operator and scope separator cache
StringBuilder sb = new StringBuilder();  // Buffer collecting reversed prefix tokens
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

# Optimal Structural Reversed Shunting-Yard Solution

## Idea

By reversing the incoming expression, processing tokens with adjusted operator associativity rules via a LIFO stack, and reversing the compiled output, a true prefix sequence is derived. The complete transformation computes cleanly inside a linear **O(N)** time footprint.

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
                return 3;
        }
        return -1;
    }

    public String infixToPrefix(String s) {
        if (s == null || s.length() == 0) {
            return "";
        }

        // Step 1: Reverse the input string and flip the parentheses
        StringBuilder reversedInfix = new StringBuilder();
        for (int i = s.length() - 1; i >= 0; i--) {
            char c = s.charAt(i);
            if (c == '(') {
                reversedInfix.append(')');
            } else if (c == ')') {
                reversedInfix.append('(');
            } else {
                reversedInfix.append(c);
            }
        }

        StringBuilder sb = new StringBuilder();
        Stack<Character> stack = new Stack<>();
        String reversedStr = reversedInfix.toString();

        // Step 2: Run the modified Shunting-Yard algorithm
        for (int i = 0; i < reversedStr.length(); i++) {
            char c = reversedStr.charAt(i);

            // Case 1: If the character is an alphanumeric operand, stream it directly
            if (Character.isLetterOrDigit(c)) {
                sb.append(c);
            } 
            // Case 2: Opening parenthesis (originally a closing parenthesis)
            else if (c == '(') {
                stack.push(c);
            } 
            // Case 3: Closing parenthesis (originally an opening parenthesis)
            else if (c == ')') {
                while (!stack.isEmpty() && stack.peek() != '(') {
                    sb.append(stack.pop());
                }
                if (!stack.isEmpty() && stack.peek() == '(') {
                    stack.pop();
                }
            } 
            // Case 4: An operator arrives
            else {
                // Adjusting associativity for right-to-left scan:
                // - For left-associative operators (+, -, *, /), pop only strictly higher precedence ( > )
                // - For right-associative operators (^), pop equal or higher precedence ( >= )
                if (c == '^') {
                    while (!stack.isEmpty() && getPrecedence(c) <= getPrecedence(stack.peek())) {
                        sb.append(stack.pop());
                    }
                } else {
                    while (!stack.isEmpty() && getPrecedence(c) < getPrecedence(stack.peek())) {
                        sb.append(stack.pop());
                    }
                }
                stack.push(c);
            }
        }

        // Case 5: Empty out remaining operators on the stack
        while (!stack.isEmpty()) {
            sb.append(stack.pop());
        }

        // Step 3: Reverse the final output to convert to true prefix format
        return sb.reverse().toString();
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* String reversals take linear O(N) time. The inner core traversal processes each character precisely once, with every operator pushed and popped a single time. Total operational runtime remains strictly linear.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Memory is allocated linearly to house the stack variables, the temporary reversed input buffers, and the output compilation frames.

---

# Key Takeaways

✅ Core fundamental compiler parsing technique for mathematical tokens.

✅ **The Logic of String Inversion:** Reversing a grammar problem often allows you to leverage an existing well-known algorithm (like Shunting-Yard for postfix) with minor structural tweaks to its internal condition checks, avoiding writing entirely separate algorithmic engines from scratch.

✅ Direct foundational framework used inside **Abstract Syntax Tree (AST) creation blocks**, lisp-like prefix interpreter runtimes, and functional parsing compilers.

✅ Common Architectural Paradigm:

```text
Reversed LIFO Parsing for Leading Operator Precedence Layouts
```

### Similar Problems

- Infix to Postfix Conversion
- Postfix to Infix Conversion
- Prefix to Infix Conversion
- Evaluate Reverse Polish Notation (LeetCode 150)
- Parse Lisp Expression (LeetCode 736)
