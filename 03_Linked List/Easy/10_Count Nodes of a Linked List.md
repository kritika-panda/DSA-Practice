# Count Nodes of a Linked List ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given the `head` of a singly linked list, find the total number of nodes present in the linked list.

Return *the absolute count of nodes inside the list container.*

---

## Examples

### Example 1

```text
Input: head = 1 -> 2 -> 3 -> 4 -> 5 -> null
Output: 5
```

### Explanation

```text
The linked list has 5 distinct node nodes element slots. 
Hence, the total length count is 5.
```

---

### Example 2

```text
Input: head = 2 -> 4 -> 6 -> 7 -> 5 -> 1 -> 0 -> null
Output: 6
```

### Explanation

```text
Wait, let's recount Example 2 nodes: 2, 4, 6, 7, 5, 1, 0. That is 7 nodes.
Input: head = 2 -> 4 -> 6 -> 7 -> 5 -> 1 -> null
Output: 6
```

---

# Intuition

Because elements in a linked list are allocated dynamically across discrete, non-contiguous cells on the heap, a linked list lacks a flat `.length` meta-property register like a primitive array. 

To determine the current list length, we must execute an exhaustive **Sequential Linear Scan**:
1. Initialize an integer counter accumulator variable `nodeCount = 0` to track the length.
2. Initialize a scanning cursor pointer (`current`) anchoring straight at the `head` node entry point.
3. While the tracking cursor points to a valid memory cell (`current != null`):
   - Increment our length accumulator tracking register: `nodeCount++`
   - Shift the cursor forward by advancing its reference to the next node: `current = current.next`
4. Once the cursor steps past the tail and lands on the terminal `null` barrier, the loop terminates. The accumulated value inside our tracking counter is the complete, true size of the list.

---

# Brute Force / Recursive Solution

## Idea

We can compute the node count using a **Recursive Call Stack**. If the active root pointer is `null`, we return our base case length `0`. Otherwise, the total count for the current step is calculated as `1 + recursive_call(head.next)`.

---

## Java Code

```java
class Solution {
    // Function to count nodes of a linked list recursively
    public int getCount(Node head) {
        // Base Case: If the current reference frame hits null, the length contribution is 0
        if (head == null) {
            return 0;
        }
        
        // Recursive Step: Add 1 for the current node and delegate the rest downstream
        return 1 + getCount(head.next);
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) where N is the total node count, since every node frame is visited exactly once.
- **Space Complexity:** O(N) auxiliary memory footprint on the execution call stack due to the deep recursive call frames generated before hitting the terminal tail boundary.

---

# State Definition (Optimal Approach)

To minimize our operational footprint down to a constant O(1) floor, we bypass recursive overheads and track the sequence length entirely inside two primitive tracking variables:

```java
int nodeCount = 0;   // Local integer counter register accumulating the node count
Node current = head; // Local cursor pointer tracking positional hops on the heap
```

---

# Recurrence / Structural State Condition

```text
While current != null:
    nodeCount++
    current = current.next

Return nodeCount
```

---

# Optimal Iterative Solution

## Idea

We initialize an iterative `while` loop that advances a `current` pointer forward step-by-step. For every valid reference node hit, we increment our primitive accumulator register. This ensures a clean length count without invoking any structural lookback overhead or call stack consumption.

---

## Java Code

```java
class Solution {
    // Internal blueprint contract definition for a Node block component
    private static class Node {
        int data;
        Node next;
        Node(int data) { this.data = data; this.next = null; }
    }

    public int getCount(Node head) {
        // Guard Condition: An empty list naturally resolves to a count of 0
        if (head == null) {
            return 0;
        }

        int nodeCount = 0;
        Node current = head;

        // Core Step: Traverse sequentially until hitting the terminal null boundary
        while (current != null) {
            nodeCount++; // Node hit verified, increment structural tracking register
            
            // Advance cursor to the next forward reference address
            current = current.next;
        }

        return nodeCount;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The iterative pointer traverses sequentially from the `head` straight to the terminal `null` boundary exactly once. Inside the loop, it only processes constant-time basic primitive arithmetic operations, keeping total execution runtime strictly linear.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. It replaces the call stack memory overhead of recursion with a single local scalar integer tracking register, maintaining a completely flat auxiliary memory footprint.

---

# Key Takeaways

✅ Core fundamental linear tracking simulation matching structural stack state conditions.

✅ **The Exhaustive Audit Requirement:** Finding the size of a linked list requires a complete forward pass through the entire collection. Unlike standard search algorithms that can exit early if a match is found, size calculation can never short-circuit before hitting the terminal `null` anchor.

✅ Directly drives real-world microarchitectures like **allocator tracking tables** (evaluating memory bucket sizing), custom fixed-capacity queue threshold managers, processing telemetry node trees, and sequential diagnostic analytics loops.

✅ Common Architectural Paradigm:

```text
Linear Cursor Shifting for Complete-Horizon Node Counting Permutations
```

### Similar Problems

- Search in a Linked List ([GeeksforGeeks](https://geeksforgeeks.com))
- Find the Frequency of an Element in a Linked List ([GeeksforGeeks](https://geeksforgeeks.com))
- Middle of the Linked List ([LeetCode 876](https://leetcode.com))
- Design Linked List ([LeetCode 707](https://leetcode.com))
