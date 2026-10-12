# Merge k Sorted Lists

## Problem Statement

You are given an array of `k` linked-lists `lists`, each linked-list is sorted in ascending order.

*Merge all the linked-lists into one sorted linked-list and return it.*

The overall runtime complexity must be strictly bounded by:

```text
O(N * log(k))
```

*(where N is the total number of nodes across all k linked-lists, and k is the number of linked-lists)*

---

## Examples

### Example 1

**Input**

```text
lists = [[1->4->5], [1->3->4], [2->6]]
```

**Output**

```text
1->1->2->3->4->4->5->6
```

**Explanation**

The linked-lists are:
```text
[
  1->4->5,
  1->3->4,
  2->6
]
```
Merging them into one sorted list yields: `1->1->2->3->4->4->5->6`.

---

### Example 2

**Input**

```text
lists = []
```

**Output**

```text
[]
```

---

### Example 3

**Input**

```text
lists = [[]]
```

**Output**

```text
[]
```

---

## Brute Force Approach

Collect all node values from every single linked list into a standard dynamic array, apply a baseline comparison-based sorting algorithm to sort the values, and then construct a completely brand-new linked list out of the sorted elements.

### Steps

1. Create a `List<Integer>` to hold all numeric values.
2. Traverse through each head pointer in `lists` and step through all its nodes, copying every `node.val` into the list.
3. Sort the list globally using `Collections.sort()`.
4. Create a dummy head node and iteratively link new `ListNode` entries together to map out the sorted collection.

### Complexity

```text
Time Complexity: O(N * log(N)) // where N is the total number of nodes
Space Complexity: O(N) // to allocate the temporary list and entirely new node structures
```

This approach is highly inefficient because it discards the critical fact that **each individual list is already sorted**. It also wastes extensive memory by instantiating new duplicate nodes on the heap. We can optimize this down to **\(O(N \log k)\)** time and constant auxiliary space using a Min-Heap.

---

# Optimal Approach: Min-Heap (PriorityQueue) Lookahead Pointer Tracking

## Key Idea

Because every input list is already sorted in ascending order, the absolute smallest element across all remaining nodes must always sit at **one of the current head pointers** of the `k` lists.

This property allows us to dynamically extract the smallest available item using a **Min-Heap (PriorityQueue)** capped at a maximum capacity of `k`:
1. **Initialize the Heap:** Create a Min-Heap configured to compare nodes by their numeric value (`node.val`). Push the non-null head nodes of all `k` lists onto the heap.
2. **Stitching Loop via Dummy Anchor:** Initialize a `dummy` node to act as the stable anchor root of our merged output list, and a tracking pointer `curr = dummy`.
3. While the heap is not empty:
   - The absolute smallest node among all available options is sitting at the root of the heap. Pop it off via `.poll()`.
   - Link `curr.next` directly to this extracted node, and advance our tracking pointer: `curr = curr.next`.
   - **Lookahead Insertion:** If the popped node has a valid succeeding node (`node.next != null`), push `node.next` onto the heap immediately to keep that list's pointer option path active.
4. Return `dummy.next`.

---

## Visual Understanding

Suppose `lists = [[1->4->5], [1->3->4], [2->6]]`:

- **Step 1 (Populate initial heap window with the 3 head nodes):**
  - Heap contains: `[1 (List 0), 1 (List 1), 2 (List 2)]`.
  - `dummy` pointer set up. `curr` points to `dummy`.
- **Step 2 (Iterative Extraction Loop):**
  - **Cycle 1:** Poll root `1 (List 0)`. Link: `curr.next = 1`. Advance `curr`. List 0 has next element `4`, so offer `4` to heap. Heap becomes: `[1 (List 1), 2 (List 2), 4 (List 0)]`.
  - **Cycle 2:** Poll root `1 (List 1)`. Link: `curr.next = 1`. Advance `curr`. List 1 has next element `3`, so offer `3` to heap. Heap becomes: `[2 (List 2), 3 (List 1), 4 (List 0)]`.
  - **Cycle 3:** Poll root `2 (List 2)`. Link: `curr.next = 2`. Advance `curr`. List 2 has next element `6`, so offer `6` to heap. Heap becomes: `[3 (List 1), 4 (List 0), 6 (List 2)]`.

The lists are woven together dynamically in-place without generating extra duplicate node copies.

---

## Partition Variables

Let:

```java
PriorityQueue<ListNode> minHeap = new PriorityQueue<>((a, b) -> Integer.compare(a.val, b.val));
ListNode dummy = new ListNode(0);
ListNode curr = dummy;
```

---

### Border Elements

The algorithm validates bounds and filters out completely empty arrays or null sub-elements seamlessly during the initial mapping pass:

```java
if (lists == null || lists.length == 0) {
    return null;
}
```

---

## Correct Partition Condition

The pointer stitching and heap lookahead adjustments run until all node references are exhausted completely:

```java
while (!minHeap.isEmpty()) {
    ListNode smallestNode = minHeap.poll();
    curr.next = smallestNode;
    curr = curr.next;
    
    if (smallestNode.next != null) {
        minHeap.offer(smallestNode.next);
    }
}
```

---

## Java Solution

```java
import java.util.PriorityQueue;

class ListNode {
    int val;
    ListNode next;
    ListNode() {}
    ListNode(int val) { this.val = val; }
    ListNode(int val, ListNode next) { this.val = val; this.next = next; }
}

class Solution {

    public ListNode mergeKLists(ListNode[] lists) {
        // Fast-fail: Return null immediately if the input structure is completely empty
        if (lists == null || lists.length == 0) {
            return null;
        }

        // Initialize a Min-Heap configured with a custom lambda comparator matching node values
        PriorityQueue<ListNode> minHeap = new PriorityQueue<>((a, b) -> Integer.compare(a.val, b.val));

        // Step 1: Push the first non-null head node of each list onto the heap
        for (ListNode head : lists) {
            if (head != null) {
                minHeap.offer(head);
            }
        }

        // Initialize a dummy anchor node to build the result list in-place safely
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;

        // Step 2: Extract the smallest node iteratively and update lookahead tracks
        while (!minHeap.isEmpty()) {
            ListNode smallestNode = minHeap.poll();
            
            // Stitch the extracted node onto our result chain
            curr.next = smallestNode;
            curr = curr.next;

            // If the popped node has a valid successor, push it onto the heap 
            // to keep its list pointer active
            if (smallestNode.next != null) {
                minHeap.offer(smallestNode.next);
            }
        }

        // Return the true head of the newly merged sorted linked list
        return dummy.next;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Bounded Heap Merge** | **O(N * log(k))** | **O(k)** | Loops through `N` total nodes. Since the min-heap size never crosses `k` elements, each pull and push operation costs an optimized \(O(\log k)\) time. The heap holds at most `k` references in memory. |

---

## Key Insight

Capping the heap container to size `k` leverages the pre-sorted property of each independent path, reducing global sorting constraints down to a fast lookahead selection sweep over active heads.

```text
Time  : O(N * log(k)) optimal k-way branch scaling
Space : O(k) auxiliary pointer heap tracking allocation
```

---

## Similar Problems

1. Merge Two Sorted Lists (21)
2. Kth Largest Element in an Array (215)
3. Sort a K-Sorted Array (Nearly Sorted Array)
4. Merge Sorted Array (88)
5. Ugly Numbers II (264)
