# Find the Frequency of an Element in a Linked List ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given a singly linked list and a target integer `key`, find the **total frequency of occurrence** of the `key` inside the linked list. 

Return *the absolute count of times the `key` appears across all node element slots.*

---

## Examples

### Example 1

```text
Input: head = 1 -> 2 -> 1 -> 2 -> 1 -> 3 -> 1, key = 1
Output: 4
```

### Explanation

```text
The target integer 1 appears exactly 4 times in the given linked list sequence. 
Hence, the returned count is 4.
```

---

### Example 2

```text
Input: head = 8 -> 4 -> 5, key = 7
Output: 0
```

### Explanation

```text
The value 7 does not exist in any node of the linked list. 
The total frequency count is 0.
```

---

# Intuition

Because elements in a linked list are scattered dynamically on the heap without contiguous spatial boundaries, we cannot use random index offsets to check frequencies. To calculate the frequency of a target value, we must perform an exhaustive **Sequential Linear Scan**:

1. Initialize a counter register variable `frequencyCount = 0` up front.
2. Initialize a sliding cursor tracking reference pointer (`current`) anchoring at the `head`.
3. As we slide the cursor forward step-by-step, we check the data payload:
   - If `current.data == key`, it matches our target filter, so we increment `frequencyCount++`.
   - If it doesn't match, we bypass it.
4. Move the cursor forward by advancing its reference address to the next linked node frame: `current = current.next`.
5. We repeat this processing cycle until the cursor hits the terminal boundary `null`, ensuring the entire collection area is thoroughly audited.

---

# Brute Force / Recursive Solution

## Idea

We can compute the frequency using a **Recursive Call Stack**. If the current node is `null`, return the base case `0`. If the node data matches the key, return `1 + recursive_call(current.next)`. Otherwise, return `0 + recursive_call(current.next)`.

---

## Java Code

```java
class Solution {
    public int count(Node head, int search_for) {
        // Base Case: If we hit the terminal null boundary, the search path ends
        if (head == null) {
            return 0;
        }

        // Check if the current node matches the search criteria
        int matchContribution = (head.data == search_for) ? 1 : 0;

        // Recursive Pass: Aggregate current contribution with downstream results
        return matchContribution + count(head.next, search_for);
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) where N is the total number of nodes, as the algorithm must process every element in the chain exactly once.
- **Space Complexity:** O(N) auxiliary call stack allocation overhead due to the deep recursive stack activation frames generated while sweeping toward the tail.

---

# State Definition (Optimal Approach)

To optimize our tracking footprint to an absolute constant O(1) floor, we drop recursive calls and track state updates entirely inside two scalar primitive fields:

```java
int frequencyCount = 0; // Local integer counter accumulating matching occurrences
Node current = head;    // Tracking cursor navigating reference hops on the heap
```

---

# Recurrence / Structural State Condition

```text
While current != null:
    If current.data == search_for -> frequencyCount++
    current = current.next

Return frequencyCount
```

---

# Optimal Iterative Solution

## Idea

We launch a high-speed iterative `while` loop that advances a `current` pointer forward. If a node satisfies our target match criteria, we immediately increment our counter register. This guarantees a complete element audit while ensuring memory costs remain flat.

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

    public int count(Node head, int search_for) {
        // Guard Condition: An empty list automatically holds a frequency count of 0
        if (head == null) {
            return 0;
        }

        int frequencyCount = 0;
        Node current = head;

        // Core Step: Run a single forward pass over the data sequence
        while (current != null) {
            if (current.data == search_for) {
                frequencyCount++; // Target occurrence located, increment tracking register
            }
            // Move cursor forward to the next reference link address
            current = current.next;
        }

        return frequencyCount;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The code performs exactly one pass from the `head` pointer straight to the terminal `null` boundary. Every node is examined exactly once, with the inner loop executing basic primitive comparisons in true O(1) constant time, bounding total runtime strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. It replaces the call stack memory overhead of recursion with a single local scalar integer tracking register, maintaining a completely flat auxiliary memory footprint.

---

# Key Takeaways

✅ Core fundamental linear aggregation pattern for compiling frequency logs in linked data structures.

✅ **Complete Pass Requirement:** Unlike standard "Search in a Linked List" problems where short-circuiting out early upon the first match speeds up code, counting frequencies requires an exhaustive, complete pass through the entire list to handle potential matching nodes further down the tail.

✅ Directly models software configurations like **cache collision density analytics** (auditing hash map buckets chains length), computing log data frequency distributions, network routing stream frequency counters, and telemetry record filters.

✅ Common Architectural Paradigm:

```text
Linear Cursor Shifting for Complete-horizon Stream Frequency Aggregations
```

### Similar Problems

- Search in a Linked List ([GeeksforGeeks](https://geeksforgeeks.com))
- Count Nodes of Linked List ([LeetCode 707 Variant](https://leetcode.com))
- Remove Linked List Elements ([LeetCode 203](https://leetcode.com))
- Find the Frequency of Elements in Array
