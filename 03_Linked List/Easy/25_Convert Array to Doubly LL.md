# Convert Array to Doubly Linked List ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given an array of integers `arr`, your task is to construct a **Doubly Linked List (DLL)** using the elements of the array and return *the head pointer of the newly created list*.

The order of elements inside the doubly linked list must exactly match the chronological order of elements inside the array.

---

## Examples

### Example 1

```text
Input: arr = [1, 2, 3, 4, 5]
Output: 1 <-> 2 <-> 3 <-> 4 <-> 5 <-> null
```

### Explanation

```text
The elements are linked sequentially. 
Each intermediate node holds explicit links pointing to both its predecessor and its successor.
The head's prev pointer points to null, and the tail's next pointer points to null.
```

---

### Example 2

```text
Input: arr = [10]
Output: null <- 10 -> null
```

---

# Intuition

To translate a contiguous block of data (like an array) into a dynamic reference chain on the heap, we must allocate a new **Node object** for every element and link them together symmetrically.

Because a Doubly Linked List requires **bi-directional pointers**, each step of the pipeline must enforce two structural invariants:
1. The `next` pointer of our preceding node must reference the new incoming node (`prevNode.next = newNode`).
2. The `prev` pointer of our new incoming node must reference the preceding node (`newNode.prev = prevNode`).

We can achieve this elegantly in a **single linear pass**:
- Allocate the absolute first element of the array as our baseline `head` node.
- Maintain a running pointer tracking our current terminal boundary node (let's call this cursor `current`).
- Loop through the remaining elements of the array starting from index 1. For each value, allocate a new node, establish the forward and backward pointer links between `current` and the new node, and then advance our `current` cursor forward onto the new node.

---

# State Definition

The structural translation tracks sequence accumulation using two reference pointers:

```java
Node head = new Node(arr[0]); // Anchor root pointer tracking the beginning of the list
Node current = head;          // Local cursor tracking the active tail during construction
```

---

# Recurrence / Structural Loop Condition

```text
For i from 1 to arr.length - 1:
    Node newNode = new Node(arr[i])
    current.next = newNode
    newNode.prev = current
    current = newNode
```

---

# Optimal Single-Pass Constructive Solution

## Idea

We isolate the first element to initialize our list anchors safely, preventing null pointer crashes. We then scan the rest of the array linearly, executing bidirectional pointer bindings on-the-fly. This translates array segments into an identical DLL chain inside a flat linear time footprint.

---

## Java Code

```java
class Solution {
    // Internal blueprint contract definition for a Doubly Linked List Node Component
    private static class Node {
        int data;
        Node prev;
        Node next;

        Node(int data) {
            this.data = data;
            this.prev = null;
            this.next = null;
        }
    }

    public Node constructDLL(int[] arr) {
        // Guard Condition: If the source data array is empty, return null
        if (arr == null || arr.length == 0) {
            return null;
        }

        // Phase 1: Initialize the absolute first node to serve as our head anchor
        Node head = new Node(arr[0]);
        Node current = head; // Cursor pointer tracking the current tail of the expanding DLL

        // Phase 2: Run a single pass to generate and stitch remaining elements bi-directionally
        for (int i = 1; i < arr.length; i++) {
            Node newNode = new Node(arr[i]); // Allocate a fresh new node on the heap

            // Establish the bi-directional pointer bridges
            current.next = newNode;          // Link preceding node forward to the new node
            newNode.prev = current;          // Link the new node backward to the preceding node

            // Shift our active construction cursor forward onto our new tail node
            current = newNode;
        }

        return head; // Return the master head pointer anchor of the newly minted list
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The array loop processes each of the N elements exactly once. Inside the iteration block, node allocations and pointer assignment bindings take true O(1) constant time, bounding the total execution runtime strictly linearly relative to the array length N.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Heap allocation scales linearly to generate N distinct node container components on the heap to store our data records. No extra auxiliary lookahead tables or matrix markers are required, keeping auxiliary space bounded at O(1).

---

# Key Takeaways

✅ Baseline creation archetype converting contiguous data structures to non-contiguous reference links.

✅ **The Head Isolation Safety Pattern:** Isolating the absolute first index entry to initialize the list root (`head`) before entering loops is a major structural safeguard. It entirely eliminates the need to run repetitive `if (head == null)` check loops during every step of the iteration pass.

✅ Natively drives system architecture patterns like **deserialization layers** loading linear database array row fields straight into real-time memory caches, file buffer setups, protocol formatters, and telemetry streaming queues.

✅ Common Architectural Paradigm:

```text
Contiguous-to-Reference Serializations via Symmetric Pointer Binding Sweeps
```

### Similar Problems

- Introduction to Linked List ([GeeksforGeeks](https://geeksforgeeks.com))
- All Operations in a Doubly Linked List ([LeetCode 707 Variant](https://leetcode.com))
- Reverse a Doubly Linked List ([GeeksforGeeks](https://geeksforgeeks.com))
- Convert Binary Tree to Doubly Linked List ([LeetCode 426](https://leetcode.com))
