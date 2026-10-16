# Remove Duplicates from a Sorted Doubly Linked List ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given the `head` of a **sorted Doubly Linked List (DLL)**, delete all duplicate nodes such that each element appears only once. Return *the head of the modified sorted doubly linked list*.

---

## Examples

### Example 1

```text
Input: head = 1 <=> 1 <=> 1 <=> 2 <=> 3 <=> 4 <=> 4
Output: 1 <=> 2 <=> 3 <=> 4
```

### Explanation

```text
The nodes containing duplicate values (the extra 1s and the extra 4) are unlinked 
and excised. The remaining unique items form a stable 1 <=> 2 <=> 3 <=> 4 list structure.
```

---

### Example 2

```text
Input: head = 2 <=> 2 <=> 2
Output: 2
```

### Explanation

```text
All elements share identical values, leaving exactly one single node representation after the pass.
```

---

# Intuition

Because the input doubly linked list is already **sorted**, all duplicate values are guaranteed to sit adjacent to each other in a contiguous chunk. 

In a **Doubly Linked List**, each node contains both `next` and `prev` pointers. When unlinking a duplicate node `current.next`, we can skip past it in constant time by modifying the pointer boundaries:
1. Connect `current`'s forward reference directly to the node following the duplicate: `current.next = current.next.next`.
2. If that node exists, redirect its backward reference straight back to `current`: `current.next.prev = current`.

We can scan through the list sequentially from left to right using a single pass:
- Compare the value of our active node (`current`) with the value of its immediate neighbor (`current.next`).
- If `current.data == current.next.data`, we locate a duplicate. We slice out the neighbor node instantly by re-routing the bidirectional links. We do **not** move the cursor forward yet, as the newly bridged node must be evaluated against `current` in the next iteration step (to handle longer contiguous duplicate runs like `1 <=> 1 <=> 1`).
- If the values do not match, the value boundary transitions safely. We move our `current` tracking cursor exactly one step forward.

---

# State Definition

To modify reference connections entirely in-place inside a single pass, we track state transformations using a single local pointer register:

```java
Node current = head; // Traversal cursor navigating the sorted bi-directional chain
```

---

# Recurrence / Structural Splicing Condition

```text
While current != null AND current.next != null:
    If current.data == current.next.data:
        Node duplicateNode = current.next
        current.next = duplicateNode.next
        If duplicateNode.next != null -> duplicateNode.next.prev = current
    Else:
        current = current.next
```

---

# Optimal Single-Pass In-Place Solution

## Idea

We initialize an iterative `while` loop that scans ahead by one node position. When adjacent value conflicts trigger, the forward and backward link paths are bridged directly across the duplicate element to decouple it. We only step the cursor forward when a clean value transition is verified, optimizing space down to O(1).

---

## Java Code

```java
class Solution {
    // Structural framework definition for a Doubly Linked List Node Component
    private static class Node {
        int data;
        Node prev;
        Node next;
        Node(int data) {
            this.data = data;
            this.prev = null;
            this.next = null;
        }
    }

    public static Node removeDuplicates(Node head) {
        // Guard Condition: An empty list or single-node list contains no duplicates
        if (head == null || head.next == null) {
            return head;
        }

        Node current = head;

        // Core Step: Single forward pass lookahead scan
        while (current != null && current.next != null) {
            if (current.data == current.next.data) {
                // Duplicate node discovered -> cache the target reference address
                Node duplicateNode = current.next;

                // Step 1: Bridge the current node's next pointer forward past the duplicate
                current.next = duplicateNode.next;

                // Step 2: Bridge the trailing node's prev pointer backward straight to current
                if (duplicateNode.next != null) {
                    duplicateNode.next.prev = current;
                }
                
                // Note: We do NOT advance 'current' here because the newly linked 
                // next node must be verified for duplicates in the next iteration step.
            } else {
                // Distinct unique value confirmed -> advance the tracking cursor pointer safely
                current = current.next;
            }
        }

        // Return the modified, de-duplicated list root anchor head
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
*Explanation:* The `current` pointer traverses from the `head` straight to the terminal `null` boundary exactly once. Every node link in the list of length N undergoes a bounded number of constant-time reference updates, keeping the total execution timeline strictly linear.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By mutatively adjusting object links directly on the heap using local variable registers, the algorithm completely removes the need for auxiliary arrays, runtime stacks, or tracking hash sets, running inside a flat, constant space footprint.

---

# Key Takeaways

✅ Core baseline template for in-place sorted deduplication across bi-directional reference paths.

✅ **The Conditional Iteration Invariant:** Just like singly linked tracks, you must **ONLY** advance your tracking cursor (`current = current.next`) inside the `else` block (when no deletion occurs). Advancing the pointer unconditionally breaks lookup logic, causing the loop to leap past long identical duplicate sequences (e.g., leaving a duplicate trailing in `1 <=> 1 <=> 1`).

✅ Directly drives real-world architectures like **database index compression handlers** (purging repetitive contiguous keys from sorted storage engines in-place), real-time sensor logging streams deduplication, and transaction ledger deduplicators.

✅ Common Architectural Paradigm:

```text
Bilateral Lookahead Link Splicing for In-Place Contiguous Block Deduplications
```

### Similar Problems

- Remove Duplicates from Sorted List ([LeetCode 83](https://leetcode.com))
- Delete All Occurrences of a Given Key in a Doubly Linked List ([GeeksforGeeks](https://geeksforgeeks.com))
- Remove Duplicates from Unsorted List
- Remove Linked List Elements ([LeetCode 203](https://leetcode.com))
