# Linked List Cycle ([LeetCode 141](https://leetcode.com))

## Problem Statement

Given `head`, the head of a linked list, determine if the linked list has a **cycle** in it.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, `pos` is used to denote the index of the node that tail's `next` pointer is connected to. **Note that `pos` is not passed as a parameter**.

Return `true` *if there is a cycle in the linked list*. Otherwise, return `false*.

---

## Examples

### Example 1

```text
Input: head = [3,2,0,-4], pos = 1
Output: true
```

### Explanation

```text
There is a cycle in the linked list, where the tail (-4) connects back to the node at index 1 (2).
3 -> 2 -> 0 -> -4
     ▲          │
     └──────────┘
```

---

### Example 2

```text
Input: head =, pos = 0
Output: true
```

### Explanation

```text
There is a cycle in the linked list, where the tail (2) connects back to the node at index 0 (1).
1 -> 2
▲    │
└────┘
```

---

### Example 3

```text
Input: head =, pos = -1
Output: false
```

### Explanation

```text
There is no cycle in the linked list.
1 -> null
```

---

# Intuition

A naive approach would check for terminal nodes using a standard traversal loop, but if a cycle exists, the loop will run forever because it will never hit `null`.

To handle this cleanly, we can track history or use a geometric pointer race strategy:
- **History Tracking:** As we move through the list, we can log each node's memory address reference inside a lookup set. If we land on a node pointer that is already logged, we have discovered a cycle.
- **The Pointer Race Strategy:** We can completely avoid the memory penalty of a lookup set by using **Floyd's Cycle-Finding Algorithm (Tortoise and Hare)**. We start two pointers at the `head`: a slow pointer (`slow`) that moves forward exactly 1 step at a time, and a fast pointer (`fast`) that moves forward exactly 2 steps at a time.
  - If the list is a straight line, the `fast` pointer will eventually run out of bounds and hit `null`, proving no cycle exists.
  - If a loop is trapped inside the list, the pointers will both enter the loop and cycle indefinitely. Because the `fast` pointer closes the gap between itself and the `slow` pointer by exactly 1 step during every loop iteration, the `fast` pointer is mathematically guaranteed to lap and **meet** the `slow` pointer from behind (`slow == fast`).

---

# Brute Force Solution

## Idea

We allocate an external `HashSet` container to hold visited node references. We traverse sequentially from the `head`. Before moving to a node, we check if its address pointer is already logged inside our tracking set. If it is, a cycle is confirmed, returning `true`. If we hit a `null` barrier, the path is linear, returning `false`.

---

## Java Code

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) {
            return false;
        }

        Set<ListNode> visitedNodes = new HashSet<>();
        ListNode current = head;

        // Traverse until we hit the terminal null boundary
        while (current != null) {
            // If the node reference already exists in the set, a cycle is confirmed
            if (visitedNodes.contains(current)) {
                return true;
            }
            
            // Record the current node reference address onto the hash set
            visitedNodes.add(current);
            current = current.next;
        }

        return false; // Traversal safely hit null, confirming a linear track
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm visits each node in the list at most once.
- **Space Complexity:** O(N) auxiliary space allocation penalty on the heap to store up to N node references inside the `HashSet` collection.

---

# State Definition (Optimal Approach)

To optimize our tracking footprint to an absolute constant O(1) floor, we drop external collections and manage states using two pointer anchors running at different speeds:

```java
ListNode slow = head; // Pointer advancing exactly 1 reference node step per iteration
ListNode fast = head; // Pointer advancing exactly 2 reference node steps per iteration
```

---

# Recurrence / Structural State Condition

```text
While fast != null AND fast.next != null:
    slow = slow.next
    fast = fast.next.next
    If slow == fast -> Return true

Return false
```

---

# Optimal Two-Pointer (Tortoise and Hare) Solution

## Idea

We initialize both `slow` and `fast` pointers at the `head`. Inside an iterative loop, `slow` advances 1 step while `fast` advances 2 steps. We safeguard against null pointer exceptions by verifying `fast != null && fast.next != null` in our loop condition block. If the two pointers match at any point, we exit early and return `true`.

---

## Java Code

```java
public class Solution {
    // Structural framework definition for LeetCode Node component block
    static class ListNode {
        int val;
        ListNode next;
        ListNode(int x) {
            val = x;
            next = null;
        }
    }

    public boolean hasCycle(ListNode head) {
        // Guard Condition: An empty list or single-node list with no loop cannot have a cycle
        if (head == null || head.next == null) {
            return false;
        }

        ListNode slow = head;
        ListNode fast = head;

        // Core Step: Run a dual-speed pointer race over the data sequence
        while (fast != null && fast.next != null) {
            slow = slow.next;          // Slow runner moves forward 1 step
            fast = fast.next.next;     // Fast runner moves forward 2 steps

            // If a cycle is present, the fast runner will eventually trap and meet slow
            if (slow == fast) {
                return true; // Match located, cycle confirmed!
            }
        }

        // Fast runner successfully hit a terminal null boundary, confirming no cycle
        return false;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* If no cycle exists, the `fast` pointer reaches the end of the list of length N in N/2 steps, running in linear time. If a cycle exists, once both pointers enter the loop of length K, the distance between them is at most K. Since the gap decreases by 1 step every iteration, they will meet in at most K steps. Because K ≤ N, the overall time complexity remains strictly linear.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By analyzing pointer reference equality directly on the heap, it eliminates auxiliary hash tables or stacks entirely, operating inside a flat, constant space footprint.

---

# Key Takeaways

✅ Core fundamental pointer tracking template for identifying circular graphs and cycles.

✅ **The Fast Lookahead Safety Rule:** When managing multi-step traversal pointers, you must **ALWAYS** verify both the fast pointer itself and its immediate successor for null values (`fast != null && fast.next != null`) inside your loop condition. Omitting the `fast.next` check will cause the program to crash with a `NullPointerException` whenever it evaluates `fast.next.next` on a linear tail node.

✅ Directly drives real-world architectures like **network packet routing loop detectors**, concurrency deadlock detectors, graph dependency validators, memory resource trackers, and distributed systems.

✅ Common Architectural Paradigm:

```text
Dual-Speed Reference Racing for Closed-Horizon Loop Detection
```

### Similar Problems

- Linked List Cycle II (Extract the exact starting node of a cycle - [LeetCode 142](https://leetcode.com))
- Happy Number ([LeetCode 202](https://leetcode.com))
- Find the Duplicate Number ([LeetCode 287](https://leetcode.com))
- Middle of the Linked List ([LeetCode 876](https://leetcode.com))
