# Maximum Product of Two Elements in an Array ([LeetCode 1464](https://leetcode.com))

## Problem Statement

Given the array of integers `nums`, you will choose two different indices `i` and `j` of that array. Return *the maximum value of `(nums[i]-1)*(nums[j]-1)`*.

---

## Examples

### Example 1

```text
Input: nums = [3,4,5,2]
Output: 12
```

### Explanation

```text
If you choose the indices i=1 and j=2 (sorted 0-indexed), you will get the maximum value:
(nums[1]-1)*(nums[2]-1) = (4-1)*(5-1) = 3*4 = 12.
```

---

### Example 2

```text
Input: nums = [1,5,4,5]
Output: 16
```

### Explanation

```text
Choosing the indices i=1 and j=3 yields (nums[1]-1)*(nums[3]-1) = (5-1)*(5-1) = 4*4 = 16.
```

---

### Example 3

```text
Input: nums = [3,7]
Output: 12
```

---

# Intuition

The expression we want to maximize is `(nums[i] - 1) * (nums[j] - 1)`. Since all the integers in the array are positive, this value is strictly maximized by selecting the **two largest elements** present in the array.

We can solve this problem in two main ways:
1. **Sorting:** Sort the entire array in ascending order. The two largest elements will reside at the absolute end of the array (`nums[n-1]` and `nums[n-2]`). This takes an \(O(N \log N)\) time footprint.
2. **Dual-Register Tracking Pass:** We can optimize the lookups down to a **single linear pass** by maintaining two tracking variables (`first` and `second`) to capture the highest and second-highest values dynamically. As we scan each `num` sequentially:
   - If `num > first`, the historical maximum cascades down to the runner-up slot (`second = first`), and `first` absorbs `num`.
   - Else if `num > second`, `second` absorbs `num`.

This second strategy isolates the top two values inside a single sweep, skipping heavy sorting latency.

---

# State Definition

The multi-register linear scanning pipeline tracks the unique largest hierarchies using two primitive registers:

```java
int first = Integer.MIN_VALUE;  // Global maximum tracker register
int second = Integer.MIN_VALUE; // Global second maximum tracker register
```

---

# Recurrence / Cascading Rank Conditions

```text
For each num in nums:
    If num > first:
        second = first
        first = num
    Else If num > second:
        second = num
Return (first - 1) * (second - 1)
```

---

# Optimal Single-Pass Cascading Solution

## Idea

We initialize our tracking variables to minimum integer bounds. We run a single sequential pass, shifting values down the hierarchy chain cleanly. Finally, we calculate the mathematical product of the offset values using O(1) constant auxiliary storage space.

---

## Java Code

```java
class Solution {
    public int maxProduct(int[] nums) {
        // Guard Condition: Arrays with less than two elements cannot form pairs
        if (nums == null || nums.length < 2) {
            return 0;
        }

        // Initialize the top two tracking registers
        int first = Integer.MIN_VALUE;
        int second = Integer.MIN_VALUE;

        // Core Step: Single forward pass hierarchy cascade
        for (int num : nums) {
            if (num > first) {
                second = first; // Old maximum drops to the runner-up slot
                first = num;    // New absolute maximum anchored
            } else if (num > second) {
                second = num;   // New second maximum anchored
            }
        }

        // Calculate and return the target product metric profile
        return (first - 1) * (second - 1);
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
*Explanation:* The pointer sweeps through each item of the array exactly once. At each element, it performs at most two primitive scalar comparison statements, locking total execution runtime strictly linearly relative to the array length N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. The state mutations and value cascading updates run entirely inside local primitive tracking registers on the heap, bypassing the need for sorting structures or external array buffers.

---

# Key Takeaways

✅ Core baseline template for multi-state stream tracks, top-K filters, and stream hierarchy reductions.

✅ **The Hierarchy Cascading Shift Rule:** When tracking a runner-up value, remember that updating the absolute top-tier variable (`first`) **always requires shifting the older historical value down to the next position** (`second = first`). Directly overriding the primary maximum without shifting the old value will corrupt your data lookups.

✅ Directly drives real-world microarchitectures like **leaderboard extreme value matchers** (e.g., maximizing pairing metrics of peak data points), sensor correlation telemetry processors, and streaming signal processing layers.

### Similar Problems

- Find the Largest Element in an Array ([GeeksforGeeks](https://geeksforgeeks.com))
- Second and Third Largest Elements in an Array
- Kth Largest Element in an Array ([LeetCode 215](https://leetcode.com))
- Maximum Product Subarray ([LeetCode 152](https://leetcode.com))
