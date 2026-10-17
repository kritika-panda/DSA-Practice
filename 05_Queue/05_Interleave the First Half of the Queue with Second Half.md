# Interleave the First Half of the Queue with Second Half

**Problem Statement**

Given a queue of integers of **even size**, rearrange the elements by interleaving the first half of the queue with the second half.

You are allowed to use a **stack** as auxiliary space to solve the problem.

---

## Example

```java
// Input Queue
[1, 2, 3, 4, 5, 6, 7, 8]

// Output Queue
[1, 5, 2, 6, 3, 7, 4, 8]
```

---

# Intuition

A queue works on a **FIFO** (First-In-First-Out) basis. To interleave the elements, we need to access the front element of the first half and pair it with the front element of the second half. 

Since a stack works on a **LIFO** (Last-In-First-Out) basis, passing elements back and forth between a queue and a stack reverses their order. By carefully reversing the first half, pushing it back, and shifting elements around, we can realign the sequence so they interleave perfectly.

---

# Approach 1: Using an Auxiliary Stack

## Data Structure

```java
Stack<Integer> stack
```

Used to temporarily reverse the order of elements to position them correctly.

---

## Algorithm Steps

Given a queue of size `N`:

1. **Push the first half** of the queue into the stack.
2. **Enqueue the stack elements back** to the queue (this reverses the first half at the back of the queue).
3. **Dequeue the remaining second half** from the front and enqueue them to the back.
4. **Push the first half** (which is now reversed at the front) back into the stack.
5. **Interleave** by popping from the stack and dequeuing from the queue one by one.

---

# Dry Run

### Input Queue
```java
[1, 2, 3, 4, 5, 6, 7, 8]  (N = 8, N/2 = 4)
```

### Step 1: Push first half to Stack
```java
Queue: [5, 6, 7, 8]
Stack: [1, 2, 3, 4] <- Top
```

### Step 2: Enqueue Stack back to Queue
```java
Queue: [5, 6, 7, 8, 4, 3, 2, 1]
Stack: []
```

### Step 3: Dequeue first N/2 elements and Enqueue to back
```java
Queue: [4, 3, 2, 1, 5, 6, 7, 8]
Stack: []
```

### Step 4: Push first half to Stack again
```java
Queue: [5, 6, 7, 8]
Stack: [4, 3, 2, 1] <- Top
```

### Step 5: Interleave Stack and Queue
```java
Pop 1, Enqueue 1 -> Queue: [5, 6, 7, 8, 1]
Poll 5, Enqueue 5 -> Queue: [6, 7, 8, 1, 5]
...
Final Queue: [1, 5, 2, 6, 3, 7, 4, 8]
```

---

# Java Solution

```java
import java.util.*;

class Solution {

    public static void interLeaveQueue(Queue<Integer> q) {
        if (q.isEmpty() || q.size() % 2 != 0) {
            return;
        }

        Stack<Integer> stack = new Stack<>();
        int halfSize = q.size() / 2;

        // 1. Push first half to stack
        for (int i = 0; i < halfSize; i++) {
            stack.push(q.poll());
        }

        // 2. Enqueue stack elements back to queue
        while (!stack.isEmpty()) {
            q.add(stack.pop());
        }

        // 3. Move the remaining second half to the back
        for (int i = 0; i < halfSize; i++) {
            q.add(q.poll());
        }

        // 4. Push the first half to stack again
        for (int i = 0; i < halfSize; i++) {
            stack.push(q.poll());
        }

        // 5. Interleave the stack and queue elements
        while (!stack.isEmpty()) {
            q.add(stack.pop());
            q.add(q.poll());
        }
    }
}
```

---

# Complexity Analysis

### Time Complexity

```java
O(N)
```
Each element is processed a constant number of times across the stack shifts.

### Space Complexity

```java
O(N)
```
The stack stores exactly half of the total elements `N/2` at any given time.

---

# Approach 2: Using an Auxiliary Queue (Cleaner)

## Observation

If we are allowed to use another queue instead of a stack, the process becomes significantly cleaner because we do not have to fight the reversing property of a LIFO stack.

### Data Structures

```java
Queue<Integer> firstHalf
```

---

## Java Solution (Alternative)

```java
import java.util.*;

class Solution {

    public static void interLeaveQueueWithQueue(Queue<Integer> q) {
        Queue<Integer> firstHalf = new LinkedList<>();
        int halfSize = q.size() / 2;

        // Extract first half
        for (int i = 0; i < halfSize; i++) {
            firstHalf.add(q.poll());
        }

        // Interleave directly
        while (!firstHalf.isEmpty()) {
            q.add(firstHalf.poll());
            q.add(q.poll());
        }
    }
}
```

---

# Complexity Analysis (Queue Approach)

### Time Complexity

```java
O(N)
```

### Space Complexity

```java
O(N)
```

---

# Comparison

| Approach | Extra Data Structure | Logic Complexity | Operations Count |
|---|---|---|---|
| **Stack Approach** | `Stack` | Moderate (Requires Reversals) | 5 Loops |
| **Queue Approach** | `Queue` | Direct & Simple | 2 Loops |

---

# Key Takeaways

- Queues enforce **FIFO** while Stacks enforce **LIFO**. 
- Using a stack requires moving elements multiple times to cancel out the reversing nature of the stack.
- Using a secondary queue solves the problem linearly in just **two simple phases**.
- Core concepts:
  - Queue Operations (`poll`, `add`)
  - Stack Operations (`push`, `pop`)
  - Two-Pointer Interleaving Logic
