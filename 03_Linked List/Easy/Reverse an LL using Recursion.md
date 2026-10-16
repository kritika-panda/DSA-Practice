# Reverse a Linked List ([LeetCode 206](https://leetcode.com))

## Problem Statement

Given the `head` of a singly linked list, reverse the list, and return *the reversed list*.

---

## Examples

### Example 1

```text
Input: head = [1,2,3,4,5]
Output: [5,4,3,2,1]
```

---

### Example 2

```text
Input: head = [1,2]
Output: [2,1]
```

---

### Example 3

```text
Input: head = []
Output: []
```

---

# Intuition

To reverse a singly linked list in a single pass without copying data elements onto an external array, we must manipulate the node reference arrows directly on the heap. 

In a standard linked list, each node points to its successor (`A -> B -> C`). Reversing the list means flipping each pointer arrow to point backwards to its predecessor instead (`A <- B <- C`).

We can achieve this dynamically by maintaining three sliding reference pointers concurrently as we traverse forward:
1. **`prev`**: Tracks the node immediately behind our current position. This acts as the destination anchor for our reversed link arrow. It initializes to `null` because the old head node must become the new tail boundary.
2. **`curr`**: Tracks our active node position. This is the node whose pointer arrow we are currently flipping.
3. **`next`**: Tracks the node immediately ahead of us. Because flipping `curr.next` to point backward severs our link to the rest of the list, we must explicitly cache `curr.next` inside this variable *before* making the switch to avoid getting stranded on the heap.

---

# Brute Force Solution

## Idea

We allocate an external stack to hold all node data values. We traverse sequentially from the `head` and push each value onto our stack. Once the collection is compiled, we iterate through the list a second time, popping values from our stack to overwrite the original node payloads. While this functions cleanly, it runs in multi-pass times and wastes extra memory on the heap.

---

## Java Code

```java
import java.util.Stack;

class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null) {
            return null;
        }

        Stack<Integer> valueStack = new Stack<>();
        ListNode current = head;

        // Step 1: Collect all data values onto the stack
        while (current != null) {
            valueStack.push(current.val);
            current = current.next;
        }

        // Step 2: Overwrite the data elements in reverse order
        current = head;
        while (current != null) {
            current.val = valueStack.pop();
            current = current.next;
        }

        return head;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm performs two full sequential passes across the data length.
- **Space Complexity:** O(N) auxiliary space overhead to store data elements inside the `Stack` container on the heap.

---

# State Definition (Optimal Approach)

To drop our runtime footprint down to an absolute constant O(1) memory floor, we bypass external collections and swap structural pointer anchors completely in-place:

```java
ListNode prev = null; // References the preceding node block frame
ListNode curr = head; // References our active positional tracking node frame
```

---

# Recurrence / Structural State Condition

```text
While curr != null:
    ListNode nextNode = curr.next // Cache the lookahead link destination
    curr.next = prev              // Reverse the pointer arrow backward
    prev = curr                   // Move the predecessor frame forward
    curr = nextNode               // Move the active frame forward
```

---

# Optimal Single-Pass In-Place Solution

## Idea

We initialize an iterative `while` loop that handles link transformations on the fly. By caching the next node before modifying references, pointer flips execute safely without any lookahead loss. Once `curr` steps past the tail into `null`, the `prev` pointer lands exactly on the new head node of our reversed list.

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

    public ListNode reverseList(ListNode head) {
        // Guard Condition: An empty list or single-node list requires no reversal
        if (head == null || head.next == null) {
            return head;
        }

        ListNode prev = null; // The old head will eventually point to null as the new tail
        ListNode curr = head;

        // Core Step: Single forward pass over the data sequence
        while (curr != null) {
            ListNode nextNode = curr.next; // 1. Cache the lookahead node reference address
            
            curr.next = prev;              // 2. Flip the pointer arrow to point backward
            
            prev = curr;                   // 3. Move the predecessor anchor forward
            curr = nextNode;               // 4. Move the active cursor tracker forward
        }

        // Once curr hits null, prev is anchored securely on the new reversed head node
        return prev;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The loop sweeps sequentially from the `head` straight to the terminal `null` boundary exactly once. Every node link within the list of length N is mutated in true O(1) constant time, bounding total runtime strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By mutatively adjusting object reference pointers directly on the heap, the algorithm completely removes the need for auxiliary storage arrays, tracking stacks, or deep recursive invocation frames, running inside a completely flat constant space footprint.

---

# Key Takeaways

✅ Core fundamental pointer manipulation archetype used across advanced memory transformations.

✅ **The Sequence Cache Invariant:** Always store `curr.next` inside a temporary lookahead variable (`nextNode`) *before* executing `curr.next = prev`. If you break this order, you permanently break the forward chain link on the heap, causing you to lose your reference address pointer to the remaining suffix of the list.

✅ Directly drives real-world microarchitectures like **network packet sequence inversion processors**, undo/redo state logs, parsing runtime expression engines, and database indexing query optimization passes.

✅ Common Architectural Paradigm:

```text
Three-Pointer Sliding State Displacement for In-Place Reference Inversions
```

### Similar Problems

- Reverse Linked List II ([LeetCode 92](https://leetcode.com))
- Reverse Nodes in k-Group ([LeetCode 25](https://leetcode.com))
- Palindrome Linked List ([LeetCode 234](https://leetcode.com))
- Merge Two Sorted Lists ([LeetCode 21](https://leetcode.com))
