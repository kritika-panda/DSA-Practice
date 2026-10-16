# Linked List Cycle II ([LeetCode 142](https://leetcode.com))

## Problem Statement

Given the `head` of a linked list, return *the node where the cycle begins*. If there is no cycle, return `null`.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, `pos` is used to denote the index of the node that tail's `next` pointer is connected to (0-indexed). **Note that `pos` is not passed as a parameter**.

**Do not modify** the linked list.

The overall runtime complexity must be strictly bounded by:

```text
O(N)
```

---

## Examples

### Example 1

```text
Input: head = [3,2,0,-4], pos = 1
Output: tail connects to node index 1
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
Output: tail connects to node index 0
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
Output: no cycle
```

---

# Intuition

This problem builds directly upon *Linked List Cycle I*. Finding *if* a cycle exists is the first half of the puzzle, but locating the exact entry point requires a beautiful mathematical relationship exposed by **Floyd's Cycle-Finding Algorithm**.

Let's define three distances:
- L₁: The distance from the `head` to the absolute start of the cycle.
- L₂: The distance from the start of the cycle to the intersection point where `slow` and `fast` meet.
- C: The total length of the cycle loop.

When `slow` and `fast` meet:
- Distance traveled by `slow` = L₁ + L₂
- Distance traveled by `fast` = L₁ + L₂ + n × C (where n is the number of complete laps around the cycle)

Since `fast` travels at exactly twice the speed of `slow`:
\[2 \times (L_1 + L_2) = L_1 + L_2 + n \times C\]
\[L_1 + L_2 = n \times C\]
\[L_1 = n \times C - L_2\]

This formula reveals a striking geometric fact: **The distance from the `head` to the cycle start (L₁) is exactly equal to the distance from the intersection point to the cycle start traveling forward (n × C - L₂)**.

Therefore, if we place one pointer back at the `head` and leave the other pointer at the intersection point, and then move **both forward at the exact same speed (1 step at a time)**, they are mathematically guaranteed to meet precisely at the origin node of the cycle!

---

# Brute Force Solution

## Idea

We allocate an external `HashSet` container to hold visited node pointers. We traverse sequentially from the `head`. Before moving to a node, we check if its address pointer is already logged inside our tracking set. The very first node that repeats is our cycle entry node. If we safely hit `null`, there is no cycle.

---

## Java Code

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null || head.next == null) {
            return null;
        }

        Set<ListNode> visitedNodes = new HashSet<>();
        ListNode current = head;

        while (current != null) {
            // The first node to re-appear inside our set is the origin of the cycle loop
            if (visitedNodes.contains(current)) {
                return current;
            }
            
            visitedNodes.add(current);
            current = current.next;
        }

        return null; // Hit a terminal null barrier, indicating no cycle loop
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm visits each node in the list at most once.
- **Space Complexity:** O(N) auxiliary memory footprint on the heap to store up to N node address references inside the tracking `HashSet` collection.

---

# State Definition (Optimal Approach)

To optimize our tracking footprint to an absolute constant O(1) floor, we drop external collections and manage states using two pointer anchors running at different speeds, followed by a synchronized matching pass:

```java
ListNode slow = head; // First runner advancing exactly 1 reference node step per iteration
ListNode fast = head; // Second runner advancing exactly 2 reference node steps per iteration
```

---

# Recurrence / Structural State Condition

```text
1. Phase 1: Run slow and fast until they intersect (slow == fast). If fast hits null, return null.
2. Phase 2: Reset slow = head.
3. Phase 3: While slow != fast, advance slow = slow.next and fast = fast.next.
4. Return slow.
```

---

# Optimal Two-Pointer (Tortoise and Hare) Solution

## Idea

We initialize both `slow` and `fast` pointers at the `head`. In Phase 1, `slow` moves 1 step and `fast` moves 2 steps until they intersect. If `fast` hits a `null` boundary, we return `null` immediately. In Phase 2, we reset `slow` back to the `head`, leave `fast` at the meeting point, and advance both pointers exactly 1 step at a time. The node where they meet is returned as our cycle origin.

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

    public ListNode detectCycle(ListNode head) {
        // Guard Condition: An empty list or single-node list cannot contain a cycle
        if (head == null || head.next == null) {
            return null;
        }

        ListNode slow = head;
        ListNode fast = head;
        boolean hasCycle = false;

        // Phase 1: Detect if a cycle exists using the dual-speed pointer race
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;

            if (slow == fast) {
                hasCycle = true; // Runners met, loop detected!
                break;
            }
        }

        // If no cycle is present, return null safely
        if (!hasCycle) {
            return null;
        }

        // Phase 2: Relocate one pointer back to the head
        slow = head;

        // Phase 3: Advance both pointers 1 step at a time until they collide
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next; // fast slows down to match slow's speed
        }

        // The point of collision is mathematically guaranteed to be the cycle origin
        return slow;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* Phase 1 takes at most N steps for pointers to meet inside a list of length N. Phase 2 and 3 take exactly L₁ steps (distance from head to cycle start), which is strictly less than N. Total operations scale completely inside flat linear limits.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By calculating pointer reference intersections directly on the heap, it eliminates auxiliary hash tables or stacks entirely, operating inside a flat, constant space footprint.

---

# Key Takeaways

✅ High-tier fundamental pointer tracking template combining spatial equations with graph traversals.

✅ **The Fast-Pointer Calibration Rule:** In Phase 3 of the algorithm, the `fast` pointer **must slow down** to move at exactly the same speed as `slow` (1 step per iteration). Forgetting to adjust `fast`'s step multiplier to 1 will cause the pointers to overshoot each other, missing the entry node completely.

✅ Directly drives production workflows like **network message entry point tracing tools**, tracking source reference elements in recursive dependencies graphs, debugging compiler pointer memory leaks, and telemetry parsing trackers.

✅ Common Architectural Paradigm:

```text
Bilateral Multi-Speed Racing with Synchronized Phase Shifts for Node Traps
```

### Similar Problems

- Linked List Cycle ([LeetCode 141](https://leetcode.com))
- Find the Duplicate Number ([LeetCode 287](https://leetcode.com))
- Happy Number ([LeetCode 202](https://leetcode.com))
- Intersection of Two Linked Lists ([LeetCode 160](https://leetcode.com))
