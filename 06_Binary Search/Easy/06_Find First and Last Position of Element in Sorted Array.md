# Find First and Last Position of Element in Sorted Array

## Problem Statement

Given an array of integers `nums` sorted in non-decreasing order, find the starting and ending position of a given `target` value.

If `target` is not found in the array, return `[-1, -1]`.

You must write an algorithm with **O(log n)** runtime complexity.

---

## Examples

### Example 1

```text
Input: nums =, target = 8
Output: [3, 4]
```

---

### Example 2

```text
Input: nums =, target = 6
Output: [-1, -1]
```

### Explanation

```text
6 does not exist in the array, so its first and last positions are returned as -1.
```

---

### Example 3

```text
Input: nums = [], target = 0
Output: [-1, -1]
```

---

# Key Property: Modified Binary Search Strategy

```text
First Occurrence: nums[mid] == target ---> Record index, move high = mid - 1 (look left)
Last Occurrence:  nums[mid] == target ---> Record index, move low = mid + 1  (look right)
```

Because the array is already sorted, a classic binary search can find the target efficiently. However, since the target can appear multiple times consecutively, finding the exact boundaries requires modifying the search behavior:
* When looking for the **first occurrence**, finding a match does not stop the search. We record the index as a candidate and shift the search boundary to the **left half** (`high = mid - 1`) to find earlier duplicates.
* When looking for the **last occurrence**, we record the index as a candidate and shift the search boundary to the **right half** (`low = mid + 1`) to find later duplicates.

---

# Intuition

We divide the problem into two separate binary search routines:

### 1. `findFirst` Function
* We maintain an answer tracker variable `val` initialized to `-1`.
* During standard binary search iterations, if `nums[mid] == target`, we have found a valid match. We record it (`val = mid`) and continue searching left (`high = mid - 1`) to see if it starts even earlier.
* Standard binary search rules apply if `nums[mid]` is strictly greater or less than the target.

### 2. `findLast` Function
* We maintain an answer tracker variable `val` initialized to `-1`.
* If `nums[mid] == target`, we record the position (`val = mid`) and continue searching right (`low = mid + 1`) to check for later duplicate entries.
* Standard binary search rules apply if `nums[mid]` is strictly greater or less than the target.

---

# Java Implementation

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int[] res = new int[]{-1, -1};
        
        // Find the boundary bounds independently
        res[0] = findFirst(nums, target);
        res[1] = findLast(nums, target);
        
        return res;
    }
    
    // Binary search routine to find the leftmost index of the target
    public static int findFirst(int[] nums, int target) {
        int low = 0;
        int val = -1;
        int high = nums.length - 1;
        
        while (low <= high) {
            int mid = (low + high) / 2;
            
            if (nums[mid] == target) {
                val = mid;       // Record match candidate index
                high = mid - 1;  // Keep looking left for earlier occurrences
            } else if (nums[mid] > target) {
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return val;
    }
    
    // Binary search routine to find the rightmost index of the target
    public static int findLast(int[] nums, int target) {
        int low = 0;
        int val = -1;
        int high = nums.length - 1;
        
        while (low <= high) {
            int mid = (low + high) / 2;
            
            if (nums[mid] == target) {
                val = mid;       // Record match candidate index
                low = mid + 1;   // Keep looking right for later occurrences
            } else if (nums[mid] > target) {
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return val;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(log N) where N is the total number of elements in the `nums` array. The algorithm runs two independent binary searches. Each search divides the remaining search pool strictly in half at every step, yielding an optimal logarithmic runtime profile.
* **Space Complexity:** O(1) auxiliary space. The calculation runs completely in-place using only a few local primitive variables, ignoring the fixed O(1) space allocated for the output result array.
