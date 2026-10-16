# Rotate List ([LeetCode 61](https://leetcode.com))

## Problem Statement

Given the `head` of a linked list, rotate the list to the right by `k` places.

---

## Examples

### Example 1

```text
Input: head =, k = 2
Output: [4,5,1,2,3]
```

### Explanation

```text
Initial List: 1 -> 2 -> 3 -> 4 -> 5 -> null
- Rotate right by 1: 5 -> 1 -> 2 -> 3 -> 4 -> null
- Rotate right by 2: 4 -> 5 -> 1 -> 2 -> 3 -> null
```

---

### Example 2

```text
Input: head =, k = 4
Output: [2,0,1]
```

### Explanation

```text
Initial List: 0 -> 1 -> 2 -> null
- Rotate right by 1: 2 -> 0 -> 1 -> null
- Rotate right by 2: 1 -> 2 -> 0 -> null
- Rotate right by 3: 0 -> 1 -> 2 -> null (back to original)
- Rotate right by 4: 2 -> 0 -> 1 -> null
Notice that rotating by 4 on a list of length 3 is equivalent to rotating by 4 % 3 = 1.
```

---

# Intuition

If we analyze the rotation operation visually, rotating a list to the right by `k` places means taking the last `k` nodes of the list and moving them to the absolute front. 

To accomplish this efficiently without wasteful repetitive passes:
1. **Find the Length (N):** We must first traverse the list to compute its length N and locate the original terminal tail node.
2. **Handle Large k Values:** If k is greater than or equal to N, many rotations are redundant because rotating a list N times brings it back to its original order. We can use modulo math to find the effective number of shifts needed: `k = k % N`. If `k == 0`, no rotation is needed, and we can exit early.
3. **The Cyclic Ring Trick:** Instead of immediately searching for cut points, we can connect the original tail node's `next` pointer straight back to the `head` node. This temporarily turns our linear list into a closed **Circular Linked List**.
4. **Locate the Cut Point:** The new tail node of our rotated list will be positioned exactly `N - k` steps from the original head. We advance a cursor `N - k` times to land on this new tail node.
5. **Break the Ring:** We cache the next node as our brand new `head` (`newHead = newTail.next`), and then sever the circular loop by setting `newTail.next = null`.

---

# Brute Force Solution

## Idea

A naive solution simulates the rotation explicitly k times. In each single rotation pass, we traverse down to the second-to-last node, extract the final tail node, attach it to the front of the list, and update its successor pointer. This takes an expensive \(O(N \times k)\) time footprint, causing performance timeouts for large values of k.

---

## Java Code

```java
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        // Guard Condition: Handles trivial edge states
        if (head == null || head.next == null || k == 0) {
            return head;
        }

        ListNode current = head;
        int length = 1;
        while (current.next != null) {
            length++;
            current = current.next;
        }

        // Reduce redundant circular loops
        k = k % length;
        
        // Simulating the rotation one by one
        for (int i = 0; i < k; i++) {
            ListNode prev = null;
            ListNode tail = head;
            
            while (tail.next != null) {
                prev = tail;
                tail = tail.next;
            }
            
            // Re-route endpoints
            tail.next = head;
            prev.next = null;
            head = tail;
        }

        return head;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** \(O(N \times (k \pmod N))\) which scales quadratically in the worst-case configuration.
- **Space Complexity:** O(1) constant memory footprint.

---

# State Definition (Optimal Approach)

To optimize our tracking footprint into an efficient single pass with an absolute constant O(1) memory floor, we track state transformations using primitive reference pointers:

```java
ListNode tail = head; // Pointer used to measure size and link the cyclic loop ring
int length = 1;      // Scalar tracker variable accumulating list size metrics
```

---

# Recurrence / Structural Ring Condition

```text
1. Traverse to tail: while tail.next != null -> tail = tail.next, length++
2. Adjust rotation: k = k % length. If k == 0 -> return head
3. Connect ring: tail.next = head
4. Find new tail: steps = length - k
   For i from 1 to steps -> tail = tail.next
5. Break ring: newHead = tail.next, tail.next = null
Return newHead
```

---

# Optimal Circular Ring Solution

## Idea

We measure the list length and connect the final tail node directly back to the head, turning our list into a circular loop. We then advance exactly `length - k` steps from the head to find our new tail location. We capture the new head reference from its successor link, and cleanly break the circular connection to flatten the list back out.

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

    public ListNode rotateRight(ListNode head, int k) {
        // Guard Condition: An empty list, single node list, or 0 shift request needs no rotation
        if (head == null || head.next == null || k == 0) {
            return head;
        }

        // Step 1: Calculate the true length of the list and locate the tail node
        ListNode tail = head;
        int length = 1;
        while (tail.next != null) {
            length++;
            tail = tail.next;
        }

        // Step 2: Use modulo arithmetic to eliminate redundant full-circle cycles
        k = k % length;
        if (k == 0) {
            return head; // The effective shift matches baseline states, exit early
        }

        // Step 3: Link the tail back onto the head to temporarily forge a closed circular ring loop
        tail.next = head;

        // Step 4: Advance exactly (length - k) times to arrive right on the new tail node
        int stepsToNewTail = length - k;
        ListNode newTail = head;
        for (int i = 1; i < stepsToNewTail; i++) {
            newTail = newTail.next;
        }

        // Step 5: Capture the new head reference address and break the circular link safely
        ListNode newHead = newTail.next;
        newTail.next = null;

        return newHead; // Return the new root entry point pointer anchor
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The initial scan to find the list length takes exactly N steps. After the ring is formed, locating the split node takes at most N steps. Because the pointers slide continuously forward without nested lookbacks, total execution operations remain strictly bounded inside linear limits.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. By mutatively adjusting object reference links directly on the heap using local tracking variables, the algorithm completely drops the need for auxiliary matrix tables or stack arrays.

---

# Key Takeaways

✅ Core fundamental paradigm for processing cyclic shifts using dynamic graph reduction techniques.

✅ **The Value of Modulo Reduction:** Always use modulo arithmetic (`k = k % length`) whenever a problem involves circular array rotation or cyclic shifting bounds. This ensures that massive shift values (e.g., k = 10⁹) collapse instantly into trivial constant-time operations.

✅ Directly drives real-world microarchitectures like **operating system ring buffers** (managing cyclic bit displacements), text layout text-wrapping engines, audio track playlist loop shifters, and networking data packet routers.

✅ Common Architectural Paradigm:

```text
Symmetric Ring Closures with Linear Offsets for In-Place Temporal Shifts
```

### Similar Problems

- Rotate Array ([LeetCode 189](https://leetcode.com))
- Split Linked List in Parts ([LeetCode 725](https://leetcode.com))
- Remove Nth Node From End of List ([LeetCode 19](https://leetcode.com))
- Introduction to Circular Linked List
