# Squares of a Sorted Array ([LeetCode 977](https://leetcode.com))

## Problem Statement

Given an integer array `nums` sorted in **non-decreasing** order, return *an array of **the squares of each number** sorted in non-decreasing order*.

**Performance Targets:**
- **Time Complexity:** strictly bounded by **O(N)**.
- **Auxiliary Space Complexity:** highly optimal at **O(1)** (excluding the output array).

---

## Examples

### Example 1

```text
Input: nums = [-4,-1,0,3,10]
Output: [0,1,9,16,100]
```

### Explanation

```text
After squaring, the array becomes.
After sorting, it becomes.
```

---

### Example 2

```text
Input: nums = [-7,-3,2,3,11]
Output: [4,9,9,49,121]
```

---

# Intuition

A naive approach would square every element in-place and then sort the array using standard quicksort or Timsort. However, sorting takes an expensive **\(O(N \log N)\)** time footprint and fails to leverage the fact that the original array is *already sorted*.

Because the input array can contain **negative numbers**, squaring reverses their magnitude sorting hierarchy (e.g., `-4` becomes `16`, which is larger than `3` squared). This implies that the absolute largest squared values are guaranteed to reside at the **extreme outer boundaries** of the array—either at the absolute far left (large negative values) or at the absolute far right (large positive values). 

To achieve a true **single-pass O(N) linear timeline**, we can deploy a **Two-Pointer (Meet-in-the-Middle) Compaction Pattern**:
1. Allocate a brand new result array `ans` of the exact same size as `nums`.
2. Anchor a `left` pointer at index `0` and a `right` pointer at index `N - 1`.
3. Track an insertion cursor `writeIdx` starting from the absolute **back of the result array** (`N - 1`) and moving leftward.
4. Compare the absolute squared magnitudes of `nums[left]` and `nums[right]`. 
5. Place the larger squared value directly into `ans[writeIdx]`, contract that respective pointer inward (`left++` or `right--`), and shift our insertion cursor backward (`writeIdx--`).

---

# State Definition

The space-optimized compaction pipeline tracks element boundaries using three index pointers:

```java
int left = 0;              // Boundary cursor anchored at the lower bounds of the search space
int right = nums.length - 1; // Boundary cursor anchored at the upper bounds of the search space
int writeIdx = nums.length - 1; // Destination index register for writing squared results backward
```

---

# Recurrence / Structural State Condition

```text
While left <= right:
    If abs(nums[left]) > abs(nums[right]):
        ans[writeIdx] = nums[left] * nums[left]
        left++
    Else:
        ans[writeIdx] = nums[right] * nums[right]
        right--
    writeIdx--
Return ans
```

---

# Optimal Single-Pass Two-Pointer Solution

## Idea

We configure a single convergence loop that terminates once our `left` and `right` boundary markers cross over each other. By populating our output array workspace **backwards from largest to smallest**, we sort the squared entries inside a single clean linear pass.

---

## Java Code

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        // Guard Condition: Handle degenerate empty input boundaries cleanly
        if (nums == null || nums.length == 0) {
            return new int[]{};
        }

        int n = nums.length;
        int[] ans = new int[n];
        
        // Initialize our boundary anchors and write cursor
        int left = 0;
        int right = n - 1;
        int writeIdx = n - 1;

        // Core Step: Single forward pass meet-in-the-middle compaction
        while (left <= right) {
            int leftSquare = nums[left] * nums[left];
            int rightSquare = nums[right] * nums[right];

            // Compare absolute magnitudes to stream the higher squared value backward
            if (leftSquare > rightSquare) {
                ans[writeIdx] = leftSquare;
                left++; // Contract lower boundary pointer rightward
            } else {
                ans[writeIdx] = rightSquare;
                right--; // Contract upper boundary pointer leftward
            }
            
            // Advance the results placement pointer backward
            writeIdx--;
        }

        return ans; // Return the fully populated sorted squared array
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
*Explanation:* The convergence loop pairs outer bounds sequentially, writing exactly one squared element into our output workspace per step. Since the pointers move inward monotonically without any nested tracking lookbacks, total operations scale strictly linearly relative to the array length N.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. Excluding the allocation of the output workspace array `ans` required to compile and return the final answer (which scales as an inherent O(N) requirement), the algorithm coordinates comparative metrics using only three local primitive register pointers, preserving a flat constant auxiliary footprint.

---

# Key Takeaways

✅ Core baseline foundational template for element compaction, sorting reduction passes, and double-ended pointer loops.

✅ **The Backward Allocation Rule:** Populating your result workspace **from back to front (`writeIdx = n - 1`)** is a highly strategic design choice for two-pointer convergence. Because the extreme outer limits of a sign-sorted stream hold the absolute maximum potential squared magnitudes, capturing and depositing the largest values first eliminates the need to calculate center-points or sort data arrays afterwards.

✅ Directly drives real-world microarchitectures like **asymmetric data stream signal encoders** (sorting telemetry fields or frequency responses where variance spans positive and negative ranges), graphics shader distance sorting filters, and coordinate normalization matrices.

### Similar Problems

- Merge Sorted Array ([LeetCode 88](https://leetcode.com))
- Two Sum II - Input Array Is Sorted ([LeetCode 167](https://leetcode.com))
- Sort Array By Parity ([LeetCode 905](https://leetcode.com))
- Container With Most Water ([LeetCode 11](https://leetcode.com))
