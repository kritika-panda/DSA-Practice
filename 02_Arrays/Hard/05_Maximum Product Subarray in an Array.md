
# Maximum Product Subarray ([LeetCode 152](https://leetcode.com))

## Problem Statement

Given an integer array `nums`, find a **contiguous non-empty subarray** within the array that has the **largest product**, and return *its product*.

The test cases are generated so that the answer will fit in a **32-bit** integer.

A **subarray** is a contiguous subsequence of the array.

---

## Examples

### Example 1

```text
Input: nums = [2,3,-2,4]
Output: 6
```

### Explanation

```text
The contiguous subarray [2,3] has the largest product = 2 * 3 = 6.
```

---

### Example 2

```text
Input: nums = [-2,0,-1]
Output: 0
```

### Explanation

```text
The result cannot be 2, because [-2,-1] is not a contiguous subarray.
The largest product achievable is 0.
```

---

# Intuition

If the array consisted entirely of positive numbers, the problem would be trivial—the product would keep growing as we extend the subarray. However, the presence of **negative numbers** and **zeros** introduces a sign-flipping property:
- Multiplying a negative number by another negative number yields a positive result. This means that a very small negative product can instantly flip to become the absolute maximum product if it encounters another negative number down the stream.
- Encountering a `0` instantly wipes out the running product accumulator, resetting it to zero.

To solve this in a **single linear pass with O(1) constant auxiliary space**, we use a variation of **Kadane's Algorithm** that tracks **two running metrics concurrently**:
1. `maxProd`: Tracks the maximum product ending at the current position.
2. `minProd`: Tracks the minimum (most negative) product ending at the current position.

When we process a new element `nums[i]`:
- If `nums[i]` is **negative**, it swaps the roles of our maximum and minimum tracks (the maximum multiplied by a negative becomes a minimum, and the minimum multiplied by a negative becomes a maximum). We can swap `maxProd` and `minProd` upfront to handle this transition cleanly.
- At each cell, we update our tracks by making a greedy choice—either we extend the current product chain or we restart a fresh subarray block at `nums[i]`:
  \[\text{maxProd} = \max(\text{nums}[i], \text{maxProd} \times \text{nums}[i])\]
  \[\text{minProd} = \min(\text{nums}[i], \text{minProd} \times \text{nums}[i])\]
- We continuously synchronize `maxProd` against a global tracker (`globalMax`) to capture the highest peak achieved at any point during the sweep.

---

# State Definition

The streaming optimization pipeline tracks product boundaries using three scalar registers:

```java
int globalMax = nums[0]; // Global register tracking the absolute highest product found
int maxProd = nums[0];   // Local tracker register storing the running maximum product
int minProd = nums[0];   // Local tracker register storing the running minimum product
```

---

# Recurrence / Structural State Condition

```text
Initialize globalMax = nums[0], maxProd = nums[0], minProd = nums[0]
For i from 1 to nums.length - 1:
    If nums[i] < 0 -> Swap(maxProd, minProd)
    
    maxProd = max(nums[i], maxProd * nums[i])
    minProd = min(nums[i], minProd * nums[i])
    
    globalMax = max(globalMax, maxProd)
Return globalMax
```

---

# Optimal Single-Pass Track-Swapping Solution

## Idea

We configure a single traversal loop starting from index 1. By executing a clean swap of our tracking registers whenever a negative value flips the boundary signs, we compute the maximum contiguous product completely in-place.

---

## Java Code

```java
class Solution {
    public int maxProduct(int[] nums) {
        // Guard Condition: Handle degenerate empty input boundaries cleanly
        if (nums == null || nums.length == 0) {
            return 0;
        }

        // Anchor trackers to the first element to support single-element inputs safely
        int globalMax = nums[0];
        int maxProd = nums[0];
        int minProd = nums[0];

        // Core Step: Single forward pass lookahead tracking sweep
        for (int i = 1; i < nums.length; i++) {
            int num = nums[i];

            // Role Inversion Invariant: A negative number flips the min and max boundaries.
            // Swapping them upfront ensures the multiplication step yields the correct trajectories.
            if (num < 0) {
                int temp = maxProd;
                maxProd = minProd;
                minProd = temp;
            }

            // Local Choice: Decide whether to continue the current tracking chain or restart at num
            maxProd = Math.max(num, maxProd * num);
            minProd = Math.min(num, minProd * num);

            // Global Synchronization: Update the absolute peak product achieved
            if (maxProd > globalMax) {
                globalMax = maxProd;
            }
        }

        return globalMax; // Return the maximum product subarray value found
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
*Explanation:* The algorithm walks through the N array elements sequentially exactly once. Inside the loop, it performs basic constant-time O(1) variable swaps, scalar multiplications, and maximum operations, locking total execution runtime strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. The state mutations and tracking updates execute completely in-place directly on primitive local variable registers, bypassing the need for auxiliary matrix tables or tracking arrays.

---

# Key Takeaways

✅ Core fundamental paradigm for processing contiguous sign-flipping sliding windows, maximum stream reductions, and dynamic optimization states.

✅ **The Dual Track Dependency Invariant:** Standard Kadane's algorithm only tracks the maximum sum because adding numbers doesn't invert sign vectors. When tracking products, a negative number acts as a bitwise or arithmetic multiplier inversion switch. Tracking the **minimum running product** alongside the maximum guarantees you capture large negative-to-positive product transitions perfectly.

✅ Directly drives real-world microarchitectures like **algorithmic trading leverage multipliers** (evaluating maximum compounding returns across a sequence of volatile high-leverage transaction logs), digital signal processing amplifiers, wave modulation calculators, and data compression blocks.

### Similar Problems

- Maximum Subarray / Kadane's Algorithm ([LeetCode 53](https://leetcode.com))
- Best Time to Buy and Sell Stock ([LeetCode 121](https://leetcode.com))
- Maximum Product of Three Numbers ([LeetCode 628](https://leetcode.com))
- Subarray Product Less Than K ([LeetCode 713](https://leetcode.com))
