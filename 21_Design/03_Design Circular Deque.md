# Design Circular Deque

## Problem Statement

Design a circular double-ended queue (deque).

Implement the `MyCircularDeque` class:

```java
MyCircularDeque(int k)
```

Initializes the deque with a maximum size of `k`.

```java
boolean insertFront(int value)
```

Adds an item at the front. Returns `true` if successful.

```java
boolean insertLast(int value)
```

Adds an item at the rear. Returns `true` if successful.

```java
boolean deleteFront()
```

Deletes an item from the front. Returns `true` if successful.

```java
boolean deleteLast()
```

Deletes an item from the rear. Returns `true` if successful.

```java
int getFront()
```

Gets the front item. Returns `-1` if the deque is empty.

```java
int getRear()
```

Gets the last item. Returns `-1` if the deque is empty.

```java
boolean isEmpty()
```

Returns whether the deque is empty.

```java
boolean isFull()
```

Returns whether the deque is full.

---

## Example

### Input

```java
["MyCircularDeque",
 "insertLast",
 "insertLast",
 "insertFront",
 "insertFront",
 "getRear",
 "isFull",
 "deleteLast",
 "insertFront",
 "getFront"]

[[3],[1],[2],[3],[4],[],[],[],[4],[]]
```

### Output

```java
[null,true,true,true,false,2,true,true,true,4]
```

---

# Intuition

A deque allows insertion and deletion from both ends.

Since the deque is circular:

- After the last index, we wrap around to index `0`.
- Before index `0`, we wrap around to the last index.

We'll use:

```java
front
rear
size
capacity
```

and an array to simulate the circular structure.

---

# Approach: Circular Array

## Data Members

```java
int[] deque;
int front;
int rear;
int size;
int capacity;
```

### Initial State

```java
front = 0
rear = 0
size = 0
```

---

## Circular Movement

### Move Forward

```java
(index + 1) % capacity
```

### Move Backward

```java
(index - 1 + capacity) % capacity
```

This handles wrap-around automatically.

---

# Operations

## insertFront(value)

Move front backward:

```java
front = (front - 1 + capacity) % capacity
```

Store value:

```java
deque[front] = value
```

Increase size.

---

## insertLast(value)

Store value at rear:

```java
deque[rear] = value
```

Move rear forward:

```java
rear = (rear + 1) % capacity
```

Increase size.

---

## deleteFront()

Move front forward:

```java
front = (front + 1) % capacity
```

Decrease size.

---

## deleteLast()

Move rear backward:

```java
rear = (rear - 1 + capacity) % capacity
```

Decrease size.

---

## getFront()

Front element exists at:

```java
deque[front]
```

---

## getRear()

Rear points to next insertion position.

Actual rear element:

```java
deque[(rear - 1 + capacity) % capacity]
```

---

# Dry Run

### Capacity = 3

```java
insertLast(1)
```

```java
[1,_,_]
front = 0
rear = 1
size = 1
```

---

```java
insertLast(2)
```

```java
[1,2,_]
front = 0
rear = 2
size = 2
```

---

```java
insertFront(3)
```

Move front backward:

```java
front = 2
```

```java
[1,2,3]
```

size = 3

Deque is full.

---

# Java Solution (Optimal)

```java
class MyCircularDeque {

    private int[] deque;
    private int front;
    private int rear;
    private int size;
    private int capacity;

    public MyCircularDeque(int k) {
        deque = new int[k];
        capacity = k;
        front = 0;
        rear = 0;
        size = 0;
    }

    public boolean insertFront(int value) {

        if (isFull()) {
            return false;
        }

        front = (front - 1 + capacity) % capacity;
        deque[front] = value;
        size++;

        return true;
    }

    public boolean insertLast(int value) {

        if (isFull()) {
            return false;
        }

        deque[rear] = value;
        rear = (rear + 1) % capacity;
        size++;

        return true;
    }

    public boolean deleteFront() {

        if (isEmpty()) {
            return false;
        }

        front = (front + 1) % capacity;
        size--;

        return true;
    }

    public boolean deleteLast() {

        if (isEmpty()) {
            return false;
        }

        rear = (rear - 1 + capacity) % capacity;
        size--;

        return true;
    }

    public int getFront() {

        if (isEmpty()) {
            return -1;
        }

        return deque[front];
    }

    public int getRear() {

        if (isEmpty()) {
            return -1;
        }

        return deque[(rear - 1 + capacity) % capacity];
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

# Complexity Analysis

| Operation | Time Complexity |
|------------|----------------|
| insertFront | O(1) |
| insertLast | O(1) |
| deleteFront | O(1) |
| deleteLast | O(1) |
| getFront | O(1) |
| getRear | O(1) |
| isEmpty | O(1) |
| isFull | O(1) |

### Space Complexity

```java
O(k)
```

where `k` is the capacity of the deque.

---

# Alternative Approach: Doubly Linked List

Use:

- Dummy head
- Dummy tail
- Size counter

Operations at both ends become:

```java
O(1)
```

However, it requires extra node objects and pointers.

---

# Comparison

| Approach | Insert/Delete | Space |
|-----------|--------------|--------|
| Circular Array | O(1) | O(k) |
| Doubly Linked List | O(1) | O(k) + pointers |

---

# Key Takeaways

- Circular Deque supports insertion and deletion from both ends.
- Use modulo arithmetic for circular movement.
- `front` points to the first element.
- `rear` points to the next insertion position.
- Track `size` to distinguish full and empty states.
- All operations run in **O(1)** time.
- Classic design question frequently asked in system design and data structure interviews.
