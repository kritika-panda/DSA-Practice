# Remove Duplicates from an Unsorted Linked List ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given the `head` of an **unsorted** linked list, delete all duplicate nodes such that each element appears only once from its initial occurrence. Return *the head of the modified linked list*.

---

## Examples

### Example 1

```text
Input: head = 5 -> 2 -> 2 -> 4 -> 5 -> 2 -> null
Output: 5 -> 2 -> 4 -> null
```

### Explanation

```text
- 5 is the first element, so it is kept.
- 2 is the first element with value 2, so it is kept.
- The next 2 is a duplicate, so it is removed.
- 4 is unique, so it is kept.
- 5 and the last 2 are duplicates of previously visited nodes, so they are both removed.
```

---

### Example 2

```text
Input: head = 1 -> 2 -> 3 -> null
Output: 1 -> 2 -> 3 -> null
```

### Explanation

```text
All elements in the list are already unique, so no nodes are deleted.
```

---

# Intuition

Unlike a sorted linked list where all duplicate nodes sit adjacent to each other, an **unsorted list** can have duplicate values scattered anywhere across the entire timeline. 

To solve this in an optimal **single linear pass**, we need a high-speed directory to remember which values we have already seen. A **HashSet** is the perfect structure for this:
- We initialize a tracking `HashSet` and place a cursor pointer (`current`) at the `head` node.
- As we move forward, we evaluate the immediate neighbor (`current.next`).
- If `current.next.val` is already logged inside our `HashSet`, it is a duplicate. We bypass and splice it out of the chain instantly: `current.next = current.next.next`.
- If the value is completely new, we log it into our set and advance our cursor forward exactly one step: `current = current.next`.

---

# Brute Force Solution

## Idea

For each node in the list, we can run a nested inner loop that scans the entire remaining suffix of the list to search for and destroy any matching duplicate nodes. While this avoids extra memory structures, it is highly inefficient for large datasets.

---

## Java Code

```java
class Solution {
    public ListNode removeDuplicates(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode ptr1 = head;

        // Outer loop picks each node one by one
        while (ptr1 != null) {
            ListNode ptr2 = ptr1;

            // Inner loop scans the remaining suffix list for duplicates
            while (ptr2.next != null) {
                if (ptr2.next.val == ptr1.val) {
                    // Duplicate found -> splice it out of the chain layout
                    ptr2.next = ptr2.next.next;
                } else {
                    ptr2 = ptr2.next;
                }
            }
            ptr1 = ptr1.next;
        }

        return head;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N²) due to the nested loops checking the entire remaining suffix for every single node.
- **Space Complexity:** O(1) auxiliary space as it only updates pointers in-place.

---

# State Definition (Optimal Approach)

To optimize our runtime tracking down to a linear O(N) track, we map history states using an external lookup container:

```java
Set<Integer> seenValues = new HashSet<>(); // Directory tracking all unique data entries seen so far
ListNode current = head;                 // Local cursor pointer tracking operational frames on the heap
```

---

# Recurrence / Structural State Condition

```text
seenValues.add(head.val)
While current != null AND current.next != null:
    If seenValues.contains(current.next.val):
        current.next = current.next.next
    Else:
        seenValues.add(current.next.val)
        current = current.next
```

---

# Optimal Single-Pass HashSet Solution

## Idea

We seed our `HashSet` with the initial `head.val`. Inside a forward `while` loop, we look ahead at `current.next`. If its value exists in the set, we drop it by updating `current.next`. We only move the `current` cursor forward when a node is confirmed unique and logged.

---

## Java Code

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    // Structural framework definition for LeetCode Node component block
    public static class ListNode {
        int val;
        ListNode next;
        ListNode() {}
        ListNode(int val) { this.val = val; }
        ListNode(int val, ListNode next) { this.val = val; this.next = next; }
    }

    public ListNode removeDuplicates(ListNode head) {
        // Guard Condition: An empty list or single-node list cannot contain duplicates
        if (head == null || head.next == null) {
            return head;
        }

        // Set directory to achieve O(1) constant-time existence checks
        Set<Integer> seenValues = new HashSet<>();
        ListNode current = head;
        
        // Log the absolute first element's value
        seenValues.add(current.val);

        // Core Step: Single forward pass over the unsorted sequence
        while (current != null && current.next != null) {
            if (seenValues.contains(current.next.val)) {
                // Duplicate value matched -> splice the next node out of the chain
                current.next = current.next.next;
            } else {
                // New distinct unique value -> record it and advance the tracking cursor
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

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The pointer sweeps sequentially from the `head` straight to the terminal `null` boundary exactly once. Inside the loop, it performs `HashSet` containment queries and insertions that run in O(1) average time, keeping total execution runtime strictly linear.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary space scales linearly to allocate up to N unique element data keys inside the `HashSet` collection container to track historical items.

---

# Key Takeaways

✅ Core fundamental design template for hash-based collection pruning operations.

✅ **The Value Matrix Selection:** When choosing between algorithms, evaluate the tradeoffs between **Time** and **Space**. For unsorted deduplication, the Brute Force option runs in O(N²) time and O(1) space. The HashSet option trades a minor O(N) memory investment to reduce the time dramatically to an optimized O(N) linear floor.

✅ Directly drives real-world microarchitectures like **un-indexed log file parsing tools**, data packet streaming buffers deduplication layers, browser storage history managers, and transactional database event deduplicators.

✅ Common Architectural Paradigm:

```text
Hash Directory Lookup Caching with Concurrent In-Place Link Splicing
```

### Similar Problems

- Remove Duplicates from Sorted List ([LeetCode 83](https://leetcode.com))
- Remove Duplicates from Sorted List II ([LeetCode 82](https://leetcode.com))
- Contains Duplicate ([LeetCode 217](https://leetcode.com))
- Intersection of Two Linked Lists ([LeetCode 160](https://leetcode.com))
