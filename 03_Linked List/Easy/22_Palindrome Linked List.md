# Palindrome Linked List ([LeetCode 234](https://leetcode.com))

## Problem Statement

Given the `head` of a singly linked list, return `true` *if it is a **palindrome** or `false` otherwise*.

A palindrome reads the same backward as forward.

**Constraints & Performance Targets:**
- Time Complexity: strictly bounded by **O(N)**.
- Auxiliary Space Complexity: highly optimal at **O(1)**.

---

## Examples

### Example 1

```text
Input: head = [1,2,2,1]
Output: true
```

### Explanation

```text
The list reads 1 -> 2 -> 2 -> 1 from left to right, and also 1 -> 2 -> 2 -> 1 from right to left.
Hence, it is a valid palindrome.
```

---

### Example 2

```text
Input: head = [1,2]
Output: false
```

---

# Intuition

A naive approach would copy all node elements into an array list and use a two-pointer approach to verify if the collection reads symmetrically. While this runs in linear time, it violates the strict O(1) space constraint by shifting data onto heap-allocated collections.

To solve this problem in **O(1) constant auxiliary space**, we can combine three elegant linked list subproblem paradigms sequentially:
1. **Locate the Center Point:** We use the **Two-Pointer (Fast & Slow)** strategy (Tortoise and Hare) to locate the exact middle of the list. By the time the fast runner hits the end of the array, the slow runner rests perfectly on the start of the second half of the sequence.
2. **Reverse the Second Half:** We sever the second half of the list and run an **In-Place Reverse** pass on it. This flips all its pointer directions backward (`A -> B -> C` turns into `A <- B <- C`).
3. **Symmetric Value Comparison:** We match values step-by-step by placing one pointer at the original `head` and a second pointer at the new reversed head of the second half. If all values are identical, the list is a valid palindrome.
4. *(Optional Best Practice)*: Reverse the second half back to its original layout to preserve structural immutability before exiting.

---

# Brute Force Solution

## Idea

We traverse the linked list from the front, collecting each node data payload into an expandable `ArrayList`. Once the tracking collection is fully compiled on the heap, we run a symmetric loop with left and right indices sliding inward to check for any value mismatches.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) {
            return true;
        }

        List<Integer> list = new ArrayList<>();
        ListNode current = head;

        // Step 1: Collect all elements onto a dynamic array list
        while (current != null) {
            list.add(current.val);
            current = current.next;
        }

        // Step 2: Use two pointers to check for symmetric parity
        int left = 0;
        int right = list.size() - 1;
        while (left < right) {
            if (!list.get(left).equals(list.get(right))) {
                return false; // Mismatch located, not a palindrome
            }
            left++;
            right--;
        }

        return true;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the code makes a clean, sequential pass to pull data and another pass to check matches.
- **Space Complexity:** O(N) auxiliary space allocation penalty due to duplicating node variables onto heap list matrices.

---

# State Definition (Optimal Approach)

To drop memory usage down to an absolute constant O(1) floor, we track state transformations using basic reference register pointers directly on the heap:

```java
ListNode slow = head; // Fast/Slow pointer tracking the centerpoint entry line
ListNode fast = head; // Fast lookahead pointer tracking boundary constraints
```

---

# Recurrence / Structural State Condition

```text
1. Find center: While fast.next != null AND fast.next.next != null -> slow = slow.next, fast = fast.next.next
2. Reverse right half: secondHalfHead = reverse(slow.next)
3. Compare: p1 = head, p2 = secondHalfHead
   While p2 != null:
       If p1.val != p2.val -> Return false
       p1 = p1.next, p2 = p2.next
Return true
```

---

# Optimal Three-Phase In-Place Solution

## Idea

We run the fast and slow pointer race to find the midpoint. Next, we slice out the second half and pass its root address to an in-place pointer reversal block. Finally, we run a synchronized matching scan across both halves. This performs the validation completely inside an optimized constant-space footprint.

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

    public boolean isPalindrome(ListNode head) {
        // Guard Condition: An empty list or single-node list is a palindrome by default
        if (head == null || head.next == null) {
            return true;
        }

        // Phase 1: Find the end of the first half using fast/slow pointers
        ListNode firstHalfEnd = findFirstHalfEnd(head);
        
        // Phase 2: Reverse the second half of the list in-place
        ListNode secondHalfStart = reverseList(firstHalfEnd.next);

        // Phase 3: Symmetric parity check between both independent halves
        ListNode p1 = head;
        ListNode p2 = secondHalfStart;
        boolean isPalindromeResult = true;

        while (isPalindromeResult && p2 != null) {
            if (p1.val != p2.val) {
                isPalindromeResult = false; // Parity failure found!
            }
            p1 = p1.next;
            p2 = p2.next;
        }

        // Clean-Up Phase: Restore original list structure before returning (optional best-practice)
        firstHalfEnd.next = reverseList(secondHalfStart);

        return isPalindromeResult;
    }

    // Helper method to isolate the middle node segment bottleneck
    private ListNode findFirstHalfEnd(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        // Check fast.next and fast.next.next to handle both odd and even splits flawlessly
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }

    // High-speed O(1) space in-place reference list reversal engine
    private ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            ListNode nextNode = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextNode;
        }
        return prev;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* Finding the middle takes N/2 steps. Reversing the second half takes another N/2 steps. The symmetric matching check runs for at most N/2 steps. Summing these phases gives a strict total operation footprint that scales completely inside flat linear O(N) limits.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By restructuring object reference links directly on the heap using local variable pointers, the algorithm bypasses auxiliary arrays or hash sets completely, maintaining a flat constant space footprint.

---

# Key Takeaways

✅ High-tier fundamental template combining multiple standalone linked list primitives into a unified solution.

✅ **The Immutability Clean-Up Best Practice:** In engineering systems pipelines, modifying input references directly on the heap can cause side effects for other parts of the application. Re-reversing the second half of the list right before returning (`firstHalfEnd.next = reverseList(secondHalfStart)`) is a highly strategic rule to maintain data immutability.

✅ Directly drives real-world microarchitectures like **asymmetric data sequence verifiers**, streaming encryption checksum validators, sequence parity detectors, and text syntax validation engines.

✅ Common Architectural Paradigm:

```text
Midpoint Structural Segmentation with In-Place Range Link Inversions
```

### Similar Problems

- Reverse Linked List ([LeetCode 206](https://leetcode.com))
- Middle of the Linked List ([LeetCode 876](https://leetcode.com))
- Palindrome Number ([LeetCode 9](https://leetcode.com))
- Reorder List ([LeetCode 143](https://leetcode.com))
