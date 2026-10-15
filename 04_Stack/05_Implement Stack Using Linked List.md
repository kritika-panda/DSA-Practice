# Implement Stack Using Linked List

## Problem Statement

Design a **Last-In, First-Out (LIFO)** Stack data structure using a singly linked list. The stack must support all standard stack operations safely and efficiently without any fixed size restrictions:
1. `push(x)`: Adds an integer `x` to the top of the stack.
2. `pop()`: Removes and returns the element from the top of the stack. If the stack is empty, it should handle a structural underflow (e.g., return a sentinel value like `-1` or throw an exception).
3. `peek()` / `top()`: Returns the element at the top of the stack without removing it.
4. `isEmpty()`: Returns a boolean indicating whether the stack contains zero elements.

The runtime complexity of each core operation (`push`, `pop`, `peek`, `isEmpty`) must be strictly bounded by:

```text
O(1)
```

---

## Example Execution Trace

```text
Initialize an empty Stack: Head -> NULL (size=0)

1. push(10) → New node forms.   Head -> [10] -> NULL
2. push(20) → Links to front.   Head -> [20] -> [10] -> NULL
3. peek()   → Reads head data: Returns 20.
4. pop()    → Removes head node. Head shifts to next. Returns 20.
5. isEmpty()→ Returns false.
```

---

# Intuition

A stack requires all insertions and deletions to occur at the exact same end (the "top").

When implementing this layout on top of a Singly Linked List:
- We can designate the **head node** of the linked list to represent the **top of the stack**.
- **Pushing Elements:** To push an element `x`, we allocate a new node. We point this new node's `next` reference to our current `head` (`newNode.next = head`), and then update our `head` pointer to point to this new node (`head = newNode`). This inserts the element at the front of the list in constant time.
- **Popping Elements:** To pop an element, we read the data inside the `head` node, shift our `head` pointer forward exactly one step (`head = head.next`), and discard the old head node.

By anchoring our operations strictly at the head of the list, we completely avoid traversing the chain to find the tail, guaranteeing **O(1) constant time performance** for every operation.

---

# State Definition

Let our internal data structures be:

```text
Node head; // Reference pointer tracking the top node element (NULL when empty)
int size;  // Scalar counter tracking the active number of elements in the stack
```

---

# Operational Guard Conditions

```text
Pop Underflows  : If (head == null) or (size == 0) -> Stack Underflow! Cannot pop.
```

---

# Optimal Structural Linked List Solution

## Idea

By treating the head of a singly linked list as the top of the stack, nodes can be added and removed dynamically without memory pre-allocation or resizing overhead. All structural mutations execute natively in O(1) constant time.

---

## Java Code

```java
class MyStack {
    // Internal node structure configuration
    private static class Node {
        int data;
        Node next;

        Node(int data) {
            this.data = data;
            this.next = null;
        }
    }

    private Node head; // Tracks the absolute top of the stack
    private int size;  // Tracks the active count of elements

    // Constructor to initialize an empty stack
    public MyStack() {
        this.head = null;
        this.size = 0;
    }

    // Pushes an integer x onto the top of the stack
    public void push(int x) {
        Node newNode = new Node(x);
        
        // Link the new node to point to the current head top
        newNode.next = head;
        
        // Update head to establish the new node as the top anchor
        head = newNode;
        size++;
    }

    // Removes and returns the top element from the stack
    public int pop() {
        // Guard Condition: Check for Stack Underflow
        if (isEmpty()) {
            System.out.println("Stack Underflow Error: Cannot pop from an empty stack.");
            return -1; // Return a sentinel value indicating operation failure
        }

        // Extract data from the active head node
        int poppedValue = head.data;
        
        // Shift head forward to isolate and drop the old top node frame
        head = head.next;
        size--;
        
        return poppedValue;
    }

    // Returns the top element without removing it
    public int peek() {
        // Guard Condition: Ensure stack contains elements to read
        if (isEmpty()) {
            System.out.println("Stack Read Error: Cannot peek inside an empty stack.");
            return -1;
        }
        return head.data;
    }

    // Constant-time check to verify if the stack is completely empty
    public boolean isEmpty() {
        return head == null; // Alternatively: return size == 0;
    }

    // Helper utility method to get the current size of the stack
    public int size() {
        return this.size;
    }
}

// Client application driver verification
public class Main {
    public static void main(String[] args) {
        MyStack stack = new MyStack();

        stack.push(10);
        stack.push(20);
        stack.push(30);

        System.out.println("Top element is: " + stack.peek());   // Expected: 30
        System.out.println("Popped element: " + stack.pop());    // Expected: 30
        System.out.println("Current top is: " + stack.peek());   // Expected: 20
        System.out.println("Current stack size: " + stack.size()); // Expected: 2
    }
}
```

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Explanation |
| :--- | :--- | :--- |
| **`push(x)`** | O(1) | Element is inserted at the front of the list using high-speed pointer adjustments. |
| **`pop()`** | O(1) | The head pointer is shifted forward one step to disconnect the node frame instantly. |
| **`peek()`** | O(1) | Direct pointer reference lookup straight to `head.data`. |
| **`isEmpty()`**| O(1) | Simple null reference check (`head == null`). |

---

### Space Complexity

```text
Space Complexity: O(N)
```
*(where N is the current total number of elements pushed into the stack. Heap memory scales dynamically with utilization rather than consuming a fixed array capacity).*

---

# Key Takeaways

✅ Foundational dynamic data structure layout optimized for modern heap environments.

✅ **Array vs. Linked List Stack Trade-Offs:** An array-based stack implementation offers slightly better performance per operation due to memory cache locality. However, it requires a rigid, predetermined maximum capacity threshold or expensive array doubling copies. A Linked List implementation provides complete **elastic memory flexibility**—growing and shrinking dynamically per node—at the slight expense of wrapping data into node pointer object allocations.

✅ Direct real-world implementation framework utilized inside runtime **evaluation engines**, managing recursion tracking frames, and syntax processing tokens.

✅ Common Architectural Paradigm:

```text
Dynamic Node Chaining via Head-Anchored Pointer Displacements
```

### Similar Problems

- Implement Stack Using Arrays
- Implement Queue Using Linked List
- Min Stack (LeetCode 155)
- Implement Queue Using Stacks (LeetCode 232)
