# Basic Calculator II (LeetCode 227)

## Problem Statement

Given a string `s` which represents an expression, evaluate this expression and return *the result of the evaluation*.

Note: You are **not** allowed to use any built-in function which evaluates strings as mathematical expressions, such as `eval()`.

The string `s` can contain:
- Digits `0-9`
- Plus `+` and minus `-` operators
- Multiplication `*` and division `/` operators
- Empty spaces `' '`

The overall runtime complexity must be strictly bounded by:

```text
O(N)
```

*(where N is the length of the string s)*

---

## Examples

### Example 1

```java
Input: s = "3+2*2"
Output: 7
```

---

### Example 2

```java
Input: s = " 3/2 "
Output: 1
```

---

### Example 3

```java
Input: s = " 3+5 / 2 "
Output: 5
```

---

# Intuition

Unlike *Basic Calculator I*, this problem does not contain parentheses `( )`, but it introduces higher-precedence operators: **multiplication (`*`) and division (`/`)**. 

To handle operator precedence correctly without multi-pass parsing, we can process the string from left to right while keeping track of the **last operator** we saw. 
1. When we parse a full `number`, we look back at the `lastOperator` that preceded it to decide what to do:
   - If the operator was `+` or `-`, we cannot add it to our final answer yet, because a high-precedence multiplication or division might be right around the corner. Instead, we push the number (or its negative counterpart) onto a **Stack** to hold it temporarily.
   - If the operator was `*` or `/`, it must be evaluated **immediately**. We pop the most recent value from our stack, perform the operation (`lastValue * number` or `lastValue / number`), and push the resulting product or quotient straight back onto the stack.
2. By delaying addition/subtraction and resolving multiplication/division instantly on the fly, the stack effectively flattens the expression. 
3. At the terminal boundary, we simply add up all the numbers remaining inside the stack to get our final result.

---

# Brute Force Solution

## Idea

A standard approach uses an explicit utility `Stack<Integer>` to cache intermediate numbers. We loop through the characters, building multi-digit integers, and push or pop elements based on operator states. At the end, a separate loop drains the stack to aggregate the total sum.

---

## Java Code

```java
import java.util.Stack;

class Solution {
    public int calculate(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        Stack<Integer> stack = new Stack<>();
        int number = 0;
        char lastOperator = '+'; // Seed with a default '+' operator block

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);

            if (Character.isDigit(c)) {
                number = 10 * number + (c - '0');
            }

            // Execute processing if the character is an operator OR we hit the final index boundary
            if (!Character.isDigit(c) && c != ' ' || i == s.length() - 1) {
                if (lastOperator == '+') {
                    stack.push(number);
                } else if (lastOperator == '-') {
                    stack.push(-number);
                } else if (lastOperator == '*') {
                    stack.push(stack.pop() * number);
                } else if (lastOperator == '/') {
                    stack.push(stack.pop() / number);
                }
                
                // Track the new incoming operator and reset the digit register
                lastOperator = c;
                number = 0;
            }
        }

        int totalSum = 0;
        while (!stack.isEmpty()) {
            totalSum += stack.pop();
        }

        return totalSum;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because every character index is scanned exactly once, and stack pushes/pops operate in constant time.
- **Space Complexity:** O(N) to store intermediate evaluation values inside the library `Stack` structure in the worst-case expression layout (e.g., `"1+1+1+1"`).

---

# State Definition (Optimal Approach)

To optimize performance and completely eliminate object-wrapping runtime taxes, we can bypass the stack collection entirely. Notice that we only ever need to look back at the **most recent value** pushed to the stack to perform multiplication or division. 

By replacing the stack container with a single tracking register variable `lastValue` and updating a rolling sum on-the-fly, we reduce our memory usage down to an absolute constant **O(1)** layout:

```java
int totalSum = 0;        // Rolling accumulator summing finalized flat terms
int lastValue = 0;       // Register caching the preceding subproblem evaluation term
int currentNumber = 0;   // Dynamic tracker assembling multi-digit integer inputs
char lastOperator = '+'; // Tracking variable recording the most recent active operator
```

---

# Recurrence / Structural State Condition

```java
if (Character.isDigit(c)) {
    currentNumber = 10 * currentNumber + (c - '0');
}
if (!Character.isDigit(c) && c != ' ' || i == s.length() - 1) {
    if (lastOperator == '+' || lastOperator == '-') {
        totalSum += lastValue; // Commit the previous term to the finalized pool
        lastValue = (lastOperator == '+') ? currentNumber : -currentNumber;
    } else if (lastOperator == '*') {
        lastValue = lastValue * currentNumber;
    } else if (lastOperator == '/') {
        lastValue = lastValue / currentNumber;
    }
    lastOperator = c;
    currentNumber = 0;
}
```

---

# Optimal O(1) Space Solution

## Idea

By resolving multiplication and division on-the-fly, keeping track of the active sub-expression term in a single variable (`lastValue`), and committing completed addition/subtraction segments to a running global total (`totalSum`) dynamically, we eliminate the need for an external stack array. This calculates the entire algebraic expression inside an optimal **O(1)** auxiliary space envelope.

---

## Java Code

```java
class Solution {
    public int calculate(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        int n = s.length();
        int totalSum = 0;
        int lastValue = 0;
        int currentNumber = 0;
        char lastOperator = '+'; // Default initial state to absorb the absolute first number

        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);

            if (Character.isDigit(c)) {
                // Parse multi-digit integer tokens natively
                currentNumber = 10 * currentNumber + (c - '0');
            }

            // Process calculations if character is an operator OR we reach the final index boundary
            if (!Character.isDigit(c) && c != ' ' || i == n - 1) {
                switch (lastOperator) {
                    case '+':
                        // Postpone addition: move lastValue to totalSum and update lastValue to current
                        totalSum += lastValue;
                        lastValue = currentNumber;
                        break;
                        
                    case '-':
                        // Postpone subtraction: move lastValue to totalSum and update lastValue to negative current
                        totalSum += lastValue;
                        lastValue = -currentNumber;
                        break;
                        
                    case '*':
                        // Execute immediate multiplication, updating our active subproduct term
                        lastValue = lastValue * currentNumber;
                        break;
                        
                    case '/':
                        // Execute immediate division, updating our active subquotient term
                        lastValue = lastValue / currentNumber;
                        break;
                }
                
                // Set up parameters for the next iteration sequence
                lastOperator = c;
                currentNumber = 0;
            }
        }

        // Commit the final remaining uncommitted tracking value to the global total pool
        totalSum += lastValue;

        return totalSum;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The array is traversed linearly exactly once. Inside, the code branches out into constant-time state updates and native arithmetic checks, keeping execution time bounded strictly linearly relative to the string length N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By using a rolling buffer register approach, it achieves an absolute constant space complexity, requiring no memory tables or auxiliary stacks.

---

# Key Pattern

This problem belongs to:

```text
Greedy Token Slicing + Precedence Buffer Evaluation
```

Common indicators:
- String mathematical parsing lacking nested parenthesis groupings.
- Multi-tier operator precedence limits require delaying baseline calculations while accelerating high-tier operators.
- State machines tracking historical operations using localized registers.

---

# Similar Problems

1. Basic Calculator (Includes parentheses, uses LIFO stacking - LeetCode 224)
2. Basic Calculator III (Unifies both parentheses and all operator tiers - LeetCode 772)
3. Evaluate Reverse Polish Notation (LeetCode 150)
4. Minimize Result by Adding Parentheses to Expression (LeetCode 2232)
