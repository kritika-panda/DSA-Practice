# Find Minimum in Rotated Sorted Array

## Problem Statement

Suppose an array of length `n` sorted in ascending order is **rotated** between `1` and `n` times. For example, the array `nums = [0,1,2,4,5,6,7]` might become:
* `[4,5,6,7,0,1,2]` if it was rotated `4` times.
* `[0,1,2,4,5,6,7]` if it was rotated `7` times.

Notice that rotating an array `[a[0], a[1], a[2], ..., a[n-1]]` 1 time results in the array `[a[n-1], a[0], a[1], a[2], ..., a[n-2]]`.

Given the sorted rotated array `nums` of **unique** elements, return the **minimum element** of this array.

You must write an algorithm that runs in **O(log n)** time.

---

## Examples

### Example 1

```text
Input: nums = [3,4,5,1,2]
Output: 1
```

### Explanation

```text
The original array was [1,2,3,4,5] rotated 3 times.
```

---

### Example 2

```text
Input: nums = [4,5,6,7,0,1,2]
Output: 0
```

### Explanation

```text
The original array was [0,1,2,4,5,6,7] and it was rotated 4 times.
```

---

### Example 3

```text
Input: nums = [11,13,15,17]
Output: 11
```

### Explanation

```text
The original array was [11,13,15,17] and it was rotated 4 times (or not rotated).
```

---

# Key Property: The Inflection Point Pivot

```text
If nums[mid] > nums[high] ---> The inflection point (minimum) lies strictly to the right.
If nums[mid] <= nums[high] ---> The right side is normally sorted; mid itself could be the minimum.
```

Because the array consists of unique elements and was originally sorted in ascending order, we can locate the minimum value by comparing our midpoint `mid` against the rightmost boundary `high`. 

Rotation splits the array into a normally sorted increasing half and an out-of-order half containing a sharp drop (the inflection point). The smallest element in the entire array sits directly at the bottom of this drop.

---

# Intuition

We maintain two pointers, `low` and `high`, to anchor our active search window. We run the loop while `low < high` so that the pointers converge perfectly on a single unique element without getting stuck in an infinite cycle.

At each step inside the loop:
1. Find the midpoint: `mid = (low + high) / 2`.
2. **Case A: `nums[mid] > nums[high]`**
   * The middle element is larger than the right boundary element. This tells us that the array drops somewhere to the right of `mid`. 
   * The minimum element cannot be `nums[mid]` or anything to its left. We shift our lower search boundary past the middle element: `low = mid + 1`.
3. **Case B: `nums[mid] <= nums[high]`**
   * The middle element is less than or equal to the right boundary element. This indicates that the right half `nums[mid...high]` is normally sorted in ascending order.
   * The minimum element must reside either at `mid` itself or somewhere to its left. We contract our search window by pulling the upper boundary directly to the midpoint: `high = mid`.

When `low` equals `high`, the search window has collapsed down to the exact index of the minimum element. We return `nums[low]`.

---

# Java Implementation

```java
class Solution {
    public int findMin(int[] nums) {
        int low = 0, high = nums.length - 1;
        
        // Loop runs until low and high converge onto the single minimum element
        while (low < high) {
            int mid = (low + high) / 2;
            
            // If the midpoint value is strictly greater than the rightmost value,
            // the pivot drop point lies in the right unsorted partition.
            if (nums[mid] > nums[high]) {
                low = mid + 1; // Discard mid and the left side
            } 
            // Otherwise, the right side is sorted normally, meaning mid
            // could be the minimum, or the minimum lies to the left.
            else {
                high = mid;    // Maintain mid as a candidate, discard the right side
            }
        }
        
        // low and high have converged to the index of the minimum element
        return nums[low];
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(log N) where N is the total length of the `nums` array. The remaining search pool is cut exactly in half at every iteration step, ensuring a clean logarithmic execution profile.
* **Space Complexity:** O(1) auxiliary space. All structural checks execute completely in-place using only primitive pointer boundary indices.
