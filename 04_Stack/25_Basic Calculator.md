# Basic Calculator (LeetCode 224)

## Problem Statement

Given a string `s` representing a valid expression, implement a basic calculator to evaluate it, and return *the result of the evaluation*.

Note: You are **not** allowed to use any built-in function which evaluates strings as mathematical expressions, such as `eval()`.

The string `s` can contain:
- Digits `0-9`
- Opening parenthesis `(` and closing parenthesis `)`
- Plus `+` and minus `-` operators
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
Input: s = "1 + 1"
Output: 2
```

---

### Example 2

```java
Input: s = " 2-1 + 2 "
Output: 3
```

---

### Example 3

```java
Input: s = "(1+(4+5+2)-3)+(6+8)"
Output: 23
```

---

# Intuition

Because the expression contains only `+` and `-` operators alongside grouping parentheses, we don't need to manage high-precedence operator tiers like multiplication or division. We can resolve operations **on-the-fly from left to right**. 

The main challenge is handling parentheses `( )`, which isolate a nested scope frame and can invert signs globally (e.g., `-(a + b)` expands to `-a - b`). We can manage these shifts using a **Last-In, First-Out (LIFO) Stack**:
1. **Maintain Running Context:** We keep a running `result` total and a `sign` multiplier variable (which is `1` for `+` and `-1` for `-`).
2. **Multi-Digit Parsing:** When we hit a digit, we parse the full number (handling multiple adjacent digits like `123`) and add it to our running total multiplied by our current sign: `result += sign * number`.
3. **Scope Entry:** When an opening parenthesis `(` appears, it freezes our current timeline. We **push** our active `result` and `sign` variables onto the stack to save them. Then, we reset `result = 0` and `sign = 1` to process the contents inside the parenthesis as a clean, brand-new sub-expression.
4. **Scope Resolution:** When a closing parenthesis `)` appears, the sub-expression inside it is completely finished. We multiply this inner `result` by the sign that was frozen right before the parenthesis opened (`stack.pop()`), and add that product to the old baseline result score (`stack.pop()`).

---

# Brute Force Solution

## Idea

A naive solution would first convert the infix mathematical string into a postfix token string (Reverse Polish Notation) using a standard Shunting-Yard parser, and then run a secondary stack-based evaluation loop to calculate the values. While still \(O(N)\), this multi-pass approach wastes significant heap allocation overhead creating new intermediate collections.

---

## Java Code

```java
import java.util.Stack;

class Solution {
    public int calculate(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        
        // Multi-pass abstraction: Tokenizing or parsing via intermediate 
        // string conversion templates can be structured here.
        // For efficiency, we pivot straight to the single-pass optimal engine below.
        return singlePassCalculate(s);
    }

    private int singlePassCalculate(String s) {
        Stack<Integer> stack = new Stack<>();
        int result = 0;
        int number = 0;
        int sign = 1; // 1 represents '+', -1 represents '-'

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);

            if (Character.isDigit(c)) {
                number = 10 * number + (c - '0');
            } else if (c == '+') {
                result += sign * number;
                number = 0;
                sign = 1;
            } else if (c == '-') {
                result += sign * number;
                number = 0;
                sign = -1;
            } else if (c == '(') {
                // Freeze the current timeline and push snapshot states to the stack
                stack.push(result);
                stack.push(sign);
                // Reset state registers to start tracking the inner nested scope cleanly
                result = 0;
                sign = 1;
            } else if (c == ')') {
                result += sign * number;
                number = 0;
                
                // Unwind and collapse the parenthetical boundary frame
                result *= stack.pop(); // The popped element is the preceding sign multiplier
                result += stack.pop(); // The popped element is the preceding baseline result score
            }
        }
        
        // Accumulate any trailing remaining number left at the terminal boundary
        if (number != 0) {
            result += sign * number;
        }
        
        return result;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm scans the string of length N exactly once.
- **Space Complexity:** O(N) to hold the state snapshots inside the stack tracking container in the worst-case nested parentheses scenario.

