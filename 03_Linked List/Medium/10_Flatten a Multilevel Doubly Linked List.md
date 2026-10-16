# Flatten a Multilevel Doubly Linked List ([LeetCode 430](https://leetcode.com))

## Problem Statement

You are given a doubly linked list which contains nodes that have a next pointer, a previous pointer, and an additional **child pointer**. This child pointer may or may point to a separate doubly linked list, also containing these special nodes. These child lists may have one or more children of their own, and so on, producing a **multilevel data structure** as shown in the example below.

Given the `head` of the first level of the list, **flatten the list** so that all the nodes appear in a single-level, doubly linked list. Let `curr` be a node with a child list. The nodes in the child list should appear **after** `curr` and **before** `curr.next` in the flattened list.

Return *the `head` of the flattened list*.

---

## Examples

### Example 1

```text
Input: head = [1,2,3,4,5,6,null,null,null,7,8,9,10,null,null,11,12]
Output: [1,2,3,7,8,11,12,9,10,4,5,6]
```

### Explanation

```text
The multilevel linked list is structured as follows:
 1 <-> 2 <-> 3 <-> 4 <-> 5 <-> 6 -> null
             │
             ▼
             7 <-> 8 <-> 9 <-> 10 -> null
                   │
                   ▼
                   11 <-> 12 -> null

Flattening results in a single, depth-first order chain:
1 <-> 2 <-> 3 <-> 7 <-> 8 <-> 11 <-> 12 <-> 9 <-> 10 <-> 4 <-> 5 <-> 6 -> null
```

---

# Intuition

If we rotate our perspective of this structure by 90 degrees, the multilevel doubly linked list behaves exactly like a **Binary Tree**:
- The `next` pointer acts like a **right child** (horizontal track).
- The `child` pointer acts like a **left child** (vertical descent track).

Flattening the list in the specified structural order matches a standard **Pre-order Tree Traversal (Depth-First Search)**. 

To achieve an optimal **O(1) auxiliary space** solution without paying a memory penalty for recursive call stacks or external array builders, we can solve it completely in-place using an iterative pointer tracking engine:
1. Initialize a `curr` pointer at the `head`.
2. As we advance `curr` forward, we check if it has a `child` pointer.
3. If a `child` exists, we must splice that child list directly between `curr` and `curr.next`:
   - Locate the **tail node** of the child list by crawling all the way to its horizontal end.
   - Connect the child tail's `next` pointer forward to the original `curr.next` (if `curr.next` is not null, its `prev` must point back to the child tail).
   - Connect `curr.next` forward to the `child` head, and link the child head's `prev` back to `curr`.
   - Explicitly sever the original vertical link by setting `curr.child = null`.
4. If no child exists, we simply advance `curr = curr.next` as normal.

---

# Brute Force Solution (Stack-Based DFS)

## Idea

We use an explicit utility `Stack` container to store the horizontal `next` segments whenever we choose to branch down into a vertical `child` track. This maps out a standard DFS structure, but requires linear auxiliary storage memory.

---

## Java Code

```java
import java.util.Stack;

class Solution {
    public Node flatten(Node head) {
        if (head == null) return null;

        Node curr = head;
        Stack<Node> stack = new Stack<>();

        while (curr != null) {
            if (curr.child != null) {
                // If a next node exists, push it onto the stack to process later
                if (curr.next != null) {
                    stack.push(curr.next);
                }
                
                // Connect curr forward to its child
                curr.next = curr.child;
                curr.child.prev = curr;
                curr.child = null; // Sever the child link
            } 
            else if (curr.next == null && !stack.isEmpty()) {
                // Reached the tail of a segment, pop the saved next segment from the stack
                Node nextNode = stack.pop();
                curr.next = nextNode;
                nextNode.prev = curr;
            }
            
            curr = curr.next;
        }
        
        return head;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm visits every node exactly once.
- **Space Complexity:** O(N) worst-case auxiliary stack space allocation if the tree structure is heavily nested or skewed vertically.

---

# State Definition (Optimal Approach)

To drop memory usage down to an absolute constant O(1) floor, we manage our path boundaries in-place using a single local traversal cursor paired with a secondary segment crawler:

```java
Node curr = head; // Master tracking cursor navigating across the flattened chain layout
```

---

# Recurrence / Structural Splicing Condition

```text
While curr != null:
    If curr.child != null:
        Node tail = curr.child
        While tail.next != null -> tail = tail.next
        
        tail.next = curr.next
        If curr.next != null -> curr.next.prev = tail
        
        curr.next = curr.child
        curr.child.prev = curr
        curr.child = null
    curr = curr.next
