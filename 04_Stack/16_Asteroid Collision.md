# Asteroid Collision (LeetCode 735)

## Problem Statement

We are given an array `asteroids` of integers representing asteroids in a row.

For each asteroid, the absolute value represents its size, and the sign represents its direction (positive meaning right, negative meaning left). Each asteroid moves at the same speed.

Find out the state of the asteroids after all collisions. If two asteroids meet, the smaller one will explode. If both are the same size, both will explode. Two asteroids moving in the same direction will never meet.

The overall runtime complexity must be strictly bounded by:

```text
O(N)
```

*(where N is the length of the asteroids array)*

---

## Examples

### Example 1

```java
Input: asteroids = [5,10,-5]
Output: [5,10]
```

### Explanation

```text
The 10 and -5 collide resulting in 10. The 5 and 10 never collide because they move right in tandem.
```

---

### Example 2

```java
Input: asteroids = [8,-8]
Output: []
```

### Explanation

```text
The 8 and -8 collide exploding each other.
```

---

### Example 3

```java
Input: asteroids = [10,2,-5]
Output: [10]
```

### Explanation

```text
The 2 and -5 collide resulting in -5. Then the 10 and -5 collide resulting in 10.
```

---

# Intuition

This problem can be effectively modeled as a **Last-In, First-Out (LIFO)** simulation problem. 

Collisions can only occur when an asteroid moving **right (positive)** is followed somewhere down the line by an asteroid moving **left (negative)**. Asteroids moving left followed by right will move away from each other and never collide.

As we process the array sequentially from left to right, we can track surviving asteroids using a **Stack**:
1. **Unconditional Safe Additions:** If the stack is empty, or the current asteroid is moving right (`> 0`), or the asteroid at the top of the stack is moving left (`< 0`), no immediate collision is possible. We safely push the current asteroid onto the stack.
2. **The Collision Loop:** If the top of the stack is moving right (`> 0`) and the incoming asteroid is moving left (`< 0`), a collision sequence is triggered:
   * If the incoming left-moving asteroid is **larger** than the right-moving top element, the top element explodes (`stack.pop()`), and the left-moving asteroid continues its path to challenge the next item down the stack.
   * If they are the **same size**, both explode (`stack.pop()`), and we destroy the incoming asteroid.
   * If the right-moving top element is **larger**, the incoming left-moving asteroid explodes instantly, and the stack remains unchanged.

---

# State Definition

The linear simulation tracking relies on a single primitive stack structure:

```text
Stack<Integer> stack = new Stack<>(); // Buffer holding currently surviving asteroids
```

---

# Recurrence / Collision Resolution Condition

```java
import java.util.Stack;

class Solution {
    public int[] asteroidCollision(int[] asteroids) {
        Stack<Integer> stack = new Stack<>();

        for (int ast : asteroids) {
            boolean alive = true;

            while (!stack.isEmpty() && stack.peek() > 0 && ast < 0) {
                int topSize = stack.peek();
                int incomingSize = -ast;

                if (topSize < incomingSize) {
                    stack.pop();
                    continue; 
                } else if (topSize == incomingSize) {
                    stack.pop();
                    alive = false; 
                    break;
                } else {
                    alive = false;
                    break;
                }
            }

            if (alive) {
                stack.push(ast);
            }
        }

        int[] result = new int[stack.size()];
        for (int i = result.length - 1; i >= 0; i--) {
            result[i] = stack.pop();
        }

        return result;
    }
}
```

---

# Optimal Linear Simulation Solution

## Idea

By pushing stable asteroids onto a stack and using a back-pruning loop to handle cascades when a left-moving entity collides with right-moving ones, every asteroid undergoes at most two stack transformations. This tracks all explosive interactions within an optimal **O(N)** time envelope.

---

## Java Code

```java
import java.util.Stack;

class Solution {
    public int[] asteroidCollision(int[] asteroids) {
        if (asteroids == null || asteroids.length == 0) {
            return new int[0];
        }

        Stack<Integer> stack = new Stack<>();

        for (int ast : asteroids) {
            // Flag to track whether the incoming asteroid survives the collision wave
            boolean alive = true;

            // Collision condition: Top of stack moves RIGHT (+) and incoming moves LEFT (-)
            while (!stack.isEmpty() && stack.peek() > 0 && ast < 0) {
                int topSize = stack.peek();
                int incomingSize = -ast; // Absolute value of left-moving asteroid

                if (topSize < incomingSize) {
                    // Top asteroid is smaller -> it explodes, pop it, check next top
                    stack.pop();
                    continue; 
                } else if (topSize == incomingSize) {
                    // Equal sizes -> both explode
                    stack.pop();
                    alive = false; 
                    break;
                } else {
                    // Top asteroid is larger -> incoming explodes instantly
                    alive = false;
                    break;
                }
            }

            // If the incoming asteroid survived all active collisions, add it to the stack
            if (alive) {
                stack.push(ast);
            }
        }

        // Step 2: Convert the remaining stack elements into a primitive output array
        int[] result = new int[stack.size()];
        for (int i = result.length - 1; i >= 0; i--) {
            result[i] = stack.pop();
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
*Explanation:* The loop executes exactly N times. Each asteroid is pushed onto the stack at most once and popped from the stack at most once across the entire execution sweep. The total number of stack operations is bounded by 2N, ensuring a strict linear runtime footprint.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary storage space scales linearly to store the un-exploded asteroids inside the stack buffer structure in the worst-case scenario where zero collisions occur.

---

# Key Takeaways

✅ Core fundamental linear simulation archetype mapping physical boundaries onto directional markers.

✅ **The Stack De-escalation Pattern:** Utilizing a LIFO stack allows sequential interactions to cascade backwards logically. When an event at index `i` has a retroactive impact on historical records at indices `< i`, using a stack resolves these cascading updates without needing multiple nested scans.

✅ Directly models software configurations like **particle physics engines**, UI layer overlap occlusion event logs, and event handling buffers in network streaming frameworks.

✅ Common Architectural Paradigm:

```text
Retroactive Timeline Elimination via LIFO Stack Filtering
```

### Similar Problems

- Backspace String Compare (LeetCode 844)
- Remove All Adjacent Duplicates In String (LeetCode 1047)
- Robot Collisions (LeetCode 2751)
- Next Greater Element I (LeetCode 496)
- Daily Temperatures (LeetCode 739)
