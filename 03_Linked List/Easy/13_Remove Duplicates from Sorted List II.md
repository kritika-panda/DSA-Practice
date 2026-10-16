# Remove Duplicates from Sorted List II ([LeetCode 82](https://leetcode.com))

## Problem Statement

Given the `head` of a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list. Return *the linked list **sorted** as well*.

---

## Examples

### Example 1

```text
Input: head = [1,2,3,3,4,4,5]
Output: [1,2,5]
```

---

### Example 2

```text
Input: head = [1,1,1,2,3]
Output: [2,3]
```

---

# Intuition

Unlike *Remove Duplicates from Sorted List I* where we kept exactly one copy of every value, this problem requires us to **completely erase all elements that have any duplicate history**. 

Because the list is already **sorted**, duplicate values are guaranteed to sit next to each other. However, since the `head` node itself might be a duplicate and need to be completely deleted (as seen in Example 2), a classic design pattern is to introduce a **Dummy Sentinel Node** right before the head.

We can establish a `pointer` anchored at the dummy node:
1. Check if the next two adjacent values are identical (`pointer.next.val == pointer.next.next.val`).
2. If they match, we log this duplicate value and run a sub-loop to continuously skip over **all** nodes matching this value until we find a new distinct value. We connect `pointer.next` directly to that next distinct node.
3. If the next two values do not match, it means the element at `pointer.next` is completely unique. We can safely advance our tracking pointer exactly one step forward: `pointer = pointer.next`.

---

# Brute Force Solution

## Idea

We traverse the linked list to compile a frequency count of all node values inside a `HashMap`. Then, we run a secondary pass across the list, using the map directory lookups to completely skip and un-link any node whose absolute frequency count is strictly greater than 1.

---

## Java Code

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        Map<Integer, Integer> frequencyMap = new HashMap<>();
        ListNode current = head;

        // Step 1: Count occurrences of all values
        while (current != null) {
            frequencyMap.put(current.val, frequencyMap.getOrDefault(current.val, 0) + 1);
            current = current.next;
        }

        ListNode dummy = new ListNode(0);
        dummy.next = head;
        current = dummy;

        // Step 2: Purge any node with a frequency > 1
        while (current.next != null) {
            if (frequencyMap.get(current.next.val) > 1) {
                current.next = current.next.next; // Bypass the duplicate node
            } else {
                current = current.next; // Unique element, move forward
            }
        }

        return dummy.next;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm performs two full sequential passes across the data length.
- **Space Complexity:** O(N) auxiliary space overhead to preserve frequency metrics inside the `HashMap` container.

---

# State Definition (Optimal Approach)

To drop the auxiliary memory cost to a constant floor and utilize the sorted order constraint efficiently, we manage pointer links entirely in-place using a dummy node framework:

```java
ListNode dummy = new ListNode(0, head); // Dummy node to protect head updates cleanly
ListNode prev = dummy;                  // Local cursor pointer tracking verified unique sub-chains
```

---

# Recurrence / Structural State Condition

```text
While prev.next != null AND prev.next.next != null:
    If prev.next.val == prev.next.next.val:
        duplicate_val = prev.next.val
        While prev.next != null AND prev.next.val == duplicate_val:
            prev.next = prev.next.next
    Else:
        prev = prev.next
```

---

# Optimal Single-Pass In-Place Solution

## Idea

We utilize a `dummy` node pointing straight to `head` and anchor our tracking cursor `prev` on it. By checking two steps ahead, we can spot a duplicate run before stepping into it. If a duplicate value matches, we use an inner loop to cut out the entire matching sequence instantly. We only advance `prev` forward when a node is guaranteed to be unique.

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
        // Guard Condition: A list with less than two nodes cannot contain duplicates
        if (head == null || head.next == null) {
            return head;
        }

        // Initialize a sentinel dummy node to shield the head boundary changes cleanly
        ListNode dummy = new ListNode(0, head);
        ListNode prev = dummy; // Tracks the last confirmed unique node in the stable chain

        // Core Step: Single forward pass lookahead scan
        while (prev.next != null && prev.next.next != null) {
            if (prev.next.val == prev.next.next.val) {
                int duplicateVal = prev.next.val;
                
                // Continuous sub-loop to completely skip ALL nodes sharing this value
                while (prev.next != null && prev.next.val == duplicateVal) {
                    prev.next = prev.next.next;
                }
                // Note: We do NOT advance 'prev' here because the new prev.next 
                // must be verified for duplicates in the next iteration step.
            } else {
                // The next node is verified unique, link it safely into the stable chain
                prev = prev.next;
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
*Explanation:* Every single node inside the list of length N is processed at most twice (once by the lookahead step and potentially once by the inner skip loop). The pointer slides continuously forward without lookback overhead, bounding total runtime strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By mutatively adjusting object reference pointers directly on the heap, the algorithm completely drops the need for external storage hash maps or lists, operating inside a flat, constant space footprint.

---

# Key Takeaways

✅ Advanced linear token excision pattern utilizing lookahead boundary validation.

✅ **The Sentinel Dummy Pattern:** Using a dummy sentinel node (`dummy.next = head`) is a critical architectural requirement whenever there is a risk that the absolute first node (`head`) of a linked list might be deleted or structurally rearranged during a mutation pass.

✅ Directly drives real-world microarchitectures like **database transaction log clean-up systems** (purging duplicated conflict frames completely), stream protocol parsing filters, formatting tools, and compiler token sanitizers.

✅ Common Architectural Paradigm:

```text
Multi-Step Lookahead Reference Splicing with Full-Sequence Duplicate Excision
```

### Similar Problems

- Remove Duplicates from Sorted List ([LeetCode 83](https://leetcode.com))
- Remove Duplicates from Unsorted List
- Merge Two Sorted Lists ([LeetCode 21](https://leetcode.com))
- Partition List ([LeetCode 86](https://leetcode.com))
