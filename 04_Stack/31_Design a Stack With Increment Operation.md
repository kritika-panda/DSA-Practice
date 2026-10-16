# Design a Stack With Increment Operation

## Problem Statement

Design a custom stack that supports the following operations:

```java
CustomStack(int maxSize)
```

Creates a stack with maximum size `maxSize`.

```java
void push(int x)
```

Adds element `x` to the stack if it has not reached the maximum size.

```java
int pop()
```

Removes and returns the top element of the stack.

Return:

```text
-1
```

if the stack is empty.

```java
void increment(int k, int val)
```

Increment the bottom `k` elements of the stack by `val`.

If there are fewer than `k` elements, increment all elements.

---

## Example

```java
CustomStack customStack =
new CustomStack(3);

customStack.push(1);
customStack.push(2);

customStack.pop();          // 2

customStack.push(2);
customStack.push(3);
customStack.push(4);

customStack.increment(5,100);

customStack.increment(2,100);

customStack.pop();          // 103

customStack.pop();          // 202

customStack.pop();          // 201

customStack.pop();          // -1
```

---

# Brute Force Approach

For increment:

```text
Increment bottom k elements
one by one
```

Example:

```text
Stack:
[1,2,3]
```

Increment:

```java
increment(2,100)
```

Result:

```text
[101,102,3]
```

---

## Problem

Increment operation takes:

```text
O(k)
```

Worst case:

```text
O(N)
```

per operation.

---

# Intuition for Optimal Solution

Instead of updating all bottom elements immediately:

```text
Store increment lazily
```

Apply it only when an element is popped.

---

# Key Observation

Suppose:

```text
Stack:

1
2
3
```

Operation:

```java
increment(2,100)
```

Instead of updating:

```text
101
102
3
```

Store:

```text
inc[1] += 100
```

meaning:

```text
When index 1 is removed,
pass increment downward.
```

---

# Data Structures

Use:

```java
stack[]
```

Stores actual values.

---

```java
inc[]
```

Stores pending increments.

---

# Visualization

Initial Stack:

```text
Index: 0 1 2

Value:
1 2 3

inc:
0 0 0
```

---

Operation:

```java
increment(2,100)
```

Store:

```text
inc[1] = 100
```

Now:

```text
stack:
1 2 3

inc:
0 100 0
```

---

Nothing is updated immediately.

---

# Pop Operation Logic

Suppose:

```text
stack:
1 2 3

inc:
0 100 0
```

Pop:

```text
3
```

No increment present.

---

Now:

```text
stack:
1 2

inc:
0 100
```

Pop again:

```text
2 + 100
=
102
```

Before removing:

```java
inc[0] += inc[1];
```

So:

```text
inc:
100
```

Now remaining element gets the pending increment.

---

# Algorithm

## Push

If stack has space:

```java
stack[top++] = x;
```

---

## Increment

Bottom:

```java
k elements
```

Store increment at:

```java
min(k, size) - 1
```

```java
inc[index] += val;
```

---

## Pop

Before removing:

```java
inc[top - 1]
```

must be passed down.

```java
inc[top - 2]
+=
inc[top - 1]
```

Then return:

```java
value + increment
```

---

# Java Code

```java
class CustomStack {

    private int maxSize;

    private int[] stack;
    private int[] inc;

    private int top;

    public CustomStack(int maxSize) {

        this.maxSize = maxSize;

        stack = new int[maxSize];
        inc = new int[maxSize];

        top = 0;
    }

    public void push(int x) {

        if (top == maxSize) {
            return;
        }

        stack[top++] = x;
    }

    public int pop() {

        if (top == 0) {
            return -1;
        }

        int index = top - 1;

        int result =
                stack[index] + inc[index];

        if (index > 0) {

            inc[index - 1]
                    += inc[index];
        }

        inc[index] = 0;

        top--;

        return result;
    }

    public void increment(
            int k,
            int val) {

        int index =
                Math.min(k, top) - 1;

        if (index >= 0) {

            inc[index] += val;
        }
    }
}
```

---

# Dry Run

## Input

```java
push(1)
push(2)
push(3)

increment(2,100)
```

---

### State

```text
Stack:
[1,2,3]

inc:
[0,100,0]
```

---

### Pop

```text
3 + 0
=
3
```

Return:

```text
3
```

State:

```text
Stack:
[1,2]

inc:
[0,100]
```

---

### Pop

```text
2 + 100
=
102
```

Move increment down:

```text
inc[0]
+=
100
```

State:

```text
Stack:
[1]

inc:
[100]
```

Return:

```text
102
```

---

### Pop

```text
1 + 100
=
101
```

Return:

```text
101
```

---

# Visualization

```text
Initial

Top
 ↓
3
2
1

increment(2,100)

Top
 ↓
3
2 (+100)
1 (+100)
```

Stored lazily as:

```text
inc[1] = 100
```

No actual updates performed.

---

# Why Lazy Increment Works?

Instead of:

```text
Updating k elements
```

immediately,

we store:

```text
Future increment information
```

and apply it during:

```text
pop()
```

This avoids repeatedly updating stack elements.

---

# Complexity Analysis

## push()

```text
O(1)
```

---

## pop()

```text
O(1)
```

---

## increment()

```text
O(1)
```

---

# Space Complexity

Two arrays:

```text
stack[]
inc[]
```

Size:

```text
O(maxSize)
```

---

# Comparison

| Operation | Brute Force | Optimal |
|------------|------------|----------|
| Push | O(1) | O(1) |
| Pop | O(1) | O(1) |
| Increment | O(k) | O(1) |

---

# Pattern Recognition

```text
Design Pattern
│
├── Design a Stack With Increment Operation ✅
├── Min Stack
├── Max Stack
├── Design Browser History
├── Design Circular Queue
├── Design Authentication Manager
└── Design Hit Counter
```

### Interview Trick

Whenever a problem asks:

```text
Range Update
Frequent Increment
Repeated Modification
```

Think:

```text
Lazy Propagation
Deferred Updates
```

The key insight here is:

```text
Store increments separately
and apply them only when needed.
```

This converts:

```text
increment()
```

from:

```text
O(N)
```

to:

```text
O(1)
```
