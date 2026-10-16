# All Operations in a Doubly Linked List ([LeetCode 707 Variant](https://leetcode.com))

## Problem Statement

Implement all fundamental structural mutation and traversal operations for a comprehensive Doubly Linked List (DLL) container class:
1. `insertAtHead(val)`: Inserts a new node with value `val` at the absolute front of the list.
2. `insertAtTail(val)`: Inserts a new node with value `val` at the absolute end of the list.
3. `insertAtPosition(index, val)`: Inserts a new node with value `val` at a specific 0-indexed position.
4. `deleteAtHead()`: Removes the absolute first node of the list and returns its value.
5. `deleteAtTail()`: Removes the absolute last node of the list and returns its value.
6. `deleteAtPosition(index)`: Removes the node at a specific 0-indexed position.

All operations must protect structural symmetry by updating both `next` and `prev` reference pointers flawlessly across all edge cases (such as handling insertions or deletions on completely empty lists or single-element list states).

---

## Structural Visualization

Consider inserting a new node with value `25` at index `2` into a symmetric bi-directional chain:

```text
Initial Chain:
Head -> [10] <=====> [20] <=========> [30] <- Tail
                             │       ▲
                             ▼       │
                          [newNode: 25]

Final Chain:
Head -> [10] <=====> [20] <=====> [25] <=====> [30] <- Tail
```

---

# Intuition

Symmetric data manipulation in a Doubly Linked List requires updating **twice as many pointer fields** as a Singly Linked List during mutations to maintain structural invariants:
- **Head & Tail Mutations:** By caching both an explicit `head` pointer and a `tail` pointer, insertions and deletions at *both* ends of the collection bypass any traversal sweeps, executing natively in **O(1) constant time**.
- **Mid-Chain Splicing:** To insert or delete a node at position `k`, we traverse sequentially to locate the node at that position. Once there, the node can instantly link around itself or bind new neighbors without tracking auxiliary lagging trailing pointer variables, because its embedded `prev` and `next` hooks contain a direct road map of its local neighborhood.

---

# Time & Space Complexity

| Operation | Time Complexity | Space Complexity | Explanation |
| :--- | :--- | :--- | :--- |
| `insertAtHead` / `deleteAtHead` | **O(1)** | **O(1)** | High-speed pointer re-routing at the front boundary anchor. |
| `insertAtTail` / `deleteAtTail` | **O(1)** | **O(1)** | High-speed pointer re-routing at the cached tail boundary anchor. |
| `insertAtPosition` / `deleteAtPosition` | **O(N)** | **O(1)** | Requires a sequential traversal pass to locate the node at the specified index. |

---

# Optimal Object-Oriented Java Blueprint Implementation

## Idea

The class object encapsulates bi-directional list mechanics cleanly. Boundary guard checks verify state safety (e.g., underflows on empty lists or overshooting boundary indices) before executing structural link modifications on the heap.

---

## Java Code

