# Sort a Linked List of 0s, 1s, and 2s ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given a singly linked list containing only **0s, 1s, and 2s**, sort the linked list in non-decreasing order (i.e., all 0s followed by all 1s, followed by all 2s) by changing the node links.

**Constraints & Performance Targets:**
- Time Complexity: strictly bounded by **O(N)**.
- Auxiliary Space Complexity: highly optimal at **O(1)**.
- You must alter the pointer **links** directly rather than just swapping the internal data values of the nodes.

---

## Examples

### Example 1

```text
Input: head = 1 -> 2 -> 2 -> 1 -> 0 -> 0 -> 2 -> 0 -> null
Output: 0 -> 0 -> 0 -> 1 -> 1 -> 2 -> 2 -> 2 -> 2 -> null
```

### Explanation

```text
The 0s, 1s, and 2s are grouped together sequentially by restructuring the node links.
```

---

### Example 2

```text
Input: head = 2 -> 1 -> 2 -> 1 -> 1 -> 2 -> 0 -> 1 -> 0 -> null
Output: 0 -> 0 -> 1 -> 1 -> 1 -> 1 -> 2 -> 2 -> 2 -> null
```

---

# Intuition

A simple counting approach would traverse the list, count the absolute frequencies of 0s, 1s, and 2s, and then do a second pass to overwrite the data values inside the nodes. However, interviews often explicitly ban this value-overwriting approach to force you to demonstrate structural pointer manipulation.

To sort the list **in-place inside a single pass while using constant memory**, we can adopt a **Three-Way Partitioning Strategy using Dummy Sentinel Nodes**:
- Create three distinct sub-chains simultaneously as we sweep forward: a `0` list, a `1` list, and a `2` list.
- To avoid handling complex head-reassignment logic and null-pointer exceptions, allocate three separate **Dummy Sentinel Nodes** (`dummy0`, `dummy1`, `dummy2`).
- Attach three cursor tracking pointers (`zero`, `one`, `two`) initialized at their respective dummies.
- As we step onto a node `current`:
  - If `current.data == 0`, route it to the 0-list: `zero.next = current`, then advance `zero = zero.next`.
  - If `current.data == 1`, route it to the 1-list: `one.next = current`, then advance `one = one.next`.
  - If `current.data == 2`, route it to the 2-list: `two.next = current`, then advance `two = two.next`.
- Advance our master traversal pointer: `current = current.next`.
- **The Stitching Step:** Once all nodes are distributed, link the tails of the sub-chains together sequentially. The tail of the 0-list connects to the head of the 1-list (or the 2-list if the 1-list is completely empty). Finally, ensure the tail of the 2-list terminates safely by pointing to `null`.

---

# Brute Force Solution (Value Overwriting)

## Idea

We traverse the linked list to count the individual occurrences of `0`, `1`, and `2`. After compiling the frequency array registers, we perform a secondary lookup pass across the nodes from front to back, sequentially overwriting node values using our cached totals.

---

## Java Code

```java
class Solution {
    public static Node segregate(Node head) {
        if (head == null || head.next == null) {
            return head;
        }

        int count0 = 0, count1 = 0, count2 = 0;
        Node current = head;

        // Step 1: Count occurrences of 0, 1, and 2
        while (current != null) {
            if (current.data == 0) count0++;
            else if (current.data == 1) count1++;
            else if (current.data == 2) count2++;
            current = current.next;
        }

        // Step 2: Overwrite internal node data values
        current = head;
        while (current != null) {
            if (count0 > 0) {
                current.data = 0;
                count0--;
            } else if (count1 > 0) {
                current.data = 1;
                count1--;
            } else if (count2 > 0) {
                current.data = 2;
                count2--;
            }
            current = current.next;
        }

        return head;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm performs two full sequential passes across the data length.
- **Space Complexity:** O(1) auxiliary memory space as it only updates local integer variables.

---

# State Definition (Optimal Link Manipulation Approach)

To isolate and link node reference parameters entirely in-place without copying data elements, we coordinate states using three tracking sub-chains:

```java
Node dummy0 = new Node(-1), zero = dummy0; // Head and tail trackers for the 0 sub-chain
Node dummy1 = new Node(-1), one = dummy1;  // Head and tail trackers for the 1 sub-chain
Node dummy2 = new Node(-1), two = dummy2;  // Head and tail trackers for the 2 sub-chain
```

---

# Recurrence / Structural Stitching Condition

```text
// Distribution
While current != null:
    If current.data == 0 -> zero.next = current, zero = zero.next
    Else If current.data == 1 -> one.next = current, one = one.next
    Else If current.data == 2 -> two.next = current, two = two.next
    current = current.next

