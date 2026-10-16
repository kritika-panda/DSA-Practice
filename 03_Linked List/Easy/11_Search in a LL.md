# Search in a Linked List ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given the `head` of a singly linked list and a target integer `key`, find whether `key` is present in the linked list or not.

Return `true` if the `key` is found in any node of the linked list, otherwise return `false`.

---

## Examples

### Example 1

```text
Input: head = 14 -> 21 -> 11 -> 30 -> 10, key = 14
Output: true
```

### Explanation

```text
The key 14 is present right at the absolute first node (the head) of the linked list. 
Hence, the output is true.
```

---

### Example 2

```text
Input: head = 1 -> 2 -> 3, key = 4
Output: false
```

### Explanation

```text
The value 4 does not exist in any node of the linked list. 
The traversal hits null, hence the output is false.
```

---

# Intuition

Because elements in a linked list are scattered across non-contiguous blocks on the heap rather than packed tightly into contiguous blocks like a primitive array, we **cannot use random access index math** (O(1) lookup) or run a Binary Search algorithm.

To find an item, we must perform a **Sequential Linear Scan**:
1. Start a cursor tracking reference pointer (`current`) at the `head` of the list.
2. Compare the `current.data` payload against our target `key`. If they match, we stop and return `true` immediately.
3. If they don't match, we advance our cursor forward by moving its reference to the next node (`current = current.next`).
4. We repeat this cycle until we either locate the target value or hit a `null` barrier, confirming that the entire list has been exhausted without finding a match.

---

# Brute Force / Recursive Solution

## Idea

We can solve this problem using **Recursion** by evaluating a base matching statement at each node step frame. If the current node is `null`, return `false`. If the current node's data matches the key, return `true`. Otherwise, recurse deeper into the remaining suffix of the chain by passing `head.next`.

---

## Java Code

```java
class Solution {
    public boolean searchKey(int n, Node head, int key) {
        // Base Case 1: If we hit a null boundary, the key does not exist
        if (head == null) {
            return false;
        }
        
        // Base Case 2: Target value match confirmed
        if (head.data == key) {
            return true;
        }
        
        // Recursive Step: Delegate the lookup to the next subsequent link segment
        return searchKey(n, head.next, key);
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) where N is the number of elements in the list, as we potentially check every single node.
- **Space Complexity:** O(N) auxiliary call stack space overhead due to the recursive call stack depth if the key is located near the terminal tail or missing entirely.

---

# State Definition (Optimal Absolute Approach)

To minimize our operational footprint to an absolute constant O(1) memory floor, we drop recursive calls and track traversal frames using a single temporary cursor pointer register:

```java
Node current = head; // Tracking cursor navigating reference hops on the heap
```

---

# Recurrence / Structural State Condition

```text
While current != null:
    If current.data == key -> Return true
    current = current.next

Return false
```

---

# Optimal Iterative Solution

## Idea

We initialize an iterative `while` loop that advances a `current` pointer forward step-by-step. If a node matches the key parameter criteria, we return `true` early. If the loop breaks naturally because it ran out of elements, it safe-lands on a terminal `false` fallback marker.

---

## Java Code

```java
class Solution {
    // Internal blueprint definition of a Node component block
    private static class Node {
        int data;
        Node next;
        Node(int data) { this.data = data; this.next = null; }
    }

    public boolean searchKey(int n, Node head, int key) {
        // Guard Condition: An empty list cannot contain any target key entries
        if (head == null) {
            return false;
        }

        Node current = head;

        // Core Step: Traverse sequentially until we hit the terminal null boundary
        while (current != null) {
            if (current.data == key) {
                return true; // Match located, break out and return instantly
            }
            // Move cursor forward to the next reference link address
            current = current.next;
        }

        return false; // Traversal completed without finding a match
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* In the worst-case scenario where the key is absent or sitting right on the terminal tail, the algorithm visits each of the N nodes exactly once. Inside the loop, it only performs constant-time primitive value evaluations, bounding execution time strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. It replaces the call stack memory overhead of recursion with a single local pointer tracking register, maintaining a completely flat auxiliary memory footprint.

---

# Key Takeaways

✅ Core baseline traversal archetype driving all lookup actions in linked linear tracks.

✅ **The Value of Short-Circuiting:** When running linear searches, returning `true` the exact instant a match is located (short-circuiting) cuts down execution latency by avoiding processing any remaining elements further down the collection tail.

✅ Directly models real-world pipelines like **hash map bucket conflict chain resolvers** (chaining links inside bucket slots), processing routing table records, sequential resource audits, and memory tracking systems.

✅ Common Architectural Paradigm:

```text
Iterative Cursor Shifting for Fast-Exit Linear Sequence Searching
```

### Similar Problems

- Design Linked List ([LeetCode 707](https://leetcode.com))
- Middle of the Linked List ([LeetCode 876](https://leetcode.com))
- Linked List Cycle ([LeetCode 141](https://leetcode.com))
- Delete Node in a Linked List ([LeetCode 237](https://leetcode.com))
