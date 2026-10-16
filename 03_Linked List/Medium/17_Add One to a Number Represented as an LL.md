# Add One to a Number Represented as a Linked List ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

A number is represented as a singly linked list such that the **most significant digit** is at the `head` of the list and the **least significant digit** is at the `tail` of the list. Your task is to add **1** to this number and return *the head of the modified linked list*.

Each node contains a single digit from `0-9`. The final number must not contain any leading zeros, except the number 0 itself.

---

## Examples

### Example 1

```text
Input: head = 4 -> 5 -> 6 -> null
Output: 4 -> 5 -> 7 -> null
```

### Explanation

```text
The list represents the number 456. 
Adding 1 yields 457. The structure is modified to 4 -> 5 -> 7.
```

---

### Example 2

```text
Input: head = 1 -> 9 -> 9 -> null
Output: 2 -> 0 -> 0 -> null
```

### Explanation

```text
The list represents the number 199. 
Adding 1 yields 200. Carries ripple backward, changing the nodes to 2 -> 0 -> 0.
```

---

### Example 3

```text
Input: head = 9 -> 9 -> null
Output: 1 -> 0 -> 0 -> null
```

### Explanation

```text
The list represents the number 99. 
Adding 1 yields 100. This creates a new most significant digit at the front of the list.
```

---

# Intuition

Because the list stores the number with the **most significant digit** at the `head`, the units place (where we must add 1) is buried at the absolute end of the list (`tail`). Mathematical addition carries propagate from right to left, which goes against the forward flow of a singly linked list.

We can solve this problem in two main ways:
1.  **Double Inversion Pattern (Iterative):** Reverse the entire list so the units place sits at the head. Run a single addition pass to propagate the carry forward, then reverse the list a second time to restore its original reading orientation.
2.  **Tracking the Final Non-Nine Digit:** Instead of reversing, we can notice that a carry ripples all the way back to the **rightmost node that is not equal to 9**. If we scan the list and keep a pointer to the last node whose value is less than 9, we can increment that node's value by 1 and instantly set all the subsequent `9` nodes to `0`. If all nodes are `9`, we add a new node `1` at the very front.

---

# Brute Force Solution (Reversal Multi-Pass)

## Idea

We reverse the entire list in-place so that the least significant digit becomes the head. We add 1 to this new head and propagate any addition overflow carries forward. Once the math completes, we reverse the list back into its original arrangement.

---

## Java Code

```java
class Solution {
    public Node addOne(Node head) {
        // Step 1: Reverse the list to bring the units place to the front
        head = reverse(head);

        Node current = head;
        int carry = 1; // Seed with our target addition value of 1

        // Step 2: Propagate addition arithmetic forward
        while (current != null && carry > 0) {
            int total = current.data + carry;
            current.data = total % 10;
            carry = total / 10;
            
            // If we are at the tail and still have a carry, append a new node
            if (current.next == null && carry > 0) {
                current.next = new Node(carry);
                carry = 0;
            }
            current = current.next;
        }

        // Step 3: Re-reverse the list to restore original reading layout
        return reverse(head);
    }

    private Node reverse(Node head) {
        Node prev = null;
        Node curr = head;
        while (curr != null) {
            Node nextNode = curr.next;
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

- **Time Complexity:** O(N) because the algorithm performs three sequential linear passes (reverse, add, re-reverse).
- **Space Complexity:** O(1) auxiliary space as it handles link transformations entirely in-place.

---

# State Definition (Optimal Single-Pass Approach)

To completely drop the overhead of multiple list reversals and solve the problem inside a **single forward pass**, we track the rightmost node that can absorb a carry without rolling over:

```java
Node lastNotNine = null; // Pointer tracking the rightmost node whose value is strictly < 9
Node current = head;     // Traversal cursor navigating reference hops
```

---

# Recurrence / Structural Slicing Condition

```text
While current != null:
    If current.data != 9 -> lastNotNine = current
    current = current.next

If lastNotNine == null:
    newHead = new Node(1)
    newHead.next = head
    Set all nodes from head to tail to 0
    Return newHead
Else:
    lastNotNine.data += 1
    Set all nodes after lastNotNine to 0
    Return head
```

---

# Optimal Single-Pass Pointer Solution

## Idea

We traverse the list from left to right to locate the last node whose value is strictly less than 9. If all elements are `9` (`lastNotNine == null`), it means adding 1 will cause a global overflow cascade (e.g., `999 + 1 = 1000`). We attach a new node `1` to the front and overwrite all original nodes to `0`. Otherwise, we increment `lastNotNine` by 1 and convert all subsequent downstream nodes to `0` instantly.

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

    public Node addOne(Node head) {
        if (head == null) {
            return new Node(1);
        }

        Node lastNotNine = null;
        Node current = head;

        // Phase 1: Single forward scan to find the rightmost node < 9
        while (current != null) {
            if (current.data != 9) {
                lastNotNine = current; // Update our absorption anchor
            }
            current = current.next;
        }

        // Phase 2: Handle the ripple overflow logic based on anchor presence
        if (lastNotNine == null) {
            // Case A: Every single digit is a 9 (e.g., 999 -> 1000)
            Node newHead = new Node(1);
            newHead.next = head;
            
            // Convert all original 9 nodes to 0
            current = head;
            while (current != null) {
                current.data = 0;
                current = current.next;
            }
            return newHead;
        } else {
            // Case B: A partial carry ripple occurs (e.g., 459 -> 460 or 189 -> 190)
            lastNotNine.data += 1; // Absorb the carry
            
            // All nodes trailing after our anchor must flush out to 0
            current = lastNotNine.next;
            while (current != null) {
                current.data = 0;
                current = current.next;
            }
            return head;
        }
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The first loop scans sequentially from the head to the tail exactly once. The second loop starts from either the head or `lastNotNine` and moves forward to overwrite values. Since both loops process elements continuously without nested backtracking, total operations remain strictly bounded inside a single linear pass.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. The algorithm manipulates data in-place inside local register variables. Even when a global overflow occurs, it allocates at most one additional single node to act as the new most significant bit, preserving a flat constant auxiliary space footprint.

---

# Key Takeaways

✅ Core fundamental arithmetic optimization pattern utilizing structural invariant lookups.

✅ **The Value of Carry Boundary Scanning:** Identifying the absolute rightmost point of change (`lastNotNine`) enables an instantaneous waterfall flush. It eliminates the need to maintain full back-references or allocate tracking stacks to handle backward mathematical carry ripples.

✅ Directly drives real-world architectures like **fixed-precision bignum counters**, hardware streaming decimal accumulators, protocol frame sequence incrementers, and telemetric stream filters.

✅ Common Architectural Paradigm:

```text
Prefix Boundary Tracking for In-Place Carry Ripple Waterfalls
```

### Similar Problems

- Plus One ([LeetCode 66](https://leetcode.com))
- Add Two Numbers ([LeetCode 2](https://leetcode.com))
- Add Two Numbers II ([LeetCode 445](https://leetcode.com))
- Multiply Strings ([LeetCode 43](https://leetcode.com))
