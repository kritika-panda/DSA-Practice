# Implement Stack Using Arrays

## Problem Statement

Design a **Last-In, First-Out (LIFO)** Stack data structure using a fixed-size or dynamic primitive array. The stack must support the following standard operations safely and efficiently:
1. `push(x)`: Adds an integer `x` to the top of the stack. If the stack is full, it should handle it (e.g., print an overflow error or resize).
2. `pop()`: Removes and returns the element from the top of the stack. If the stack is empty, it should handle it (e.g., return a sentinel value like `-1` or throw an exception).
3. `top()` / `peek()`: Returns the element at the top of the stack without removing it.
4. `isEmpty()`: Returns a boolean indicating whether the stack contains zero elements.

The runtime complexity of each core operation (`push`, `pop`, `peek`, `isEmpty`) must be strictly bounded by:

```text
O(1)
```

---

## Example Execution Trace

```text
Initialize a Stack of capacity 3: [ , , ]

1. push(10) → top index becomes 0. Stack: [10,  ,  ]
2. push(20) → top index becomes 1. Stack: [10, 20,  ]
3. peek()   → Returns 20.          Stack stays unchanged.
4. pop()    → Returns 20.          top index becomes 0. Stack: [10,  ,  ]
5. isEmpty()→ Returns false.
6. pop()    → Returns 10.          top index becomes -1. Stack: [ ,  ,  ]
7. isEmpty()→ Returns true.
```

---

# Intuition

A stack is a linear data structure that elements are inserted and removed from the exact same end (the "top"). 

When building this behavior on top of a primitive array:
- We can track the current top of the stack using a simple integer index variable, commonly named `top`.
- **The Empty Marker:** Initially, when the stack contains zero elements, we set `top = -1`.
- **Pushing Elements:** To push a new value, we first increment the `top` index pointer (`top++`) and then write the value into the array at that new index position (`arr[top] = x`).
- **Popping Elements:** To pop a value, we read the item at the current `top` position (`val = arr[top]`) and then decrement the pointer index (`top--`).

By leveraging an integer pointer to point straight to the active index memory slot, we can completely bypass any need to shift elements around during insertions or deletions, guaranteeing high-performance O(1) operations.

---

# State Definition

Let our internal data structures be:

```text
int[] arr;   // Primitive array container holding the stack elements
int top;     // Integer pointer index tracking the top element slot (-1 when empty)
int capacity; // Maximum element threshold limit allowed inside the array
```

---

# Operational Guard Conditions

```text
Push Overflows  : If (top == capacity - 1) -> Stack Overflow! Cannot push.
Pop Underflows   : If (top == -1)           -> Stack Underflow! Cannot pop.
```

---

# Optimal Structural Object-Oriented Solution

## Idea

By encapsulating the primitive array block inside a well-structured class object and enforcing strict operational boundary guard checks, the stack properties remain completely isolated. All mutations execute natively in O(1) constant time.

---

## Java Code

```java
class MyStack {
    private final int[] arr;
    private int top;
    private final int capacity;

    // Constructor to initialize the stack with a specified capacity limit
    public MyStack(int capacity) {
        this.capacity = capacity;
        this.arr = new int[capacity];
        this.top = -1; // -1 represents a completely empty stack baseline state
    }

    // Default constructor providing a standard fixed threshold capacity
    public MyStack() {
        this(1000); // Default to a capacity of 1000 items
    }

    // Pushes an integer x onto the stack safely
    public boolean push(int x) {
        // Guard Condition: Check for Stack Overflow
        if (top >= capacity - 1) {
            System.out.println("Stack Overflow Error: Cannot push " + x + ", capacity reached.");
            return false;
        }
        
        // Advance pointer and write data into the newly allocated index slot
        top++;
        arr[top] = x;
        return true;
    }

    // Removes and returns the top element from the stack
    public int pop() {
        // Guard Condition: Check for Stack Underflow
        if (isEmpty()) {
            System.out.println("Stack Underflow Error: Cannot pop from an empty stack.");
            return -1; // Return sentinel value indicating operation failed
        }
        
        // Extract data and decrement pointer to clear out the active index frame
        int poppedValue = arr[top];
        top--;
        return poppedValue;
    }

    // Returns the top element without removing it
    public int peek() {
        // Guard Condition: Ensure stack has elements to read
        if (isEmpty()) {
            System.out.println("Stack Read Error: Cannot peek inside an empty stack.");
            return -1;
        }
        
        return arr[top];
    }

    // Constant-time check to verify if the stack is completely empty
    public boolean isEmpty() {
        return top == -1;
    }

    // Helper utility method to get the current count of elements inside the stack
    public int size() {
        return top + 1;
    }
}

// Client application verification gateway
public class Main {
    public static void main(String[] args) {
        MyStack stack = new MyStack(5);

        stack.push(10);
        stack.push(20);
        stack.push(30);

        System.out.println("Top element is: " + stack.peek()); // Expected: 30
        System.out.println("Popped element: " + stack.pop());  // Expected: 30
        System.out.println("Current top is: " + stack.peek()); // Expected: 20
        System.out.println("Current stack size: " + stack.size()); // Expected: 2
    }
}
```

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Explanation |
| :--- | :--- | :--- |
| **`push(x)`** | O(1) | Direct array write using the `top` index pointer. No element shifting occurs. |
| **`pop()`** | O(1) | Direct pointer decrement operation. |
| **`peek()`** | O(1) | Direct read access via indexing (`arr[top]`). |
| **`isEmpty()`**| O(1) | Simple scalar integer equality check (`top == -1`). |

---

### Space Complexity

```text
Space Complexity: O(N)
```
*(where N is the maximum allocated capacity threshold size of the internal primitive array data storage container).*

---

# Key Takeaways

✅ Core fundamental linear data structure configuration.

✅ **Array vs. Linked List Implementations:** Implementing a stack using an array delivers maximum speed and data cache locality benefits because the variables are packed next to each other inside memory. However, it suffers from a rigid, un-resizable maximum element threshold. A Linked List structure provides infinite elastic growth capability at the expense of creating pointer object memory overhead blocks.

✅ Direct foundational framework used inside compiler **Call Stack runtime engines**, tracking **undo/redo state logs** inside text processors, and driving nested parsing workflows (e.g., validating bracket sequences).

✅ Common Architectural Paradigm:

```text
Pointer-Driven Contiguous Block Memory State Management
```

### Similar Problems

- Implement Queue Using Arrays
- Implement Stack Using Queues (LeetCode 225)
- Implement Queue Using Stacks (LeetCode 232)
- Min Stack (LeetCode 155)
