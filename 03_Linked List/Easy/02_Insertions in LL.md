# All Insertions in a Linked List ([LeetCode 707 Variant](https://leetcode.com))

## Problem Statement

Implement all fundamental node insertion operations for a Singly Linked List container class:
1. `insertAtHead(val)`: Inserts a new node with value `val` at the absolute front of the list.
2. `insertAtTail(val)`: Inserts a new node with value `val` at the absolute end of the list.
3. `insertAtPosition(index, val)`: Inserts a new node with value `val` at a specific 0-indexed position. If `index == 0`, it inserts at the head. If `index` equals the length of the list, it appends to the tail. If `index` is invalid, the operation is skipped.

All operations must handle edge cases cleanly (e.g., performing operations on a completely empty list) and maintain pointer integrity.

---

## Structural Visualization

Consider inserting a new node with data `25` at index `2` into an existing chain:

```text
Initial Chain:
Head -> [10] -> [20] ---------> [30] -> null
                  │               ▲
                  └─► [25] ───────┘
                     (New Node)

Final Chain:
Head -> [10] -> [20] -> [25] -> [30] -> null
```

---

# Intuition

Inserting into a linked list relies entirely on manipulating object reference pointers on the heap:
- **At the Head:** We create a new node, point its `next` pointer directly to our current `head`, and then update our global `head` reference to anchor on this new node. Because this requires zero data movement or list traversal, it executes instantly in **O(1) constant time**.
- **At the Tail:** We must first locate the terminal node whose `next` field is `null`. We traverse the list sequentially from the front until we find this tail node, then assign `tail.next = newNode`. This requires an **O(N) linear time** pass unless a dedicated global `tail` pointer is cached.
- **At a Position:** To insert a node at position `k`, we must traverse sequentially to find the node sitting immediately *before* that target slot (at position `k - 1`). We then splice our new node into the chain by re-routing pointers:
  ```text
  newNode.next = prevNode.next
  prevNode.next = newNode
  ```

---

# Time & Space Complexity

| Operation | Time Complexity | Space Complexity | Explanation |
| :--- | :--- | :--- | :--- |
| `insertAtHead` | **O(1)** | **O(1)** | High-speed pointer adjustment at the root entry point. |
| `insertAtTail` | **O(N)** | **O(1)** | Requires a full forward scan to discover the final terminal `null` link. |
| `insertAtPosition` | **O(N)** | **O(1)** | Requires a sequential traversal pass to locate the node at `index - 1`. |

---

# Optimal Object-Oriented Java Blueprint Implementation

## Idea

The implementation encapsulates node structure logic using clean object references. Boundary guard checks are explicitly structured to capture list anomalies (like out-of-bounds inputs or empty list triggers) before structural state modifications take place.

---

## Java Code

```java
class LinkedList {
    // Internal blueprint definition of a Singly Linked List Node
    private static class Node {
        int data;
        Node next;

        Node(int data) {
            this.data = data;
            this.next = null;
        }
    }

    private Node head; // Absolute root pointer anchor

    public LinkedList() {
        this.head = null;
    }

    /**
     	* Case 1: Insert at the absolute front of the list.
     	* Time Complexity: O(1)
     	*/
    public void insertAtHead(int val) {
        Node newNode = new Node(val);
        newNode.next = head; // Point the new node to the old head
        head = newNode;      // Move head pointer to anchor on the new node
    }

    /**
     	* Case 2: Insert at the absolute end of the list.
     	* Time Complexity: O(N)
     	*/
    public void insertAtTail(int val) {
        Node newNode = new Node(val);

        // Guard Condition: If the list is empty, tail insertion collapses to a head insertion
        if (head == null) {
            head = newNode;
            return;
        }

        // Traverse sequentially until we locate the final node pointing to null
        Node current = head;
        while (current.next != null) {
            current = current.next;
        }

        // Link the final node's next field straight to our new node
        current.next = newNode;
    }

    /**
     	* Case 3: Insert at a specific 0-indexed position.
     	* Time Complexity: O(N)
     	*/
    public void insertAtPosition(int index, int val) {
        // Guard Condition A: Negative indices are strictly invalid
        if (index < 0) {
            System.out.println("Insertion Error: Index cannot be negative.");
            return;
        }

        // Guard Condition B: Index 0 maps directly to a head insertion
        if (index == 0) {
            insertAtHead(val);
            return;
        }

        Node current = head;
        // Traverse to locate the node positioned right before the target index (index - 1)
        for (int i = 0; current != null && i < index - 1; i++) {
            current = current.next;
        }

        // Guard Condition C: If current is null, the requested index is out of bounds
        if (current == null) {
            System.out.println("Insertion Error: Index out of bounds.");
            return;
        }

        // Splice the new node safely into the middle of the chain
        Node newNode = new Node(val);
        newNode.next = current.next; // Connect new node's tail to the following segment
        current.next = newNode;      // Connect the preceding node's head to the new node
    }

    // Helper utility method to print the linked list sequence layout
    public void printList() {
        Node current = head;
        while (current != null) {
            System.out.print(current.data + " -> ");
            current = current.next;
        }
        System.out.println("null");
    }
}

// Client application verification gateway
public class Main {
    public static void main(String[] args) {
        LinkedList list = new LinkedList();

        // 1. Verify Head Insertions
        list.insertAtHead(20);
        list.insertAtHead(10);
        System.out.print("After Head Insertions: ");
        list.printList(); // Expected: 10 -> 20 -> null

        // 2. Verify Tail Insertions
        list.insertAtTail(40);
        System.out.print("After Tail Insertion:  ");
        list.printList(); // Expected: 10 -> 20 -> 40 -> null

        // 3. Verify Position Insertions
        list.insertAtPosition(2, 30);
        System.out.print("After Position 2 Insert: ");
        list.printList(); // Expected: 10 -> 20 -> 30 -> 40 -> null
    }
}
```

---

# Key Takeaways

✅ Core fundamental structural mutation archetype for linear reference grids.

✅ **The Splicing Safety Rule:** When inserting a node into the middle of a linked list, you must **ALWAYS** link the new node to the forward segment *first* (`newNode.next = current.next`) before updating the previous node's link (`current.next = newNode`). Reversing this order breaks the chain, causing you to permanently lose your reference pointer to the entire remaining suffix of the list on the heap.

✅ Natively forms the foundation for data engines like **dynamic task queue allocators**, file block linkage systems, text editor history change log chains, and graphing models.

✅ Common Architectural Paradigm:

```text
Multi-Scenario Pointer Splice Mapping via Boundary-Guarded Graph Traversals
```
