# Split Linked List in Parts ([LeetCode 725](https://leetcode.com))

## Problem Statement

Given the `head` of a singly linked list and an integer `k`, split the linked list into `k` consecutive parts.

The length of each part should be as equal as possible: no two parts should have a size differing by more than one. This may lead to some parts being `null`.

The parts should be in the order of occurrence in the input list, and parts occurring earlier should always have a size greater than or equal to parts occurring later.

Return *an array of the `k` parts*.

---

## Examples

### Example 1

```text
Input: head =, k = 5
Output: [[1],[2],[3],[],[]]
```

### Explanation

```text
The initial head splits into 5 segments. 
Since the list length (3) is less than k (5), the first 3 parts get 1 node each, and the remaining 2 parts are empty (null).
```

---

### Example 2

```text
Input: head =, k = 3
Output: [[1,2,3,4],[5,6,7],[8,9,10]]
```

### Explanation

```text
The total count of nodes is 10. We need to split them into 3 segments:
- Base size per part: 10 / 3 = 3 nodes.
- Extra nodes remaining: 10 % 3 = 1 node.
This extra 1 node is allocated to the first part, making the sizes: 4, 3, and 3.
```

---

# Intuition

To split a linked list into `k` balanced parts, we must first determine its total structural size. Once we have the total length `N`, we can calculate exactly how many nodes should belong to each segment:

1. **Calculate Dimensions:** 
   - **Base size (`baseSize = N / k`)**: Every one of the `k` parts is guaranteed to contain at least this many nodes.
   - **Remainder (`extraNodes = N % k`)**: These are the leftover nodes that cannot be distributed perfectly equally. To satisfy the rule that earlier parts must be larger, we distribute these extra nodes **one by one** to the first `extraNodes` parts.
2. **Segment Isolation & Severing:** 
   - We traverse the list with a tracking cursor. For each bucket in our output array, we count out its required size (`baseSize + (bucketIndex < extraNodes ? 1 : 0)`).
   - Once we reach the final node of the current segment, we must explicitly **sever** the connection to the rest of the list. We cache its successor (`nextPart = tail.next`), cut the thread (`tail.next = null`), and advance our master tracking cursor onto `nextPart` to start the next segment.

---

# Brute Force Solution

## Idea

We traverse the linked list to clone or extract all individual node records into a dynamic `ArrayList`. This makes index math trivial. We then calculate segment bounds using our list size, iterate through the array to generate new sub-chains, and append `null` fragments if the request boundaries overshoot the initial collection size.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public ListNode[] splitListToParts(ListNode head, int k) {
        List<ListNode> nodes = new ArrayList<>();
        ListNode current = head;

        // Step 1: Collect all nodes into a dynamic list
        while (current != null) {
            nodes.add(current);
            current = current.next;
        }

        int n = nodes.size();
        int baseSize = n / k;
        int extraNodes = n % k;

        ListNode[] result = new ListNode[k];
        int nodeIdx = 0;

        // Step 2: Slice the list and break pointers manually
        for (int i = 0; i < k; i++) {
            if (nodeIdx >= n) {
                result[i] = null;
                continue;
            }

            result[i] = nodes.get(nodeIdx);
            int currentPartSize = baseSize + (i < extraNodes ? 1 : 0);
            
            // Advance to the tail of the current segment
            nodeIdx += currentPartSize;
            
            // Sever the link at the tail node
            if (nodeIdx - 1 < n) {
                nodes.get(nodeIdx - 1).next = null;
            }
        }

        return result;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N + k) because the algorithm passes through the list length N to store nodes, and then iterates k times to compile segments.
- **Space Complexity:** O(N) auxiliary space overhead to map out all node address reference flags inside the helper array list container on the heap.

---

# State Definition (Optimal Approach)

To drop the memory allocation cost down to a constant floor and split everything in-place, we track state values using scalar math registers:

```java
int totalLength = 0;      // Scalar register accumulating list size metrics
ListNode current = head;  // Active tracking cursor navigating reference hops
```

---

# Recurrence / Structural Slicing Condition

```text
For i from 0 to k - 1:
    result[i] = current
    partSize = baseSize + (i < extraNodes ? 1 : 0)
    
    Traverse (partSize - 1) times to reach segment tail
    If tail != null:
        nextPart = tail.next
        tail.next = null
        current = nextPart
```

---

# Optimal Single-Pass In-Place Splicing Solution

## Idea

We measure the list length using a single quick linear pass. We then use div and mod math to calculate segment bucket constraints. By stepping down the list and severing the trailing `.next` reference vectors on-the-fly, we isolate each sub-list cleanly in-place inside an optimal **O(1) auxiliary space** envelope.

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

    public ListNode[] splitListToParts(ListNode head, int k) {
        ListNode[] result = new ListNode[k];
        
        // Step 1: Compute the absolute total length of the linked list
        int totalLength = 0;
        ListNode current = head;
        while (current != null) {
            totalLength++;
            current = current.next;
        }

        // Step 2: Establish the core slicing dimensions
        int baseSize = totalLength / k;   // Minimum node quota guaranteed per bucket segment
        int extraNodes = totalLength % k; // Count of leftover nodes distributed to earlier segments

        current = head;
        
        // Step 3: Iterate through each of the k target parts to carve out sub-lists
        for (int i = 0; i < k; i++) {
            // Guard Condition: If our cursor runs dry, the remaining parts naturally default to null
            if (current == null) {
                result[i] = null;
                continue;
            }

            // Anchor the beginning of the current part into our result array slot
            result[i] = current;
            
            // Calculate total items assigned to this bucket (incorporating extra padding nodes)
            int currentPartSize = baseSize + (i < extraNodes ? 1 : 0);

            // Traverse sequentially to locate the terminal tail node of this sub-segment
            ListNode tail = current;
            for (int j = 1; j < currentPartSize; j++) {
                tail = tail.next;
            }

            // Capture the lookahead link address for the next segment, then sever the current tail
            current = tail.next;
            tail.next = null;
        }

        return result;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N + k)
```
*Explanation:* Computing the total length takes exactly N steps. The second processing loop walks down the elements step-by-step to chop the chain into segments, visiting each element exactly once. If k > N, the loop executes k iterations to fill remaining array slots with `null`. Total execution runtime scales strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. Excluding the output `ListNode[]` array required to return the final answer, the algorithm performs all transformations entirely in-place by mutatively rearranging existing reference links, preserving a flat constant space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for dynamic chunk partitioning and stream slicing.

✅ **The Early Exit Guard Invariant:** When k > N, the list runs completely empty before all `k` array slots are populated. Including a clear `if (current == null)` short-circuit branch safely populates remaining array offsets with `null` entries, avoiding pointer errors.

✅ Directly drives real-world microarchitectures like **parallel thread pool load balancers** (chopping long linear request queues into balanced segments for execution), stream batching engines, and block file memory aggregators.

✅ Common Architectural Paradigm:

```text
In-Place Reference Thread Truncation via Fractional Remainder Distributions
```

### Similar Problems

- Partition List ([LeetCode 86](https://leetcode.com))
- Rotate List ([LeetCode 61](https://leetcode.com))
- Odd Even Linked List ([LeetCode 328](https://leetcode.com))
- Reorder List ([LeetCode 143](https://leetcode.com))
