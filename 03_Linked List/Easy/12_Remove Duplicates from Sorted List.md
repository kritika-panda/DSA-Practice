# Remove Duplicates from Sorted List ([LeetCode 83](https://leetcode.com))

## Problem Statement

Given the `head` of a sorted linked list, delete all duplicates such that each element appears only once. Return *the linked list **sorted** as well*.

---

## Examples

### Example 1

```text
Input: head = [1,1,2]
Output: [1,2]
```

---

### Example 2

```text
Input: head = [1,1,2,3,3]
Output: [1,2,3]
```

---

# Intuition

Because the input linked list is already **sorted**, all duplicate nodes are guaranteed to be clustered adjacent to each other in a contiguous block. This structural property allows us to solve the problem in a single pass without any sorting algorithms or extra memory collections.

We can establish a cursor tracking reference pointer (`current`) initialized at the `head`:
1. Compare the value of the `current` node with the value of its immediate neighbor (`current.next`).
2. If `current.val == current.next.val`, a duplicate is found. We bypass and excise the duplicate node by re-routing the link pointer directly to the successor's successor: `current.next = current.next.next`.
3. If the values do not match, it means the duplicate run for the active value has fully ended. We safely advance our cursor pointer exactly one step forward: `current = current.next`.
4. We repeat this comparison until `current` or `current.next` lands on the terminal `null` barrier.

---

# Brute Force Solution

## Idea

We allocate an external `HashSet` to keep track of all visited node values. We traverse sequentially from the `head`. If a node's value is already logged inside our tracking set, we skip it by adjusting pointers. While this handles any list topology, it fails to exploit the sorted property and incurs a memory allocation penalty on the heap.

---

## Java Code

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return null;
        }

        Set<Integer> seenValues = new HashSet<>();
        ListNode current = head;
        seenValues.add(current.val);

        while (current.next != null) {
            if (seenValues.contains(current.next.val)) {
                // Duplicate value matched -> splice it out of the chain layout
                current.next = current.next.next;
            } else {
                // Unique value discovered -> record it and advance cursor
                seenValues.add(current.next.val);
                current = current.next;
            }
        }

        return head;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm sweeps sequentially across the list length exactly once.
- **Space Complexity:** O(N) auxiliary space overhead to preserve unique values inside the `HashSet` collection.

---

# State Definition (Optimal Approach)

To drop the memory cost to a constant floor and utilize the sorted order constraint efficiently, we manage pointer links entirely in-place with a single local cursor variable:

```java
ListNode current = head; // Local cursor pointer tracking operational matches on the heap
```

---

# Recurrence / Structural State Condition

```text
While current != null AND current.next != null:
    If current.val == current.next.val:
        current.next = current.next.next
    Else:
        current = current.next
```

---

# Optimal Single-Pass In-Place Solution

## Idea

We initialize an iterative `while` loop that handles value matches on the fly. If adjacent elements conflict, we update `current.next` to slice out the duplicate node. We only advance the `current` cursor forward when a unique element transition is verified. This ensures all identical duplicates are flattened within a constant memory space.

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

    public ListNode deleteDuplicates(ListNode head) {
        // Guard Condition: An empty list or single-node list contains no duplicates
        if (head == null || head.next == null) {
            return head;
        }

        ListNode current = head;

        // Core Step: Single forward pass over the sorted sequence
        while (current != null && current.next != null) {
            if (current.val == current.next.val) {
                // Adjacent duplicate detected -> skip the next node by re-routing links
                current.next = current.next.next;
            } else {
                // Distinct unique value confirmed -> advance cursor pointer forward safely
                current = current.next;
            }
        }

        return head; // Return the modified, de-duplicated list root anchor
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The loop traverses from the `head` pointer straight to the terminal `null` boundary exactly once. Every node link in the list of length N is processed a constant number of times, bounding total runtime strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By mutatively adjusting object reference pointers directly on the heap, the algorithm completely removes the need for auxiliary storage matrices or tracking sets, running inside a completely flat constant space footprint.

---

# Key Takeaways

✅ Core fundamental design template for linear data stream deduplication.

✅ **The Conditional Advancement Rule:** When removing adjacent duplicate runs, you must **ONLY** advance your tracking cursor (`current = current.next`) inside the `else` branch (when no match occurs). If you advance the cursor unconditionally during a match step, you will fail to catch longer contiguous duplicate sequences (such as `[1, 1, 1]`), leaving trailing duplicates behind.

✅ Directly drives real-world microarchitectures like **database sorted index compression modules**, text editing formatting systems, streaming telemetry log cleaners, and cache compiler layer pipelines.

✅ Common Architectural Paradigm:

```text
In-Place Reference Splicing via Adjacent Value Horizon Matching
```

### Similar Problems

- Remove Duplicates from Sorted List II ([LeetCode 82](https://leetcode.com))
- Remove Duplicates from Unsorted List
- Remove Element ([LeetCode 27](https://leetcode.com))
- Merge Two Sorted Lists ([LeetCode 21](https://leetcode.com))
