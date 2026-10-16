# Middle of the Linked List ([LeetCode 876](https://leetcode.com))

## Problem Statement

Given the `head` of a singly linked list, return *the **middle node** of the linked list*.

If there are **two middle nodes**, return **the second middle node**.

---

## Examples

### Example 1

```text
Input: head = [1,2,3,4,5]
Output: [3,4,5]
```

### Explanation

```text
The middle node of the list is node 3 (value 3), with its trailing sequence.
1 -> 2 -> 3 -> 4 -> 5 -> null
          ▲ (Middle)
```

---

### Example 2

```text
Input: head = [1,2,3,4,5,6]
Output: [4,5,6]
```

### Explanation

```text
Since the list has an even number of nodes (6), there are two middle nodes (values 3 and 4).
The problem states we must return the second middle node, which is node 4 (value 4) along with its trailing sequence.
1 -> 2 -> 3 -> 4 -> 5 -> 6 -> null
               ▲ (Second Middle)
```

---

# Intuition

A naive approach requires two full passes over the list: first traversing from head to tail to count the total number of nodes (N), and then running a second loop to step forward exactly N/2 times to reach the center.

We can optimize this into a **single linear pass** using the **Two-Pointer (Fast & Slow) Design Pattern**, also known as the Tortoise and Hare approach:
- We initialize a `slow` pointer and a `fast` pointer at the `head` of the list.
- We advance them inside a loop where `slow` moves forward exactly **1 step** per iteration, while `fast` moves forward exactly **2 steps** per iteration.
- Because `fast` travels at exactly twice the speed of `slow`, by the time `fast` reaches the absolute end of the list (`null`), `slow` is mathematically guaranteed to be positioned exactly at the **middle node** of the list!

---

# Brute Force Solution

## Idea

We perform an initial loop pass across the list length to count the total number of elements (N). Then, we re-anchor a tracking cursor back at the `head` and advance it forward exactly N/2 times using a standard loop to extract the middle target node.

---

## Java Code

```java
class Solution {
    public ListNode middleNode(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        int nodeCount = 0;
        ListNode current = head;

        // Step 1: Compute the total count of nodes
        while (current != null) {
            nodeCount++;
            current = current.next;
        }

        // Step 2: Traverse exactly N / 2 times to reach the center
        current = head;
        for (int i = 0; i < nodeCount / 2; i++) {
            current = current.next;
        }

        return current;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm performs two full sequential passes across the list (one pass of N steps to count, and a second pass of N/2 steps to locate the center).
- **Space Complexity:** O(1) auxiliary space as it only utilizes primitive iteration tracking variables.

---

# State Definition (Optimal Approach)

To optimize our tracking footprint into a single pass with an absolute constant O(1) memory floor, we manage states using two pointer anchors running at different speeds:

```java
ListNode slow = head; // Cursor pointer advancing exactly 1 reference node step per iteration
ListNode fast = head; // Cursor pointer advancing exactly 2 reference node steps per iteration
```

---

# Recurrence / Structural State Condition

```text
While fast != null AND fast.next != null:
    slow = slow.next
    fast = fast.next.next

Return slow
```

---

# Optimal Two-Pointer (Tortoise and Hare) Solution

## Idea

We initialize both `slow` and `fast` pointers straight at the `head` node. Inside an iterative loop, `slow` advances 1 step while `fast` advances 2 steps. We protect against null pointer exceptions by verifying `fast != null && fast.next != null` inside our loop condition block. When `fast` runs out of bounds, `slow` sits perfectly at the middle node.

---

## Java Code

```java
class Solution {
    // Structural framework definition for LeetCode Node component block
    public static class ListNode {
        int val;
        ListNode next;
        ListNode() {}
        ListNode(int val) { this.val = val; }
        ListNode(int val, ListNode next) { this.val = val; this.next = next; }
    }

    public ListNode middleNode(ListNode head) {
        // Guard Condition: An empty list or single-node list is its own middle node
        if (head == null || head.next == null) {
            return head;
        }

        ListNode slow = head;
        ListNode fast = head;

        // Core Step: Run a dual-speed pointer race over the data sequence
        while (fast != null && fast.next != null) {
            slow = slow.next;          // Slow runner moves forward 1 step
            fast = fast.next.next;     // Fast runner moves forward 2 steps
        }

        // When fast reaches the end, slow sits directly on the middle node
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
*Explanation:* The loop runs exactly N/2 times because the `fast` pointer skips ahead by 2 elements per step. Inside the loop, it only performs constant-time pointer assignment lookups, reducing total operations to a single clean pass.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By tracking pointer reference updates directly on the heap, it entirely eliminates auxiliary hash tables, arrays, or stacks, operating inside a flat, constant space footprint.

---

# Key Takeaways

✅ Core baseline traversal archetype driving all centerpoint lookup actions in linked linear tracks.

✅ **Even vs. Odd List Boundaries:** The condition `while (fast != null && fast.next != null)` handles both list shapes flawlessly. In an odd list (e.g., length 5), `fast` lands exactly on the tail node (`fast.next == null`), causing the loop to break while `slow` rests on index 2 (the middle). In an even list (e.g., length 6), `fast` lands straight on `null`, causing the loop to break while `slow` rests on index 3 (the second middle node), satisfying the tie-breaker requirement perfectly.

✅ Directly drives real-world microarchitectures like **Divide and Conquer linked algorithms** (e.g., finding center partitions to drive Merge Sort on Linked Lists), load balancing routers, analytics trackers, and buffer splitters.

✅ Common Architectural Paradigm:

```text
Dual-Speed Reference Racing for Single-Pass Centerpoint Discoveries
```

### Similar Problems

- Linked List Cycle ([LeetCode 141](https://leetcode.com))
- Remove Nth Node From End of List ([LeetCode 19](https://leetcode.com))
- Palindrome Linked List ([LeetCode 234](https://leetcode.com))
- Reorder List ([LeetCode 143](https://leetcode.com))
