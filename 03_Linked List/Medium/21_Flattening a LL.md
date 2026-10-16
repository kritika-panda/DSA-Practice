# Flattening a Linked List ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given a linked list where every node has a **next** pointer and a **bottom** (or child) pointer pointing to a separate sub-linked list, your task is to flatten the multi-level structure into a single sorted singly linked list.

- The master linked list is sorted horizontally via `next` pointers.
- Each vertical sub-linked list reaching down from a master node is also sorted via `bottom` pointers.
- The flattened list must contain all the nodes from all layers, sorted in strictly **non-decreasing** order, connected exclusively via their **bottom** pointers. All `next` pointers must be nullified.

---

## Examples

### Example 1

```text
Input: 
5 -> 10 -> 19 -> 28
│    │     │     │
▼    ▼     ▼     ▼
7    20    22    35
│          │     │
▼          ▼     ▼
8          50    40
│                │
▼                ▼
30               45

Output: 5 -> 7 -> 8 -> 10 -> 19 -> 20 -> 22 -> 28 -> 30 -> 35 -> 40 -> 45 -> 50 -> null
```

### Explanation

```text
All nodes from the multi-level grid are un-nested, combined, and sorted 
sequentially from smallest to largest into a single vertical chain.
```

---

# Intuition

Since the vertical sub-lists are already sorted independently, this problem can be beautifully reduced to a **multi-way merge subproblem**, mirroring the combination phase of **Merge Sort**.

Imagine processing the horizontal master nodes from right to left using recursion:
- If we assume the entire suffix chain to the right of our current node has already been completely flattened into a single sorted vertical list, our only remaining task is to merge our current vertical sub-list with that pre-flattened suffix list.
- We can implement a robust two-pointer `merge` engine that takes two vertical lists, compares their front-most elements step-by-step, and weaves them together via their `bottom` pointer fields.
- By unwinding backward through the recursive call stack, the horizontal layers collapse layer by layer into a unified vertical column.

---

# Brute Force Solution

## Idea

We traverse the entire multi-level grid structurally, pulling every node value out into an expandable dynamic array. We sort this array using a built-in sorting utility (like standard Timsort). Finally, we iterate through the sorted values to allocate a brand-new singly linked list chain from scratch. This approach is highly sub-optimal as it completely ignores the pre-sorted properties of the inputs and wastes memory.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

class Solution {
    public Node flatten(Node root) {
        if (head == null) return null;

        List<Integer> values = new ArrayList<>();
        Node horizontal = head;

        // Step 1: Collect all elements across the entire grid
        while (horizontal != null) {
            Node vertical = horizontal;
            while (vertical != null) {
                values.add(vertical.data);
                vertical = vertical.bottom;
            }
            horizontal = horizontal.next;
        }

        // Step 2: Sort the collected values
        Collections.sort(values);

        // Step 3: Rebuild a clean vertical list from scratch
        Node dummy = new Node(-1);
        Node current = dummy;
        for (int val : values) {
            current.bottom = new Node(val);
            current = current.bottom;
        }

        return dummy.bottom;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N log N) where N is the total count of nodes across all layers, driven by the array sorting phase.
- **Space Complexity:** O(N) auxiliary space allocation penalty on the heap to duplicate elements inside the array list container.

---

# State Definition (Optimal Recursive Approach)

To flatten layers in-place by mutatively updating reference pointers, we track subproblem states using a bottom-up recursive convergence framework:

```java
Node flattenedSuffix = flatten(root.next); // Recursively flatten the entire list to our right
return merge(root, flattenedSuffix);       // Merge the current vertical column with the flattened suffix
```

---

# Recurrence / Structural State Condition

```text
Base Case:
If root == null OR root.next == null -> Return root

Recursive Step:
root.next = flatten(root.next) // Flatten suffix segments first
root = mergeTwoSortedLists(root, root.next) // Collapse and weave columns via bottom links
Return root
```

---

# Optimal Multi-Pass Recursive Merge Solution

## Idea

We use a divide-and-conquer strategy by driving deep horizontally to the end of the `next` chain first. As the recursive call stack unwinds backward, we trigger an in-place two-way sorting merge between the current column and the newly returned suffix chain. We force all connections onto the `bottom` path and nullify the horizontal references.

---

## Java Code

```java
class Solution {
    // Internal blueprint definition of a Multi-Level Node component block
    private static class Node {
        int data;
        Node next;
        Node bottom;

        Node(int d) {
            data = d;
            next = null;
            bottom = null;
        }
    }

    public Node flatten(Node root) {
        // Base Case: If the list is empty or reaches the final horizontal node, exit early
        if (root == null || root.next == null) {
            return root;
        }

        // Phase 1: Travel horizontally to the end and flatten the remaining suffix lists first
        root.next = flatten(root.next);

        // Phase 2: Merge the current vertical column list with the pre-flattened suffix list
        root = mergeTwoLists(root, root.next);

        // Return the newly consolidated local root anchor frame
        return root;
    }

    // High-speed two-pointer sorting merge engine operating via bottom references
    private Node merge(Node a, Node b) {
        // Guard cases: If one list is empty, return the other segment instantly
        if (a == null) return b;
        if (b == null) return a;

        Node result;

        // Choose the smaller node value to serve as the local link anchor
        if (a.data <= b.data) {
            result = a;
            // Recursively link subsequent segments to the bottom reference slot
            result.bottom = merge(a.bottom, b);
        } else {
            result = b;
            // Recursively link subsequent segments to the bottom reference slot
            result.bottom = merge(a, b.bottom);
        }

        // Enforce structural cleanup: nullify the horizontal next reference vector completely
        result.next = null;
        
        return result;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* Let N be the total number of elements inside the multi-level grid structure. Every single node reference field is processed a constant number of times during the column merge sequences, guaranteeing a true linear runtime.

---

### Space Complexity

```text
Space Complexity: O(M)
```
*Explanation:* Memory overhead scales to accommodate the runtime execution call stack frames, bounded by the number of horizontal master nodes M. Outside of the call stack, all pointer swaps happen completely in-place, yielding O(1) constant auxiliary space.

---

# Key Takeaways

✅ Advanced 2D matrix transformation utilizing bottom-up list convergence patterns.

✅ **The Next-Nullification Rule:** When collapsing multi-level indices into a single vertical sequence, you must explicitly nullify the horizontal references (`result.next = null`) inside the merge engine. Failing to wipe out the old `next` pointer vectors will trap outdated memory allocations, corrupting downstream single-tracker traversals.

✅ Directly models execution workflows like **sparse multi-indexed database record mergers**, indexing inversion engines, stream priority grouping blocks, and sparse spreadsheet column flattener utilities.

✅ Common Architectural Paradigm:

```text
Horizontal Divide-and-Conquer Descents with Synchronized Vertical Two-Way Pointer Weavings
```

### Similar Problems

- Merge Two Sorted Lists ([LeetCode 21](https://leetcode.com))
- Merge k Sorted Lists ([LeetCode 23](https://leetcode.com))
- Flatten a Multilevel Doubly Linked List ([LeetCode 430](https://leetcode.com))
- Sort List ([LeetCode 148](https://leetcode.com))
