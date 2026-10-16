# Convert Binary Number in a Linked List to Integer ([LeetCode 1290](https://leetcode.com))

## Problem Statement

Given `head` which is a reference node to a singly-linked list. The value of each node in the linked list is either `0` or `1`. The linked list holds the binary representation of a number.

Return *the decimal value of the number in the linked list*.

The most significant bit is at the head of the linked list.

---

## Examples

### Example 1

```text
Input: head = [1,0,1]
Output: 5
```

### Explanation

```text
The binary representation is (101) in base 2, which converts to:
1 * 2² + 0 * 2¹ + 1 * 2⁰ = 4 + 0 + 1 = 5.
```

---

### Example 2

```text
Input: head = [0]
Output: 0
```

---

# Intuition

As we traverse a binary sequence from left to right, each time we move forward to a new bit, the value accumulated so far shifts left by one position (multiplying by 2) to make room for the new bit. This numerical stream accumulation pattern matches **Horner's Method** for polynomial evaluation.

Instead of parsing the entire list first to calculate the total length (or exponent depth) up front, we can maintain a single rolling `decimalValue` accumulator variable initialized to `0`. 
For every node we step onto during our forward traversal:
1. Shift the running total left by multiplying it by 2: `decimalValue = decimalValue * 2`
2. Add the current node's binary digit payload: `decimalValue = decimalValue + current.val`

By rolling values forward on-the-fly, we avoid multi-pass scans or auxiliary array collections completely.

---

# Brute Force Solution

## Idea

We traverse the linked list sequentially to collect all binary bits into a dynamic `ArrayList` or string block. Once the collection is compiled, we iterate backwards from the tail to calculate the decimal values using exponent math (\(2^i\)).

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public int getDecimalValue(ListNode head) {
        List<Integer> binaryDigits = new ArrayList<>();
        ListNode current = head;

        // Step 1: Collect all binary digits into a linear container
        while (current != null) {
            binaryDigits.add(current.val);
            current = current.next;
        }

        int decimalValue = 0;
        int n = binaryDigits.size();

        // Step 2: Compute exponent summation from right to left
        for (int i = 0; i < n; i++) {
            int bit = binaryDigits.get(n - 1 - i);
            decimalValue += bit * Math.pow(2, i);
        }

        return decimalValue;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** \(O(N)\) because the code performs two sequential passes across the data length.
- **Space Complexity:** \(O(N)\) auxiliary memory allocation tax to store raw digit copies inside the list container on the heap.

---

# State Definition (Optimal Approach)

To optimize our tracking footprint to an absolute constant \(O(1)\) floor, we drop list containers and compute the numerical transformations concurrently inside a single register:

```java
int decimalValue = 0; // High-speed running accumulator register tracking the numerical stream
```

---

# Recurrence / Structural State Condition

```text
For each node visited from head to tail:
    decimalValue = (decimalValue << 1) | node.val
```
*(Note: Shifting left by 1 bit `<< 1` is a high-speed bitwise optimization equivalent to multiplying by 2, and bitwise OR `|` appends the bit cleanly).*

---

# Optimal Single-Pass Bitwise Solution

## Idea

We start a `current` tracker pointer at the `head`. As it moves forward step-by-step, we use bitwise operators to shift our running total left by one column (`decimalValue << 1`) and pipe in the current bit value using a bitwise OR (`| current.val`). This processes the string sequence inside a single high-speed linear pass.

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

    public int getDecimalValue(ListNode head) {
        int decimalValue = 0;
        ListNode current = head;

        // Core Step: Single forward pass over the data sequence
        while (current != null) {
            // High-speed bitwise execution equivalent to: (decimalValue * 2) + current.val
            decimalValue = (decimalValue << 1) | current.val;
            
            // Advance pointer to the next forward reference link
            current = current.next;
        }

        return decimalValue;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The algorithm performs a single pass from the `head` pointer straight to the terminal `null` boundary exactly once. Inside the loop, all operations use raw hardware bitwise registers, executing in true \(O(1)\) constant time.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. It stores tracking states entirely inside a single primitive integer variable, requiring zero auxiliary memory grids or tables.

---

# Key Takeaways

✅ Core fundamental bitwise pattern for streaming sequence evaluation.

✅ **The Power of Left Shifting:** Multiplying an integer by its base constant value when moving left-to-right flattens the execution space. For binary strings (Base 2), `decimalValue << 1` shifts the bit sequence instantly. The same logic applies to decimal string parsing (Base 10) by running `total = total * 10 + digit`.

✅ Directly drives microarchitectures like **network packet network telemetry stream deserializers**, compiler token layout readers, data stream compressors, and binary payload decoder modules.

✅ Common Architectural Paradigm:

```text
Bitwise Horner Reduction for Real-Time Streaming Data Deserializations
```

### Similar Problems

- Add Two Numbers ([LeetCode 2](https://leetcode.com))
- Reverse Linked List ([LeetCode 206](https://leetcode.com))
- Multiply Strings ([LeetCode 43](https://leetcode.com))
- Base 7 ([LeetCode 504](https://leetcode.com))
