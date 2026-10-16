# Intersection of Two Linked Lists ([LeetCode 160](https://leetcode.com))

## Problem Statement

Given the heads of two singly linked-lists `headA` and `headB`, return *the node at which the two lists intersect*. If the two linked lists have no intersection at all, return `null`.

For example, the following two linked lists begin to intersect at node `c1`:

```text
A:          a1 → a2 ──┐
                      ↳ c1 → c2 → c3
B:     b1 → b2 → b3 ──┘
```

**Constraints & Performance Targets:**
- The inputs contain **no cycles** within the individual list structures.
- **Do not modify** the structure or values of the original lists.
- Time Complexity: strictly bounded by **O(N + M)**.
- Auxiliary Space Complexity: highly optimal at **O(1)**.

---

## Examples

### Example 1

```text
Input: intersectVal = 8, listA =, listB =, skipA = 2, skipB = 3
Output: Intersected at '8'
```

### Explanation

```text
The intersected node's value is 8. From the head of A, it reads. From the head of B, it reads. 
The intersection point is the shared memory reference of node 8.
```

---

### Example 2

```text
Input: intersectVal = 0, listA =, listB =, skipA = 3, skipB = 2
Output: null
```

### Explanation

```text
The two lists do not intersect, so there is no shared node reference. Return null.
```

---

# Intuition

If both linked lists were of equal length, we could simply place a pointer at each head, advance them forward at the exact same speed, and check when they land on the same node reference (`ptrA == ptrB`).

However, the lists usually have different lengths, causing the pointers to reach the intersection point out of sync. We can fix this using a **Coordinate Alignment Shift**:
- Let N be the length of list A, and M be the length of list B.
- If pointer `ptrA` traverses all of list A (N steps) and then switches to the head of list B, and pointer `ptrB` traverses all of list B (M steps) and then switches to the head of list A, **both pointers will travel exactly N + M total steps!**
- By swapping heads after their first baseline pass, the longer path automatically absorbs the index offset difference. When they enter their second pass, they become perfectly aligned.
- If there is an intersection, they will collide exactly at the starting intersection node. If there is no intersection, they will hit the terminal `null` boundary at the exact same moment (`null == null`), exiting the loop safely.

---

# Brute Force Solution

## Idea

We allocate an external `HashSet` container to hold all memory reference addresses of the nodes in list A. Next, we scan through list B sequentially. For each node in list B, we check if its reference already exists in our set. The very first node that matches is our intersection point.

---

## Java Code

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) return null;

        Set<ListNode> visitedNodes = new HashSet<>();
        ListNode currentA = headA;

        // Step 1: Log all reference node addresses from list A
        while (currentA != null) {
            visitedNodes.add(currentA);
            currentA = currentA.next;
        }

        ListNode currentB = headB;
        // Step 2: Scan list B to check for the first intersecting reference match
        while (currentB != null) {
            if (visitedNodes.contains(currentB)) {
                return currentB; // Intersection located!
            }
            currentB = currentB.next;
        }

        return null; // No intersection found
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N + M) where N and M are the lengths of both lists.
- **Space Complexity:** O(N) auxiliary space memory allocation penalty on the heap to store list A references inside the `HashSet`.

---

# State Definition (Optimal Approach)

To drop memory usage down to an absolute constant O(1) floor, we manage our path lengths using two tracking pointer anchors directly on the heap:

```java
ListNode pA = headA; // Tracking pointer initialized at the root of list A
ListNode pB = headB; // Tracking pointer initialized at the root of list B
```

---

# Recurrence / Structural State Condition

```text
While pA != pB:
    pA = (pA == null) ? headB : pA.next
    pB = (pB == null) ? headA : pB.next

Return pA
```

---

# Optimal Two-Pointer Coordinate Alignment Solution

## Idea

We initialize pointers `pA` and `pB` at their respective heads. Inside a single `while` loop, we advance both pointers forward 1 step per iteration. The moment a pointer hits `null`, we swap it to the head of the opposite list. This handles both matching collisions and non-intersecting fallbacks inside an elegant constant space profile.

---

## Java Code

```java
public class Solution {
    // Structural framework definition for LeetCode Node component block
    public static class ListNode {
        int val;
        ListNode next;
        ListNode(int x) {
            val = x;
            next = null;
        }
    }

    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        // Guard Condition: If either list is empty, an intersection is structurally impossible
        if (headA == null || headB == null) {
            return null;
        }

        ListNode pA = headA;
        ListNode pB = headB;

        // Core Step: Run a synchronized alignment sweep
        while (pA != pB) {
            // If pointer reaches the end, switch it to the opposite list's head
            pA = (pA == null) ? headB : pA.next;
            pB = (pB == null) ? headA : pB.next;
        }

        // Returns either the intersected node reference OR null if both hit the end concurrently
        return pA;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N + M)
```
*Explanation:* In the worst-case scenario where the lists do not intersect, both pointers will complete exactly two passes (one on list A and one on list B) before hitting `null` simultaneously. This takes exactly N + M steps, keeping the runtime complexity strictly linear.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By using pointer routing logic directly on the heap, the algorithm bypasses auxiliary arrays, hash sets, or nested loops entirely, running inside a flat, constant space footprint.

---

# Key Takeaways

✅ Core fundamental design pattern for spatial coordinate alignment in dual linear tracks.

✅ **Reference Equality vs. Value Equality:** When validating an intersection point, always check for reference equality (`pA == pB`) rather than value equality (`pA.val == pB.val`). Multiple distinct nodes across different lists can share identical integer values without sharing the same memory block allocation.

✅ Directly drives real-world microarchitectures like **database join index validators**, resolving shared memory reference links in object graphs, memory optimization page mergers, and distributed tracing logs.

✅ Common Architectural Paradigm:

```text
Bilateral Suffix Alignment via Cross-Chain Reference Commutations
```

### Similar Problems

- Linked List Cycle ([LeetCode 141](https://leetcode.com))
- Linked List Cycle II ([LeetCode 142](https://leetcode.com))
- Remove Nth Node From End of List ([LeetCode 19](https://leetcode.com))
- Minimum Common Value ([LeetCode 2540](https://leetcode.com))
