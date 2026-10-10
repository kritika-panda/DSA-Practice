# Search in Rotated Sorted Array

## Problem Statement

There is an integer array `nums` sorted in ascending order (with **distinct** values).

Prior to being passed to your function, `nums` is possibly rotated at an unknown pivot index `k` (`1 <= k < nums.length`) such that the resulting array becomes `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]` (**0-indexed**). For example, `[0,1,2,4,5,6,7]` might be rotated at pivot index `4` and become `[4,5,6,7,0,1,2]`.

Given the array `nums` **after** the rotation and an integer `target`, return the index of `target` if it is in `nums`, or `-1` if it is not in `nums`.

You must write an algorithm with **O(log n)** runtime complexity.

---

## Examples

### Example 1

```text
Input: nums =, target = 0
Output: 4
```

---

### Example 2

```text
Input: nums =, target = 3
Output: -1
```

---

### Example 3

```text
Input: nums =, target = 0
Output: -1
```

---

# Key Property: Local Sortedness At Every Split

```text
Rotation breaks global sortedness, but it preserves local sortedness in at least one half at every split.
```

If you pick any element in a rotated sorted array as a midpoint (`mid`), it will split the array into two halves. Because the array was originally sorted and rotated only once, **at least one of these two halves is guaranteed to be perfectly sorted**. 

```text
Array: [4, 5, 6, 7, 0, 1, 2]  ---> mid = 7 (Index 3)
Left Half:   [4, 5, 6, 7]     ---> Sorted (nums[low] <= nums[mid])
Right Half:  [0, 1, 2]        ---> Sorted (but contains the rotation drop)
```

By identifying which half is sorted, we can check if the target falls within that sorted range. If it does, we search that half; otherwise, we can confidently eliminate it and search the opposite half.

---

# Intuition

We maintain two pointers, `low` and `high`, to track our active binary search window.

At each step inside the loop:
1. Find the midpoint: `mid = (low + high) / 2`.
2. **Target Found:** If `nums[mid] == target`, return `mid` immediately.
3. **Determine Sorted Half:**
   * **Case A: Left Half is Sorted** (`nums[low] <= nums[mid]`)
     * Check if the target is bounded inside this sorted segment: `nums[low] <= target && target < nums[mid]`.
     * If true, move left: `high = mid - 1`.
     * If false, the target must be in the right half: `low = mid + 1`.
   * **Case B: Right Half is Sorted** (`nums[low] > nums[mid]`)
     * Check if the target is bounded inside this sorted segment: `nums[mid] < target && target <= nums[high]`.
     * If true, move right: `low = mid + 1`.
     * If false, the target must be in the left half: `high = mid - 1`.

If the pointers cross and the target was never hit, return `-1`.

---

# Java Implementation

```java
class Solution {
    public int search(int[] nums, int target) {
        int low = 0;
        int high = nums.length - 1;
        
        // Continue while there is still a valid search range
        while (low <= high) {
            int mid = (low + high) / 2;
            
            // If target found, return index
            if (nums[mid] == target) {
                return mid;
            }
            
            // Condition 1: Check if the left part is perfectly sorted
            if (nums[low] <= nums[mid]) {
                // Check if the target lies within the sorted left boundaries
                if (nums[low] <= target && target < nums[mid]) {
                    high = mid - 1; // Search left
                } else {
                    low = mid + 1;  // Search right
                }
            } 
            // Condition 2: The right part must be perfectly sorted instead
            else {
                // Check if the target lies within the sorted right boundaries
                if (nums[mid] < target && target <= nums[high]) {
                    low = mid + 1;  // Search right
                } else {
                    high = mid - 1; // Search left
                }
            }
        }
        return -1;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(log N) where N is the total length of the `nums` array. At each step of the binary search loop, we discard exactly half of the search pool based on range boundary validations, ensuring a logarithmic execution profile.
* **Space Complexity:** O(1) auxiliary space. The matching operations run entirely in-place without using extra linear collections.