```java
class DoublyLinkedList {
    // Internal blueprint definition of a Doubly Linked List Node Component
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

    private Node head; // Head anchor pointer (Front/MRU Boundary)
    private Node tail; // Tail anchor pointer (Back/LRU Boundary)
    private int size;  // Tracks the active count of items currently stored

    public DoublyLinkedList() {
        this.head = null;
        this.tail = null;
        this.size = 0;
    }

    /**
     * Operation 1: Insert at the absolute front of the DLL.
     * Time Complexity: O(1)
     */
    public void insertAtHead(int val) {
        Node newNode = new Node(val);
        if (head == null) {
            head = newNode;
            tail = newNode;
        } else {
            newNode.next = head;
            head.prev = newNode;
            head = newNode;
        }
        size++;
    }

    /**
     * Operation 2: Insert at the absolute end of the DLL.
     * Time Complexity: O(1)
     */
    public void insertAtTail(int val) {
        Node newNode = new Node(val);
        if (tail == null) {
            insertAtHead(val);
            return;
        } else {
            tail.next = newNode;
            newNode.prev = tail;
            tail = newNode;
            size++;
        }
    }

    /**
     * Operation 3: Insert at a specific 0-indexed position.
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

        Node current = head;
        // Traverse straight to the node currently occupying the target index
        for (int i = 0; i < index; i++) {
            current = current.next;
        }

        Node newNode = new Node(val);
        // Splice newNode into position right before the current node
        newNode.next = current;
        newNode.prev = current.prev;
        current.prev.next = newNode;
        current.prev = newNode;
        size++;
    }

    /**
     * Operation 4: Delete the node at the absolute front of the DLL.
     * Time Complexity: O(1)
     */
    public int deleteAtHead() {
        if (head == null) {
            System.out.println("Underflow Error: Cannot delete from an empty list.");
            return -1;
        }
        int removedVal = head.data;
        head = head.next;
        if (head == null) {
            tail = null; // List has run dry
        } else {
            head.prev = null; // Clear out dangling historical pointer references
        }
        size--;
        return removedVal;
    }

    /**
     * Operation 5: Delete the node at the absolute end of the DLL.
     * Time Complexity: O(1)
     */
    public int deleteAtTail() {
        if (tail == null) {
            System.out.println("Underflow Error: Cannot delete from an empty list.");
            return -1;
        }
        int removedVal = tail.data;
        tail = tail.prev;
        if (tail == null) {
            head = null; // List has run dry
        } else {
            tail.next = null; // Clear out dangling forward pointer references
        }
        size--;
        return removedVal;
    }

    /**
     * Operation 6: Delete a node at a specific 0-indexed position.
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

        Node current = head;
        // Traverse straight to the target node requiring destruction
        for (int i = 0; i < index; i++) {
            current = current.next;
        }

        // Route around the target node to completely isolate it from the chain
        current.prev.next = current.next;
        current.next.prev = current.prev;
        size--;
    }

    // Bi-directional printer utility: Forward Pass
    public void printForward() {
        Node current = head;
        System.out.print("Forward Pass:  ");
        while (current != null) {
            System.out.print(current.data + " <-> ");
            current = current.next;
        }
        System.out.println("null");
    }

    // Helper utility method to get the current item count
    public int getSize() {
        return this.size;
    }
}

// Client validation application gateway
public class Main {
    public static void main(String[] args) {
        DoublyLinkedList dll = new DoublyLinkedList();

        // 1. Verify Boundary Insertions
        dll.insertAtHead(20);
        dll.insertAtHead(10);
        dll.insertAtTail(40);
        System.out.print("After Base Insertions:   ");
        dll.printForward(); // Expected: 10 <-> 20 <-> 40 <-> null

        // 2. Verify Mid-Chain Splicing Insertion
        dll.insertAtPosition(2, 30);
        System.out.print("After Position 2 Insert: ");
        dll.printForward(); // Expected: 10 <-> 20 <-> 30 <-> 40 <-> null

        // 3. Verify Deletion Operations
        dll.deleteAtHead();
        dll.deleteAtTail();
        System.out.print("After Head & Tail Drop:  ");
        dll.printForward(); // Expected: 20 <-> 30 <-> null

        dll.deleteAtPosition(1);
        System.out.print("After Mid-Chain Deletion: ");
        dll.printForward(); // Expected: 20 <-> null
    }
}
```

---

# Key Takeaways

✅ Advanced architectural configuration optimizing spatial entry points across both endpoints.

✅ **The Core Link Symmetry Rule:** Every single node addition or deletion executed anywhere inside the mid-chain segment of a DLL must modify **four separate reference pointers** concurrently to remain valid. Forgetting to bind the backward reference (`newNode.prev` or `next.prev`) creates dangling historical remnants on the heap, corrupting the reverse lookup pipeline.

✅ Natively runs as the structural architecture driving standard core data systems like **LRU/LFU cache evictions buffers**, system process task sliders, media player playback log tracks, and complex memory registry pools.

✅ Common Architectural Paradigm:

```text
Symmetric Quadrilateral Reference Pinning for Real-Time Double-Ended Mutations
```
