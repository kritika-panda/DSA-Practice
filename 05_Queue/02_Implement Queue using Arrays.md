# Implement Queue Using Arrays

## Problem Statement

Design a **First-In, First-Out (FIFO)** Queue data structure using a primitive array. The queue must support the following standard operations safely and efficiently:
1. `enqueue(x)` / `push(x)`: Adds an integer `x` to the back (rear) of the queue. If the queue is full, it should handle a structural overflow.
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
Initialize a Circular Queue of capacity 3: [ , , ] (front=0, rear=0, size=0)

1. enqueue(10) → rear=1, size=1. Queue: [10,   ,   ]
2. enqueue(20) → rear=2, size=2. Queue: [10, 20,   ]
3. peek()      → Returns 10.      Queue stays unchanged.
4. dequeue()   → Returns 10.      front=1, size=1. Queue: [  , 20,   ]
5. enqueue(30) → rear=0, size=2. Queue: [  , 20, 30] (Circular wrap-around!)
6. isEmpty()   → Returns false.
```

---

# Intuition

A queue inserts elements at one end (`rear`) and removes them from the opposite end (`front`). 

If we implement this using a linear, non-wrapping array approach, deleting an item from the front creates a dilemma: we either have to shift all remaining elements forward (costing an inefficient O(N) runtime) or leave the empty slots stranded, leading to wasted space at the front.

To achieve true constant-time **O(1) performance** for all operations without wasting memory slots, we use a **Circular Queue structure**:
- We wrap our array indices using a **Modulo Operator (`%`)** combined with the array's capacity.
- **Pointers Map:** We maintain a `front` pointer, a `rear` pointer, and an active `size` counter.
- **Enqueueing Data:** To add an item, we write it at the `rear` location and then advance the pointer circularly: `rear = (rear + 1) % capacity`.
- **Dequeueing Data:** To remove an item, we read the element at `front` and advance the pointer circularly: `front = (front + 1) % capacity`.

---

# State Definition

Let our internal class tracking data variables be:

```text
int[] arr;    // Contiguous primitive array container holding the queue elements
int front;    // Index pointer tracking the absolute head of the queue
int rear;     // Index pointer tracking the next open writing slot at the tail
int size;     // Active counter tracking the exact number of elements currently stored
int capacity; // Maximum capacity upper bound threshold of the queue
```

---

# Operational Guard Conditions

```text
Queue Overflows  : If (size == capacity) -> Queue Overflow! Cannot enqueue.
Queue Underflows : If (size == 0)        -> Queue Underflow! Cannot dequeue.
```

---

# Optimal Structural Object-Oriented Solution

## Idea

By wrapping the array array blocks inside a circular-modulo logic wrapper class, tail indices wrap around to reuse abandoned head slots seamlessly. This maintains maximum resource efficiency entirely inside an optimal **O(1)** execution profile.

---

## Java Code (Circular Array Wrapper)

```java
class MyQueue {
    private final int[] arr;
    private int front;
    private int rear;
    private int size;
    private final int capacity;

    // Constructor to initialize the queue with a custom capacity limit
    public MyQueue(int capacity) {
        this.capacity = capacity;
        this.arr = new int[capacity];
        this.front = 0;
        this.rear = 0;
        this.size = 0;
    }

    // Default constructor providing a standard fallback structural capacity
    public MyQueue() {
        this(1000); // Default threshold sizing to 1000 items
    }

    // Adds an integer x to the back of the queue
    public boolean enqueue(int x) {
        // Guard Condition: Verify if the queue is completely full
        if (size == capacity) {
            System.out.println("Queue Overflow Error: Cannot enqueue " + x + ", capacity reached.");
            return false;
        }

        // Insert item at the current rear position
        arr[rear] = x;
        
        // Advance rear pointer using circular modulo logic mapping
        rear = (rear + 1) % capacity;
        size++;
        return true;
    }

    // Removes and returns the front element from the queue
    public int dequeue() {
        // Guard Condition: Verify if the queue contains zero elements
        if (isEmpty()) {
            System.out.println("Queue Underflow Error: Cannot dequeue from an empty queue.");
            return -1; // Return sentinel value indicating transaction failure
        }

        // Extract the element from the front pointer position
        int dequeuedValue = arr[front];
        
        // Advance front pointer using circular modulo logic mapping
        front = (front + 1) % capacity;
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
        return arr[front];
    }

    // Constant-time scalar check to verify if the queue is empty
    public boolean isEmpty() {
        return size == 0;
    }

    // Helper utility method to get the current count of elements stored
    public int size() {
        return this.size;
    }
}

// Client validation application driver
public class Main {
    public static void main(String[] args) {
        MyQueue queue = new MyQueue(3);

        queue.enqueue(10);
        queue.enqueue(20);
        queue.enqueue(30);

        System.out.println("Front element is: " + queue.peek()); // Expected: 10
        System.out.println("Dequeued element: " + queue.dequeue()); // Expected: 10
        
        // Testing circular reuse of the abandoned first index memory slot
        queue.enqueue(40); 
        
        System.out.println("New front element is: " + queue.peek()); // Expected: 20
        System.out.println("Current queue size: " + queue.size());    // Expected: 3
    }
}
```

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Explanation |
| :--- | :--- | :--- |
| **`enqueue(x)`** | O(1) | Direct array assignment using index `rear`. Modulo calculations run in constant time. |
| **`dequeue()`** | O(1) | Direct lookup via index `front` accompanied by a simple pointer increment step. |
| **`peek()`** | O(1) | High-speed indexing straight to `arr[front]`. |
| **`isEmpty()`**| O(1) | Simple scalar check evaluating `size == 0`. |

---

### Space Complexity

```text
Space Complexity: O(N)
```
*(where N is the maximum allocated capacity size of the internal contiguous primitive array data storage container).*

---

# Key Takeaways

✅ Baseline fundamental linear data structure layout.

✅ **The Value of Modulo Arithmetic:** Utilizing circular wrap-around indexing via `(index + 1) % capacity` converts a fundamentally restrictive static array into an infinite recycling storage stream, avoiding the high cost of shifting elements.

✅ Universally deployed inside real-world systems like **Network Socket Buffers** (e.g., managing a sliding window stream or packet buffer lists), thread communication message channels, and printer spool print-job logs.

✅ Common Architectural Paradigm:

```text
Contiguous Modular Array Buffering via Circular Pointer Offsets
```

### Similar Problems

- Implement Stack Using Arrays
- Design Circular Queue (LeetCode 622)
- Design Circular Deque (LeetCode 641)
- Implement Queue Using Stacks (LeetCode 232)
