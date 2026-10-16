# Check If Linked List Is Circular ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given the `head` of a singly linked list, determine whether the list is **circular** or not. 

A linked list is called circular if it not null and the **absolute final node loops back to point straight to the first node (the head)** of the list. Return `true` if the given linked list forms a completely closed circular loop, otherwise return `false`.

---

## Examples

### Example 1

```text
Input: Head -> 10 -> 20 -> 30 -> 40 
               ▲                  │
               └──────────────────┘ (Loops back to Head)
Output: true
```

### Explanation

```text
The terminal node 40 does not point to null. Instead, it references back to the 
initial head pointer (10), completing a valid closed circular loop.
```

---

### Example 2

```text
Input: Head -> 10 -> 20 -> 30 -> null
Output: false
```

### Explanation

```text
The final node 30 terminates cleanly by pointing straight to null, 
indicating a standard linear linked list structure.
```

---

# Intuition

To verify if a linked list is circular:
- A linear list terminates the moment it encounters a `null` reference pointer.
- A circular list never hits `null`; instead, its path will eventually loop back around to its original entry point.
- We can track this behavior cleanly by starting a temporary traversal pointer (`current`) right after the head node (`head.next`). 
- We move `current` forward step-by-step down the chain. If `current` encounters `null` at any point, the list cannot be circular. If `current` cycles all the way back to match the original `head` pointer (`current == head`), we confirm that the list forms a closed circular loop.

*(Note: This specific problem checks if the entire list forms a loop back to the **very first node (the head)**. If a list contains a hidden loop somewhere in its middle section without reaching the head, that is classified as a general graph cycle problem, solvable via Floyd's Tortoise and Hare algorithm).*

---

# Brute Force Solution

## Idea

We can cache all visited node address references inside a `HashSet` as we traverse forward. If we encounter a node pointer that already exists inside our tracking set, we evaluate if it matches our initial `head` node. If it does, the list is circular. If we hit a `null` barrier first, the collection is linear.

---

## Java Code

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public boolean isCircular(Node head) {
        // An empty list is traditionally not considered circular
        if (head == null) {
            return false;
        }

        Set<Node> visitedNodes = new HashSet<>();
        Node current = head;

        while (current != null) {
            // If the node has already been visited, a loop exists
            if (visitedNodes.contains(current)) {
                // Verify if the loop cycles specifically back to the head anchor
                return current == head;
            }
            
            // Record the current node address reference on the hash set
            visitedNodes.add(current);
            current = current.next;
        }

        return false; // Safely hit null, confirming a linear path structure
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm processes each node in the chain at most once.
- **Space Complexity:** O(N) auxiliary memory footprint to store node references inside the `HashSet` container.

---

# State Definition (Optimal Approach)

To optimize our tracking footprint down to a constant O(1) floor, we bypass external collections entirely. We only need a single tracking reference pointer variable to verify the circular loop constraint:

```java
Node current = head.next; // Secondary lookup pointer starting one step ahead of the head root
```

---

# Recurrence / Structural State Condition

```text
While current != null AND current != head:
    current = current.next

Return (current == head)
```

---

# Optimal Single-Pointer Solution

## Idea

We start a `current` tracking pointer at `head.next`. We advance it continuously inside a loop. If `current` runs out of bounds (`current == null`), the structure cannot be circular, returning `false`. If `current` catches up to equal our original `head` reference anchor, the continuous loop requirement is satisfied, returning `true`.

---

## Java Code

```java
class Solution {
    // Internal node architecture contract blueprint reference
    private static class Node {
        int data;
        Node next;
        Node(int data) { this.data = data; this.next = null; }
    }

    public boolean isCircular(Node head) {
        // Guard Condition: An empty list cannot form a valid circular track
        if (head == null) {
            return false;
        }

        // Initialize a parsing index pointer one step ahead of the head node
        Node current = head.next;

        // Traverse sequentially down the line until we hit null OR loop back to the head
        while (current != null && current != head) {
            current = current.next;
        }

        // The list is circular if and only if the pointer cycled completely back to the head
        return (current == head);
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The array loop processes each node in the sequence exactly once. It either terminates upon hitting the terminal `null` boundary at node N or closes its iteration cycle when it wraps back to the entry cell, guaranteeing a linear timeline.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By using pointer reference comparisons directly on the heap, it eliminates intermediate table structures, operating inside an absolute constant space footprint.

---

# Key Takeaways

✅ Baseline linked list loop validation template using pointer reference comparisons.

✅ **Circular List vs. General Cycle Detection:** Pay close attention to the definition. A **Circular Linked List** means the *entire* list loops cleanly from tail to head (`tail.next == head`). If a list has a cycle locked in its internal body (e.g., `10 -> 20 -> 30 -> 40 -> 20`), this is a general loop problem requiring **Floyd’s Cycle Finding Algorithm (Tortoise and Hare)**.

✅ Natively runs within critical system environments like **Operating System CPU task queues (Round-Robin)**, circular streaming buffer allocators, network routing loops, and audio/video track continuous playlist managers.

✅ Common Architectural Paradigm:

```text
Sequential Reference Traversal for Closed-Loop Boundary Validations
```

### Similar Problems

- Linked List Cycle ([LeetCode 141](https://leetcode.com))
- Linked List Cycle II ([LeetCode 142](https://leetcode.com))
- Design Circular Queue ([LeetCode 622](https://leetcode.com))
- Find the Winner of the Circular Game ([LeetCode 1823](https://leetcode.com))
