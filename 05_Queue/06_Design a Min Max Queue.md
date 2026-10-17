# Design a Min Max Queue

**Problem Statement**

Design a Queue-like data structure that supports standard FIFO operations alongside retrieving the minimum and maximum elements efficiently. 

The structure must support:

```java
MinMaxQueue()
```

Initialize the data structure.

```java
void enqueue(int val)
```

Pushes an element `val` to the back of the queue.

```java
int dequeue()
```

Removes and returns the element at the front of the queue.

```java
int getMin()
```

Retrieves the minimum element currently in the queue.

```java
int getMax()
```

Retrieves the maximum element currently in the queue.

---

## Example

```java
MinMaxQueue mmQueue = new MinMaxQueue();

mmQueue.enqueue(10);
mmQueue.enqueue(5);
mmQueue.enqueue(20);

mmQueue.getMin(); // 5
mmQueue.getMax(); // 20

mmQueue.dequeue(); // 10

mmQueue.getMin(); // 5
mmQueue.dequeue(); // 5

mmQueue.getMin(); // 20
```

---

# Intuition

A regular Queue tracks insertion order, but finding the minimum or maximum element would take `O(N)` linear time if we searched through all elements. 

To achieve constant time `O(1)` lookups for bounds, we can introduce two auxiliary Double-Ended Queues (**Deques**):
- A **Max-Deque** that maintains elements in a strict monotonically decreasing order.
- A **Min-Deque** that maintains elements in a strict monotonically increasing order.

By checking the front of these deques, we instantly know the current limits of the main queue. When elements are deleted from the main queue, we check if they also need to be removed from the front of our tracking deques.

---

# Approach: Monotonic Deques

## Data Structures

```java
Queue<Integer> mainQueue
Deque<Integer> maxDeque
Deque<Integer> minDeque
```

---

## enqueue(val)

1. Append `val` to the `mainQueue`.
2. **For maxDeque**: Remove elements from the back that are smaller than `val` to maintain decreasing order. Then push `val` to the back.
3. **For minDeque**: Remove elements from the back that are larger than `val` to maintain increasing order. Then push `val` to the back.

---

## dequeue()

1. Poll the front element from `mainQueue`.
2. If this element matches the front of `maxDeque`, poll it from `maxDeque` as well.
3. If this element matches the front of `minDeque`, poll it from `minDeque` as well.
4. Return the element.

---

# Dry Run

### Operations
```java
enqueue(10)
enqueue(5)
enqueue(20)
```

### State after insertions

```java
mainQueue: [10, 5, 20]
maxDeque:  [20]       // 10 and 5 were removed from the back because 20 is larger
minDeque:  [10, 5]    // 20 is larger than 5, so it appends nicely at the back
```

- `getMin()` peeks front of `minDeque` -> `10`
- `getMax()` peeks front of `maxDeque` -> `20`

---

# Java Solution

```java
import java.util.*;

class MinMaxQueue {

    private Queue<Integer> mainQueue;
    private Deque<Integer> maxDeque;
    private Deque<Integer> minDeque;

    public MinMaxQueue() {
        mainQueue = new LinkedList<>();
        maxDeque = new LinkedList<>();
        minDeque = new LinkedList<>();
    }

    public void enqueue(int val) {
        mainQueue.add(val);

        // Maintain monotonic decreasing order for max tracking
        while (!maxDeque.isEmpty() && maxDeque.peekLast() < val) {
            maxDeque.pollLast();
        }
        maxDeque.addLast(val);

        // Maintain monotonic increasing order for min tracking
        while (!minDeque.isEmpty() && minDeque.peekLast() > val) {
            minDeque.pollLast();
        }
        minDeque.addLast(val);
    }

    public int dequeue() {
        if (mainQueue.isEmpty()) {
            throw new NoSuchElementException("Queue is empty");
        }

        int removedVal = mainQueue.poll();

        // Remove from tracking structures if matching the boundary
        if (maxDeque.peekFirst() == removedVal) {
            maxDeque.pollFirst();
        }
        if (minDeque.peekFirst() == removedVal) {
            minDeque.pollFirst();
        }

        return removedVal;
    }

    public int getMin() {
        if (minDeque.isEmpty()) {
            throw new NoSuchElementException("Queue is empty");
        }
        return minDeque.peekFirst();
    }

    public int getMax() {
        if (maxDeque.isEmpty()) {
            throw new NoSuchElementException("Queue is empty");
        }
        return maxDeque.peekFirst();
    }
}
```

---

# Complexity Analysis

### enqueue()

```java
O(1) Amortized
```
Although there is a while loop, each element is pushed and popped from the deques at most once.

### dequeue()

```java
O(1)
```

### getMin() / getMax()

```java
O(1)
```

---

# Alternative Approach: Two Stacks (The Queue-Stack Transformation)

## Observation

We can implement a queue using two stacks (an In-Stack and an Out-Stack). Since a **Min Max Stack** can track min/max states in strict `O(1)` time by caching boundaries with every push, a queue built on top of those stacks inherits the same constraints naturally!

### Data Structures

```java
MinMaxStack inStack
MinMaxStack outStack
```

---

# Complexity Comparison

| Implementation Method | enqueue() | dequeue() | getMin() / getMax() | Space Complexity |
|---|---|---|---|---|
| **Monotonic Deques** | O(1) amortized | O(1) | O(1) | O(N) |
| **Two Stacks** | O(1) | O(1) amortized | O(1) | O(N) |

---

# Key Takeaways

- Use Monotonic Deques to capture the continuous shifting range inside window spaces.
- Stale boundaries drop off from the front while smaller/larger values are eliminated from the back to preserve sorting rules.
- Alternatively, you can compose this behavior safely by backing it with **two isolated MinMax Stacks**.
- Core concepts:
  - Monotonic Deque
  - Double-Ended Queue (Deque)
  - Amortized O(1) Complexity
