# Design Front Middle Back Queue

## Problem Statement

Design a queue that supports operations in the front, middle, and back.

Implement the `FrontMiddleBackQueue` class:

```java
FrontMiddleBackQueue()
```

Initializes the queue.

```java
void pushFront(int val)
```

Adds `val` to the front of the queue.

```java
void pushMiddle(int val)
```

Adds `val` to the middle of the queue.

```java
void pushBack(int val)
```

Adds `val` to the back of the queue.

```java
int popFront()
```

Removes and returns the front element. Returns `-1` if the queue is empty.

```java
int popMiddle()
```

Removes and returns the middle element. Returns `-1` if the queue is empty.

If there are two middle positions, remove the front middle one.

```java
int popBack()
```

Removes and returns the back element. Returns `-1` if the queue is empty.

---

## Example

### Input

```java
["FrontMiddleBackQueue",
 "pushFront",
 "pushBack",
 "pushMiddle",
 "pushMiddle",
 "popFront",
 "popMiddle",
 "popMiddle",
 "popBack",
 "popFront"]

[[],[1],[2],[3],[4],[],[],[],[],[]]
```

### Output

```java
[null,null,null,null,null,1,3,4,2,-1]
```

---

# Intuition

A normal deque supports:

```java
Front Operations -> O(1)
Back Operations  -> O(1)
```

But accessing the middle requires:

```java
O(n)
```

To support middle operations efficiently, maintain **two deques**:

```java
left
right
```

such that:

```java
left.size() == right.size()
```

or

```java
left.size() == right.size() + 1
```

This ensures:

```java
Middle = last element of left
```

---

# Key Invariant

Always maintain:

```java
left.size() >= right.size()
```

and

```java
left.size() - right.size() <= 1
```

Example:

```java
Queue = [1,2,3,4,5]
```

Stored as:

```java
left  = [1,2,3]
right = [4,5]
```

Middle:

```java
3
```

---

# Rebalancing

After every operation:

### Case 1

```java
left.size() > right.size() + 1
```

Move:

```java
left.removeLast()
```

to

```java
right.addFirst()
```

---

### Case 2

```java
left.size() < right.size()
```

Move:

```java
right.removeFirst()
```

to

```java
left.addLast()
```

---

# Operations

## pushFront(val)

Insert into:

```java
left
```

Then rebalance.

---

## pushMiddle(val)

If:

```java
left.size() > right.size()
```

move one element from:

```java
left → right
```

Then insert at:

```java
left.addLast(val)
```

---

## pushBack(val)

Insert into:

```java
right
```

Then rebalance.

---

## popFront()

Remove:

```java
left.removeFirst()
```

If left is empty:

```java
right.removeFirst()
```

Then rebalance.

---

## popMiddle()

Middle always equals:

```java
left.removeLast()
```

Then rebalance.

---

## popBack()

If:

```java
right not empty
```

remove:

```java
right.removeLast()
```

Otherwise:

```java
left.removeLast()
```

Then rebalance.

---

# Dry Run

### Operations

```java
pushFront(1)
```

```java
left  = [1]
right = []
```

---

```java
pushBack(2)
```

```java
left  = [1]
right = [2]
```

---

```java
pushMiddle(3)
```

```java
left  = [1,3]
right = [2]
```

Queue:

```java
[1,3,2]
```

---

```java
pushMiddle(4)
```

```java
left  = [1,4]
right = [3,2]
```

Queue:

```java
[1,4,3,2]
```

---

```java
popMiddle()
```

Remove:

```java
4
```

Queue:

```java
[1,3,2]
```

---

# Java Solution (Optimal)

```java
import java.util.*;

class FrontMiddleBackQueue {

    private Deque<Integer> left;
    private Deque<Integer> right;

    public FrontMiddleBackQueue() {
        left = new ArrayDeque<>();
        right = new ArrayDeque<>();
    }

    private void balance() {

        if (left.size() > right.size() + 1) {
            right.offerFirst(left.pollLast());
        }

        if (left.size() < right.size()) {
            left.offerLast(right.pollFirst());
        }
    }

    public void pushFront(int val) {
        left.offerFirst(val);
        balance();
    }

    public void pushMiddle(int val) {

        if (left.size() > right.size()) {
            right.offerFirst(left.pollLast());
        }

        left.offerLast(val);
    }

    public void pushBack(int val) {
        right.offerLast(val);
        balance();
    }

    public int popFront() {

        if (isEmpty()) {
            return -1;
        }

        int result;

        if (!left.isEmpty()) {
            result = left.pollFirst();
        } else {
            result = right.pollFirst();
        }

        balance();

        return result;
    }

    public int popMiddle() {

        if (isEmpty()) {
            return -1;
        }

        int result = left.pollLast();

        balance();

        return result;
    }

    public int popBack() {

        if (isEmpty()) {
            return -1;
        }

        int result;

        if (!right.isEmpty()) {
            result = right.pollLast();
        } else {
            result = left.pollLast();
        }

        balance();

        return result;
    }

    private boolean isEmpty() {
        return left.isEmpty() && right.isEmpty();
    }
}
```

---

# Why is the Middle Always in `left`?

We maintain:

```java
left.size() == right.size()
```

or

```java
left.size() == right.size() + 1
```

Thus:

### Even Length

```java
[1,2] [3,4]
```

Middle choices:

```java
2 and 3
```

Problem requires removing:

```java
front middle
```

which is:

```java
2 = left.last
```

### Odd Length

```java
[1,2,3] [4,5]
```

Middle:

```java
3 = left.last
```

Hence:

```java
popMiddle() = left.pollLast()
```

always works.

---

# Complexity Analysis

| Operation | Time Complexity |
|------------|----------------|
| pushFront | O(1) |
| pushMiddle | O(1) |
| pushBack | O(1) |
| popFront | O(1) |
| popMiddle | O(1) |
| popBack | O(1) |

### Space Complexity

```java
O(n)
```

---

# Comparison with Other Approaches

### ArrayList

```java
pushMiddle  -> O(n)
popMiddle   -> O(n)
```

Not optimal.

### Doubly Linked List

Middle lookup becomes difficult without extra pointers.

### Two Deques

```java
All operations -> O(1)
```

Optimal solution expected in interviews.

---

# Key Takeaways

- Split the queue into two balanced deques.
- Maintain:
  ```java
  left.size() == right.size()
  ```
  or
  ```java
  left.size() == right.size() + 1
  ```
- The middle element is always the last element of `left`.
- Rebalance after every insertion and deletion.
- All operations run in **O(1)** time.
- Classic design problem combining **Deque**, **Balancing**, and **Queue Design** concepts.
