# Delete the Middle Node of a Linked List ([LeetCode 2095](https://leetcode.com))

## Problem Statement

You are given the `head` of a linked list. **Delete the middle node**, and return *the head of the modified linked list*.

The **middle node** of a linked list of size `n` is the \(\lfloor n / 2 \rfloor\)-th node from the start using **0-indexed** elements.
- For n = 1, 2, 3, 4, 5, the middle nodes are at indices 0, 1, 1, 2, 2 respectively.

---

## Examples

### Example 1

```text
Input: head = [1,3,4,7,1,2,6]
Output: [1,3,4,1,2,6]
```

### Explanation

```text
The size of the list is 7. The middle node is at index floor(7 / 2) = 3, which contains value 7.
We delete this node, resulting in the chain: 1 -> 3 -> 4 -> 1 -> 2 -> 6.
```

---

### Example 2

```text
Input: head = [1,2,3,4]
Output: [1,2,4]
```

### Explanation

```text
The size of the list is 4. The middle node is at index floor(4 / 2) = 2, which contains value 3.
We delete this node, resulting in the chain: 1 -> 2 -> 4.
```

---

### Example 3

```text
Input: head = [2,1]
Output: [2]
```

### Explanation

```text
The size of the list is 2. The middle node is at index floor(2 / 2) = 1, which contains value 1.
We delete this node, resulting in the chain: 2.
```

---

# Intuition

To delete a node in a singly linked list, we need to modify the `.next` pointer of the node positioned **immediately before** the node we want to remove. 

We can locate the exact middle node using the **Two-Pointer (Fast & Slow)** strategy, but with a slight structural adjustment to keep our slow pointer exactly one step behind the middle node:
1. Initialize a sentinel **Dummy Node** pointing straight to our head node (`dummy.next = head`).
2. Set our `slow` pointer at this dummy node, and the `fast` pointer at the actual `head`.
3. Advance `slow` forward by exactly **1 step** per loop iteration, while advancing `fast` forward by exactly **2 steps** per loop iteration.
4. Because `fast` travels at exactly twice the speed of `slow` starting from one node ahead, by the time `fast` reaches the end of the list (`null` or a terminal tail), `slow` will be positioned perfectly on the node **right before the middle node**.
5. We delete the middle node instantly by updating: `slow.next = slow.next.next`.

---

# Brute Force Solution

## Idea

We run an initial loop pass across the list length to count the total number of elements (N). If N = 1, we return `null` immediately. Otherwise, we re-anchor a tracking cursor at the `head` and step forward exactly \(\lfloor N / 2 \rfloor - 1\) times to land right before the center node, then splice it out.

---

## Java Code

```java
class Solution {
    public ListNode deleteMiddle(ListNode head) {
        // If there is only one node, deleting it leaves an empty list
        if (head == null || head.next == null) {
            return null;
        }

        int nodeCount = 0;
        ListNode current = head;

        // Step 1: Compute the total count of nodes
        while (current != null) {
            nodeCount++;
            current = current.next;
        }

        // Step 2: Target the node positioned immediately before the middle
        int targetIdx = nodeCount / 2;
        current = head;
        for (int i = 0; i < targetIdx - 1; i++) {
            current = current.next;
        }

        // Step 3: Splice out the middle node
        current.next = current.next.next;

        return head;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm performs two full sequential passes across the list (one pass of N steps to count, and a second pass of N/2 steps to locate the center).
- **Space Complexity:** O(1) auxiliary space as it only utilizes primitive iteration tracking variables.

---

# State Definition (Optimal Approach)

To optimize our tracking footprint into a single pass with an absolute constant O(1) memory floor, we handle states using a dummy node paired with dual-speed reference pointers:

```java
ListNode dummy = new ListNode(0, head); // Sentinel dummy node shielding head updates cleanly
ListNode slow = dummy;                  // Runner advancing 1 step at a time (ends right before middle)
ListNode fast = head;                   // Lookahead runner advancing 2 steps at a time
```

---

# Recurrence / Structural State Condition

```text
While fast != null AND fast.next != null:
    slow = slow.next
    fast = fast.next.next

slow.next = slow.next.next
Return dummy.next
```

---

# Optimal Two-Pointer (Tortoise and Hare) Solution

## Idea

We initialize `slow` at our sentinel `dummy` and `fast` at `head`. Inside an iterative loop, `slow` advances 1 step while `fast` advances 2 steps. The loop condition `fast != null && fast.next != null` prevents null pointer exceptions. When the loop terminates, `slow.next` is exactly the middle node. We route `slow.next` straight around it to `slow.next.next`.

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

    public ListNode deleteMiddle(ListNode head) {
        // Guard Condition: A list with only one node becomes empty after deletion
        if (head == null || head.next == null) {
            return null;
        }

        // Initialize a sentinel dummy node to handle short edge cases cleanly
        ListNode dummy = new ListNode(0, head);
        ListNode slow = dummy;
        ListNode fast = head;

        // Core Step: Run a staggered dual-speed pointer race over the data sequence
        while (fast != null && fast.next != null) {
            slow = slow.next;      // Slow runner moves forward 1 step
            fast = fast.next.next; // Fast runner moves forward 2 steps
        }

        // When the loop ends, slow lands exactly on the node PRECEDING the middle node
        slow.next = slow.next.next; // Bypass and decouple the middle node from the chain

        return dummy.next; // Return the head of the modified list anchored past dummy
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The pointer loops slide continuously forward across the list length exactly once. The loop runs exactly N/2 times because the `fast` pointer skips ahead by 2 elements per step, reducing total operations to a single clean pass.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By mutatively adjusting existing object references directly on the heap using local variable pointers, the algorithm completely removes the need for auxiliary tracking tables, matrices, or collections.

---

# Key Takeaways

✅ Core baseline template for single-pass node deletion and centerpoint tracking.

✅ **The Staggered Predecessor Strategy:** Finding a middle node is trivial, but modifying it requires the node *before* it. By starting your `slow` pointer one index behind `fast` (e.g., initializing `slow = dummy` while `fast = head`), you inherently shift your landing position backward by exactly one slot, removing the need for a separate tracking variable.

✅ Natively runs within critical system environments like **memory compaction routines** (de-allocating middle segments of buffer chains), load balancing throttlers, stream aggregation segments, and protocol compilers.

✅ Common Architectural Paradigm:

```text
Staggered Dual-Speed Reference Racing for Single-Pass Predecessor Splicing
```

### Similar Problems

- Middle of the Linked List ([LeetCode 876](https://leetcode.com))
- Remove Nth Node From End of List ([LeetCode 19](https://leetcode.com))
- Reorder List ([LeetCode 143](https://leetcode.com))
- Remove Linked List Elements ([LeetCode 203](https://leetcode.com))
