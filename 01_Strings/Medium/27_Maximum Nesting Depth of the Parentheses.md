# Maximum Nesting Depth of the Parentheses

**LeetCode 1614 - Maximum Nesting Depth of the Parentheses**

## Problem Statement

A string is a **valid parentheses string** (denoted VPS) if it satisfies any of the following:
* It is an empty string `""`, or a single character not equal to `"("` or `")"`.
* It can be written as `AB` (`A` concatenated with `B`), where `A` and `B` are VPS's.
* It can be written as `(A)`, where `A` is a VPS.

We can similarly define the **nesting depth** `depth(S)` of any VPS `S` as follows:
* `depth("") = 0`
* `depth(C) = 0`, where `C` is a string with a single character not equal to `"("` or `")"`.
* `depth(A + B) = max(depth(A), depth(B))`, where `A` and `B` are VPS's.
* `depth("(" + A + ")") = 1 + depth(A)`, where `A` is a VPS.

Given a VPS represented as a string `s`, return the **maximum nesting depth** of `s`.

---

## Example

```java
String s = "(1+(2*3)+((8)/4))+1";

Solution sol = new Solution();
sol.maxDepth(s); // Returns 3
```

**Explanation:** The digit `8` is inside of `3` nested pairs of parentheses. Therefore, the maximum nesting depth is `3`.

---

# Intuition

A naive thought might suggest using a **Stack** data structure to push opening brackets `(` and pop them when encountering closing brackets `)`. While a stack perfectly simulates structural nesting, it takes up `O(N)` auxiliary memory to hold the characters. 

Since the problem guarantees that the input string `s` is *always* a valid parentheses string (VPS), we do not need to validate the structure or store the brackets themselves. Instead, we can simply maintain a running integer counter representing the current depth of nesting.

The core observation is:
* Every time we see an opening parenthesis `(`, our nesting level **increases** by `1`.
* Every time we see a closing parenthesis `)`, our nesting level **decreases** by `1`.
* The maximum nesting depth of the string will simply be the **maximum value** that our depth counter reaches at any point during a single left-to-right pass.

---

# Approach: Counter Optimization (Simulation)

## Data Structure

```java
int currentDepth
```
An integer tracker that increments on `(` and decrements on `)`. This replaces the auxiliary space of a physical stack.

---

## Algorithm Steps

1. Initialize `currentDepth = 0` and `maxDepth = 0`.
2. Iterate through each character `ch` of the string `s` from left to right:
   - If `ch == '('`, increment `currentDepth` by `1`.
   - Update `maxDepth = Math.max(maxDepth, currentDepth)` to capture the peak nesting level.
   - If `ch == ')'`, decrement `currentDepth` by `1`.
3. Return `maxDepth` after the loop terminates.

---

# Dry Run

### Input
```java
s = "(1+((2)))"
```

### Execution Trace

- **Initialization:** `currentDepth = 0`, `maxDepth = 0`

| Index `i` | Character `ch` | `currentDepth` | `maxDepth` Update | Action Taken |
|---|---|---|---|---|
| `0` | `(` | `1` | `max(0, 1) -> 1` | Open bracket encountered |
| `1` | `1` | `1` | `1` | Non-bracket ignored |
| `2` | `+` | `1` | `1` | Non-bracket ignored |
| `3` | `(` | `2` | `max(1, 2) -> 2` | Open bracket encountered |
| `4` | `(` | `3` | `max(2, 3) -> 3` | Open bracket encountered |
| `5` | `2` | `3` | `3` | Non-bracket ignored |
| `6` | `)` | `2` | `3` | Close bracket encountered |
| `7` | `)` | `1` | `3` | Close bracket encountered |
| `8` | `)` | `0` | `3` | Close bracket encountered |

### Result
```java
maxDepth = 3
```

---

# Java Solution

```java
class Solution {
    public int maxDepth(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        int currentDepth = 0;
        int maxDepth = 0;

        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);

            if (ch == '(') {
                currentDepth++;
                maxDepth = Math.max(maxDepth, currentDepth);
            } else if (ch == ')') {
                currentDepth--;
            }
        }

        return maxDepth;
    }
}
```

---

# Complexity Analysis

### Time Complexity

```java
O(N)
```
The string is processed in a single pass of length `N`. At each character, the algorithm executes simple constant time `O(1)` conditional checks and arithmetic modifications, keeping overall runtime strictly linear.

### Space Complexity

```java
O(1)
```
Unlike stack-based approaches that scale with the depth of the nesting, this simulation uses only two integer pointers (`currentDepth` and `maxDepth`) allocating no auxiliary heap or array memory.

---

# Comparison of Approaches

| Approach | Time Complexity | Space Complexity | Pros / Cons |
|---|---|---|---|
| **Stack Emulation** | O(N) | O(N) | Tracks physical structure, but utilizes unnecessary memory overhead. |
| **Counter Optimization** (Optimal) | O(N) | O(1) | Highly optimized. Achieves optimal time and minimal memory bounds by leveraging structural validity guarantees. |

---

# Key Takeaways

- **Exploit String Constraints:** When a problem states that a string is already valid (like VPS), you can often strip away robust data structures like stacks or queues in favor of simplistic counters.
- **Peak Value Accumulation:** Tracking a rolling global maximum alongside a running localized state is a highly effective design pattern for depth, height, and frequency simulation problems.
- Core concepts:
  - String Parsing
  - Structural Simulation
  - State Tracking Optimizations
- Expected interview preference: **Counter Optimization Approach**.
