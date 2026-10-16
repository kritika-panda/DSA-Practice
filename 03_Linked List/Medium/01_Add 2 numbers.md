# Add Two Numbers ([LeetCode 2](https://leetcode.com))

## Problem Statement

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order**, and each of their nodes contains a single digit. Add the two numbers and return *the sum as a linked list*.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

---

## Examples

### Example 1

```text
Input: l1 =, l2 = [5,6,4]
Output: [7,0,8]
```

### Explanation

```text
The two linked lists represent the numbers 342 and 465 respectively.
342 + 465 = 807.
The result stored in reverse order is 7 -> 0 -> 8.
```

---

### Example 2

```text
Input: l1 =, l2 = [0]
Output: [0]
```

---

### Example 3

```text
Input: l1 =, l2 = [9,9,9,9]
Output: [8,9,9,9,0,0,0,1]
```

---

# Intuition

Because the digits are stored in **reverse order**, the least significant digits (the units place) are positioned right at the `head` pointers of both lists. This matches standard right-to-left addition math (like column addition with carrying) perfectly. We can process the addition directly from left to right as we traverse forward.

We can simulate schoolbook addition using a single pointer traversal:
1. Introduce a **Dummy Sentinel Node** to act as a stable anchor root for the resulting sum chain.
2. Maintain a running `carry` variable tracking the division overflow from the previous columns.
3. In each step, compute the sum of the digits from `l1` and `l2` (if available) along with the active `carry`.
4. Update the carry for the next column: `carry = sum / 10`.
5. Create a new node with the value `sum % 10` and stitch it onto the tail of our expanding sum list.
6. Advance all active list pointers forward exactly one step.
7. **The Residual Carry Rule:** If the loops finish because both lists run completely dry, but a leftover `carry > 0` remains, we must append one final terminal node holding that carry value (e.g., handling `99 + 1 = 100`).

---

# Brute Force Solution

## Idea

A naive solution converts both linked lists into long integers or high-precision string representations, adds them mathematically using a BigInteger engine, and then processes the digits of the sum backward to build a brand new linked list. This requires multi-pass loops and creates unneeded garbage on the heap.

---

## Java Code

```java
import java.math.BigInteger;

class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        StringBuilder sb1 = new StringBuilder();
        StringBuilder sb2 = new StringBuilder();

        // Step 1: Extract string representations backward
        while (l1 != null) {
            sb1.append(l1.val);
            l1 = l1.next;
        }
        while (l2 != null) {
            sb2.append(l2.val);
            l2 = l2.next;
        }

        // Step 2: Invert to match normal number reading layout and add
        BigInteger num1 = new BigInteger(sb1.reverse().toString());
        BigInteger num2 = new BigInteger(sb2.reverse().toString());
        BigInteger sum = num1.add(num2);

        String sumStr = new StringBuilder(sum.toString()).reverse().toString();
        ListNode dummy = new ListNode(0);
        ListNode current = dummy;

        // Step 3: Parse the string characters back into new nodes
        for (char c : sumStr.toCharArray()) {
            current.next = new ListNode(c - '0');
            current = current.next;
        }

        return dummy.next;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N + M) where N and M are the lengths of both lists, but with extra string allocation overhead.
- **Space Complexity:** O(N + M) auxiliary memory tax to store string tokens on heap memory.

---

# State Definition (Optimal Approach)

To drop the memory cost to a constant floor and handle the mathematical accumulation inside a single pass, we manage states entirely using numerical trackers:

```java
ListNode dummy = new ListNode(0); // Sentinel dummy node shielding the root changes
ListNode current = dummy;          // Tracking cursor navigating the new sum list
int carry = 0;                     // Tracker monitoring addition overflows (0 or 1)
```

---

# Recurrence / Structural State Condition

```text
While l1 != null OR l2 != null OR carry != 0:
    val1 = (l1 != null) ? l1.val : 0
    val2 = (l2 != null) ? l2.val : 0
    total = val1 + val2 + carry
    
    carry = total / 10
    current.next = new ListNode(total % 10)
    
    current = current.next
    if l1 != null -> l1 = l1.next
    if l2 != null -> l2 = l2.next
```

---

# Optimal Single-Pass In-Place Simulation Solution

## Idea

We anchor a `current` cursor on our sentinel `dummy` node. We run an iterative loop that handles digit extraction defensively, filling missing list spots with `0` values. By factoring the `carry != 0` check directly into our loop boundary statement, the final residual carry digit is seamlessly appended without an extra trailing lookahead check block.

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

    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        // Initialize a sentinel dummy node to securely anchor the head of our sum list
        ListNode dummy = new ListNode(0);
        ListNode current = dummy; // Cursor tracking the tail of our growing sum chain
        int carry = 0;            // Accumulator tracking base-10 numerical carry values

        // Core Step: Single forward pass over the data sequences concurrently
        // Loop boundary statement captures lingering carries seamlessly
        while (l1 != null || l2 != null || carry != 0) {
            // Defensive extraction: Use 0 if a list has already run dry
            int val1 = (l1 != null) ? l1.val : 0;
            int val2 = (l2 != null) ? l2.val : 0;

            // Compute total column summation
            int sum = val1 + val2 + carry;
            
            // Calculate new carry for the subsequent iteration step
            carry = sum / 10;
            
            // Allocate and link a new node containing the units place remainder
            current.next = new ListNode(sum % 10);
            current = current.next; // Advance master tracking cursor

            // Advance the source stream pointers if valid nodes remain
            if (l1 != null) l1 = l1.next;
            if (l2 != null) l2 = l2.next;
        }

        return dummy.next; // Return the head of the sum list anchored right past dummy
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(max(N, M))
```
*Explanation:* The algorithm traverses forward precisely once, bounded by the maximum length between the two number strings. Inside the loop, it performs only constant-time basic primitive arithmetic operations, keeping the time complexity strictly linear.

---

### Space Complexity

```text
Space Complexity: O(max(N, M))
```
*Explanation:* Memory allocation scales linearly to generate the output nodes for the combined sum list. Outside of the required output nodes, the auxiliary space used for local execution variables matches an optimal O(1) constant tier.

---

# Key Takeaways

✅ Core fundamental arithmetic simulation pattern for processing linked token streams.

✅ **The Value of Combined Boundary Checks:** Embedding `carry != 0` directly into the master `while` loop condition statement ensures that edge cases like `99 + 1` naturally spawn a final trailing `1` node during the final loop step. This avoids writing repetitive, error-prone post-loop cleanup blocks.

✅ Directly drives real-world microarchitectures like **Arbitrary-Precision Arithmetic packages (BigInt)**, streaming pipeline payload decoders, networking checksum calculation systems, and financial telemetry processors.

✅ Common Architectural Paradigm:

```text
Bilateral Stream Accumulation with Carry Register Invariants
```

### Similar Problems

- Add Two Numbers II (Digits stored in forward order - [LeetCode 445](https://leetcode.com))
- Plus One ([LeetCode 66](https://leetcode.com))
- Multiply Strings ([LeetCode 43](https://leetcode.com))
- Merge Two Sorted Lists ([LeetCode 21](https://leetcode.com))
