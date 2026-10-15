# Next Greater Element II (LeetCode 503)

## Problem Statement

Given a circular integer array `nums` (i.e., the next element of `nums[nums.length - 1]` is `nums[0]`), return *the **next greater element** for every element in `nums`*.

The **next greater element** of an element `x` is the first greater element to its traversing-order right next to it, which means we could search circularly to find its next greater element. If it doesn't exist, return `-1` for this element.

The overall runtime complexity must be strictly bounded by:

```text
O(N)
```

*(where N is the length of the nums array)*

---

## Examples

### Example 1

```java
Input: nums =
Output: [2,-1,2]
```

### Explanation

```text
The next greater element for each value is:
- nums[0] = 1: The next greater element to the right is 2.
- nums[1] = 2: No element is strictly greater than 2 even if searching circularly. Ans = -1.
- nums[2] = 1: Wrapping circularly back to the beginning, the first element greater than 1 is 2.
```

---

### Example 2

```java
Input: nums =
Output: [2,3,4,-1,4]
```

---

# Intuition

This is an advanced variation of the classic **Next Greater Element** problem. The unique challenge here is the **circular property** of the array. An element can find its next greater counterpart by wrapping around to the front of the array.

A standard trick to simulate a circular array wrap-around is to conceptually **double the array** (concatenate it with itself, i.e., `nums` followed by `nums`). This creates an array of size `2N`, allowing any element from the original range to look ahead into a full copy of the array sequence.

Instead of actually allocating an expensive new array of size `2N`, we can optimize this by running our loop **exactly 2N times** and mapping indices back to the original range using the **Modulo Operator (`% N`)**.

We use a **Monotonic Decreasing Stack** holding *indices* processed from right to left (from index `2N - 1` down to `0`):
- We pop indices from the stack whose values are **less than or equal to** our current element `nums[i % n]`.
- For the first virtual pass (indices `2N - 1` down to `N`), we populate the stack to build up the right-hand circular context history.
- For the second real pass (indices `N - 1` down to `0`), the item at the top of the stack is guaranteed to be the correct next greater element. We record it in our results array.

---

# State Definition

Let our internal tracking structures be:

```text
Stack<Integer> stack = new Stack<>(); // Monotonic stack keeping array indices sorted decreasingly by value
int[] result = new int[n];           // Output array storing the next greater elements
```

---

# Recurrence / Stack Contract Condition

```text
Scan from i = 2N - 1 down to 0:
    index = i % N
    While stack is not empty AND nums[stack.peek()] <= nums[index]:
        stack.pop()
    
    If i < N:
        result[index] = stack.isEmpty() ? -1 : nums[stack.peek()]
        
    stack.push(index)
```

---

# Optimal Monotonic Stack Solution

## Idea

By simulating a doubled array using index modulo calculations and filtering out smaller elements dynamically via a monotonic stack, circular history lookups are resolved cleanly. This completes the entire evaluation in a single linear **O(N)** pass.

---

## Java Code

```java
import java.util.Arrays;
import java.util.Stack;

class Solution {
    public int[] nextGreaterElements(int[] nums) {
        if (nums == null || nums.length == 0) {
            return new int[0];
        }

        int n = nums.length;
        int[] result = new int[n];
        Arrays.fill(result, -1); // Seed with default -1 values upfront
        
        // Stack to store indices rather than direct values
        Stack<Integer> stack = new Stack<>();

        // Loop 2N times backwards to seamlessly simulate a circular array sequence
        for (int i = 2 * n - 1; i >= 0; i--) {
            int index = i % n; // Circular index wrap-around mapping

            // Maintain the monotonic decreasing property based on array element values
            while (!stack.isEmpty() && nums[stack.peek()] <= nums[index]) {
                stack.pop();
            }

            // Only populate results array during the second real pass (when i < N)
            if (i < n) {
                result[index] = stack.isEmpty() ? -1 : nums[stack.peek()];
            }

            // Push current index onto the stack to act as a future candidate
            stack.push(index);
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
*Explanation:* The loop executes exactly 2N times. Each index position from the array is pushed onto and popped from the stack at most twice across the entire execution sweep, bounding the overall operational time complexity strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary space scales linearly to maintain indices inside the monotonic stack, which can store up to N entries in worst-case monotonically increasing array topologies.

---

# Key Takeaways

✅ Advanced algorithmic extension specializing in circular array data models.

✅ **The Index Modulo Paradigm:** When a problem introduces circular wrap-around rules, looping 2N times and applying `i % N` provides full contextual lookup windows without wasting heap allocations to explicitly clone or resize data structures.

✅ Direct real-world implementation engine inside **circular ring buffers**, task scheduling loops with continuous rotating priority allocations, and cycling data telemetry streams.

✅ Common Architectural Paradigm:

```text
Virtual Array Flattening via Circular Modulo Indexing and Monotonic Reduction
```

### Similar Problems

- Next Greater Element I (LeetCode 496)
- Next Greater Element III (LeetCode 556)
- Daily Temperatures (LeetCode 739)
- Largest Rectangle in Histogram (LeetCode 84)
- Online Stock Span (LeetCode 901)
