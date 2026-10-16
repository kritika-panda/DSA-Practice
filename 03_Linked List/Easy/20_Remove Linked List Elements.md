# Remove Linked List Elements ([LeetCode 203](https://leetcode.com))

## Problem Statement

Given the `head` of a linked list and an integer `val`, remove all the nodes of the linked list that has `Node.val == val`, and return *the new head of the modified linked list*.

---

## Examples

### Example 1

```text
Input: head =, val = 6
Output: [1,2,3,4,5]
```

---

### Example 2

```text
Input: head = [], val = 1
Output: []
```

---

### Example 3

```text
Input: head =, val = 7
Output: []
```

---

# Intuition

The core challenge of this problem is that the nodes requiring removal can appear anywhere in the list—including at the very beginning (`head`). If the `head` node contains the target value `val`, it must be completely excised, which changes the entry root pointer of the entire list.

To handle head removals and edge cases cleanly without writing complex conditional branches, we can leverage a **Dummy Sentinel Node Pattern**:
1. Create a temporary dummy node and point its `next` pointer straight to our actual `head` (`dummy.next = head`).
2. Maintain a tracking cursor pointer (`current`) initialized at this `dummy` node.
3. As we traverse forward, we look ahead at the immediate neighbor (`current.next`).
4. If `current.next.val == val`, we find a target node. We remove it instantly by re-routing the link pointer directly around it to its successor: `current.next = current.next.next`.
5. If the value does not match, the next node is safe. We advance our cursor pointer exactly one step forward: `current = current.next`.
6. Once the loop terminates, the modified list's true root is safely captured right behind our sentinel helper at `dummy.next`.

---

# Brute Force Solution

## Idea

We can solve this problem recursively by validating nodes from back to front. We recurse deeper into the remaining suffix of the chain by passing `head.next`. As the call stack unwinds backward, if the active `head.val == val`, we skip it by returning `head.next` to the previous caller frame. Otherwise, we link it to the processed subproblem and return `head`.

---

## Java Code

```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        // Base Case: If we hit a null boundary, the search path ends
        if (head == null) {
            return null;
        }
        
        // Recursive Step: Process the rest of the list first
        head.next = removeElements(head.next, val);
        
        // If the current node matches the target value, return its successor to skip it
        if (head.val == val) {
            return head.next;
        }
        
        return head; // Current node is safe, return it
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm visits every node in the chain exactly once.
- **Space Complexity:** O(N) auxiliary stack memory allocation tax due to the recursive call stack depth generated while sweeping toward the tail.

---

# State Definition (Optimal Iterative Approach)

To drop the memory cost to a constant floor and process all modifications entirely in-place, we combine a sentinel dummy framework with a single tracking variable:

```java
ListNode dummy = new ListNode(0, head); // Dummy node protecting head updates cleanly
ListNode current = dummy;               // Local tracking cursor navigating reference hops
```

---

# Recurrence / Structural State Condition

```text
While current.next != null:
    If current.next.val == val:
        current.next = current.next.next
    Else:
        current = current.next

Return dummy.next
```

---

# Optimal Single-Pass In-Place Solution

## Idea

We initialize a `dummy` node pointing directly to `head` and anchor our tracking cursor `current` on it. By constantly inspecting `current.next`, we can spot a target value and slice it out before stepping onto it. We only move the `current` cursor forward when the next node is verified safe, matching a constant memory footprint.

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

    public ListNode removeElements(ListNode head, int val) {
        // Initialize a sentinel dummy node to shield the head boundary changes cleanly
        ListNode dummy = new ListNode(0, head);
        ListNode current = dummy; // Cursor tracking the last confirmed stable node

        // Core Step: Single forward pass over the sequence layout
        while (current.next != null) {
            if (current.next.val == val) {
                // Target value matched -> skip the next node by re-routing links
                current.next = current.next.next;
            } else {
                // Element is safe -> advance the tracking cursor pointer forward
                current = current.next;
            }
        }

        return dummy.next; // Return the new root entry point anchored right past dummy
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The iterative pointer traverses sequentially from our dummy node straight to the terminal `null` boundary exactly once. Every node link in the list of length N is processed in true O(1) constant time, bounding total runtime strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By mutatively adjusting object reference pointers directly on the heap, the algorithm completely removes the need for auxiliary storage arrays, tracking hash sets, or deep recursive invocation frames.

---

# Key Takeaways

✅ Core fundamental design template for in-place linear value filtering.

✅ **The Conditional Advancement Rule:** Just like removing duplicates, you must **ONLY** advance your tracking cursor (`current = current.next`) inside the `else` branch (when no deletion occurs). If you advance the cursor unconditionally during a delete step, you will fail to catch consecutive target elements (such as removing `7` from `[7, 7]`), leaving matching nodes stranded in the list.

✅ Directly drives real-world microarchitectures like **memory block garbage collection systems** (purging dirty or unallocated pages), transaction log cleaners, network packet stream segment filtering, and syntax token scrubbers.

✅ Common Architectural Paradigm:

```text
Sentinel-Shielded Pointer Splicing for Non-Continuous Value Filtering
```

### Similar Problems

- Remove Duplicates from Sorted List ([LeetCode 83](https://leetcode.com))
- Delete Node in a Linked List ([LeetCode 237](https://leetcode.com))
- Remove Element ([LeetCode 27](https://leetcode.com))
- Odd Even Linked List ([LeetCode 328](https://leetcode.com))
