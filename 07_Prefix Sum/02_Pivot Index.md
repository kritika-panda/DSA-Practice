# Find Pivot Index

## Problem Statement

Given an array of integers `nums`, calculate the **pivot index** of this array.

### Definition

The **pivot index** is the index where the sum of all the numbers strictly to the left of the index is equal to the sum of all the numbers strictly to the right of the index.

If the index is on the left edge of the array, the left sum is `0` because there are no elements to the left. This also applies to the right edge of the array.

Return the **leftmost pivot index**. If no such index exists, return `-1`.

---

## Examples

### Example 1

```text
Input: nums = [1, 7, 3, 6, 5, 6]
Output: 3
```

### Explanation

```text
The pivot index is 3.
Left sum = nums[0] + nums[1] + nums[2] = 1 + 7 + 3 = 11
Right sum = nums[4] + nums[5] = 5 + 6 = 11
Since Left Sum == Right Sum (11 == 11), index 3 is the pivot.
```

---

### Example 2

```text
Input: nums = [1, 2, 3]
Output: -1
```

### Explanation

```text
There is no index that satisfies the conditions in the array.
```

---

### Example 3

```text
Input: nums = [2, 1, -1]
Output: 0
```

### Explanation

```text
The pivot index is 0.
Left sum = 0 (no elements to the left of index 0)
Right sum = nums[1] + nums[2] = 1 + (-1) = 0
```

---

# Key Concept: Prefix Sum Balance

```text
[ Left Sum ] + [ Pivot Element ] + [ Right Sum ] = Total Sum
```

Instead of running two inner loops to independently compute the left and right sums for each position, we can optimize this mathematically. If we know the total sum of the array, the right sum at any index `i` can be derived dynamically:

```text
Right Sum = Total Sum - Left Sum - nums[i]
```

---

# Intuition

1. **Phase 1 (Total Sum Calculation):** Run an initial linear scan to calculate the aggregate `totalSum` of all elements in the array.
2. **Phase 2 (Equilibrium Check):** Iterate through the array from left to right while maintaining a running `leftSum` tracker (initially `0`). At each index `i`:
   * Check if `leftSum == totalSum - leftSum - nums[i]`.
   * If it matches, we have found the leftmost pivot index. Return `i` immediately.
   * If it does not match, add the current element to our running total (`leftSum += nums[i]`) and move to the next index.

---

# Java Implementation

```java
class Solution {
    public int pivotIndex(int[] nums) {
        int totalSum = 0;
        int leftSum = 0;

        // Step 1: Calculate the total sum of the entire array
        for (int num : nums) {
            totalSum += num;
        }

        // Step 2: Iterate and check for the equilibrium pivot position
        for (int i = 0; i < nums.length; i++) {
            // Right sum is mathematically derived from totalSum and leftSum
            if (leftSum == totalSum - leftSum - nums[i]) {
                return i; // Return immediately to guarantee the leftmost index
            }
            leftSum += nums[i];
        }

        return -1;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total length of the `nums` array. The algorithm uses two consecutive independent loops. The first loop calculates the sum in O(N) time, and the second loop finds the pivot point in O(N) time.
* **Space Complexity:** O(1) auxiliary space. The calculation runs entirely in-place using only two local primitive integer tracking variables (`totalSum` and `leftSum`).
