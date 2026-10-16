# Swap Nodes in Pairs ([LeetCode 24](https://leetcode.com))

## Problem Statement

Given a singly linked list, swap every two adjacent nodes and return *its head*. You must solve the problem without modifying the values in the list's nodes (i.e., only nodes themselves may be changed).

---

## Examples

### Example 1

```text
Input: head = [1,2,3,4]
Output: [2,1,4,3]
```

### Explanation

```text
Initial List: 1 -> 2 -> 3 -> 4 -> null
- Swap pair (1, 2) -> 2 -> 1
- Swap pair (3, 4) -> 4 -> 3
Stitched together: 2 -> 1 -> 4 -> 3 -> null.
```

---

### Example 2

```text
Input: head = []
Output: []
```

---

### Example 3

```text
Input: head = [1]
Output: [1]
```

---

# Intuition

This problem can be effectively modeled as a sequential **Pairwise Inversion** task. We want to take groups of two adjacent nodes and flip their forward pointing vectors, then securely connect the tail of the newly flipped pair to the head of the subsequent group.

Since the initial `head` node itself will be swapped and shifted to the second position (changing the root entry point of the list), we can introduce a **Dummy Sentinel Node** right before the head to act as a stable layout anchor.
1. Place a tracking cursor pointer (`prev`) initialized at the dummy node.
2. Check if there are at least two nodes available ahead to form a valid pair (`prev.next != null && prev.next.next != null`).
3. If a pair exists, let's label them `first` and `second`:
   - `first = prev.next`
   - `second = prev.next.next`
4. Re-route the arrows to invert their structural alignment:
   - Connect `first` to the remainder of the list: `first.next = second.next`
   - Flip `second` to point backward to `first`: `second.next = first`
   - Connect the stable predecessor `prev` forward to `second`: `prev.next = second`
5. Shift our master `prev` tracking anchor forward exactly two steps onto `first` (which is now the tail of the flipped pair) and repeat.

---

# Brute Force / Recursive Solution

## Idea

We can solve this problem recursively by dividing the list into a current pair and the remaining suffix subproblem. We extract the first two nodes, pass the rest of the list (`second.next`) to a recursive call, link the current pair backwards, and return `second` as the new root of the local frame.

---

## Java Code

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        // Base Case: If the list is empty or has only one node, no swap can occur
        if (head == null || head.next == null) {
            return head;
        }

        // Nodes to be swapped
        ListNode first = head;
        ListNode second = head.next;

        // Recursive Step: Process the remaining suffix and link it to the first node's tail
        first.next = swapPairs(second.next);
        
        // Flip the pointer link between the current pair
        second.next = first;

        // Return second node since it becomes the new head of this reversed segment
        return second;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because every node in the list of length N is visited exactly once.
- **Space Complexity:** O(N) auxiliary stack memory allocation tax due to the recursive call stack depth generated while sweeping toward the terminal tail boundary.

---

# State Definition (Optimal Approach)

To drop the memory footprint down to a flat O(1) floor, we track the sub-chain transformations in-place using localized tracking pointers:

```java
ListNode dummy = new ListNode(0, head); // Sentinel dummy node shielding the head updates cleanly
ListNode prev = dummy;                  // Local cursor tracking the stable predecessor anchor
```

---

# Recurrence / Structural State Condition

```text
While prev.next != null AND prev.next.next != null:
    first = prev.next
    second = prev.next.next
    
    first.next = second.next
    second.next = first
    prev.next = second
    
    prev = first
```

---

# Optimal Single-Pass In-Place Iterative Solution

## Idea

We anchor our tracking cursor `prev` on our sentinel `dummy` node. By evaluating two nodes ahead inside an iterative loop, we can perform our 3-pointer link rearrangements in-place on the heap without any lookahead loss. This safely rearranges all reference pointers inside a single pass.

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

    public ListNode swapPairs(ListNode head) {
        // Guard Condition: A list with less than two nodes requires no swapping
        if (head == null || head.next == null) {
            return head;
        }

        // Initialize a sentinel dummy node to shield the head boundary changes cleanly
        ListNode dummy = new ListNode(0, head);
        ListNode prev = dummy; // Tracks the tail of our already stabilized swapped list

        // Core Step: Pairwise inversion loop
        while (prev.next != null && prev.next.next != null) {
            // Identify the two nodes requiring inversion
            ListNode first = prev.next;
            ListNode second = prev.next.next;

            // Execute the pointer re-routing operations
            first.next = second.next;  // 1. Link first node to the upcoming segment head
            second.next = first;       // 2. Flip second node backward to point to first
            prev.next = second;        // 3. Connect preceding stable list to second

            // Shift our tracking predecessor cursor forward onto the first node 
            // (first is now the new terminal tail of the active swapped pair)
            prev = first;
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
*Explanation:* The pointer tracking loop slides continuously forward across the list length exactly once. Inside the loop, it performs a fixed number of constant-time reference assignments per pair, bounding total execution runtime strictly linearly relative to the list size N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By mutatively adjusting existing object links directly on the heap using local helper tracking variables, the algorithm completely avoids any new node allocations, helper arrays, or recursive stack frames.

---

# Key Takeaways

✅ Core fundamental paradigm for segment clustering and in-place interval pointer flipping.

✅ **The Pred-Anchor Advancement Rule:** After performing a pairwise swap, your tracking predecessor cursor `prev` must be moved onto the `first` node variable (`prev = first`) rather than `second`. Because the arrows have flipped, the `first` node has shifted structurally to the back position of the pair, making it the correct trailing anchor for the next loop pass.

✅ Directly drives real-world microarchitectures like **endianness memory byte-swapping arrays**, processing de-interleaved token buffers, network packet stream formatters, and graphic parsing engines.

✅ Common Architectural Paradigm:

```text
Sentinel-Shielded 3-Way Pointer Swapping for In-Place Segment Inversions
```

### Similar Problems

- Reverse Linked List ([LeetCode 206](https://leetcode.com))
- Reverse Nodes in k-Group ([LeetCode 25](https://leetcode.com))
- Rotate List ([LeetCode 61](https://leetcode.com))
- Odd Even Linked List ([LeetCode 328](https://leetcode.com))
