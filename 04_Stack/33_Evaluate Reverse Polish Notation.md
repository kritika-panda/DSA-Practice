# Evaluate Reverse Polish Notation

## Problem Statement

You are given an array of strings `tokens` that represents an arithmetic expression in **Reverse Polish Notation (RPN)**.

Evaluate the expression and return its value.

Valid operators are:

```text
+
-
*
/
```

Each operand may be an integer or another expression.

Division between two integers should truncate toward zero.

---

## Example 1

```text
Input:
tokens = ["2","1","+","3","*"]

Output:
9
``*

### Evaluation

```text
(2 + 1) * 3

= 3 * 3

= 9
```

---

## Exam*le 2

```text
Input:
tokens = ["4","13","5","/","+"]

Output:
6
```

### Evaluation

```text
13 / 5 = 2

4 + 2 = 6
```

---

## Example 3

```text
Input:
tokens =
["10","6","9","3","+","-11","*","/","*","17","+","5","+"]

Output:
22
```

---

# What is Reverse Polish Notation?

Unlike normal expressions:

```text
2 + 3
```

RPN places the operator after operands.

```text
2 3 +
```

---

## Examples

### Infix

```text
(2 + 1) * 3
```

### Postfix (RPN)

```text
2 1 + 3 *
```

---

# Intuition

When processing tokens:

### Number

```text
Push into stack
```

### Operator

```text
Pop two elements
Apply operation
Push result back
```

Because:

```text
Operator always works on
most recent operands.
```

A stack perfectly supports this behavior.

---

# Data Structure

Use:

```java
Stack<Integer>
```

Operations:

```text
Push Number → O(1)

Pop Operands → O(1)

Push Result → O(1)
```

---

# Algorithm

Traverse every token.

---

## Case 1

Current token is a number.

```java
stack.push(number);
```

---

## Case 2

Current token is an operator.

Pop:

```java
b = stack.pop();
a = stack.pop();
```

Perform:

```java
a + b
a - b
a * b
a / b
```

Push result back.

---

# Important Observation

Order matters for:

```text
-
/
```

Example:

```text
4 2 -
```

Means:

```text
4 - 2
```

NOT

```text
2 - 4
```

Therefore:

```java
int b = stack.pop();
int a = stack.pop();
```

Then:

```java
a - b
```

---

# Java Code

```java
import java.util.*;

public class Main {

    public static int evalRPN(String[] tokens) {

        Stack<Integer> stack =
                new Stack<>();

        for (String token : tokens) {

            switch (token) {

                case "+":
                {
                    int b = stack.pop();
                    int a = stack.pop();

                    stack.push(a + b);
                    break;
                }

                case "-":
                {
                    int b = stack.pop();
                    int a = stack.pop();

                    stack.push(a - b);
                    break;
                }

                case "*":
                {
                    int b = stack.pop();
                    int a = stack.pop();

                    stack.push(a * b);
                    break;
                }

                case "/":
                {
                    int b = stack.pop();
                    int a = stack.pop();

                    stack.push(a / b);
                    break;
                }

                default:
                    stack.push(
                            Integer.parseInt(token)
                    );
            }
        }

        return stack.pop();
    }

    public static void main(String[] args) {

        String[] tokens =
                {"2","1","+","3","*"};

        System.out.println(
                evalRPN(tokens)
        );
    }
}
```

---

# Dry Run

## Input

```text
["2","1","+","3","*"]
```

---

### Token = "2"

```text
Stack = [2]
```

---

### Token = "1"

```text
Stack = [2,1]
```

---

### Token = "+"

Pop:

```text
1
2
```

Compute:

```text
2 + 1 = 3
```

Push:

```text
Stack = [3]
```

---

### Token = "3"

```text
Stack = [3,3]
```

---

### Token = "*"

Pop:

```text
3
3
```

Compute:

```text
3 * 3 = 9
```

Push:

```text
Stack = [9]
```

---

### Final Answer

```text
9
```

---

# Visualization

```text
Tokens:

2 1 + 3 *

Process:

2 → Push

1 → Push

+ → Pop 2,1
    Push 3

3 → Push

* → Pop 3,3
    Push 9
```

Final Stack:

```text
[9]
```

Answer:

```text
9
```

---

# Why Stack Works?

RPN always evaluates:

```text
Most recent operands first
```

Example:

```text
5 2 3 * +
```

Process:

```text
2 * 3 = 6

5 + 6 = 11
```

The most recent values are always needed first.

This is exactly:

```text
LIFO
```

behavior.

---

# Time Complexity

Let:

```text
N = number of tokens
```

Each token is processed once.

```text
O(N)
```

---

# Space Complexity

Stack may store:

```text
O(N)
```

elements in the worst case.

---

# Example with Division

Input:

```text
["4","13","5","/","+"]
```

Processing:

```text
13 / 5 = 2

4 + 2 = 6
```

Result:

```text
6
```

---

# Pattern Recognition

```text
Stack Pattern
│
├── Valid Parentheses
├── Min Stack
├── Evaluate Reverse Polish Notation ✅
├── Basic Calculator
├── Daily Temperatures
├── Next Greater Element
├── Largest Rectangle in Histogram
└── Remove K Digits
```

### Interview Trick

Whenever you see:

```text
Postfix Expression
Reverse Polish Notation
Expression Evaluation
Operator after Operands
```

Think:

```text
Stack
```

The key idea is:

```text
Number  → Push

Operator → Pop 2 values,
           evaluate,
           push result
```

This gives an optimal solution in:

```text
O(N)
```
time.