```

---

# Optimal Single-Pass In-Place Splicing Solution

## Idea

We iterate forward step-by-step. The exact moment we find a child list, we find its tail and splice the entire child chain into our current horizon layer. By keeping our master cursor `curr` moving forward through the child nodes we just spliced in, any deeper nested children are naturally caught and flattened in chronological order.

---

## Java Code

```java
class Solution {
    // Structural framework definition for LeetCode Node component block
    private static class Node {
        public int val;
        public Node prev;
        public Node next;
        public Node child;
    }

    public Node flatten(Node head) {
        if (head == null) {
            return null;
        }

        Node curr = head;

        // Core Step: Single forward pass mutative unweaving
        while (curr != null) {
            // If the current node contains a vertical child branch track
            if (curr.child != null) {
                
                // Step 1: Isolate and locate the absolute tail node of the child sub-chain
                Node childTail = curr.child;
                while (childTail.next != null) {
                    childTail = childTail.next;
                }

                // Step 2: Bridge the child sub-chain's tail to the original parent's next segment
                childTail.next = curr.next;
                if (curr.next != null) {
                    curr.next.prev = childTail; // Connect backward link safely
                }

                // Step 3: Redirect parent's next pointer to step directly into the child head entry
                curr.next = curr.child;
                curr.child.prev = curr;

                // Step 4: Explicitly sever the vertical pointer to preserve strict 1D topology invariants
                curr.child = null;
            }

            // Move the master cursor forward (natively advances straight into the newly injected child segment)
            curr = curr.next;
        }

        return head; // Return the root head, which remains intact
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* Although the nested `while` loop crawling to find `childTail` looks like it could trigger O(N²) quadratic limits, each node link in the multilevel structure is traversed a maximum of two times (once by the tail finder crawler, and once by the master `curr` pointer). This guarantees a strict linear runtime.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By mutatively rearranging object reference links directly on the heap using local variable registers, the algorithm completely removes the need for auxiliary arrays, runtime stacks, or recursion, running inside a flat, constant space footprint.

---

# Key Takeaways

✅ High-tier fundamental data structure manipulation pattern combining tree traversals with linked list splicing.

✅ **The Lazy Flattening Rule:** When we splice a child list into the horizontal track, we do **NOT** advance `curr` past the child segment; instead, `curr` moves exactly one step forward into the child head. This structural choice ensures that if the child list contains children of its own further down, the master loop captures and flattens them natively, handling deeply nested multilevel topologies flawlessly.

✅ Natively forms the layout engine behind production systems like **JSON/XML tree flattening protocol filters**, file system hierarchical directory flattener modules, nested text block processors, and document document layout models.

✅ Common Architectural Paradigm:

```text
In-Place Reference Weaving for Multi-Dimensional Graph Compressions
```

### Similar Problems

- Flatten Binary Tree to Linked List ([LeetCode 114](https://leetcode.com))
- Populating Next Right Pointers in Each Node II ([LeetCode 117](https://leetcode.com))
- Copy List with Random Pointer ([LeetCode 138](https://leetcode.com))
- Reverse Nodes in k-Group ([LeetCode 25](https://leetcode.com))
