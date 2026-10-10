# Find Peak Element

## Problem Statement

A peak element is an element that is strictly greater than its neighbors.

Given a **0-indexed** integer array `nums`, find a peak element, and return its index. If the array contains multiple peaks, return the index to **any of the peaks**.

You may imagine that `nums[-1] = nums[n] = -∞`. In other words, an element is always considered to be strictly greater than a neighbor that is outside the array.

You must write an algorithm that runs in **O(log n)** time complexity.

---

## Examples

### Example 1

```text
Input: nums = [1, 2, 3, 1]
Output: 2
```

### Explanation

```text
3 is a peak element and your function should return the index number 2.
```

---

### Example 2

```text
Input: nums = [1, 2, 1, 3, 5, 6, 4]
Output: 5
```

### Explanation

```text
Your function can return either index number 1 (where the peak element is 2) 
or index number 5 (where the peak element is 6).
```

---

# Key Property: Binary Slope Tracing

```text
If nums[mid] > nums[mid + 1] ---> Downward slope. A peak exists to the left (including mid).
If nums[mid] < nums[mid + 1] ---> Upward slope. A peak exists strictly to the right.
```

An array can be visualized as a sequence of rising and falling mountain slopes. We do not need to locate every mountain peak linearly. By looking at a single element `mid` and its immediate neighbor `mid + 1`, we can deduce the current **local slope direction**. 

Because boundaries are treated as negative infinity (`-∞`), following a rising slope is guaranteed to lead to a peak eventually.

---

# Intuition

We maintain two pointers, `low` and `high`, to track our active search window. We run the loop while `low < high` to prevent checking out of bounds at `mid + 1` and allow the pointers to converge on a single peak element.

At each step inside the loop:
1. Find the midpoint: `mid = (low + high) / 2`.
2. **Case A: `nums[mid] > nums[mid + 1]`**
   * The current element is strictly greater than its right neighbor. This indicates that the slope is currently falling or dipping to the right.
   * A peak must exist either at `mid` itself or somewhere in the left partition. We contract our search window by pulling the upper boundary directly to the midpoint: `high = mid`.
3. **Case B: `nums[mid] <= nums[mid + 1]`**
   * The current element is less than or equal to its right neighbor. This indicates that the slope is rising towards the right.
   * A peak must reside strictly to the right of `mid`. We shift our lower search boundary past the middle element: `low = mid + 1`.

When `low` equals `high`, the search space has collapsed down to a single valid peak index.

---

# Java Implementation

```java
class Solution {
    public int findPeakElement(int[] nums) {
        int low = 0, high = nums.length - 1;
        
        // Loop runs until low and high converge onto a single peak index
        while (low < high) {
            int mid = (low + high) / 2;
            
            // Condition 1: Check if the slope is falling to the right
            if (nums[mid] > nums[mid + 1]) {
                // Peak lies to the left or is the mid element itself
                high = mid;
            } 
            // Condition 2: The slope is rising to the right instead
            else {
                // Peak lies strictly to the right of the current mid position
                low = mid + 1;
            }
        }
        
        // low and high have converged to a valid peak element index
        return low;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(log N) where N is the total length of the `nums` array. At each iteration step, the slope validation rules eliminate exactly half of the remaining index positions, ensuring a logarithmic execution profile.
* **Space Complexity:** O(1) auxiliary space. All structural comparisons execute completely in-place, tracking ranges using only primitive pointer variables.
