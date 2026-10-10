# Single Element in a Sorted Array

## Problem Statement

You are given a sorted array consisting of integers where every element appears exactly twice, except for one element which appears exactly once.

Return the **single element** that appears only once.

Your solution must run in **O(log n)** time complexity and **O(1)** space complexity.

---

## Examples

### Example 1

```text
Input: nums = [1, 1, 2, 3, 3, 4, 4, 8, 8]
Output: 2
```

---

### Example 2

```text
Input: nums = [3, 3, 7, 7, 10, 11, 11]
Output: 10
```

---

# Key Property: Index Parity Alignment

Before the single element appears, duplicates always start on an **even index** and end on an **odd index** `(even, odd)`. 

After the single element appears, this pattern shifts because the single element takes up exactly one slot. Every duplicate pair following the single element will now start on an **odd index** and end on an **even index** `(odd, even)`.

```text
Indices:   0   1   2   3   4   5   6   7   8
Values:   [1,  1,  2,  3,  3,  4,  4,  8,  8]
Pairs:    |--1--|  ^  |--3--|  |--4--|  |--8--|
Pattern:  (E,  O) Single (O,  E) (O,  E) (O,  E)
```

By checking the index parity of a duplicate pair at `mid`, we can determine whether the single element lies to our left or to our right.

---

# Intuition

### Step 1: Handle Edge Cases Upfront
We check array boundaries manually to simplify the core binary search loop and avoid out-of-bounds validations:
* If the array has only 1 element, return it.
* If the first element does not equal the second, the first element is the single element.
* If the last element does not equal the second-to-last, the last element is the single element.

### Step 2: Binary Search Partitioning
We set our search space `low` and `high` between the adjusted boundaries. At each step:
1. Find the midpoint: `mid = (low + high) / 2`.
2. **Target Check:** If `nums[mid]` is different from both `nums[mid - 1]` and `nums[mid + 1]`, then `nums[mid]` is the single element. Return it.
3. **Left-Half Verification (Valid Alignment):**
   * If `mid` is **odd** and `nums[mid] == nums[mid - 1]`, or
   * If `mid` is **even** and `nums[mid] == nums[mid + 1]`
   * This confirms we are still in the normal `(even, odd)` zone before the single element. The unique element must lie strictly to the right, so we move right: `low = mid + 1`.
4. **Right-Half Verification (Shifted Alignment):** If the parity rule is violated, we have crossed past the single element into the `(odd, even)` zone. The unique element must lie to the left, so we move left: `high = mid - 1`.

---

# Java Implementation

```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int low = 0;
        int n = nums.length - 1;
        int high = n;
        
        // Edge Case 1: Array has only a single element
        if (nums.length == 1) {
            return nums[0];
        }
        // Edge Case 2: The single element is at the very beginning
        if (nums[1] != nums[0]) {
            return nums[0];
        }
        // Edge Case 3: The single element is at the very end
        if (nums[n - 1] != nums[n]) {
            return nums[n];
        }

        // Perform Binary Search on the remaining interior elements
        while (low <= high) {
            int mid = (low + high) / 2;
            
            // If the element doesn't match either neighbor, it is the unique element
            if (mid > 0 && mid < n && nums[mid] != nums[mid - 1] && nums[mid] != nums[mid + 1]) {
                return nums[mid];
            }
            
            // Check if we are in the left half (pre-single element zone)
            // Pattern: (Even Index == Next Element) OR (Odd Index == Previous Element)
            if ((mid % 2 != 0 && mid > 0 && nums[mid] == nums[mid - 1]) || 
                (mid % 2 == 0 && mid < n && nums[mid] == nums[mid + 1])) {
                low = mid + 1; // Single element lies to the right
            } 
            // We are in the right half (post-single element zone)
            else {
                high = mid - 1; // Single element lies to the left
            }
        }
        return -1;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(log N) where N is the total length of the `nums` array. The search pool is cut exactly in half at each iteration step, ensuring a logarithmic runtime profile.
* **Space Complexity:** O(1) auxiliary space. The calculation runs entirely in-place using only primitive loop indices.
