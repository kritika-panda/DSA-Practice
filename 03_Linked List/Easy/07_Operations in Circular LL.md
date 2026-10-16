# All Operations in a Circular Linked List ([LeetCode 707 Variant](https://leetcode.com))

## Problem Statement

Implement all fundamental structural mutation and traversal operations for a comprehensive Singly Circular Linked List (CLL) container class using the optimized **tail-anchor design pattern**:
1. `insertAtHead(val)`: Inserts a new node with value `val` at the absolute front of the circular loop.
2. `insertAtTail(val)`: Inserts a new node with value `val` at the absolute end of the circular loop.
3. `insertAtPosition(index, val)`: Inserts a new node with value `val` at a specific 0-indexed position inside the cycle.
4. `deleteAtHead()`: Removes the absolute first node of the circular loop and returns its value.
5. `deleteAtTail()`: Removes the absolute last node of the circular loop and returns its value.
6. `deleteAtPosition(index)`: Removes the node at a specific 0-indexed position within the cycle.

All operations must protect pointer integrity across all edge cases (such as handling mutations on completely empty loops or single-node circular tracks) without ever inducing infinite loops.

---

## Structural Visualization

Consider inserting a new node with data `25` at index `2` into an existing continuous loop:

```text
Initial Loop:
┌────────────────────────────────────────┐
│                                        │
▼                                        │
[Node 1: Head] -> [Node 2] -> [Node 3: Tail]
                     │            ▲
                     └─► [25] ────┘ (Splice Node)

Final Loop:
┌──────────────────────────────────────────────────┐
│                                                  │
▼                                                  │
[Node 1: Head] -> [Node 2] -> [25] -> [Node 4: Tail]
```

---

# Intuition

Managing a Circular Linked List using a standard `head` pointer requires scanning the entire collection in $O(N)$ time just to find the tail whenever you want to insert or delete at the end. 

We can completely optimize this by caching an explicit pointer to the **`tail`** node instead of the head:
- The tail node is located directly at **`tail`**.
- The head node is instantly accessible in constant time at **`tail.next`**.

This architectural layout enables high-speed insertions and deletions at *both* ends of the circular loop in **$O(1)$ constant time** because you hold immediate structural anchors to both edge slots simultaneously. When inserting or deleting at a specific mid-chain index, we loop sequentially up to the target index using the head node (`tail.next`) as our chronological origin point.

---

# Time & Space Complexity

| Operation | Time Complexity | Space Complexity | Explanation |
| :--- | :--- | :--- | :--- |
| `insertAtHead` / `deleteAtHead` | **$O(1)$** | **$O(1)$** | Constant-time pointer shifting using the direct path `tail.next`. |
| `insertAtTail` / `deleteAtTail` | **$O(1)$** | **$O(1)$** | Constant-time tail pointer adjustments using the direct `tail` anchor reference. |
| `insertAtPosition` / `deleteAtPosition` | **$O(N)$** | **$O(1)$** | Requires a sequential traversal loop pass to locate the node at `index - 1`. |

---

# Optimal Object-Oriented Java Blueprint Implementation

## Idea

The class object encapsulates circular reference loops cleanly. Boundary guard conditions handle state transformations (like handling underflows on empty cycles or resolving single-element node teardowns) defensively before reference assignments change on the heap.

---

## Java Code

