# Remove Nth Node From End of List ([LeetCode 19](https://leetcode.com))

## Problem Statement

Given the `head` of a linked list, remove the `n`-th node from the end of the list and return *its head*.

---

## Examples

### Example 1

```text
Input: head =, n = 2
Output: [1,2,3,5]
```

### Explanation

```text
The 2nd node from the end of the list is node 4 (value 4).
We remove it, linking node 3 directly to node 5: 1 -> 2 -> 3 -> 5.
```

---

### Example 2

```text
Input: head =, n = 1
Output: []
```

---

### Example 3

```text
Input: head =, n = 1
Output: [1]
```

---

# Intuition

A naive approach would require two full passes over the list: first traversing from head to tail to count the total number of nodes (L), calculating the target index from the front (L - n), and then running a second loop to step forward and cut out the node.

We can optimize this into a **single linear pass** using the **Two-Pointer (Sliding Window / Fast & Slow) Design Pattern**:
- We want to place a pointer on the node located **immediately before** the node requiring removal. 
- If we create a gap of exactly `n` nodes between a `fast` pointer and a `slow` pointer by advancing `fast` ahead first, they form a fixed-size lookahead window.
- If we then advance both `fast` and `slow` forward at the exact same speed (1 step at a time), the gap remains locked at `n`.
- The moment the `fast` pointer reaches the absolute end of the list (`null`), the trailing `slow` pointer is mathematically guaranteed to be positioned exactly on the node **right before the n-th node from the end**!
- To cleanly handle edge cases where the `head` itself needs to be removed (n equals the length of the list), we anchor our `slow` pointer on a **Dummy Sentinel Node** right before the head.

---

# Brute Force Solution

## Idea

We perform an initial loop pass across the list length to count the total number of elements (L). If L = n, the head node is the victim, so we return `head.next`. Otherwise, we re-anchor a tracking cursor back at the `head` and step forward exactly L - n - 1 times to land right before the target node, then splice it out.

---

## Java Code

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        int length = 0;
        ListNode current = head;

        // Step 1: Compute the total count of nodes
        while (current != null) {
            length++;
            current = current.next;
        }

        // Edge Case: If the head node itself must be removed
        if (length == n) {
            return head.next;
        }

        // Step 2: Target the node positioned immediately before the n-th node from the end
        int targetIdx = length - n;
        current = head;
        for (int i = 0; i < targetIdx - 1; i++) {
            current = current.next;
        }

        // Step 3: Splice out the target node
        current.next = current.next.next;

        return head;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm performs two full sequential passes across the list (one pass of N steps to count, and a second pass of up to N steps to locate the deletion point).
- **Space Complexity:** O(1) auxiliary space as it only utilizes primitive iteration tracking variables.

---

# State Definition (Optimal Approach)

To optimize our tracking footprint into a single pass with an absolute constant O(1) memory floor, we manage states using a dummy node paired with a fixed lookahead window:

```java
ListNode dummy = new ListNode(0, head); // Sentinel dummy node shielding head updates cleanly
ListNode slow = dummy;                  // Trailing pointer that will land right before the target node
ListNode fast = dummy;                  // Leading pointer used to establish the fixed index gap
```

---

# Recurrence / Structural State Condition

```text
1. Advance fast pointer forward n + 1 steps to establish the offset window gap.
2. While fast != null:
       slow = slow.next
       fast = fast.next
3. slow.next = slow.next.next
Return dummy.next
```

---

# Optimal Two-Pointer (Fixed Window Gap) Solution

## Idea

We initialize both `slow` and `fast` pointers at our sentinel `dummy` node. First, we advance `fast` forward exactly `n + 1` steps to create the locked spacing window gap. Then, we slide both pointers forward simultaneously 1 step at a time. When `fast` hits `null`, `slow` rests perfectly right before the target node. We update `slow.next = slow.next.next` to decouple the node instantly.

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

    public ListNode removeNthFromEnd(ListNode head, int n) {
        // Initialize a sentinel dummy node to handle head removals cleanly
        ListNode dummy = new ListNode(0, head);
        ListNode slow = dummy;
        ListNode fast = dummy;

        // Phase 1: Advance the fast pointer forward to create a gap of exactly n nodes
        // We go n + 1 steps so slow lands exactly on the predecessor node
        for (int i = 0; i <= n; i++) {
            fast = fast.next;
        }

        // Phase 2: Slide both pointers forward simultaneously until fast hits the boundary null
        while (fast != null) {
            slow = slow.next;
            fast = fast.next;
        }

        // Phase 3: Splice out the target n-th node from the end
        slow.next = slow.next.next;

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
*Explanation:* The `fast` pointer sweeps sequentially from the dummy node straight to the terminal `null` boundary exactly once. The overall array track is traversed in a single pass of exactly N+1 total steps, keeping execution runtime strictly linear.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By mutatively rearranging existing reference links directly on the heap using local variable pointers, the algorithm completely drops the need for auxiliary tracking tables, matrices, or collections.

---

# Key Takeaways

✅ Core baseline template for single-pass window tracking and relative distance lookup tasks.

✅ **The Value of the Sentinel Dummy Link:** When a problem carries a risk that the absolute first node (`head`) might be the node requiring removal (e.g., deleting the 5th node from the end of a list of length 5), anchoring a `dummy` node upfront avoids crashing out with empty references and provides a unified deletion workflow for all nodes.

✅ Directly drives real-world microarchitectures like **streaming data sliding window filters**, networking buffer management (evicting data payloads positioned at fixed historical offsets), and transaction log trims.

✅ Common Architectural Paradigm:

```text
Fixed Reference Gap Racing for Single-Pass Suffix Predecessor Splicing
```

### Similar Problems

- Middle of the Linked List ([LeetCode 876](https://leetcode.com))
- Delete the Middle Node of a Linked List ([LeetCode 2095](https://leetcode.com))
- Rotate List ([LeetCode 61](https://leetcode.com))
- Palindrome Linked List ([LeetCode 234](https://leetcode.com))
