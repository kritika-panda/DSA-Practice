# Implement Queue Using Linked List

## Problem Statement

Design a **First-In, First-Out (FIFO)** Queue data structure using a singly linked list. The queue must support all standard queue operations safely, efficiently, and with completely elastic growth capabilities:
1. `enqueue(x)` / `push(x)`: Adds an integer `x` to the back (rear) of the queue.
2. `dequeue()` / `pop()`: Removes and returns the element from the front of the queue. If the queue is empty, it should handle a structural underflow (e.g., return a sentinel value like `-1`).
3. `front()` / `peek()`: Returns the element at the front of the queue without removing it.
4. `isEmpty()`: Returns a boolean indicating whether the queue contains zero elements.

The runtime complexity of each core operation (`enqueue`, `dequeue`, `front`, `isEmpty`) must be strictly bounded by:

```text
O(1)
```

---

## Example Execution Trace

```text
Initialize an empty Queue: front -> null, rear -> null (size=0)

1. enqueue(10) → Node(10) formed. front -> (10) <- rear
2. enqueue(20) → Links to rear.   front -> (10) -> (20) <- rear
3. peek()      → Reads front: Returns 10.
4. dequeue()   → Removes front node. Returns 10. front -> (20) <- rear
5. isEmpty()   → Returns false.
```

---

# Intuition

A queue inserts elements at one end (`rear`) and removes them from the opposite end (`front`).

When building this behavior on top of a Singly Linked List, traversing the entire list from head to tail to append a new element would take an inefficient O(N) linear time. To ensure optimal **O(1) constant-time performance** for insertions, we maintain **two independent reference pointers**:
- **`front` Pointer:** Points to the head node of the linked list. Deletions (`dequeue`) take place here by shifting `front = front.next`.
- **`rear` Pointer:** Points to the absolute terminal tail node of the list. Insertions (`enqueue`) append elements straight here via `rear.next = newNode` followed by moving the `rear` pointer to the new node.

By maintaining explicit control over both structural endpoints, elements pass through the collection seamlessly with zero traversal overhead.

---

# State Definition

Let our internal data tracking pointers be:

```text
Node front; // Reference pointer tracking the front node of the queue (head)
Node rear;  // Reference pointer tracking the tail node of the queue
int size;   // Scalar counter tracking the active number of elements currently stored
```

---

# Operational Guard Conditions

```text
Queue Underflows : If (front == null) or (size == 0) -> Queue Underflow! Cannot dequeue.
```

---

# Optimal Structural Linked List Solution

## Idea

By utilizing a dual-pointer tracking mechanism targeting both the front and rear nodes of a singly linked list, elements are managed dynamically. This avoids the size restrictions and index wrap-around management of primitive arrays entirely within a constant-time execution profile.

---

## Java Code (Dual-Pointer Queue)

```java
class MyQueue {
    // Internal node structure configuration
    private static class Node {
        int data;
        Node next;

        Node(int data) {
            this.data = data;
            this.next = null;
        }
    }

    private Node front; // Tracks the head element for departures
    private int size;   // Tracks the active count of items

    // Constructor to initialize an empty queue
    public MyQueue() {
        this.front = null;
        this.rear = null;
        this.size = 0;
    }

    // Adds an integer x to the back of the queue
    public void enqueue(int x) {
        Node newNode = new Node(x);

        // Case A: If the queue is completely empty, the new node acts as both front and rear
        if (this.rear == null) {
            this.front = newNode;
            this.rear = newNode;
            size++;
            return;
        }

        // Case B: Link the new node to the tail of the list and shift the rear pointer forward
        this.rear.next = newNode;
        this.rear = newNode;
        size++;
    }

    // Removes and returns the front element from the queue
    public int dequeue() {
        // Guard Condition: Check for Queue Underflow
        if (isEmpty()) {
            System.out.println("Queue Underflow Error: Cannot dequeue from an empty queue.");
            return -1; // Return sentinel value indicating transaction failure
        }

        // Extract data from the active front pointer position
        int dequeuedValue = this.front.data;

        // Shift front forward to isolate and drop the old head node
        this.front = this.front.next;

        // If front shifts to null, the queue has run dry; clear rear reference to match
        if (this.front == null) {
            this.rear = null;
        }

        size--;
        return dequeuedValue;
    }

    // Returns the front element without removing it
    public int peek() {
        // Guard Condition: Ensure elements exist to read
        if (isEmpty()) {
            System.out.println("Queue Read Error: Cannot peek inside an empty queue.");
            return -1;
        }
        return this.front.data;
    }

    // Constant-time check to verify if the queue is empty
    public boolean isEmpty() {
        return this.front == null; // Alternatively: return size == 0;
    }

    // Helper utility method to get the current count of elements stored
    public int size() {
        return this.size;
    }
}

// Client validation application driver
public class Main {
    public static void main(String[] args) {
        MyQueue queue = new MyQueue();

        queue.enqueue(10);
        queue.enqueue(20);
        queue.enqueue(30);

        System.out.println("Front element is: " + queue.peek());      // Expected: 10
        System.out.println("Dequeued element: " + queue.dequeue());   // Expected: 10
        System.out.println("New front element is: " + queue.peek());  // Expected: 20
        System.out.println("Current queue size: " + queue.size());    // Expected: 2
    }
}
```

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Explanation |
| :--- | :--- | :--- |
| **`enqueue(x)`** | O(1) | Element is appended directly via the cached `rear` pointer slot. |
| **`dequeue()`** | O(1) | The `front` pointer steps forward exactly once to disconnect the node instantly. |
| **`peek()`** | O(1) | Direct data reference lookup via `front.data`. |
| **`isEmpty()`**| O(1) | Simple null reference check evaluation (`front == null`). |

---

### Space Complexity

```text
Space Complexity: O(N)
```
*(where N is the current total number of elements actively enqueued. Memory footprint tracks utilization dynamically on the heap without any block capacity limitations).*

---

# Key Takeaways

✅ Baseline dynamic data structure template designed for flexible resource scheduling.

✅ **Array vs. Linked List Queue Trade-Offs:** An array-based queue requires complex index wrapping calculations using modulo arithmetic (`%`) to recycle abandoned indices and features a rigid maximum size limit. A linked-list queue entirely bypasses wrap-around management and size ceilings, but incurs a minor memory tax for storing individual link pointers (`next`) on the heap.

✅ Critical foundational component matching **Task Processing Spools**, messaging queues inside event-driven microservices architectures, and driving sequential level-order traversals (e.g., Breadth-First Search).

✅ Common Architectural Paradigm:

```text
Dual-Anchored Dynamic Node Chaining for End-to-End Processing Streams
```

### Similar Problems

- Implement Stack Using Linked List
- Implement Queue Using Arrays
- Design Circular Queue (LeetCode 622)
- Implement Queue Using Stacks (LeetCode 232)
