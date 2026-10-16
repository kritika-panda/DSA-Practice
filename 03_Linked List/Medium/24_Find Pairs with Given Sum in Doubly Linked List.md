# Find Pairs with Given Sum in a Doubly Linked List ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given a **sorted** Doubly Linked List (DLL) of unique positive integers and an integer `target`, find all distinct pairs of nodes whose values sum up to the given `target`.

Return *a list of pairs, where each pair is represented as an array/list of two integers `[val1, val2]` sorted in ascending order of the first element*.

---

## Examples

### Example 1

```text
Input: head = 1 <=> 2 <=> 4 <=> 5 <=> 6 <=> 8 <=> 9, target = 7
Output: [[1, 6], [2, 5]]
```

### Explanation

```text
The distinct pairs that sum up to 7 are:
- 1 + 6 = 7
- 2 + 5 = 7
The results are compiled in ascending order of their first terms.
```

---

### Example 2

```text
Input: head = 1 <=> 5 <=> 6, target = 12
Output: []
```

### Explanation

```text
No two values in the list add up to 12, so an empty collection is returned.
```

---

# Intuition

Since the Doubly Linked List is already **sorted**, we can bypass expensive nested search loops or extra hash tracking structures by implementing a classic **Two-Pointer (Meet-in-the-Middle)** strategy. 

In an array, this requires anchoring pointers at index `0` and index `N-1`. Thanks to the bi-directional architecture of a Doubly Linked List, we can duplicate this exact behavior using node references:
- Place a `left` pointer anchor at the absolute front of the list (`head`).
- Crawl a `right` pointer anchor forward all the way to the absolute end of the list (`tail`).
- While `left` and `right` have not crossed or met (`left != right && left.prev != right`):
  - Compute the local summation: `sum = left.data + right.data`.
  - If `sum == target`, we have found a valid pair! We record `[left.data, right.data]`, then slide **both** pointers inward simultaneously (`left = left.next`, `right = right.prev`).
  - If `sum < target`, our total value is too small. Because the list is sorted, we can only increase the sum by shifting our left bound forward to a larger number: `left = left.next`.
  - If `sum > target`, our total value is too large. We can only decrease the sum by shifting our right bound backward to a smaller number: `right = right.prev`.

---

# Brute Force Solution

## Idea

We use a nested loop structure. The outer loop picks a node `ptr1` starting from the head, and the inner loop scans forward from `ptr1.next` to evaluate every remaining node `ptr2`. If `ptr1.data + ptr2.data == target`, we log the pair. This takes a sub-optimal \(O(N^2)\) time footprint.

---

## Java Code

```java
import java.util.ArrayList;

class Solution {
    public static ArrayList<ArrayList<Integer>> findPairsWithGivenSum(Node head, int target) {
        ArrayList<ArrayList<Integer>> result = new ArrayList<>();
        Node ptr1 = head;

        // Outer loop picks each node one by one
        while (ptr1 != null) {
            Node ptr2 = ptr1.next;
            
            // Inner loop scans the remainder of the list
            while (ptr2 != null) {
                if (ptr1.data + ptr2.data == target) {
                    ArrayList<Integer> pair = new ArrayList<>();
                    pair.add(ptr1.data);
                    pair.add(ptr2.data);
                    result.add(pair);
                }
                ptr2 = ptr2.next;
            }
            ptr1 = ptr1.next;
        }
        return result;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** \(O(N^2)\) due to the nested loops searching every coordinate combination.
- **Space Complexity:** O(1) auxiliary space as it only manages local tracking variables.

---

# State Definition (Optimal Approach)

To calculate values entirely inside a single pass, we track our spatial boundaries using a double-ended reference pair:

```java
Node left = head;  // Pointer anchored at the start of the list (smallest numbers)
Node right = head; // Pointer anchored at the end of the list (largest numbers)
```

---

# Recurrence / Structural Splicing Condition

```text
1. Initialize right: while right.next != null -> right = right.next
2. Loop: while left != right AND left.prev != right:
             int sum = left.data + right.data
             if sum == target -> Record, left = left.next, right = right.prev
             else if sum < target -> left = left.next
             else -> right = right.prev
```

---

# Optimal Two-Pointer Meet-in-the-Middle Solution

## Idea

We scan to the very end of the list to locate the `tail` node. Once both the head and tail are pinned, we slide the two bounds inward toward each other based on whether the running sum falls below or overshoots the target boundary limit, minimizing operations down to a true linear footprint.

---

## Java Code

```java
import java.util.ArrayList;

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

    public static ArrayList<ArrayList<Integer>> findPairsWithGivenSum(Node head, int target) {
        ArrayList<ArrayList<Integer>> result = new ArrayList<>();
        
        // Guard Condition: An empty list or single-node list cannot form a pair
        if (head == null || head.next == null) {
            return result;
        }

        Node left = head;
        Node right = head;

        // Phase 1: Advance the right pointer forward to locate the absolute tail node
        while (right.next != null) {
            right = right.next;
        }

        // Phase 2: Slide pointers inward until they meet or cross over each other
        while (left != right && left.prev != right) {
            int currentSum = left.data + right.data;

            if (currentSum == target) {
                // Pair located -> compile structural match variables
                ArrayList<Integer> pair = new ArrayList<>();
                pair.add(left.data);
                pair.add(right.data);
                result.add(pair);

                // Advance both pointers inward simultaneously
                left = left.next;
                right = right.prev;
            } 
            else if (currentSum < target) {
                // Sum is too small -> shift the left bound forward to find a larger value
                left = left.next;
            } 
            else {
                // Sum is too large -> shift the right bound backward to find a smaller value
                right = right.prev;
            }
        }

        return result;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* Phase 1 takes at most N steps to discover the final tail pointer location. Phase 2 walks the pointers inward toward each other, meaning every element inside the list of length N is processed exactly once total across both boundaries, bounding runtime strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. Excluding the storage space needed to collect and return the matching target arrays (which is an inherent output criteria requirement), the algorithm coordinates comparisons completely in-place using local pointer variables, preserving a flat constant footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for searching sorted structures using bilateral double-ended array tracks.

✅ **The Convergence Termination Rule:** When running a meet-in-the-middle pass on a linked chain, your loop must check both `left != right` (for odd-length lists where they land on the same center node) and `left.prev != right` (for even-length lists where the pointers cross each other past adjacent boundaries). This prevents them from flying past each other into infinite matching loops.

✅ Natively runs within critical system environments like **high-speed key-value composite table indexers**, real-time matching engines, networking packet correlation layers, and financial trade ledger filters.

✅ Common Architectural Paradigm:

```text
Bidirectional Boundary Squeezing on Sorted Nodes for Target Sum Extractions
```

### Similar Problems

- Two Sum ([LeetCode 1](https://leetcode.com))
- Two Sum II - Input Array Is Sorted ([LeetCode 167](https://leetcode.com))
- Container With Most Water ([LeetCode 11](https://leetcode.com))
- 3Sum ([LeetCode 15](https://leetcode.com))
