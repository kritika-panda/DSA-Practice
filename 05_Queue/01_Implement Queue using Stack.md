# Implement Queue Using Stacks (LeetCode 232)

## Problem Statement

Implement a **First-In, First-Out (FIFO)** Queue data structure using only two **Last-In, First-Out (LIFO)** Stacks. The implemented queue must support all standard queue operations safely and efficiently:
1. `push(x)` / `enqueue(x)`: Pushes element `x` to the back of the queue.
2. `pop()` / `dequeue()`: Removes the element from the front of the queue and returns it.
3. `peek()` / `front()`: Returns the element at the front of the queue without removing it.
4. `empty()`: Returns a boolean indicating whether the queue contains zero elements.

**Notes:**
- You must use only standard operations of a stack—which means only `push to top`, `peek/pop from top`, `size`, and `is empty` operations are valid.
- Depending on your language, the stack may not be supported natively. You may use a list or deque (double-ended queue) as long as you use it strictly as a standard LIFO stack.

The amortized time complexity of each core operation must be strictly bounded by:

```text
O(1)
```

---

## Example Execution Trace

```text
Initialize a MyQueue object:

1. push(10) → input stack:.        output stack: [ ]
2. push(20) → input stack:.    output stack: [ ]
3. peek()   → Shifts data. 
              input stack: [ ].         output stack: [20, 10] → Returns 10.
4. pop()    → Removes from output: Returns 10.
              input stack: [ ].         output stack: [20]
5. empty()  → Returns false.
```

---

# Intuition

A single stack reverses the chronological order of elements (LIFO). However, if we pop elements out of one stack and push them directly into a **second stack**, the order of those elements is **reversed a second time**, perfectly restoring the original First-In, First-Out (FIFO) chronological sequence!

We can implement this optimally using a **Two-Stack Amortized Layout**:
1. **`inputStack`**: Serves as our fast entry gate. Every single `push(x)` operation pushes `x` onto this stack instantly in O(1) constant time.
2. **`outputStack`**: Serves as our fast exit gate. Retrieval operations (`pop` and `peek`) read straight from the top of this stack.
3. **The Data Transfer Trigger:** When a retrieval operation occurs:
   - If `outputStack` already has elements, we read from it instantly.
   - If `outputStack` is completely empty, we pop *all* elements from `inputStack` one by one and push them into `outputStack`. This bulk transfer flips the elements into the correct FIFO order. 

While a single data transfer pass takes O(N) linear time, each element is moved across the stacks exactly once. This guarantees that over a sequence of operations, the **amortized time complexity per operation is exactly O(1) constant time**.

---

# State Definition

The structural state layout relies on two separate native stack containers:

```text
Stack<Integer> inputStack = new Stack<>();  // Fast insertion buffer
Stack<Integer> outputStack = new Stack<>(); // Fast retrieval buffer
```

---

# Operational Re-indexing Logic

```text
For peek() or pop():
    If outputStack is empty:
        While inputStack is not empty:
            outputStack.push(inputStack.pop())
```

---

# Optimal Dual-Stack Amortized Solution

## Idea

By decoupling data insertion from data retrieval and only reversing elements in bulk when the exit buffer runs completely dry, element transitions are balanced optimally. This satisfies the strict FIFO interface constraints without shifting elements back and forth during every operation.

---

## Java Code

```java
import java.util.Stack;

class MyQueue {
    // Two standard LIFO stacks to simulate FIFO behavior
    private final Stack<Integer> inputStack;
    private final Stack<Integer> outputStack;

    /** Initialize your data structure here. */
    public MyQueue() {
        this.inputStack = new Stack<>();
        this.outputStack = new Stack<>();
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        // High-speed constant time insertion
        inputStack.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        // Ensure the exit buffer is filled with the correct chronological sequence
        shiftElementsIfNeeded();
        if (outputStack.isEmpty()) {
            throw new IllegalStateException("Queue Underflow Error: Cannot dequeue from an empty queue.");
        }
        return outputStack.pop();
    }
    
    /** Get the front element. */
    public int peek() {
        // Ensure the exit buffer is filled with the correct chronological sequence
        shiftElementsIfNeeded();
        if (outputStack.isEmpty()) {
            throw new IllegalStateException("Queue Read Error: Cannot peek inside an empty queue.");
        }
        return outputStack.peek();
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        // The queue is completely empty only when both storage layers are clear
        return inputStack.isEmpty() && outputStack.isEmpty();
    }

    /** Helper method to transfer elements from input to output stack when empty */
    private void shiftElementsIfNeeded() {
        // Only trigger the inversion transfer tax if the exit gate is dry
        if (outputStack.isEmpty()) {
            while (!inputStack.isEmpty()) {
                outputStack.push(inputStack.pop());
            }
        }
    }
}

/**
 * Client validation application entry point.
 */
public class Main {
    public static void main(String[] args) {
        MyQueue queue = new MyQueue();

        queue.push(1);
        queue.push(2);
        
        System.out.println("Front element: " + queue.peek()); // Expected: 1
        System.out.println("Dequeued element: " + queue.pop()); // Expected: 1
        System.out.println("Is queue empty? " + queue.empty()); // Expected: false
    }
}
```

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Amortized Complexity | Explanation |
| :--- | :--- | :--- | :--- |
| **`push(x)`** | O(1) | O(1) | Direct insertion onto the top of `inputStack`. |
| **`pop()`** | O(N) worst-case | O(1) | O(1) amortized because elements are transferred in a single batch only when `outputStack` is empty. |
| **`peek()`** | O(N) worst-case | O(1) | Relies on the same conditional batch transfer rule. |
| **`empty()`** | O(1) | O(1) | Flat empty validation checks on both stack containers. |

---

### Space Complexity

```text
Space Complexity: O(N)
```
*(where N is the total number of element nodes stored across both internal stack data collections).*

---

# Key Takeaways

✅ Advanced data structure design challenge specializing in amortized cost accounting.

✅ **The Power of Amortized Analysis:** While a specific single `pop()` call can cost O(N) operations if it triggers a full data transfer, each element is pushed onto `inputStack` once, popped from `inputStack` once, pushed onto `outputStack` once, and popped from `outputStack` once. The total work per item across its entire lifetime is exactly 4 operations, yielding a stable **amortized cost of O(1)**.

✅ Foundational model used inside **buffered hardware architectures** where dual memory registers isolate asynchronous incoming data bursts from sequential output processing pipes.

✅ Common Architectural Pattern:

```text
Dual-Buffer Lazy Sequence Reversal via Stack Cascades
```

### Similar Problems

- Implement Stack Using Queues (LeetCode 225)
- Implement Stack Using Arrays
- Implement Queue Using Arrays
- Design Circular Queue (LeetCode 622)
