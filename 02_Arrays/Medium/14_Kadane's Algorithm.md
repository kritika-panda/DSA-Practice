# Maximum Subarray / Kadane's Algorithm ([LeetCode 53](https://leetcode.com))

## Problem Statement

Given an integer array `nums`, find the **contiguous subarray** (containing at least one number) which has the **largest sum** and return *its sum*.

A **subarray** is a contiguous part of an array.

**Performance Targets:**
- **Time Complexity:** strictly bounded by **O(N)**.
- **Auxiliary Space Complexity:** highly optimal at **O(1)**.

---

## Examples

### Example 1

```text
Input: nums = [-2,1,-3,4,-1,2,1,-5,4]
Output: 6
```

### Explanation

```text
The contiguous subarray [4,-1,2,1] has the largest sum = 4 + (-1) + 2 + 1 = 6.
```

---

### Example 2

```text
Input: nums = [1]
Output: 1
```

---

### Example 3

```text
Input: nums = [5,4,-1,7,8]
Output: 23
```

---

# Intuition

A brute-force approach would check every single possible subarray combination using nested loops, which triggers an inefficient O(N²) quadratic runtime penalty.

To optimize the calculation down to a **single linear pass**, we can deploy **Kadane's Algorithm**, which uses a dynamic local greedy choice strategy:
- As we traverse the array from left to right, we maintain a running sum of the active subarray segment (`currentSum`).
- At each new element `nums[i]`, we must make a fundamental choice: **Should we append `nums[i]` to our existing subarray chain, or should we discard the history and start a brand-new subarray right at `nums[i]`?**
- If the historical `currentSum` has dropped below zero, adding it to `nums[i]` would only drag down the value of `nums[i]`. Thus, we greedily choose to reset and restart our subarray path whenever the incoming element itself is greater than the combined running total:
  \[\text{currentSum} = \max(\text{nums}[i], \text{currentSum} + \text{nums}[i])\]
- We continuously synchronize this local choice against a global tracker (`maxSum`) to capture the highest peak achieved at any point during the sweep.

---

# State Definition

The streaming optimization pipeline tracks structural sum boundaries using two scalar registers:

```java
int maxSum = nums[0];    // Global register tracking the absolute highest subarray sum found
int currentSum = nums[0]; // Local register tracking the running sum of the active subarray
```

---

# Recurrence / Structural State Condition

```text
Initialize maxSum = nums[0], currentSum = nums[0]
For i from 1 to nums.length - 1:
    currentSum = max(nums[i], currentSum + nums[i])
    maxSum = max(maxSum, currentSum)
Return maxSum
```

---

# Optimal Single-Pass Kadane's Algorithm Solution

## Idea

We initialize our tracking variables to the value of the absolute first element to handle edge cases where the array consists entirely of negative numbers. We run a single sequential pass, updating the local and global maximums in-place with true constant auxiliary space.

---

## Java Code

```java
class Solution {
    public int maxSubArray(int[] nums) {
        // Guard Condition: Handle degenerate empty input boundaries cleanly
        if (nums == null || nums.length == 0) {
            return 0;
        }

        // Anchor trackers to the first element to support all-negative array profiles safely
        int currentSum = nums[0];
        int maxSum = nums[0];

        // Core Step: Single forward pass sliding greedy sweep
        for (int i = 1; i < nums.length; i++) {
            // Local Choice: Decide whether to continue the current chain or restart at nums[i]
            currentSum = Math.max(nums[i], currentSum + nums[i]);
            
            // Global Synchronization: Update the absolute maximum peak achieved
            if (currentSum > maxSum) {
                maxSum = currentSum;
            }
        }

        return maxSum; // Return the maximum contiguous subarray sum found
    }
}
```

---

## Complexity Analysis

Let N be the total length of the input array `nums`.

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The algorithm walks through the N array elements sequentially exactly once. Inside the loop, it performs basic constant-time O(1) scalar maximum and addition operations, locking total execution runtime strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. The state tracking mutations and greedy path choices execute completely in-place directly on primitive local variable registers, bypassing the need for auxiliary matrix tables or tracking buffers.

---

# Key Takeaways

✅ Core fundamental paradigm for processing contiguous sliding window optimizations, maximum stream reductions, and streaming data aggregations.

✅ **The All-Negative Array Rule:** Never initialize your global maximum tracker to `0` (`int maxSum = 0`). If the input array consists entirely of negative numbers (e.g., `[-5, -2, -8]`), initializing to `0` will yield a corrupt answer of `0`, which doesn't exist in the data. Always anchor initial variables to `nums[0]` so negative peaks are tracked accurately.

✅ Directly drives real-world microarchitectures like **stock market maximum profit window trackers** (isolating the most profitable contiguous trading days), real-time sensor signal burst analyzers, computer graphics streaming filters, and network telemetry packet analyzers.

### Similar Problems

- Maximum Product Subarray ([LeetCode 152](https://leetcode.com))
- Best Time to Buy and Sell Stock ([LeetCode 121](https://leetcode.com))
- Maximum Sum Circular Subarray ([LeetCode 918](https://leetcode.com))
- Longest Turbulent Subarray ([LeetCode 978](https://leetcode.com))
