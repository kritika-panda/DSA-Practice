# Binary Search

## Problem Statement

Given an array of integers `nums` which is sorted in ascending order, and an integer `target`, write a function to search `target` in `nums`. If `target` exists, then return its index. Otherwise, return `-1`.

You must write an algorithm with **O(log n)** runtime complexity.

---

## Examples

### Example 1

```text
Input: nums = [-1, 0, 3, 5, 9, 12], target = 9
Output: 4
```

### Explanation

```text
9 exists in nums and its index is 4.
```

---

### Example 2

```text
Input: nums = [-1, 0, 3, 5, 9, 12], target = 2
Output: -1
```

### Explanation

```text
2 does not exist in nums so return -1.
```

---

# Key Property: Sorted Search Space Division

```text
[ Low Index ] ---------> [ Mid Index ] <--------- [ High Index ]
```

Because the array is already sorted in **ascending order**, we do not need to scan every element linearly. By comparing our `target` with the middle element (`mid`), we can discard half of the remaining search space at every single step, allowing us to find the element in logarithmic time.

---

# Intuition

We maintain two pointers, `low` and `high`, representing the boundaries of our active search window.

At every step of the `while (low <= high)` loop:
1. Find the middle element index: `mid = (low + high) / 2`.
2. **Case 1:** `target == nums[mid]`. We found the element! Return its index `mid` immediately.
3. **Case 2:** `target < nums[mid]`. Because the array is sorted, if the target is smaller than the middle element, it must reside in the left half. We shrink our search space by shifting the upper boundary: `high = mid - 1`.
4. **Case 3:** `target > nums[mid]`. If the target is larger than the middle element, it must reside in the right half. We shrink our search space by shifting the lower boundary: `low = mid + 1`.

If the loop terminates and `low` crosses `high` without finding a match, the target is not present in the array. Return `-1`.

---

## Technical Note: Integer Overflow Avoidance

While the expression `(low + high) / 2` works perfectly for smaller arrays, it can cause an **integer overflow bug** in languages like Java or C++ if the sum of `low` and `high` exceeds `Integer.MAX_VALUE` (2³¹ - 1). 

To make your code production-safe for exceptionally massive arrays, it is best practice to rewrite the midpoint calculation like this:
```text
int mid = low + (high - low) / 2;
```

---

# Java Implementation

```java
class Solution {
    public int search(int[] nums, int target) {
        int low = 0;
        int high = nums.length - 1;
        
        while (low <= high) {
            // Standard midpoint calculation
            int mid = (low + high) / 2;
            
            // Check if the target matches the midpoint element
            if (target == nums[mid]) {
                return mid;
            } 
            // Target is smaller, discard the right half
            else if (target < nums[mid]) {
                high = mid - 1;
            } 
            // Target is larger, discard the left half
            else {
                low = mid + 1;
            }
        }
        
        // Target was not found in the array
        return -1;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(log N) where N is the total number of elements in the `nums` array. At each iteration of the loop, the remaining search window is cut exactly in half, resulting in a logarithmic runtime profile.
* **Space Complexity:** O(1) auxiliary space. The calculation executes completely in-place using only three primitive integer pointer variables (`low`, `high`, and `mid`).
