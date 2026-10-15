# Next Smaller Element

## Problem Statement

Given an array of integers `A`, find the **next smaller element** for each element in the array. 

The **next smaller element** of an element `x` is the **first smaller** element that is positioned **to its right** in the array. If no such element exists to the right of `x`, then the answer for that specific element is `-1`.

The overall runtime complexity must be strictly bounded by:

```text
O(N)
```

*(where N is the length of the array A)*

---

## Examples

### Example 1

```text
Input: A = [4, 5, 2, 10, 8]
Output: [2, 2, -1, 8, -1]
```

### Explanation

```text
Evaluating elements from left to right:
- 4: The first element to its right smaller than 4 is 2.
- 5: The first element to its right smaller than 5 is 2.
- 2: There is no element smaller than 2 to its right. Ans = -1.
- 10: The first element to its right smaller than 10 is 8.
- 8: There are no elements to the right of 8. Ans = -1.
```

---

### Example 2

```text
Input: A = [3, 2, 1]
Output: [2, 1, -1]
```

---

# Intuition

A naive approach would look up each element and scan all elements to its right using nested loops, costing an inefficient \(O(N^2)\) runtime. 

To solve this in optimal **linear time**, we use a **Monotonic Increasing Stack** processed from right to left:
- As we traverse the array `A` backward, we want the stack to contain potential right-hand candidates sorted in a strictly increasing sequence from bottom to top.
- When evaluating the current element `A[i]`, any element at the top of the stack that is **greater than or equal to** `A[i]` cannot be the next smaller element for `A[i]` nor for any elements to its left. We greedily **pop** them off the stack.
- After clearing out larger elements, if the stack is not empty, the item at the top is the absolute first smaller element to the right of `A[i]`.
- We record this in our results array and then push `A[i]` onto the stack to act as a candidate for elements to its left.

---

# State Definition

Let our internal tracking structures be:

```text
Stack<Integer> stack = new Stack<>(); // Monotonic stack keeping values sorted increasingly
int[] result = new int[n];           // Output array storing next smaller elements
```

---

# Recurrence / Stack Contract Condition

```java
import java.util.Stack;

class Solution {
    public int[] nextSmallerElement(int[] A) {
        int n = A.length;
        int[] result = new int[n];
        Stack<Integer> stack = new Stack<>();

        for (int i = n - 1; i >= 0; i--) {
            // Maintain monotonic increasing property: pop larger or equal elements
            while (!stack.isEmpty() && stack.peek() >= A[i]) {
                stack.pop();
            }
            // Record next smaller element or -1 if empty
            result[i] = stack.isEmpty() ? -1 : stack.peek();
            // Push current element as a candidate for the left elements
            stack.push(A[i]);
        }
        return result;
    }
}
```

---

# Optimal Monotonic Stack Solution

## Idea

By processing the source array backward and aggressively pruning larger elements from the stack, each element is evaluated a constant number of times. This bypasses nested lookahead scans and computes the result array in a single linear **O(N)** pass.

---

## Java Code

```java
import java.util.Stack;

class Solution {
    public int[] nextSmallerElement(int[] A) {
        if (A == null || A.length == 0) {
            return new int[0];
        }

        int n = A.length;
        int[] result = new int[n];
        Stack<Integer> stack = new Stack<>();

        // Process the array from right to left
        for (int i = n - 1; i >= 0; i--) {
            int current = A[i];

            // Maintain monotonic increasing property: pop larger or equal elements
            while (!stack.isEmpty() && stack.peek() >= current) {
                stack.pop();
            }

            // If the stack is empty, no element to the right is smaller
            result[i] = stack.isEmpty() ? -1 : stack.peek();

            // Push the current element onto the stack to act as a candidate for the left elements
            stack.push(current);
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
*Explanation:* The array is scanned exactly once. Each element is pushed onto the stack exactly once and popped from the stack at most once across the entire execution sweep. The total number of stack operations is bounded by \(2N\), ensuring a strict linear runtime footprint.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary space scales linearly to store the elements inside the monotonic stack and allocate the output results array of size \(N\).

---

# Key Takeaways

✅ Core foundational design pattern for spatial and geometric range proximity lookups.

✅ **Choosing Stack Invariants:** To find the next **smaller** element, we maintain a monotonic **increasing** stack (from bottom to top). This ensures that smaller values remain at the bottom to catch larger incoming values on the left. Conversely, finding the next **greater** element requires a monotonic **decreasing** stack.

✅ Universally used inside **graphics rendering computation blocks** (calculating baseline alignments or shadowing), telemetry sequence indices trackers, and compilation parsing systems.

✅ Common Architectural Paradigm:

```text
Monotonic Horizon Reduction for Range Proximity Lookups
```

### Similar Problems

- Next Greater Element I (LeetCode 496)
- Next Smaller Element II / Previous Smaller Element (PSE)
- Daily Temperatures (LeetCode 739)
- Largest Rectangle in Histogram (LeetCode 84)
- Sum of Subarray Minimums (LeetCode 907)
