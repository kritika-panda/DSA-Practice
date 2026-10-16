# Delete All Occurrences of a Given Key in a Doubly Linked List ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given the `head` of a **Doubly Linked List (DLL)** and an integer `key`, delete all nodes from the list that contain a value equal to `key`. 

Return *the head of the modified doubly linked list*.

---

## Examples

### Example 1

```text
Input: head = 1 <=> 2 <=> 2 <=> 1 <=> 2 <=> 2, key = 2
Output: 1 <=> 1
```

### Explanation

```text
All nodes with a value payload of 2 are completely unlinked and uncoupled from the chain. 
The remaining items contract to form a stable 1 <=> 1 list structure.
```

---

### Example 2

```text
Input: head = 2 <=> 2 <=> 2, key = 2
Output: null
```

### Explanation

```text
Every node in the doubly linked list matches the deletion target, draining the collection completely.
```

---

# Intuition

In a **Doubly Linked List**, each node contains both `next` and `prev` reference pointers. This symmetric structure is highly advantageous because it allows any arbitrary node to unlink and remove itself from the middle of the chain in **O(1) constant time** without needing to maintain manual lagging variables.

To delete a node `curr` in-place:
1. Update its predecessor's forward link to bypass it: `curr.prev.next = curr.next` (if `curr.prev` is not null).
2. Update its successor's backward link to bypass it: `curr.next.prev = curr.prev` (if `curr.next` is not null).

We can scan through the list sequentially from left to right using a single pass:
- If the `curr.data == key` condition is satisfied, we perform the pointer re-routing operations to isolate the node.
- **Handling Head Reassignments:** If the node being deleted happens to be the absolute first node of the list (`curr == head`), our entry root changes. We simply advance the global `head` reference forward by one slot (`head = curr.next`).
- If no match occurs, we advance our tracking cursor forward exactly one step as normal.

---

# State Definition

To modify reference connections entirely in-place inside a single pass, we track state transformations using a basic reference register pointer:

```java
Node curr = head; // Master tracking cursor navigating across the bi-directional chain layout
```

---

# Recurrence / Structural Splicing Condition

```text
While curr != null:
    If curr.data == key:
        Node nextNode = curr.next
        If curr == head -> head = nextNode
        If curr.prev != null -> curr.prev.next = curr.next
        If curr.next != null -> curr.next.prev = curr.prev
        curr = nextNode
    Else:
        curr = curr.next
```

---

# Optimal Single-Pass In-Place Solution

## Idea

We initialize an iterative `while` loop that handles value matches on the fly. When a node matches the target key parameter, we slice it out of the chain by bridging its neighbors' forward and backward links directly across it. We adjust the `head` reference dynamically if deletions cut across the starting boundary slot.

---

## Java Code

```java
class Solution {
    // Structural framework definition for a Doubly Linked List Node Component
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

    public static Node deleteAllOccurrences(Node head, int key) {
        // Guard Condition: An empty list contains no target nodes to delete
        if (head == null) {
            return null;
        }

        Node curr = head;

        // Core Step: Single forward pass mutative unweaving
        while (curr != null) {
            if (curr.data == key) {
                // Cache the next node reference before altering links
                Node nextNode = curr.next;

                // Case A: The deletion targets the absolute front head node
                if (curr == head) {
                    head = nextNode;
                }

                // Step 1: Bridge the predecessor node's next link forward around curr
                if (curr.prev != null) {
                    curr.prev.next = curr.next;
                }

                // Step 2: Bridge the successor node's prev link backward around curr
                if (curr.next != null) {
                    curr.next.prev = curr.prev;
                }

                // Advance the master cursor forward straight to the cached next node
                curr = nextNode;
            } else {
                // Element is unique and safe -> advance the tracking cursor pointer forward
                curr = curr.next;
            }
        }

        // Return the newly reassigned or preserved head pointer root anchor
        return head;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The tracking pointer sweeps from the `head` straight to the terminal `null` boundary exactly once. Inside the loop, all pointer mutations execute inside a fixed number of constant-time assignment checks, keeping total execution runtime strictly linear relative to the list length N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. By mutatively adjusting object links directly on the heap using local variable registers, the algorithm completely removes the need for auxiliary arrays, runtime stacks, or tracking collections.

---

# Key Takeaways

✅ Baseline node excision template demonstrating high-speed doubly-linked bidirectional mutations.

✅ **The Head Displacement Safety Check:** When deleting values from a linked structure, always account for head modifications. Checking `if (curr == head)` ensures that if the front boundary elements are dropped, the root pointer shifts forward accurately, preventing dangling entries.

✅ Directly drives real-world microarchitectures like **in-memory cache eviction queues** (instantly scrubbing all elements matching a target expired key or thread owner), transaction logging engines, session database routers, and file buffer pools.

✅ Common Architectural Paradigm:

```text
Bilateral Reference Decoupling for High-Speed Mid-Chain Node Erasures
```

### Similar Problems

- Remove Linked List Elements ([LeetCode 203](https://leetcode.com))
- Remove Duplicates from Sorted List ([LeetCode 83](https://leetcode.com))
- All Operations in a Doubly Linked List ([LeetCode 707 Variant](https://leetcode.com))
- Delete Node in a Linked List ([LeetCode 237](https://leetcode.com))
