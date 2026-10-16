# Find the Length of a Loop in a Linked List ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given the `head` of a singly linked list, determine if the linked list contains a **loop (cycle)**. If a loop is present, return the **total number of nodes** that form that loop. If there is no loop inside the linked list, return `0`.

---

## Examples

### Example 1

```text
Input: head = 3 -> 2 -> 0 -> -4 
               ▲          │
               └──────────┘ (Loops back to Node 2)
Output: 3
```

### Explanation

```text
A loop is present in the linked list spanning 3 nodes: 2, 0, and -4. 
Hence, the total length count of the loop is 3.
```

---

### Example 2

```text
Input: head = 1 -> 2 -> 3 -> null
Output: 0
```

### Explanation

```text
The list terminates cleanly by pointing to null, indicating that no loop exists.
```

---

# Intuition

This problem is a direct extension of **Linked List Cycle Detection**. 

We can locate and measure a loop inside a single pass using **Floyd's Cycle-Finding Algorithm (Tortoise and Hare)**:
1. **Detect the Loop:** We initialize a `slow` pointer and a `fast` pointer at the `head`. `slow` moves forward exactly 1 step at a time, while `fast` moves forward exactly 2 steps at a time. If they run into a `null` barrier, no loop exists. If they crash into each other (`slow == fast`), a loop is confirmed.
2. **Measure the Length:** When the pointers meet, they are trapped somewhere inside the cycle. We can freeze the `fast` pointer at this meeting junction and treat it as a stationary landmark. We then start a step counter initialized to `1` and advance the `slow` pointer forward exactly 1 step at a time. We continuously increment our counter as `slow` moves around the ring. The exact moment `slow` cycles completely back to hit our parked `fast` pointer (`slow == fast`), the loop has closed, and our counter holds the true total length of the cycle loop.

---

# Brute Force Solution

## Idea

We traverse sequentially from the `head` and store each visited node's memory address reference mapped to its 0-indexed chronological arrival timestamp inside a `HashMap`. If we hit a node that already exists in our map directory, a loop is detected. The length of the cycle is computed by subtracting the old timestamp from our current timestamp.

---

## Java Code

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    // Internal node architecture contract blueprint reference
    private static class Node {
        int data;
        Node next;
        Node(int data) { this.data = data; this.next = null; }
    }

    public int countNodesInLoop(Node head) {
        if (head == null) return 0;

        Map<Node, Integer> visitedMap = new HashMap<>();
        Node current = head;
        int currentTimestamp = 0;

        while (current != null) {
            // If the node has already been visited, a loop exists
            if (visitedMap.containsKey(current)) {
                // Length = current time index - matching historical time index
                return currentTimestamp - visitedMap.get(current);
            }

            // Log current node and its time index entry
            visitedMap.put(current, currentTimestamp);
            current = current.next;
            currentTimestamp++;
        }

        return 0; // Met null, meaning no loop exists
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm processes each node in the list at most once.
- **Space Complexity:** O(N) auxiliary space memory allocation penalty on the heap to store up to N node references inside the `HashMap` registry.

---

# State Definition (Optimal Approach)

To drop our tracking footprint down to a flat O(1) floor, we track states entirely in-place using two pointer anchors running at different speeds, followed by a local scalar loop counter:

```java
Node slow = head; // Pointer advancing exactly 1 reference node step per iteration
Node fast = head; // Pointer advancing exactly 2 reference node steps per iteration
```

---

# Recurrence / Structural Loop Condition

```text
1. Phase 1: while fast != null AND fast.next != null:
                 slow = slow.next, fast = fast.next.next
                 if slow == fast -> Loop found! Proceed to Phase 2
             Return 0 (if loop breaks without collision)

2. Phase 2: int loopLength = 1
             slow = slow.next
             while slow != fast:
                 slow = slow.next
                 loopLength++
             Return loopLength
```

---

# Optimal Two-Pointer Cycle Measurement Solution

## Idea

We use the dual-speed runner pass to find if a cycle is present. Once the pointers collide, we park `fast` right at the collision coordinate node. We then slide `slow` forward step-by-step around the cycle loop track, counting increments until it returns to match the parked `fast` reference anchor.

---

## Java Code

```java
class Solution {
    // Internal blueprint definition of a Node component block
    private static class Node {
        int data;
        Node next;
        Node(int data) { this.data = data; this.next = null; }
    }

    public int countNodesInLoop(Node head) {
        // Guard Condition: Lists with less than two nodes cannot form a valid cycle loop
        if (head == null || head.next == null) {
            return 0;
        }

        Node slow = head;
        Node Node = head; // fast pointer
        boolean hasLoop = false;

        // Phase 1: Detect if a loop exists using the dual-speed pointer race
        while (Node != null && Node.next != null) {
            slow = slow.next;         // Slow runner moves forward 1 step
            Node = Node.next.next;    // Fast runner moves forward 2 steps

            if (slow == Node) {
                hasLoop = true; // Runners met, loop confirmed!
                break;
            }
        }

        // If no loop is present, return 0 early
        if (!hasLoop) {
            return 0;
        }

        // Phase 2: Compute the total number of nodes inside the loop
        int loopLength = 1;
        slow = slow.next; // Advance slow exactly 1 step away from the junction point

        // Crawl slow forward around the ring track until it hits the parked fast pointer
        while (slow != Node) {
            loopLength++;
            slow = slow.next;
        }

        return loopLength; // Return the absolute count of nodes forming the loop ring
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* Phase 1 takes at most N steps for pointers to intersect inside a list of length N. Phase 2 takes exactly K steps, where K is the length of the loop (K ≤ N). Because the total operations slide continuously forward without nested tracking lookbacks, total execution time remains strictly bounded within linear limits.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By tracking pointer reference equality directly on the heap using local variable registers, the algorithm completely removes the need for auxiliary arrays, hash sets, or stack storage tables, operating inside a flat, constant space footprint.

---

# Key Takeaways

✅ Core baseline template for graph cycle size measurements and multi-phase pointer tracking.

✅ **The Landmark Parking Rule:** When measuring loop size features, keeping one pointer stationary to act as a benchmark landmark reference node (`while slow != fast`) is an elegant engineering choice. It allows you to wrap around a cyclic sequence safely without writing complex flag checking or lookback trackers.

✅ Directly drives real-world architectures like **network routing link verification analyzers** (detecting and counting hop steps inside traffic routing loops), memory leak leak debugging engines, thread deadlock counters, and cyclic protocol filters.

✅ Common Architectural Paradigm:

```text
Bilateral Reference Racing with Stationary Landmark Anchors for Cycle Sizing
```

### Similar Problems

- Linked List Cycle ([LeetCode 141](https://leetcode.com))
- Linked List Cycle II ([LeetCode 142](https://leetcode.com))
- Find the Town Judge ([LeetCode 997](https://leetcode.com))
- Happy Number ([LeetCode 202](https://leetcode.com))
