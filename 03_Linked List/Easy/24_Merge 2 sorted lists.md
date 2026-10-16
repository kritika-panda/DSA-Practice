# Merge Two Sorted Lists ([LeetCode 21](https://leetcode.com))

## Problem Statement

You are given the heads of two sorted linked lists `list1` and `list2`.

Merge the two lists into one **sorted** linked list. The list should be made by splicing together the nodes of the first two lists.

Return *the head of the merged linked list*.

---

## Examples

### Example 1

```text
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
```

---

### Example 2

```text
Input: list1 = [], list2 = []
Output: []
```

---

### Example 3

```text
Input: list1 = [], list2 = [0]
Output: [0]
```

---

# Intuition

Since both input linked lists are already **sorted** independently, we can synthesize a unified sorted chain using a **Two-Pointer Linear Sweep** strategy (similar to the merge step in the Merge Sort algorithm).

We can compare the front-most unmerged elements from both lists side-by-step. To avoid complex head-reassignment edge cases (like handling when one list starts out completely empty), we introduce a **Dummy Sentinel Node** to act as a stable anchor root.
1. Place a tracking cursor pointer (`current`) initialized at the dummy node.
2. Compare `list1.val` and `list2.val`. 
3. Whichever node contains the **smaller value** is safely spliced into the next slot of our merged chain:
   - If `list1.val <= list2.val`, we connect `current.next = list1` and advance `list1 = list1.next`.
   - Otherwise, we connect `current.next = list2` and advance `list2 = list2.next`.
4. Shift our master `current` tracking pointer forward exactly one step: `current = current.next`.
5. Repeat this process until one of the lists runs dry. 
6. **The Residual Stitching Step:** If one list is exhausted first, the remaining elements of the other list are already sorted and intact. We can attach the rest of that surviving segment directly to our tail in a single O(1) pointer assignment: `current.next = (list1 != null) ? list1 : list2`.

---

# Brute Force / Recursive Solution

## Idea

We can solve this problem elegantly using **Recursion** by picking the smaller head node at the current step and delegating the remaining merging task to subsequent frames. If either list is `null`, we return the other list as a baseline fallback boundary.

---

## Java Code

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        // Base Case 1: If list1 runs out of elements, append the rest of list2
        if (list1 == null) return list2;
        // Base Case 2: If list2 runs out of elements, append the rest of list1
        if (list2 == null) return list1;

        // Choose the node containing the smaller payload value
        if (list1.val <= list2.val) {
            list1.next = mergeTwoLists(list1.next, list2);
            return list1;
        } else {
            list2.next = mergeTwoLists(list1, list2.next);
            return list2;
        }
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N + M) where N and M are the lengths of `list1` and `list2` respectively, as every single node is processed precisely once.
- **Space Complexity:** O(N + M) auxiliary call stack allocation overhead due to the deep recursive activation records generated while weaving towards the tails.

---

# State Definition (Optimal Iterative Approach)

To optimize our tracking footprint to an absolute constant O(1) floor, we drop recursive call stacks and weave pointer links entirely in-place:

```java
ListNode dummy = new ListNode(0); // Sentinel dummy node shielding head changes cleanly
ListNode current = dummy;          // Local tracking cursor navigating reference hops
```

---

# Recurrence / Structural State Condition

```text
While list1 != null AND list2 != null:
    If list1.val <= list2.val:
        current.next = list1
        list1 = list1.next
    Else:
        current.next = list2
        list2 = list2.next
    current = current.next

current.next = (list1 != null) ? list1 : list2
```

---

# Optimal Single-Pass In-Place Iterative Solution

## Idea

We anchor a `current` tracker cursor on our sentinel `dummy` node. By evaluating values step-by-step inside a simple iterative loop, we splice the smaller node onto the tail of our expanding track. Once the loop breaks because one side runs out of data, a final single assignment links the remaining sub-chain instantly.

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

    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        // Initialize a sentinel dummy node to anchor the merged list head safely
        ListNode dummy = new ListNode(0);
        ListNode current = dummy; // Cursor tracking the tail of our newly stitched chain

        // Core Step: Leapfrog pointers over each other based on value priority bounds
        while (list1 != null && list2 != null) {
            if (list1.val <= list2.val) {
                current.next = list1; // Append list1 node to merged tail
                list1 = list1.next;   // Advance list1 tracking pointer
            } else {
                current.next = list2; // Append list2 node to merged tail
                list2 = list2.next;   // Advance list2 tracking pointer
            }
            // Advance the master tracking cursor forward precisely one step
            current = current.next;
        }

        // Residual Stitching: Link the entire surviving sorted segment in a single O(1) step
        current.next = (list1 != null) ? list1 : list2;

        return dummy.next; // Return the head of the sorted list anchored right past dummy
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N + M)
```
*Explanation:* The iterative pointer loops step forward until one input list finishes, taking at most N + M steps total. Inside the loop, it executes simple constant-time node reference swaps, bounding total runtime strictly linearly relative to the combined data lengths.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By mutatively adjusting existing object references directly on the heap, the algorithm completely avoids any new node allocations, helper arrays, or recursive call stack space.

---

# Key Takeaways

✅ Core baseline structural manipulation archetype for linear reference weaving.

✅ **The Power of Residual Splicing:** Unlike primitive array merging steps where a loop is required to copy over leftover items when one array runs dry, linked lists allow you to append an arbitrarily long remaining sub-chain in a single, constant-time O(1) pointer connection step (`current.next = ...`).

✅ Directly drives real-world microarchitectures like **Merge Sort sorting pipelines on external storage systems**, processing sorted transaction feeds, database index merges, and stream aggregation blocks.

✅ Common Architectural Paradigm:

```text
Sentinel-Shielded Pointer Weaving with Constant-Time Residual Splicing
```

### Similar Problems

- Merge k Sorted Lists ([LeetCode 23](https://leetcode.com))
- Sort List ([LeetCode 148](https://leetcode.com))
- Merge Sorted Array ([LeetCode 88](https://leetcode.com))
- Partition List ([LeetCode 86](https://leetcode.com))
