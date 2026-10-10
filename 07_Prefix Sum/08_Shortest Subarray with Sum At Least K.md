# Shortest Subarray with Sum At Least K

## Problem Statement

Given an integer array `nums` and an integer `k`, return the **length of the shortest non-empty subarray** of `nums` with a sum of **at least `k`**. If there is no such subarray, return `-1`.

A **subarray** is a contiguous part of an array. Note that `nums` can contain **negative integers**.

---

## Examples

### Example 1

```text
Input: nums =, k = 1
Output: 1
```

---

### Example 2

```text
Input: nums =, k = 4
Output: -1
```

---

### Example 3

```text
Input: nums = [2, -1, 2], k = 3
Output: 3
```

### Explanation

```text
The shortest subarray with a sum of at least 3 is [2, -1, 2] with a sum of 3.
Subarrays like [2] (sum=2) or [-1, 2] (sum=1) do not satisfy the condition.
```

---

# Key Concept: Monotonic Queue over Prefix Sums

When an array contains only **positive integers**, we can find the shortest subarray satisfying a target sum using a standard two-pointer sliding window. However, when the array contains **negative integers**, the prefix sums are no longer monotonically increasing. Adding an element can cause the running sum to drop.

To resolve this efficiently, we transform the array into its `prefixSum` representation and maintain indices in a **Double-Ended Queue (Deque)** that enforces two properties:

### 1. Maintain Monotonicity (Increasing Order)
If we encounter a new prefix sum `prefixSum[i]` that is **less than or equal to** the prefix sum at the back of the queue (`prefixSum[deque.peekLast()]`), the older index can be safely evicted. A smaller prefix sum at a later index is always a superior starting boundary for finding a *shorter* valid subarray.

### 2. Optimize the Left Boundary
As we process `prefixSum[i]`, we check the front of the queue. If `prefixSum[i] - prefixSum[deque.peekFirst()] >= k`, we have found a valid subarray. We record its length (`i - deque.pollFirst()`) and continuously pop elements from the front as long as the condition holds to find the shortest window.

---

# Intuition

1. **Prefix Sum Construction:** Allocate a `long[]` array of size `N + 1` to hold cumulative sums (`prefixSum[0] = 0`). Using a `long` datatype prevents integer overflow bugs.
2. **Deque Operations:** Traverse the prefix sums from index `0` to `N`:
   * **Shrink from Front:** While the queue is not empty and the current prefix sum minus the prefix sum at the front is `... >= k`, calculate the length, update `minLen`, and remove that front index permanently (it cannot yield a shorter window in the future).
   * **Prune from Back:** While the queue is not empty and the current prefix sum is less than or equal to the prefix sum at the back, pop the back element to preserve strict increasing monotonicity.
   * **Append:** Push the current index `i` onto the back of the queue.

---

# Java Implementation

```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public int shortestSubarray(int[] nums, int k) {
        int n = nums.length;
        int minLen = n + 1;
        
        // Step 1: Construct prefix sums using long to avoid integer overflow
        long[] prefixSum = new long[n + 1];
        for (int i = 0; i < n; i++) {
            prefixSum[i + 1] = prefixSum[i] + nums[i];
        }

        // Deque to store indices of prefixSum in a strictly increasing order
        Deque<Integer> deque = new ArrayDeque<>();

        // Step 2: Traverse prefix sums and maintain monotonic layout
        for (int i = 0; i <= n; i++) {
            // Check from the front if a valid window is found
            while (!deque.isEmpty() && prefixSum[i] - prefixSum[deque.peekFirst()] >= k) {
                minLen = Math.min(minLen, i - deque.pollFirst());
            }

            // Maintain monotonicity by pruning larger prefix sums from the back
            while (!deque.isEmpty() && prefixSum[i] <= prefixSum[deque.peekLast()]) {
                deque.pollLast();
            }

            // Add the current index to the queue
            deque.addLast(i);
        }

        // Return -1 if minLen was never updated, otherwise return the shortest length
        return minLen == n + 1 ? -1 : minLen;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total length of the `nums` array. Every index `i` from `0` to `N` is pushed onto the Deque exactly once and popped from either the front or the back at most once, guaranteeing a strict linear runtime profile.
* **Space Complexity:** O(N) auxiliary space. This accounts for the `N + 1` sized `prefixSum` array, plus the Deque structure which stores up to `N` index elements in the worst-case scenario.