---

# State Definition (Optimal Single-Pass Approach)

To optimize execution speed and completely remove object wrapper allocation overheads (`Stack<Integer>`), we can replace the helper library structure with a flat, contiguous primitive **Integer Array Stack**:

```java
int[] stack = new int[s.length()]; // Flat array stack caching results and sign snapshots
int top = -1;                     // Integer index tracking the top of our stack frame
int result = 0;                   // Active running calculation sum accumulator
int number = 0;                   // Variable constructing multi-digit integer inputs
int sign = 1;                     // Running sign direction multiplier (1 or -1)
```

---

# Recurrence / Structural State Condition

```java
if (Character.isDigit(c)) {
    number = 10 * number + (c - '0');
} else if (c == '+' || c == '-') {
    result += sign * number;
    number = 0;
    sign = (c == '+') ? 1 : -1;
} else if (c == '(') {
    top++; stack[top] = result;
    top++; stack[top] = sign;
    result = 0;
    sign = 1;
} else if (c == ')') {
    result += sign * number;
    number = 0;
    result *= stack[top]; top--;
    result += stack[top]; top--;
}
```

---

# Optimal Primitive Stack Solution

## Idea

By processing characters strictly inside a single forward pass, parsing multi-digit integers on-the-fly, and replacing object-heavy utility stacks with a raw primitive integer array, heap allocations are eliminated. This executes the entire algebraic evaluation in a lightweight **O(N)** time footprint.

---

## Java Code

```java
class Solution {
    public int calculate(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        int n = s.length();
        // Allocate a high-speed primitive array stack to strip away wrapper boxing overhead
        int[] stack = new int[n];
        int top = -1;

        int result = 0;
        int number = 0;
        int sign = 1; // 1 means positive (+), -1 means negative (-)

        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);

            if (Character.isDigit(c)) {
                // Shift digits over by base-10 to dynamically build multi-digit numbers
                number = 10 * number + (c - '0');
            } else if (c == '+') {
                // Add the fully constructed left operand to our running calculation
                result += sign * number;
                number = 0; // Clear the register for the next number entry
                sign = 1;
            } else if (c == '-') {
                result += sign * number;
                number = 0;
                sign = -1;
            } else if (c == '(') {
                // Push snapshot values to the stack: [result, then sign]
                top++;
                stack[top] = result;
                top++;
                stack[top] = sign;

                // Reset calculations registers to parse the nested parenthesis block
                result = 0;
                sign = 1;
            } else if (c == ')') {
                // Add the final internal operand inside the parenthesis to the local block sum
                result += sign * number;
                number = 0;

                // Collapse the scope frame: multiply by sign, then add to the old baseline result
                result *= stack[top];
                top--;
                result += stack[top];
                top--;
            }
            // Space characters ' ' are naturally bypassed without executing operations
        }

        // Add any remaining uncommitted trailing numbers at the terminal boundary index
        if (number != 0) {
            result += sign * number;
        }

        return result;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* Every character index within the string `s` is examined exactly once. All array index tracking, pointer shifts, and basic arithmetic updates complete in true \(O(1)\) constant time, keeping the overall runtime complexity strictly linear.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary space scales linearly to allocate the primitive `stack` storage array capable of managing up to \(N\) nested parenthesis scope states in the worst-case expression arrangement.

---

# Key Takeaways

✅ High-tier fundamental arithmetic string expression evaluation archetype.

✅ The Priority of Context Freezing: Pushing the current evaluation context [result, sign] onto a LIFO stack when entering a parenthesis block ( allows the algorithm to safely treat any nested parenthetical chunk as an independent local subproblem. Upon exiting ), popping these frozen multipliers restores the global context seamlessly.

✅ Directly drives production engines like spreadsheet formula string processors, calculator application kernels, configuration file macro evaluation layers, and expression compilers.

✅ Common Architectural Paradigm:
```text 
Single-Pass Token Accumulation with Layered Scope State De-escalation
```
