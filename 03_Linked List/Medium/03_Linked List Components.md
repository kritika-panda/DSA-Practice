# Linked List Components ([LeetCode 817](https://leetcode.com))

## Problem Statement

You are given the `head` of a linked list containing unique integer values. You are also given an integer array `nums` which is a subset of the values present in the linked list.

Return *the number of **connected components** in `nums`*, where a connected component is defined as a contiguous sequence of nodes in the linked list whose values appear entirely within `nums`.

---

## Examples

### Example 1

```text
Input: head = [0,1,2,3], nums = [0,1,3]
Output: 2
```

### Explanation

```text
The linked list looks like: 0 -> 1 -> 2 -> 3 -> null
- The values 0 and 1 are connected in the list and both are present in nums. This forms 1 component: [0, 1].
- The value 2 is not in nums, breaking the contiguous chain.
- The value 3 is in nums. This forms a second component: [3].
Total connected components = 2.
```

---

### Example 2

```text
Input: head = [0,1,2,3,4], nums = [0,3,1,4]
Output: 2
```

### Explanation

```text
The linked list looks like: 0 -> 1 -> 2 -> 3 -> 4 -> null
- Nodes 0 and 1 are connected and present in nums -> Component 1: [0, 1].
- Node 2 is not in nums, breaking the chain.
- Nodes 3 and 4 are connected and present in nums -> Component 2: [3, 4].
Total connected components = 2.
```

---

# Intuition

A naive approach would check each node's value against `nums` using a linear array scan, which would cost an expensive \(O(N \times M)\) runtime. 

To optimize this into a **single linear pass**, we can convert `nums` into a high-speed lookup table:
- We load all values from `nums` into a **HashSet**. This drops our lookups down to O(1) constant time.
- As we traverse the linked list sequentially from left to right, we can count components by identifying where a chain **begins** or **ends**.
- An elegant trick is to count a component the exact moment a contiguous segment **ends**. 
- While scanning a node `current`:
  1. Check if `current.val` is present in our `HashSet`.
  2. If it is present, but its immediate successor `current.next` is either `null` or contains a value *not* present in our `HashSet`, it means we have reached the absolute tail boundary of a connected component block.
  3. We increment our component counter register by 1.
- By looking one step ahead, we count every independent valid chunk exactly once.

---

# Brute Force Solution

## Idea

For every node in the linked list, we manually scan the entire array `nums` to verify if its value exists. We track the connectivity status using a boolean flag register, incrementing our counter whenever a transition from a matched node to an un-matched node occurs.

---

## Java Code

```java
class Solution {
    public int numComponents(ListNode head, int[] nums) {
        if (head == null || nums == null || nums.length == 0) {
            return 0;
        }

        int components = 0;
        boolean inComponent = false;
        ListNode current = head;

        while (current != null) {
            boolean found = false;
            // Linear scan through nums array to find match
            for (int num : nums) {
                if (num == current.val) {
                    found = true;
                    break;
                }
            }

            if (found) {
                // If we were not previously in a component, a new component starts
                if (!inComponent) {
                    inComponent = true;
                    components++;
                }
            } else {
                // Chain breaks
                inComponent = false;
            }
            current = current.next;
        }

        return components;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** \(O(N \times M)\) where N is the number of nodes in the linked list and M is the length of the `nums` array. This triggers performance bottlenecks for large inputs.
- **Space Complexity:** O(1) auxiliary space footprint as it only tracks primitive scalar variables.

---

# State Definition (Optimal Approach)

To drop our runtime tracking down to a linear track, we manage history states using a fast lookup set:

```java
Set<Integer> numSet = new HashSet<>(); // Directory tracking all subset data entries for O(1) lookups
int componentCount = 0;               // Local tracking counter aggregating component segments
```

---

# Recurrence / Structural State Condition

```text
While current != null:
    If numSet.contains(current.val) AND (current.next == null OR NOT numSet.contains(current.next.val)):
        componentCount++
    current = current.next
```

---

# Optimal Single-Pass HashSet Solution

## Idea

We populate a `HashSet` with all values from `nums`. We initialize an iterative `while` loop that advances a `current` cursor forward. By evaluating the presence of `current.val` combined with a lookahead check on `current.next`, we increment our counter the exact moment a contiguous sub-chain terminates, computing results in a single clean pass.

---

## Java Code

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    // Structural framework definition for LeetCode Node component block
    public static class ListNode {
        int val;
        ListNode next;
        ListNode() {}
        ListNode(int val) { this.val = val; }
        ListNode(int val, ListNode next) { this.val = val; this.next = next; }
    }

    public int numComponents(ListNode head, int[] nums) {
        // Guard Condition: Empty datasets contain no components
        if (head == null || nums == null || nums.length == 0) {
            return 0;
        }

        // Load elements into a HashSet to achieve O(1) average time existence lookups
        Set<Integer> numSet = new HashSet<>();
        for (int num : nums) {
            numSet.add(num);
        }

        int componentCount = 0;
        ListNode current = head;

        // Core Step: Single forward pass lookahead scan
        while (current != null) {
            // A component boundary terminates if current node is in the subset AND 
            // the next node is either null OR breaks the subset continuity check
            if (numSet.contains(current.val) && (current.next == null || !numSet.contains(current.next.val))) {
                componentCount++; // Segment finalized, increment tracking register
            }
            
            // Advance cursor to the next forward reference link address
            current = current.next;
        }

        return componentCount;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N + M)
```
*Explanation:* Populating the `HashSet` takes O(M) time where M is the size of `nums`. The cursor pointer sweeps from the `head` to the terminal `null` boundary exactly once, taking O(N) steps. Inside the loop, it performs constant-time set queries, bounding total runtime strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(M)
```
*Explanation:* Auxiliary space scales linearly to allocate up to M subset integer entries inside the `HashSet` collection container to achieve high-speed existence checks.

---

# Key Takeaways

✅ Core fundamental paradigm for segment clustering and in-place interval tracking.

✅ **The Tail End Boundary Counting Trick:** When counting subsets or intervals across a linear track, counting at the **end** of a segment (`current.next == null || !set.contains(next)`) removes the need to track extra boolean state toggle registers or lagging variables, simplifying loop branches.

✅ Directly drives real-world microarchitectures like **graph component analyzers**, text editor grouping systems (e.g., highlighting chunks of contiguous matched spelling tokens), memory page de-fragmenters, and stream packet grouping controllers.

✅ Common Architectural Paradigm:

```text
Hash Directory Mapping with Lookahead Boundary Checking for Stream Segmentations
```

### Similar Problems

- Linked List Cycle ([LeetCode 141](https://leetcode.com))
- Remove Linked List Elements ([LeetCode 203](https://leetcode.com))
- Number of Islands ([LeetCode 200](https://leetcode.com))
- Longest Consecutive Sequence ([LeetCode 128](https://leetcode.com))
