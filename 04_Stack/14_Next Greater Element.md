# Next Greater Element I (LeetCode 496)

## Problem Statement

The **next greater element** of some element `x` in an array is the **first greater** element that is **to the right** of `x` in the same array.

You are given two **0-indexed** integer arrays `nums1` and `nums2`, where `nums1` is a subset of `nums2`.

For each `0 <= i < nums1.length`, find the index `j` such that `nums2[j] == nums1[i]` and determine the **next greater element** of `nums2[j]` in `nums2`. If there is no next greater element, then the answer for this query is `-1`.

Return *an array `ans` of length `nums1.length` such that `ans[i]` is the **next greater element** as described above.*

The overall runtime complexity must be strictly bounded by:

```text
O(N + M)
```

*(where N is the length of nums2 and M is the length of nums1)*

---

## Examples

### Example 1

```java
Input: nums1 =, nums2 = [1,3,4,2]
Output: [-1,3,-1]
```

### Explanation

```text
The next greater element for each value in nums1 is evaluated via nums2:
- 4: Looking right of 4 in nums2, there is only 2. No element is > 4. Ans = -1.
- 1: Looking right of 1 in nums2, the first element > 1 is 3. Ans = 3.
- 2: Looking right of 2 in nums2, there are no elements to the right. Ans = -1.
```

---

### Example 2

```java
Input: nums1 =, nums2 = [1,2,3,4]
Output: [3,-1]
```

---

# Intuition

A naive approach would look up each element's position in `nums2` and then scan everything to its right using a nested loop, costing an inefficient \(O(M \times N)\) runtime. 

To solve this in optimal **linear time**, we can solve the "Next Greater Element" problem for *all* elements in `nums2` upfront. We use a **Monotonic Decreasing Stack** processed from right to left:
- As we traverse `nums2` backward, we want the stack to contain potential right-hand candidates sorted in a strictly decreasing sequence from bottom to top.
- When evaluating the current element `nums2[i]`, any element at the top of the stack that is **less than or equal to** `nums2[i]` is useless for both the current element and any elements further to its left. We greedily **pop** them off the stack.
- After clearing out smaller elements, if the stack still has elements, the item at the top is the absolute first greater element to the right of `nums2[i]`.
- We cache this relationship inside a `HashMap` mapping `element -> nextGreaterElement`. Finally, we build our output by running a constant-time lookup for each item in `nums1`.

---

# State Definition

Let our internal tracking structures be:

```text
Stack<Integer> stack = new Stack<>(); // Monotonic stack keeping elements sorted decreasingly
Map<Integer, Integer> map = new HashMap<>(); // Lookup cache storing {element : next_greater_element}
```

---

# Recurrence / Stack Contract Condition

```text
Scan nums2 from right to left (i = n-1 down to 0):
    While stack is not empty AND stack.peek() <= nums2[i]:
        stack.pop()
    
    nextGreater = stack.isEmpty() ? -1 : stack.peek()
    map.put(nums2[i], nextGreater)
    stack.push(nums2[i])
```

---

# Optimal Monotonic Stack Solution

## Idea

By processing the source array backward and aggressively pruning smaller elements from the stack, each element is evaluated a constant number of times. Caching these bounds inside a hash map allows us to resolve all queries in linear **O(N + M)** time.

---

## Java Code

```java
import java.util.HashMap;
import java.util.Map;
import java.util.Stack;

class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Map<Integer, Integer> nextGreaterMap = new HashMap<>();
        Stack<Integer> stack = new Stack<>();

        // Step 1: Compute the Next Greater Element for all items in nums2 from right to left
        for (int i = nums2.length - 1; i >= 0; i--) {
            int current = nums2[i];

            // Maintain monotonic decreasing property: pop smaller or equal elements
            while (!stack.isEmpty() && stack.peek() <= current) {
                stack.pop();
            }

            // If the stack is empty, no element to the right is larger
            int nextGreater = stack.isEmpty() ? -1 : stack.peek();
            
            // Cache the result for this element
            nextGreaterMap.put(current, nextGreater);

            // Push current element onto the stack to act as a candidate for elements to its left
            stack.push(current);
        }

        // Step 2: Construct the output array for nums1 using O(1) map lookups
        int[] result = new int[nums1.length];
        for (int i = 0; i < nums1.length; i++) {
            result[i] = nextGreaterMap.get(nums1[i]);
        }

        return result;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N + M)
```
*Explanation:* The array `nums2` of length N is scanned once. Each element is pushed onto and popped from the stack at most once, which guarantees an O(N) runtime footprint. Building the final answer array takes O(M) time using constant-time hash map lookups, yielding a strict O(N + M) total time complexity.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary space scales linearly to store the elements inside the monotonic stack and maintain the key-value mappings within the `HashMap`.

---

# Key Takeaways

✅ Core foundational design pattern for geometric range lookup properties.

✅ **The Power of Monotonic Stacks:** A monotonic stack maintains a strict sorted order of its elements by systematically popping items that violate its invariant. Whenever a problem asks for the *"first greater element to the right/left"* or *"nearest bottleneck obstacle"*, a monotonic stack turns a quadratic search space into an optimized linear track.

✅ Universally deployed inside real-world systems like **graph rendering calculation layers** (e.g., determining column layouts or hidden occlusion vectors), stocks line technical indicators, and text editor layout formatters.

✅ Common Architectural Paradigm:

```text
Monotonic Horizon Reduction for Range Proximity Lookups
```

### Similar Problems

- Next Greater Element II (Circular array variant - LeetCode 503)
- Next Greater Element III (LeetCode 556)
- Daily Temperatures (LeetCode 739)
- Largest Rectangle in Histogram (LeetCode 84)
- Online Stock Span (LeetCode 901)
