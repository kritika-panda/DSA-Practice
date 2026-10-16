# Sort List ([LeetCode 148](https://leetcode.com))

## Problem Statement

Given the `head` of a linked list, return *the list sorted in **non-decreasing** order*.

The sorting solution must fulfill the following performance constraints:
- **Time Complexity:** strictly bounded by **O(N log N)**.
- **Auxiliary Space Complexity:** highly optimal at **O(1)** or **O(log N)** for the recursive call stack.

---

## Examples

### Example 1

```text
Input: head = [4,2,1,3]
Output: [1,2,3,4]
```

---

### Example 2

```text
Input: head = [-1,5,3,4,0]
Output: [-1,0,3,4,5]
```

---

### Example 3

```text
Input: head = []
Output: []
```

---

# Intuition

Sorting a linked list in \(O(N \log N)\) time naturally points toward **Merge Sort** or **Quick Sort**. For linked lists, Merge Sort is universally preferred because:
1. Linked lists do not allow random access, making pivot selection in Quick Sort inefficient.
2. In-place merging of two lists requires only pointer updates, avoiding the O(N) extra memory penalty that arrays pay during Merge Sort.

The standard Top-Down recursive Merge Sort operates by:
1. **Splitting the List:** We use a **Fast & Slow Pointer** race (Tortoise and Hare) to locate the exact center of the list. We sever the link between the first and second half to create two independent subproblems.
2. **Conquer:** Recursively break down each half until we reach individual standalone nodes.
3. **Merge (Weaving):** Combine both sorted subproblems into a single non-decreasing sequence by leapfrogging reference pointers.

---

# Brute Force Solution

## Idea

We traverse the linked list to extract all node values into an expandable dynamic array list. We sort this array using a built-in sorting method (e.g., standard Timsort), and then perform a second pass over the linked list to overwrite the original node payloads with the sorted array values. This approach fails to alter structural pointer links directly and violates optimal space invariants.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        List<Integer> values = new ArrayList<>();
        ListNode current = head;

        // Step 1: Collect all data values
        while (current != null) {
            values.add(current.val);
            current = current.next;
        }

        // Step 2: Sort the array collection
        Collections.sort(values);

        // Step 3: Overwrite original node payloads
        current = head;
        for (int val : values) {
            current.val = val;
            current = current.next;
        }

        return head;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** \(O(N \log N)\) driven by the collection sorting phase.
- **Space Complexity:** O(N) auxiliary space overhead to duplicate data onto the array list container on the heap.

---

# State Definition (Optimal Top-Down Approach)

To implement sorting completely by altering node link pointers in-place, we track subproblem state divisions recursively:

```java
ListNode mid = getMid(head);       // Locate the center split node point
ListNode left = sortList(head);    // Recursively sort the left sub-list partition
ListNode right = sortList(mid);    // Recursively sort the right sub-list partition
return merge(left, right);         // Weave both sorted halves into a stable chain
```

---

# Recurrence / Structural State Condition

```text
Base Case:
If head == null OR head.next == null -> Return head

Divide & Conquer Step:
midNode = getMidPoint(head)
nextPart = midNode.next
midNode.next = null  // Sever the list into two parts

leftSorted = sortList(head)
rightSorted = sortList(nextPart)

Return mergeTwoSortedLists(leftSorted, rightSorted)
```

---

# Optimal Top-Down Merge Sort Solution

## Idea

We use a fast and slow pointer lookahead approach to isolate the middle node of our list. To prevent infinite loops, we disconnect the first half from the second half right at the center split point. We recurse deeper until we isolate standalone nodes, and then use an iterative sentinel dummy loop to weave the sorted sub-chains back together cleanly.

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

    public ListNode sortList(ListNode head) {
        // Base Case: A list with 0 or 1 nodes is already sorted implicitly
        if (head == null || head.next == null) {
            return head;
        }

        // Step 1: Locate the center point node of the current list layer
        ListNode mid = getMid(head);
        ListNode nextToMid = mid.next;
        
        // Sever the connection between the first half and second half
        mid.next = null;

        // Step 2: Recursively sort both sub-list sections independently
        ListNode left = sortList(head);
        ListNode right = sortList(nextToMid);

        // Step 3: Merge both sorted subproblems together
        return merge(left, right);
    }

    // High-speed Fast/Slow pointer race engine to locate list center points
    private ListNode getMid(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        
        // Check fast.next and fast.next.next to land slow right on the 
        // predecessor node of even boundaries, making list slicing symmetrical
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }

    // Standard in-place sorting merge engine combining two sorted chains
    private ListNode merge(ListNode list1, ListNode list2) {
        ListNode dummy = new ListNode(0);
        ListNode current = dummy;

        while (list1 != null && list2 != null) {
            if (list1.val <= list2.val) {
                current.next = list1;
                list1 = list1.next;
            } else {
                current.next = list2;
                list2 = list2.next;
            }
            current = current.next;
        }

        // Append the surviving intact sorted segment in a single constant-time assignment
        current.next = (list1 != null) ? list1 : list2;

        return dummy.next;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N log N)
```
*Explanation:* Finding the midpoint takes O(N) operations at each depth layer. The total height depth of the recursive tree splits is bounded strictly by \(\log N\). At each depth layer, the total combined work performed across all merges is bounded linearly by O(N), locking total time complexity securely to \(O(N \log N)\) dimensions.

---

### Space Complexity

```text
Space Complexity: O(log N)
```
*Explanation:* The code performs all structural mutations in-place without generating any new node blocks or using external arrays. Auxiliary memory overhead is driven exclusively by the recursive call activation logs tracking tree depth boundaries, which costs \(O(\log N)\) memory space.

*(Note: If true absolute O(1) constant space is required, you can implement Merge Sort iteratively using a Bottom-Up approach with an explicit block size multiplier step loop).*

---

# Key Takeaways

✅ High-tier fundamental sorting architecture representing the golden standard for list sorting algorithms.

✅ **The Symmetrical Slicing Midpoint Rule:** When splitting a list for Merge Sort, your midpoint finder should use the lookahead check `while (fast.next != null && fast.next.next != null)`. This ensures that in an even list layout (e.g., length 4), `slow` stops on index 1 (value 2). This lets you sever the list into two perfectly equal chunks of size 2, preventing infinite recursive state loops.

✅ Directly drives real-world microarchitectures like **external merge sorting tools** for large dataset parsing systems, database indexing engines, stream priorities managers, and file log classifiers.

✅ Common Architectural Paradigm:

```text
Logarithmic Tree Splitting with In-Place Linear Reference Pointer Weaving
```

### Similar Problems

- Merge Two Sorted Lists ([LeetCode 21](https://leetcode.com))
- Merge k Sorted Lists ([LeetCode 23](https://leetcode.com))
- Insertion Sort List ([LeetCode 147](https://leetcode.com))
- Sort Colors ([LeetCode 75](https://leetcode.com))
