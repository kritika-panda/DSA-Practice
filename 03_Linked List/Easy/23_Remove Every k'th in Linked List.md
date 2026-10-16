# Remove Every K'th Node in a Linked List ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given a singly linked list, your task is to **remove every k-th node** from the linked list. 

- The elimination index starts counting from 1. 
- If k = 1, all nodes should be removed, resulting in an empty list (`null`).
- If k is greater than the length of the linked list, no nodes should be removed.

Return *the head of the modified linked list*.

---

## Examples

### Example 1

```text
Input: head = 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> 8 -> null, k = 3
Output: 1 -> 2 -> 4 -> 5 -> 7 -> 8 -> null
```

### Explanation

```text
Counting sequentially from 1:
- Node 3 (value 3) is removed.
- Node 6 (value 6) is removed.
The remaining nodes stay in their original relative positions.
```

---

### Example 2

```text
Input: head = 1 -> 2 -> 3 -> 4 -> null, k = 5
Output: 1 -> 2 -> 3 -> 4 -> null
```

### Explanation

```text
Since k = 5 is strictly greater than the list size (4), no nodes are removed.
```

---

### Example 3

```text
Input: head = 1 -> 2 -> null, k = 1
Output: null
```

### Explanation

```text
Every 1st node is removed, which completely drains the list.
```

---

# Intuition

To skip over specific periodic coordinates without mutating data elements, we can scan the list linearly while keeping an active element counter.

Since a deletion in a singly linked list requires editing the `.next` pointer of the node positioned immediately *before* the victim node, we can position a tracking cursor at our current node and check how many steps we have traveled.
- If k = 1, we can short-circuit immediately and return `null`.
- For k > 1, we initialize a `current` tracker pointer at the `head` and an integer counter `count = 1`.
- As we move forward, instead of checking the current cell, we look ahead at `current.next`. Its sequential position index will be `count + 1`.
- If `(count + 1) % k == 0`, the upcoming node lands perfectly on a k-th interval benchmark. We remove it instantly by re-routing the current node's link around it: `current.next = current.next.next`.
- If it does not match, the upcoming node is safe. We simply advance our cursor pointer forward: `current = current.next`.
- Each time we step forward or successfully splice out a node, we increment our position count accordingly.

---

# Brute Force Solution

## Idea

We traverse the linked list to pull all individual node references out into an expandable dynamic array list. We then iterate through this list to filter out any element matching the condition `(i + 1) % k == 0`. Finally, we loop through our clean array list to re-stitch all forward pointer links together from scratch.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public Node deleteK(Node head, int k) {
        if (head == null || k <= 0) return head;
        if (k == 1) return null;

        List<Node> nodes = new ArrayList<>();
        Node current = head;

        // Step 1: Collect all node references into an array
        while (current != null) {
            nodes.add(current);
            current = current.next;
        }

        List<Node> remainingNodes = new ArrayList<>();
        // Step 2: Filter elements based on 1-indexed base values
        for (int i = 0; i < nodes.size(); i++) {
            if ((i + 1) % k != 0) {
                remainingNodes.add(nodes.get(i));
            }
        }

        // Guard Condition: If no elements survived the deletion matrix
        if (remainingNodes.isEmpty()) return null;

        // Step 3: Re-stitch the pointer links sequentially
        for (int i = 0; i < remainingNodes.size() - 1; i++) {
            remainingNodes.get(i).next = remainingNodes.get(i + 1);
        }
        remainingNodes.get(remainingNodes.size() - 1).next = null; // Clean termination

        return remainingNodes.get(0);
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the code makes a few complete linear passes across the collection sizing.
- **Space Complexity:** O(N) auxiliary space allocation penalty due to duplicating node addresses into array matrices on heap memory.

---

# State Definition (Optimal Approach)

To drop the memory cost to a constant floor and handle the updates in-place, we track states using a single pointer along with an incremental scalar integer register:

```java
Node current = head; // Tracking cursor navigating reference hops on the heap
int count = 1;       // 1-indexed state counter tracking positional progress
```

---

# Recurrence / Structural State Condition

```text
If k == 1 -> Return null
While current != null AND current.next != null:
    If (count + 1) % k == 0:
        current.next = current.next.next
        count++ 
    current = current.next
    count++
```

---

# Optimal Single-Pass In-Place Solution

## Idea

We handle the absolute first boundary constraint by short-circuiting out early if k = 1. For all other values, we run a lookahead loop checking `current.next`. If the lookahead index hits our target interval criteria, we skip it by updating `current.next` and adjusting the count. This flattens periodic variables using only O(1) extra space.

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

    public Node deleteK(Node head, int k) {
        // Guard Condition A: Empty lists cannot have elements removed
        if (head == null) {
            return null;
        }
        
        // Guard Condition B: If k = 1, every single node matches, draining the list entirely
        if (k == 1) {
            return null;
        }

        Node current = head;
        int count = 1; // 1-indexed baseline counter matching the head position

        // Core Step: Linear pass lookahead scan
        while (current != null && current.next != null) {
            // Check if the position of the UPCOMING node matches the k-th index criteria
            if ((count + 1) % k == 0) {
                // Splicing match confirmed -> bypass the next node by re-routing link references
                current.next = current.next.next;
                
                // Account for the removed node index slot before stepping forward
                count++; 
            }
            
            // Move cursor pointer forward safely
            current = current.next;
            count++;
        }

        return head; // Return the master head, which remains safe since k > 1
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The pointer sweeps from the `head` straight to the terminal `null` boundary exactly once. Inside the loop, it performs standard arithmetic evaluations and pointer reassignments in true O(1) constant time, bounding the total execution runtime strictly linearly relative to the list length N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By mutatively adjusting object reference pointers directly on the heap, the algorithm completely removes the need for auxiliary arrays, lists, or tracking collections, running inside a completely flat constant space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for periodic stream filtering and in-place interval node erasure.

✅ **The Value of Lookahead Index Tracking:** When deleting nodes iteratively in a singly linked track, checking the node *after* your cursor (`current.next`) avoids the need to maintain complicated lagging `previous` tracking variables. It lets you slice out a node seamlessly without losing your current structural grip on the heap.

✅ Directly drives real-world microarchitectures like **Round-Robin task eviction loops** (e.g., dropping every k-th telemetry packet to manage load throttling), signal de-multiplexing pipelines, cache pruning layers, and memory ring buffer managers.

✅ Common Architectural Paradigm:

```text
Lookahead Interval Arithmetic for In-Place Periodic Stream Splicing
```

### Similar Problems

- Remove Linked List Elements ([LeetCode 203](https://leetcode.com))
- Delete Node in a Linked List ([LeetCode 237](https://leetcode.com))
- Remove Nth Node From End of List ([LeetCode 19](https://leetcode.com))
- Remove Duplicates from Sorted List ([LeetCode 83](https://leetcode.com))
