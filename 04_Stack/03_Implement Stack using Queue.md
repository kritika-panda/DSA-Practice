# Implement Stack Using Queues (LeetCode 225)

## Problem Statement

Implement a **Last-In, First-Out (LIFO)** Stack data structure using only **First-In, First-Out (FIFO)** Queues. The implemented stack must support all standard stack operations safely and efficiently:
1. `push(x)`: Pushes element `x` onto the top of the stack.
2. `pop()`: Removes the element from the top of the stack and returns it.
3. `top()` / `peek()`: Returns the element at the top of the stack without removing it.
4. `empty()`: Returns a boolean indicating whether the stack contains zero elements.

**Notes:**
- You must use only standard operations of a queue—which means only `push to back`, `peek/pop from front`, `size`, and `is empty` operations are valid.
- Depending on your language, the queue may not be supported natively. You may use a list or deque (double-ended queue) as long as you use it strictly as a standard FIFO queue.

The amortized time complexity of the core retrieval operations should be highly optimal.

---

## Example Execution Trace

```text
Initialize a MyStack object:

1. push(10) → Queue state: [10]
2. push(20) → Invert state: [20, 10] (20 moves to the front of the queue)
3. top()    → Reads the front of the queue: Returns 20.
4. pop()    → Removes from the front: Returns 20. Queue state: [10]
5. empty()  → Returns false.
```

---

# Intuition

A queue removes elements from the front, while a stack removes elements from the top (the most recently added item). To make a queue behave like a stack, we must find a way to **force newly inserted elements to sit at the absolute front of the queue**.

We can achieve this elegantly by using a **single queue** and rotating its elements during the `push` operation:
1. When pushing an element `x`, we add it to the back of the queue as normal.
2. At this instant, `x` is trapped at the back, but it needs to be at the front to follow the LIFO stack order.
3. To fix this, we look at the size of the queue *before* `x` was added (let this be `n`). We then dequeue an element from the front and immediately enqueue it back to the rear, repeating this rotation exactly `n` times.
4. This systematically rolls all older elements behind `x`, shifting our newest element straight to the front seat. 

By restructuring the data during insertion, all retrieval operations (`pop`, `top`, `empty`) map directly to native queue methods and run in super-fast **O(1) constant time**.

---

# State Definition

The structural state configuration relies on a single native queue container:

```text
Queue<Integer> queue = new LinkedList<>(); // Standard FIFO queue driven collection
```

---

# Operational Re-indexing Logic

```text
For push(x):
    queue.offer(x)
    Loop queue.size() - 1 times:
        queue.offer(queue.poll())
```

---

# Optimal Single-Queue Architectural Solution

## Idea

By spending linear rotation time during the `push` operation to reverse the chronological order of elements on-the-fly, the internal queue remains pre-sorted into a valid LIFO layout at all times. This provides instant retrieval lookups without allocating secondary backup storage queues.

---

## Java Code

```java
import java.util.LinkedList;
import java.util.Queue;

class MyStack {
    // Single standard FIFO queue driving our LIFO stack state
    private final Queue<Integer> queue;

    /** Initialize your data structure here. */
    public MyStack() {
        this.queue = new LinkedList<>();
    }
    
    /** Push element x onto stack. */
    public void push(int x) {
        // Step 1: Add the new element to the back of the queue
        queue.offer(x);
        
        // Step 2: Rotate all preceding elements behind x
        int currentSize = queue.size();
        for (int i = 0; i < currentSize - 1; i++) {
            // Dequeue from front and immediately enqueue to back
            queue.offer(queue.poll());
        }
    }
    
    /** Removes the element on top of the stack and returns that element. */
    public int pop() {
        // Since the queue is kept pre-sorted, the top element sits right at the front
        if (empty()) {
            throw new IllegalStateException("Stack Underflow Error: Cannot pop from an empty stack.");
        }
        return queue.poll();
    }
    
    /** Get the top element. */
    public int top() {
        if (empty()) {
            throw new IllegalStateException("Stack Read Error: Cannot peek inside an empty stack.");
        }
        return queue.peek();
    }
    
    /** Returns whether the stack is empty. */
    public boolean empty() {
        return queue.isEmpty();
    }
}

/**
 * Client validation application entry point.
 */
public class Main {
    public static void main(String[] args) {
        MyStack stack = new MyStack();

        stack.push(1);
        stack.push(2);
        
        System.out.println("Top element: " + stack.top()); // Expected: 2
        System.out.println("Popped element: " + stack.pop()); // Expected: 2
        System.out.println("Is stack empty? " + stack.empty()); // Expected: false
    }
}
```

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Explanation |
| :--- | :--- | :--- |
| **`push(x)`** | O(N) | Iterates N-1 times to rotate elements, where N is the number of items currently in the stack. |
| **`pop()`** | O(1) | Direct removal from the front of the queue via `queue.poll()`. |
| **`top()`** | O(1) | Direct constant-time lookahead peek via `queue.peek()`. |
| **`empty()`** | O(1) | Native constant-time empty validation check. |

---

### Space Complexity

```text
Space Complexity: O(N)
```
*(where N is the total number of element nodes stored inside the internal queue container).*

---

# Key Takeaways

✅ Advanced structural data design puzzle focusing on collection transformations.

✅ **Push-Optimized vs. Pop-Optimized Trade-Offs:** The implementation detailed above is **Push-Optimized**, meaning we accept an O(N) tax on insertion to keep all retrieval workflows running in lightning-fast O(1) time. Alternatively, we could design a **Pop-Optimized** stack using *two* queues, leaving `push` at O(1) while paying an O(N) shift penalty during every single `pop` operation.

✅ Simulates real-world systems layouts like **restricted environment hardware controllers** where only basic FIFO pipelines are exposed via registers, but LIFO processing logic must be synthesized locally.

✅ Common Architectural Pattern:

```text
Cyclic Queue Rotation for Sequential Order Inversion
```

### Similar Problems

- Implement Queue Using Stacks (LeetCode 232)
- Implement Stack Using Arrays
- Implement Queue Using Arrays
- Min Stack (LeetCode 155)
