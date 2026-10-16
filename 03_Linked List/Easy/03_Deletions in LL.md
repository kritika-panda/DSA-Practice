# All Deletions in a Linked List ([LeetCode 707 Variant](https://leetcode.com))

## Problem Statement

Implement all fundamental node deletion operations for a Singly Linked List container class:
1. `deleteAtHead()`: Removes the absolute first node of the list and returns its value. If the list is empty, handle a structural underflow.
2. `deleteAtTail()`: Removes the absolute last node of the list and returns its value.
3. `deleteAtPosition(index)`: Removes the node at a specific 0-indexed position. If `index == 0`, it deletes the head. If the `index` is invalid or out of bounds, the operation is skipped.

All operations must handle edge cases cleanly (e.g., performing mutations on lists containing only a single element) and ensure memory is managed properly by dropping references to unlinked nodes.

---

## Structural Visualization

Consider deleting a node at index `2` (the node with value `30`) from an existing chain:

```text
Initial Chain:
Head -> [10] -> [20] -> [30] -> [40] -> null
                  │               ▲
                  └───────────────┘ (Re-route pointer)

Final Chain:
Head -> [10] -> [20] -> [40] -> null
```

---

# Intuition

Deleting nodes from a linked list is accomplished by breaking and re-routing object reference pointers on the heap:
- **From the Head:** We read the value at the current `head`, shift the `head` pointer forward exactly one step (`head = head.next`), and discard the old node. Because this requires zero traversal or element shifting, it executes instantly in **O(1) constant time**.
- **From the Tail:** We must locate the second-to-last node in the list. We traverse sequentially from the front until we find a node where `current.next.next == null`. We then drop the tail node by setting `current.next = null`. This requires an **O(N) linear time** scan.
- **From a Position:** To remove a node at position `k`, we must traverse sequentially to locate the node positioned immediately *before* that target slot (at position `k - 1`). We then splice the target node out of the chain by re-routing the previous node's link straight to the target node's successor:
  ```text
  prevNode.next = prevNode.next.next
  ```

---

# Time & Space Complexity

| Operation | Time Complexity | Space Complexity | Explanation |
| :--- | :--- | :--- | :--- |
| `deleteAtHead` | **O(1)** | **O(1)** | High-speed pointer detachment at the root entry point. |
| `deleteAtTail` | **O(N)** | **O(1)** | Requires a lookahead traversal pass to locate the second-to-last node. |
| `deleteAtPosition` | **O(N)** | **O(1)** | Requires a sequential traversal pass to locate the node at `index - 1`. |

---

# Optimal Object-Oriented Java Blueprint Implementation

## Idea

The implementation encapsulates structural node destruction logic using precise lookahead checks. Defensive guard conditions are configured upfront to catch underflow states (such as operating on empty lists or overshooting boundary indices) before references are modified on the heap.

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

    // Helper method to insert items at the tail (for initial setup verification)
    public void insertAtTail(int val) {
        Node newNode = new Node(val);
        if (head == null) {
            head = newNode;
            return;
        }
        Node current = head;
        while (current.next != null) {
            current = current.next;
        }
        current.next = newNode;
    }

    /**
     * Case 1: Delete the node at the absolute front of the list.
     * Time Complexity: O(1)
     */
    public int deleteAtHead() {
        // Guard Condition: Check for list underflow
        if (head == null) {
            System.out.println("Underflow Error: Cannot delete from an empty list.");
            return -1;
        }

        int removedVal = head.data;
        head = head.next; // Shift the head pointer forward to drop the first node frame
        return removedVal;
    }

    /**
     * Case 2: Delete the node at the absolute end of the list.
     * Time Complexity: O(N)
     */
    public int deleteAtTail() {
        // Guard Condition A: Check for list underflow
        if (head == null) {
            System.out.println("Underflow Error: Cannot delete from an empty list.");
            return -1;
        }

        // Guard Condition B: If only one node exists, it collapses to a head deletion
        if (head.next == null) {
            int removedVal = head.data;
            head = null;
            return removedVal;
        }

        // Traverse sequentially to find the second-to-last node (the pre-tail node)
        Node current = head;
        while (current.next.next != null) {
            current = current.next;
        }

        int removedVal = current.next.data;
        current.next = null; // Sever the link to the final node to trigger garbage collection
        return removedVal;
    }

    /**
     * Case 3: Delete a node at a specific 0-indexed position.
     * Time Complexity: O(N)
     */
    public void deleteAtPosition(int index) {
        // Guard Condition A: Negative indices are strictly invalid
        if (index < 0) {
            System.out.println("Deletion Error: Index cannot be negative.");
            return;
        }

        // Guard Condition B: Check for list underflow
        if (head == null) {
            System.out.println("Deletion Error: Cannot delete from an empty list.");
            return;
        }

        // Guard Condition C: Index 0 maps directly to a head deletion
        if (index == 0) {
            deleteAtHead();
            return;
        }

        Node current = head;
        // Traverse to locate the node positioned right before our target index (index - 1)
        for (int i = 0; current != null && i < index - 1; i++) {
            current = current.next;
        }

        // Guard Condition D: If current is null or target node is null, index is out of bounds
        if (current == null || current.next == null) {
            System.out.println("Deletion Error: Index out of bounds.");
            return;
        }

        // Bypass the target node by linking the previous node straight to the target's successor
        current.next = current.next.next;
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

        // Setup baseline chain: 10 -> 20 -> 30 -> 40 -> 50 -> null
        list.insertAtTail(10);
        list.insertAtTail(20);
        list.insertAtTail(30);
        list.insertAtTail(40);
        list.insertAtTail(50);
        System.out.print("Initial Chain:            ");
        list.printList();

        // 1. Verify Head Deletion
        System.out.println("Deleted Head Value:       " + list.deleteAtHead()); // Expected: 10
        System.out.print("After Head Deletion:      ");
        list.printList(); // Expected: 20 -> 30 -> 40 -> 50 -> null

        // 2. Verify Tail Deletion
        System.out.println("Deleted Tail Value:       " + list.deleteAtTail()); // Expected: 50
        System.out.print("After Tail Deletion:      ");
        list.printList(); // Expected: 20 -> 30 -> 40 -> null

        // 3. Verify Position Deletion
        System.out.println("Deleting node at index 1 (Value 30)...");
        list.deleteAtPosition(1);
        System.out.print("After Index Deletion:     ");
        list.printList(); // Expected: 20 -> 40 -> null
    }
}
```

---

# Key Takeaways

✅ Core fundamental operational lifecycle pattern driving garbage collection and resource recycling.

✅ **The Isolated Reference Deletion Rule:** In languages with automatic memory management like Java, an unlinked node object is automatically purged from the heap by the Garbage Collector once it becomes completely unreachable. By re-routing `current.next = current.next.next`, you erase the target node's only entry reference vector, isolating it for deletion.

✅ Directly drives real-world caching architectures like **Least Recently Used (LRU) eviction lanes**, transaction rollback engines, session token pool cleanup managers, and message dispatch flushing layers.

✅ Common Architectural Paradigm:

```text
Lookahead Pointer Re-routing via Boundary-Protected Sequence Traversals
```