```java
class CircularLinkedList {
    // Internal blueprint definition of a Singly Circular Linked List Node
    private static class Node {
        int data;
        Node next;

        Node(int data) {
            this.data = data;
            this.next = null;
        }
    }

    private Node tail; // Tail anchor pointer (tail.next acts as our virtual head pointer)
    private int size;  // Tracks the active count of items currently inside the loop

    public CircularLinkedList() {
        this.tail = null;
        this.size = 0;
    }

    /**
      * Operation 1: Insert at the front of the circular queue.
     * Time Complexity: O(1)
     */
    public void insertAtHead(int val) {
        Node newNode = new Node(val);

        if (tail == null) {
            // Case A: Empty loop setup -> node points circularly to itself
            tail = newNode;
            tail.next = tail;
        } else {
            // Case B: Splice the new node between the tail and the current head
            newNode.next = tail.next; // New node points to old head
            tail.next = newNode;      // Tail points to new head
        }
        size++;
    }

    /**
     * Operation 2: Insert at the end of the circular queue.
     * Time Complexity: O(1)
     */
    public void insertAtTail(int val) {
        // Structurally identical to a head insertion, except we advance 
        // the tail reference forward by one slot to establish it as the new back
        insertAtHead(val);
        tail = tail.next;
    }

    /**
     * Operation 3: Insert at a specific 0-indexed position within the cycle.
     * Time Complexity: O(N)
     */
    public void insertAtPosition(int index, int val) {
        if (index < 0 || index > size) {
            System.out.println("Insertion Error: Index out of bounds.");
            return;
        }
        if (index == 0) {
            insertAtHead(val);
            return;
        }
        if (index == size) {
            insertAtTail(val);
            return;
        }

        // Initialize our search starting at the head node (tail.next)
        Node current = tail.next;
        // Traverse sequentially to find the node right before the target index (index - 1)
        for (int i = 0; i < index - 1; i++) {
            current = current.next;
        }

        Node newNode = new Node(val);
        newNode.next = current.next;
        current.next = newNode;
        size++;
    }

    /**
     * Operation 4: Delete the node at the absolute front of the cycle.
     * Time Complexity: O(1)
     */
    public int deleteAtHead() {
        if (tail == null) {
            System.out.println("Underflow Error: Cannot delete from an empty circular list.");
            return -1;
        }

        Node head = tail.next;
        int removedVal = head.data;

        if (tail == head) {
            // Case A: Only one node exists in the loop -> clear list entirely
            tail = null;
        } else {
            // Case B: Disconnect the head node by routing tail straight to head's successor
            tail.next = head.next;
        }
        size--;
        return removedVal;
    }

    /**
     * Operation 5: Delete the node at the absolute end of the cycle.
     * Time Complexity: O(N)
     */
    public int deleteAtTail() {
        if (tail == null) {
            System.out.println("Underflow Error: Cannot delete from an empty circular list.");
            return -1;
        }

        Node head = tail.next;
        int removedVal = tail.data;

        if (tail == head) {
            // Case A: Single node teardown
            tail = null;
        } else {
            // Case B: Must find the second-to-last node to change tail orientation
            Node current = head;
            while (current.next != tail) {
                current = current.next;
            }
            // Link the second-to-last node to the head, and update tail pointer
            current.next = head;
            tail = current;
        }
        size--;
        return removedVal;
    }

    /**
     * Operation 6: Delete a node at a specific 0-indexed position within the cycle.
     * Time Complexity: O(N)
     */
    public void deleteAtPosition(int index) {
        if (index < 0 || index >= size) {
            System.out.println("Deletion Error: Index out of bounds.");
            return;
        }
        if (index == 0) {
            deleteAtHead();
            return;
        }
        if (index == size - 1) {
            deleteAtTail();
            return;
        }

        Node current = tail.next;
        // Traverse sequentially to find the node preceding the target index
        for (int i = 0; i < index - 1; i++) {
            current = current.next;
        }

        // Bypass the target node to completely decouple it from the loop
        current.next = current.next.next;
        size--;
    }

    // Safe circular print pass utilizing a do-while loop structure
    public void printList() {
        if (tail == null) {
            System.out.println("Circular Loop: Empty List.");
            return;
        }

        Node head = tail.next;
        Node current = head;
        System.out.print("Circular Loop: ");
        do {
            System.out.print(current.data + " -> ");
            current = current.next;
        } while (current != head);
        System.out.println("(Back to Head: " + head.data + ")");
    }

    public int getSize() {
        return this.size;
    }
}

// Client validation application gateway
public class Main {
    public static void main(String[] args) {
        CircularLinkedList cll = new CircularLinkedList();

        // 1. Verify Endpoints Insertions
        cll.insertAtTail(20);
        cll.insertAtTail(40);
        cll.insertAtHead(10);
        System.out.print("After Base Insertions:   ");
        cll.printList(); // Expected: 10 -> 20 -> 40 -> (Back to Head: 10)

        // 2. Verify Mid-Chain Splicing Insertion
        cll.insertAtPosition(2, 30);
        System.out.print("After Position 2 Insert: ");
        cll.printList(); // Expected: 10 -> 20 -> 30 -> 40 -> (Back to Head: 10)

        // 3. Verify Deletion Routines
        cll.deleteAtHead();
        cll.deleteAtTail();
        System.out.print("After Head & Tail Drop:  ");
        cll.printList(); // Expected: 20 -> 30 -> (Back to Head: 20)
        cll.deleteAtPosition(1);
        System.out.print("After Mid-Chain Drop:    ");
        cll.printList(); // Expected: 20 -> (Back to Head: 20)
        }
        }
```

Key Takeaways

✅ Advanced architectural sequence pattern optimizing circular node transformations.
✅ The Tail-Anchor Pattern Rule: Caching a single tail reference pointer is the standard design choice for circular configurations. Because tail.next points natively straight to the head, this single register unlocks constant-time mutations at both list edges simultaneously, preventing wasteful $O(N)$ trailing lookup iterations.
✅ Directly drives production workflows like Operating System Round-Robin thread task balancers, multiplayer match rotation frameworks, cyclical streaming data ring caches, and media loop player logs.
✅ Common Architectural Paradigm:
text Tail-Anchored Pointer Recycling for Boundary-Free Continuous Queue Management 
