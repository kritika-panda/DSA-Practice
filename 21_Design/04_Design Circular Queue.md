# Design Circular Queue

## Problem Statement

Design your implementation of the circular queue.

The circular queue is a linear data structure in which operations are performed based on the FIFO (First In First Out) principle and the last position is connected back to the first position to make a circle.

Implement the `MyCircularQueue` class:

```java
MyCircularQueue(int k)
```

Initializes the object with the size of the queue to be `k`.

```java
boolean enQueue(int value)
```

Inserts an element into the circular queue. Returns `true` if successful.

```java
boolean deQueue()
```

Deletes an element from the circular queue. Returns `true` if successful.

```java
int Front()
```

Gets the front item from the queue. Returns `-1` if the queue is empty.

```java
int Rear()
```

Gets the last item from the queue. Returns `-1` if the queue is empty.

```java
boolean isEmpty()
```

Checks whether the circular queue is empty.

```java
boolean isFull()
```

Checks whether the circular queue is full.

---

## Example

### Input

```java
["MyCircularQueue",
 "enQueue",
 "enQueue",
 "enQueue",
 "enQueue",
 "Rear",
 "isFull",
 "deQueue",
 "enQueue",
 "Rear"]

[[3],[1],[2],[3],[4],[],[],[],[4],[]]
```

### Output

```java
[null,true,true,true,false,3,true,true,true,4]
```

### Explanation

```java
MyCircularQueue circularQueue = new MyCircularQueue(3);

circularQueue.enQueue(1); // true
circularQueue.enQueue(2); // true
circularQueue.enQueue(3); // true
circularQueue.enQueue(4); // false

circularQueue.Rear();     // 3
circularQueue.isFull();   // true

circularQueue.deQueue();  // true

circularQueue.enQueue(4); // true

circularQueue.Rear();     // 4
```

---

# Intuition

A normal queue wastes space after repeated deletions:

```java
[1, 2, 3]
 ^
remove 1
```

The freed space at the beginning cannot be reused easily.

A Circular Queue solves this by wrapping around:

```java
(index + 1) % capacity
```

allowing us to reuse previously freed positions.

---

# Approach: Circular Array

Maintain:

```java
front
rear
size
capacity
```

### Initial State

```java
front = 0
rear = 0
size = 0
```

### Circular Movement

Move forward:

```java
(index + 1) % capacity
```

---

# Operations

## enQueue(value)

Check if queue is full.

Store value at:

```java
queue[rear]
```

Move rear forward:

```java
rear = (rear + 1) % capacity
```

Increase size.

---

## deQueue()

Check if queue is empty.

Move front forward:

```java
front = (front + 1) % capacity
```

Decrease size.

---

## Front()

Return:

```java
queue[front]
```

---

## Rear()

Since rear points to the next insertion position:

```java
queue[(rear - 1 + capacity) % capacity]
```

gives the actual last element.

---

# Dry Run

### Capacity = 3

Initial:

```java
queue = [_, _, _]

front = 0
rear = 0
size = 0
```

---

### enQueue(1)

```java
queue = [1, _, _]

front = 0
rear = 1
size = 1
```

---

### enQueue(2)

```java
queue = [1, 2, _]

front = 0
rear = 2
size = 2
```

---

### enQueue(3)

```java
queue = [1, 2, 3]

front = 0
rear = 0
size = 3
```

Queue becomes full.

---

### deQueue()

```java
front = 1
size = 2
```

Removed:

```java
1
```

---

### enQueue(4)

Insert at index:

```java
rear = 0
```

```java
queue = [4, 2, 3]

front = 1
rear = 1
size = 3
```

Circular reuse achieved.

---

# Java Solution (Optimal)

```java
class MyCircularQueue {

    private int[] queue;
    private int front;
    private int rear;
    private int size;
    private int capacity;

    public MyCircularQueue(int k) {
        queue = new int[k];
        capacity = k;
        front = 0;
        rear = 0;
        size = 0;
    }

    public boolean enQueue(int value) {

        if (isFull()) {
            return false;
        }

        queue[rear] = value;
        rear = (rear + 1) % capacity;
        size++;

        return true;
    }

    public boolean deQueue() {

        if (isEmpty()) {
            return false;
        }

        front = (front + 1) % capacity;
        size--;

        return true;
    }

    public int Front() {

        if (isEmpty()) {
            return -1;
        }

        return queue[front];
    }

    public int Rear() {

        if (isEmpty()) {
            return -1;
        }

        return queue[(rear - 1 + capacity) % capacity];
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public boolean isFull() {
        return size == capacity;
    }
}
```

---

# Why Use `size`?

Without `size`, when:

```java
front == rear
```

we cannot determine whether the queue is:

```java
Empty
```

or

```java
Full
```

Using:

```java
size
```

eliminates this ambiguity.

---

# Complexity Analysis

| Operation | Time Complexity |
|------------|----------------|
| enQueue | O(1) |
| deQueue | O(1) |
| Front | O(1) |
| Rear | O(1) |
| isEmpty | O(1) |
| isFull | O(1) |

### Space Complexity

```java
O(k)
```

where `k` is the queue capacity.

---

# Alternative Approach: Linked List

Use:

- Front pointer
- Rear pointer
- Current size

All operations remain:

```java
O(1)
```

but require extra node objects and pointers.

---

# Comparison

| Approach | Time | Space |
|-----------|-------|--------|
| Circular Array | O(1) per operation | O(k) |
| Linked List | O(1) per operation | O(k) + pointer overhead |

---

# Key Takeaways

- Circular Queue reuses freed positions using wrap-around.
- `rear` always points to the next insertion position.
- `front` points to the current front element.
- Maintain `size` to distinguish full and empty states.
- Use modulo arithmetic for circular traversal.
- All operations run in **O(1)** time.
- Very common design question asked in Amazon, Google, Microsoft, Adobe, and Uber interviews.
