# Search Insert Position

## Problem Statement

Given a sorted array of distinct integers `nums` and a `target` value, return the index if the `target` is found. If not, return the index where it would be if it were inserted in order.

You must write an algorithm with **O(log n)** runtime complexity.

---

## Examples

### Example 1

```text
Input: nums =, target = 5
Output: 2
```

---

### Example 2

```text
Input: nums =, target = 2
Output: 1
```

### Explanation

```text
2 is not found in the array. If it were inserted in order, it would be placed 
at index 1, shifting the remaining elements to the right.
```

---

### Example 3

```text
Input: nums =, target = 7
Output: 4
```

### Explanation

```text
7 is larger than all elements in the array. 
Therefore, it would be appended at the very end of the array at index 4.
```

---

# Key Property: The Lower Bound Equivalence

```text
Target Found      ---> Return mid
Target Not Found  ---> Return First Element > Target (Lower Bound)
```

The problem of finding the search insert position matches the exact mathematical definition of a **Lower Bound**. 
* If the `target` exists in the array, we return its exact index position.
* If the `target` does not exist, the index where it should be inserted corresponds to the index of the **first element that is strictly greater than the target**. If no such element exists (because the target is larger than everything), the insertion position becomes the length of the array (`nums.length`).

---

# Intuition

We maintain two pointers, `low` and `high`, to anchor our active search window, along with an insertion index tracking variable `val` initialized to `nums.length`.

At each step of our binary search loop:
1. Find the middle element index: `mid = (low + high) / 2`.
2. **Case 1:** `target == nums[mid]`. The target is found! We can return `mid` immediately.
3. **Case 2:** `target < nums[mid]`. The middle element is strictly greater than our target. This makes `mid` a potential insertion index candidate if the target doesn't exist.
   * We record this candidate position: `val = mid`.
   * We move our upper boundary to search the left half for an even smaller matching element: `high = mid - 1`.
4. **Case 3:** `target > nums[mid]`. The middle element is smaller than the target, so the insertion slot must lie strictly to its right. We shift our lower boundary: `low = mid + 1`.

If the loop terminates without an exact match, `val` contains the correct index position where the element should be inserted.

---

# Java Implementation

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int low = 0;
        int high = nums.length - 1;
        
        // Default position if the target is larger than all elements in the array
        int val = nums.length;
        
        while (low <= high) {
            int mid = (low + high) / 2;
            
            // Case 1: Target found
            if (target == nums[mid]) {
                return mid;
            } 
            // Case 2: Target is smaller, current mid is a potential insert candidate
            else if (target < nums[mid]) {
                val = mid;       // Record candidate index
                high = mid - 1;  // Look left for smaller values
            } 
            // Case 3: Target is larger, discard the left half
            else {
                low = mid + 1;   // Look right for larger values
            }
        }
        return val;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(log N) where N is the total length of the `nums` array. The remaining search window is cut exactly in half at each iteration step, ensuring a clean logarithmic runtime profile.
* **Space Complexity:** O(1) auxiliary space. The calculation executes completely in-place, maintaining only a few local primitive integer variables (`low`, `high`, `mid`, and `val`).
