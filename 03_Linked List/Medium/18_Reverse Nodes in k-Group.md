# Reverse Nodes in k-Group ([LeetCode 25](https://leetcode.com))

## Problem Statement

Given the `head` of a linked list, reverse the nodes of the list `k` at a time and return *the modified list*.

`k` is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of `k` then left-out nodes, in the end, should remain as they are.

You may not alter the values in the list's nodes, only nodes themselves may be changed.

---

## Examples

### Example 1

```text
Input: head =, k = 2
Output: [2,1,4,3,5]
```

### Explanation

```text
- The first group of size 2 is. Reversing it gives.
- The second group of size 2 is. Reversing it gives.
- The remaining node [5] is less than group size k (2), so it is left unchanged.
Stitched together: 2 -> 1 -> 4 -> 3 -> 5.
```

---

### Example 2

```text
Input: head =, k = 3
Output: [3,2,1,4,5]
```

### Explanation

```text
- The first group of size 3 is. Reversing it gives.
- The remaining nodes [4, 5] are less than group size k (3), so they are left unchanged.
Stitched together: 3 -> 2 -> 1 -> 4 -> 5.
```

---

# Intuition

This problem expands on the baseline **Reverse a Linked List** pattern by processing nodes in segmented blocks of size `k`. 

To solve this challenge while preserving relative structural ordering, we must handle two main operations:
1. **Lookahead Capacity Validation:** Before we attempt to reverse any segment, we must first look ahead to check if there are at least `k` valid nodes available. If the remaining count falls short of `k`, the rule states we must leave the remaining nodes completely untouched and terminate.
2. **Segment Splicing and Reversing:** When a valid block is confirmed, we isolate and reverse it in-place using a standard 3-pointer inversion track. After reversing, the old head of the group becomes its new tail. We must safely attach this new tail forward to the result of the *next* block's reversal.

To handle head shifts gracefully and provide a stable tracking base, we introduce a **Dummy Sentinel Node** right before the list head.

---

# Brute Force / Recursive Solution

## Idea

We can solve this problem elegantly using **Recursion**. We look ahead `k` steps to check if a valid block exists. If it doesn't, we return the current `head` early. If it does, we pass the successor node (`current.next`) to a recursive call to process the remaining blocks. We then reverse the current local group of size `k` in-place and link its new tail forward to the result of the recursive subproblem.

---

## Java Code

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode current = head;
        int count = 0;

        // Step 1: Look ahead to verify if there are at least k nodes available
        while (current != null && count < k) {
            current = current.next;
            count++;
        }

        // Base Case: If the remaining nodes are less than k, leave them unchanged
        if (count < k) {
            return head;
        }

        // Step 2: Recursively process subsequent groups first
        ListNode nextGroupHead = reverseKGroup(current, k);

        // Step 3: Reverse the current local group of size k in-place
        ListNode prev = nextGroupHead; // Link the tail of this group straight to the next group head
        ListNode curr = head;
        
        for (int i = 0; i < k; i++) {
            ListNode nextNode = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextNode;
        }

        // 'prev' ends up anchored securely on the new reversed head of the local group
        return prev;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because every node within the list of length N is visited a constant number of times.
- **Space Complexity:** O(N/k) auxiliary space on the execution call stack due to the recursive stack depth mapping out the group partitions.

---

# State Definition (Optimal Iterative Approach)

To optimize our tracking footprint to an absolute constant O(1) floor, we drop recursive call stacks and track loop boundaries in-place using four reference pointers:

```java
ListNode dummy = new ListNode(0, head); // Sentinel dummy node shielding head changes cleanly
ListNode groupPrev = dummy;             // Tracks the node immediately preceding the active group
```

---

# Recurrence / Structural Splicing Condition

```text
While true:
    ListNode kth = getKthNode(groupPrev, k)
    If kth == null -> Break
    
    ListNode groupStart = groupPrev.next
    ListNode nextGroupStart = kth.next
    
    // In-place reverse local chunk of size k
    ListNode prev = nextGroupStart
    ListNode curr = groupStart
    While curr != nextGroupStart:
        ListNode tmp = curr.next
        curr.next = prev
        prev = curr
        curr = tmp
        
    groupPrev.next = kth
    groupPrev = groupStart
```

---

# Optimal Single-Pass In-Place Iterative Solution

## Idea

We anchor our tracking cursor `groupPrev` on our sentinel `dummy` node. In each step of an infinite loop, we look ahead `k` positions. If a `kth` node is not found, we break out. If it is found, we run an in-place pointer inversion on that segment, connecting its tail forward to the next block's entry pointer, and then jump `groupPrev` forward to set up the next iteration.

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

    public ListNode reverseKGroup(ListNode head, int k) {
        // Guard Condition: Trivial states require no modification pass
        if (head == null || k == 1) {
            return head;
        }

        // Initialize a sentinel dummy node to shield head changes cleanly
        ListNode dummy = new ListNode(0, head);
        ListNode groupPrev = dummy; // Tracks the stable tail right before our active group segment

        while (true) {
            // Phase 1: Locate the k-th node ahead to confirm segment validity
            ListNode kth = getKth(groupPrev, k);
            if (kth == null) {
                break; // Not enough nodes left to form a full group, exit loop
            }

            // Record boundary anchors before altering link maps
            ListNode groupStart = groupPrev.next;
            ListNode nextGroupStart = kth.next;

            // Phase 2: Reverse the current local group of size k in-place
            // Initialize 'prev' to nextGroupStart so the tail links forward automatically
            ListNode prev = nextGroupStart;
            ListNode curr = groupStart;
            
            while (curr != nextGroupStart) {
                ListNode nextNode = curr.next;
                curr.next = prev; // Reverse arrow direction
                prev = curr;      // Advance trailing anchor
                curr = nextNode;  // Advance active cursor
            }

            // Phase 3: Connect the preceding stable list forward to the new group head
            groupPrev.next = kth;
            
            // Reposition groupPrev onto the old groupStart (which is now the tail of the reversed pair)
            groupPrev = groupStart;
        }

        return dummy.next; // Return the new root entry point anchored right past dummy
    }

    // Helper method to look ahead exactly k steps from a starting anchor node
    private ListNode getKth(ListNode curr, int k) {
        while (curr != null && k > 0) {
            curr = curr.next;
            k--;
        }
        return curr;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The lookahead step visits each element exactly once, and the pointer inversion step processes each element exactly once. Because the pointers slide continuously forward without repetitive nested re-scans, the total time complexity scales strictly linearly relative to the list length N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. By mutatively adjusting existing node reference pointers directly on the heap using local variable registers, the algorithm completely removes the need for auxiliary arrays, helper collections, or recursive call stack frames.

---

# Key Takeaways

✅ High-tier fundamental pointer manipulation archetype representing the peak of segment inversion templates.

✅ **The Precomputed Tail Target Rule:** Initializing your local inversion pointer `prev = nextGroupStart` instead of `null` is a highly strategic engineering choice. This forces the new tail of the reversed group to link forward to the rest of the list automatically, eliminating the need to write complex tail-reconnection branches afterwards.

✅ Directly drives real-world microarchitectures like **network packet block de-scramblers** (reversing data bytes inside fixed segment windows), file encryption streams, chunk processors, and compiler token encoders.

✅ Common Architectural Paradigm:

```text
Lookahead Interval Bounds Checking with Localized Multi-Pointer Reference Inversions
```