// Stitching
zero.next = (dummy1.next != null) ? dummy1.next : dummy2.next
one.next = dummy2.next
two.next = null
```

---

# Optimal Single-Pass In-Place Link Solution

## Idea

We process the list inside a single forward pass, dynamically unweaving the input chain into three distinct buckets. Once completed, we link the three buckets together using defensive checks to handle cases where the `1` bucket or `0` bucket might be completely empty.

---

## Java Code

```java
class Solution {
    // Structural framework definition for GeeksforGeeks Node component block
    private static class Node {
        int data;
        Node next;
        Node(int data) {
            this.data = data;
            this.next = null;
        }
    }

    public static Node segregate(Node head) {
        // Guard Condition: An empty list or single-node list is already sorted by default
        if (head == null || head.next == null) {
            return head;
        }

        // Initialize three sentinel dummy nodes to anchor sub-chains cleanly
        Node dummy0 = new Node(-1);
        Node dummy1 = new Node(-1);
        Node dummy2 = new Node(-1);

        // Cursors to track the moving tails of each sub-chain
        Node zero = dummy0, one = dummy1, two = dummy2;
        Node current = head;

        // Phase 1: Unweave the master list into three distinct buckets based on value
        while (current != null) {
            if (current.data == 0) {
                zero.next = current;
                zero = zero.next;
            } else if (current.data == 1) {
                one.next = current;
                one = one.next;
            } else if (current.data == 2) {
                two.next = current;
                two = two.next;
            }
            current = current.next;
        }

        // Phase 2: Stitch sub-chains sequentially while safely bypassing empty lists
        // The 0-list tail connects to 1-list if present; otherwise skips straight to 2-list
        zero.next = (dummy1.next != null) ? dummy1.next : dummy2.next;
        
        // The 1-list tail connects directly to the 2-list head
        one.next = dummy2.next;
        
        // Clean-Up Phase: Explicitly truncate the terminal tail to prevent circular runtime cycles
        two.next = null;

        // The true root of our newly sorted master list sits right past the 0 sentinel dummy
        return dummy0.next;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The pointer loops slide continuously forward across the list length exactly once. Inside the loop, it performs simple constant-time node reference swaps, bounding total runtime strictly linearly relative to the list size N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. By mutatively rearranging existing reference links directly on the heap using local helper sentinel nodes, the algorithm entirely avoids allocating duplicate nodes or tracking collections, maintaining a completely flat constant space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for in-place multi-category bucket partitioning and chain weaving.

✅ **The Tail Truncation Invariant:** When separating and stitching list chains on-the-fly, you must **ALWAYS** explicitly nullify the next pointer of your final tail node (`two.next = null`). Failing to clear this final reference flag leaves a dangling historical link address, which can create a cyclic infinite loop.

✅ Directly drives real-world microarchitectures like **multi-level priority job schedulers** (sorting tasks instantly across low, medium, and high lanes), de-multiplexing pipelines, networking bucket classifiers, and logging data cleaners.

✅ Common Architectural Paradigm:

```text
Multi-Sentinel Reference Splitting with Conditional Suffix Link Weaving
```

### Similar Problems

- Partition List ([LeetCode 86](https://leetcode.com))
- Odd Even Linked List ([LeetCode 328](https://leetcode.com))
- Dutch National Flag Problem / Sort Colors ([LeetCode 75](https://leetcode.com))
- Split Linked List in Parts ([LeetCode 725](https://leetcode.com))
