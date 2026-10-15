# Min Stack (LeetCode 155)

## Problem Statement

Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

Implement the `MinStack` class:
1. `MinStack()` initializes the stack object.
2. `void push(int val)` pushes the element `val` onto the stack.
3. `void pop()` removes the element on the top of the stack.
4. `int top()` gets the top element of the stack.
5. `int getMin()` retrieves the minimum element in the stack.

You must implement a solution with **O(1)** time complexity for each function.

---

## Example Execution Trace

```text
MinStack minStack = new MinStack();
1. minStack.push(-2); → Main Stack: [-2],      Min Stack: [-2]
2. minStack.push(0);  → Main Stack: [-2, 0],   Min Stack: [-2, -2]
3. minStack.push(-3); → Main Stack: [-2, 0, -3], Min Stack: [-2, -2, -3]
4. minStack.getMin(); → Returns -3.
5. minStack.pop();    → Main Stack: [-2, 0],   Min Stack: [-2, -2]
6. minStack.top();    → Returns 0.
7. minStack.getMin(); → Returns -2.
```

---

# Intuition

A normal stack already handles `push`, `pop`, and `top` in O(1) constant time. The core challenge is retrieving the minimum element in O(1) time without scanning the entire stack.

We can solve this problem by leveraging **State Synchronization with a Auxiliary Min Stack**:
- The minimum element of a stack changes predictably as elements are pushed or popped.
- We can maintain a secondary parallel stack called `minStack`. 
- For every element pushed onto the main stack, we push the *current running minimum* onto the `minStack`.
- When an element is popped from the main stack, we pop from the `minStack` as well to keep their histories perfectly in sync.
- The item sitting at the top of the `minStack` will always represent the absolute minimum element available in the main stack at that exact snapshot in time.

---

# State Definition

The structural state tracking utilizes two independent standard stack containers:

```java
import java.util.Stack;

class MinStack {
    private Stack<Integer> stack = new Stack<>();    // Primary data storage stack
    private Stack<Integer> minStack = new Stack<>(); // Secondary tracking stack for snapshots of minimums
}
```

---

# Recurrence / Stack Contract Condition

```java
public void push(int val) {
    stack.push(val);
    if (minStack.isEmpty() || val <= minStack.peek()) {
        minStack.push(val);
    } else {
        minStack.push(minStack.peek());
    }
}

public void pop() {
    stack.pop();
    minStack.pop();
}
```

---

# Optimal Structural Dual-Stack Solution

## Idea

By pairing a secondary min stack to track historical minimum boundaries alongside the primary data container, tracking changes requires only basic top comparisons. This isolates minimum mutations across insertions and deletions within a true **O(1)** time profile.

---

## Java Code

```java
import java.util.Stack;

class MinStack {
    // Primary stack to store the actual data elements
    private final Stack<Integer> stack;
    // Auxiliary stack to keep track of the minimum element snapshots
    private final Stack<Integer> minStack;

    public MinStack() {
        this.stack = new Stack<>();
        this.minStack = new Stack<>();
    }
    
    public void push(int val) {
        stack.push(val);
        
        // If auxiliary stack is empty or incoming val is smaller than or equal to 
        // the current minimum, it becomes our new local minimum snapshot
        if (minStack.isEmpty() || val <= minStack.peek()) {
            minStack.push(val);
        } else {
            // Otherwise, carry forward the existing minimum to keep histories in sync
            minStack.push(minStack.peek());
        }
    }
    
    public void pop() {
        if (stack.isEmpty()) {
            return;
        }
        // Pop from both stacks to keep their snapshot timelines synchronized
        stack.pop();
        minStack.pop();
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
}
```

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Explanation |
| :--- | :--- | :--- |
| **`push(val)`** | O(1) | Direct insertion on top of both underlying stacks. |
| **`pop()`** | O(1) | Removal from the top of both stacks. |
| **`top()`** | O(1) | High-speed constant-time `peek` lookup on the main stack. |
| **`getMin()`**| O(1) | High-speed constant-time `peek` lookup on the min stack. |

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary allocation memory scales linearly to store the elements inside both tracking containers, where N represents the maximum number of items pushed.

---

# Key Takeaways

✅ Core fundamental architecture specializing in snapshot historical state tracking.

✅ **Alternative Single-Stack Optimization:** You can implement a min stack using a **single stack** by storing the difference between the value and the minimum (`val - min`), or by pushing a custom `Pair(value, current_min)` object node onto the stack. However, the dual-stack approach is highly favored in interviews due to its clean separation of concerns and avoidance of integer overflow issues that can arise with difference-based math.

✅ Direct production blueprint matching **database undo logs** where snapshots of system state thresholds must be cached, version control history, and auditing trackers.

✅ Common Architectural Paradigm:

```text
Synchronized Auxiliary Stacking for Temporal Invariant Retrievals
```

### Similar Problems

- Max Stack (LeetCode 716)
- Implement Queue Using Stacks (LeetCode 232)
- Implement Stack Using Queues (LeetCode 225)
- Online Stock Span (LeetCode 901)
