# Odd Even Linked List ([LeetCode 328](https://leetcode.com))

## Problem Statement

Given the `head` of a singly linked list, group all the nodes with **odd indices** together followed by the nodes with **even indices**, and return *the reordered list*.

The **first** node is considered **odd**, the **second** node is **even**, and so on.

Note that the relative order inside both the even and odd groups should remain as it was in the input.

You must solve the problem in **O(1)** auxiliary space complexity and **O(N)** time complexity.

---

## Examples

### Example 1

```text
Input: head = [1,2,3,4,5]
Output: [1,3,5,2,4]
```

### Explanation

```text
The nodes at odd indices are 1, 3, and 5. The nodes at even indices are 2 and 4.
Grouping them gives: 1 -> 3 -> 5 followed by 2 -> 4.
```

---

### Example 2

```text
Input: head = [2,1,3,5,6,4,7]
Output: [2,3,6,7,1,5,4]
```

---

# Intuition

The problem requires separating nodes based on their **position (index)** rather than their node value. We need to maintain two separate sub-chains simultaneously as we move through the list: an odd sub-chain and an even sub-chain.

We can achieve this in a **single linear pass with constant space** by using multiple pointer anchors:
1. Establish an `odd` cursor starting at the first node (`head`).
2. Establish an `even` cursor starting at the second node (`head.next`).
3. Cache the starting node of the even sub-chain (`evenHead = even`) so we can stitch the two halves together at the end.
4. As we move down the list, we decouple and jump the pointer references over each other:
   - Connect the current odd node to the next odd node: `odd.next = even.next`
   - Advance the `odd` pointer forward: `odd = odd.next`
   - Connect the current even node to the next even node: `even.next = odd.next`
   - Advance the `even` pointer forward: `even = even.next`
5. Once the loop ends, we link the tail of the odd chain directly to the cached head of the even chain: `odd.next = evenHead`.

---

# Brute Force Solution

## Idea

We can allocate two separate dynamic array lists (`oddList` and `evenList`). We loop through the linked list sequentially, alternatingly adding the full node references to their respective collections. Once the elements are split, we loop through the arrays to manually re-link all `.next` pointers, ending by tying the final odd node to the first even node.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public ListNode oddEvenList(ListNode head) {
        if (head == null || head.next == null || head.next.next == null) {
            return head;
        }

        List<ListNode> oddNodes = new ArrayList<>();
        List<ListNode> evenNodes = new ArrayList<>();
        ListNode current = head;
        int index = 1;

        // Step 1: Collect nodes into separate lists based on index position
        while (current != null) {
            if (index % 2 != 0) {
                oddNodes.add(current);
            } else {
                evenNodes.add(current);
            }
            current = current.next;
            index++;
        }

        // Step 2: Re-link odd nodes sequentially
        for (int i = 0; i < oddNodes.size() - 1; i++) {
            oddNodes.get(i).next = oddNodes.get(i + 1);
        }

        // Step 3: Re-link even nodes sequentially
        for (int i = 0; i < evenNodes.size() - 1; i++) {
            evenNodes.get(i).next = evenNodes.get(i + 1);
        }
        evenNodes.get(evenNodes.size() - 1).next = null; // Terminate even tail cleanly

        // Step 4: Stitch the tail of the odd chain to the head of the even chain
        oddNodes.get(oddNodes.size() - 1).next = evenNodes.get(0);

        return oddNodes.get(0);
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the code makes a few complete linear sweeps over the data layout.
- **Space Complexity:** O(N) auxiliary memory space allocation tax to hold node elements inside the array lists.

---

# State Definition (Optimal Approach)

To drop the auxiliary memory cost to a constant floor, we manage the sub-chain transformations in-place using tracking pointer anchors directly on the heap:

```java
ListNode odd = head;             // Cursor tracking the active tail of the odd chain
ListNode even = head.next;       // Cursor tracking the active tail of the even chain
ListNode evenHead = even;        // Cached entry root node reference for the even chain segment
```

---

# Recurrence / Structural State Condition

```text
While even != null AND even.next != null:
    odd.next = even.next
    odd = odd.next
    even.next = odd.next
    even = even.next

odd.next = evenHead
```

---

# Optimal Single-Pass In-Place Solution

## Idea

We process the list linearly, leveraging the `even` pointer to gauge our lookahead boundaries safely. Because `even` is always ahead of `odd`, checking `even != null && even.next != null` ensures that both odd and even indices can leapfrog each other safely. This separates and reconnects both sub-chains inside a clean, single pass.

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

    public ListNode oddEvenList(ListNode head) {
        // Guard Condition: A list with less than three nodes is already partitioned by default
        if (head == null || head.next == null || head.next.next == null) {
            return head;
        }

        ListNode odd = head;         // Initialized at index 1
        ListNode even = head.next;   // Initialized at index 2
        ListNode evenHead = even;    // Cache the head of the even list to stitch later

        // Core Step: Leapfrog pointers over each other on-the-fly
        while (even != null && even.next != null) {
            odd.next = even.next;    // Connect current odd node to next odd node
            odd = odd.next;          // Shift odd cursor forward
            
            even.next = odd.next;    // Connect current even node to next even node
            even = even.next;        // Shift even cursor forward
        }

        // Final Step: Stitch the tail of the odd chain straight to the head of the even chain
        odd.next = evenHead;

        return head; // Return the master head, which remains the root of the odd sub-chain
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The pointer loops slide continuously forward across the list length exactly once. Inside the loop, it performs a fixed number of constant-time pointer assignments, bounding the total execution runtime strictly linearly relative to the list size N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By mutatively adjusting object reference links directly on the heap, the algorithm completely avoids auxiliary array allocations or hash tracking structures, maintaining a completely flat constant space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for in-place list partitioning and linear link weaving.

✅ **The Termination Condition Rule:** The `while (even != null && even.next != null)` loop condition is highly strategic. Because the `even` pointer is always positioned downstream of the `odd` pointer, checking `even`'s status safely handles both odd-length and even-length termination layout footprints without incurring null pointer crashes.

✅ Directly drives real-world microarchitectures like **un-interleaved memory de-multiplexers**, processing separate channel priority streams from an interleaved signal, data logging caches, and protocol token segment layout formatters.

✅ Common Architectural Paradigm:

```text
Bilateral Reference Leapfrogging for In-Place Linear Track Unweaving
```

### Similar Problems

- Partition List ([LeetCode 86](https://leetcode.com))
- Split Linked List in Parts ([LeetCode 725](https://leetcode.com))
- Reverse Linked List ([LeetCode 206](https://leetcode.com))
- Sort List ([LeetCode 148](https://leetcode.com))
