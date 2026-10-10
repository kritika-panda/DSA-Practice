# Subarray Product Less Than K

## Problem Statement

Given an array of integers `nums` and an integer `k`, return the *number of contiguous subarrays* where the product of all the elements in the subarray is strictly **less than `k`**.

---

## Examples

### Example 1

```text
Input: nums =, k = 100
Output: 8
```

### Explanation

```text
The 8 subarrays that have a product less than 100 are:, [5], [2], [6], [10, 5], [5, 2], [2, 6], and.
Note that [10, 5, 2] is not included because its product is 100, which is not strictly less than k.
```

---

### Example 2

```text
Input: num =, k = 0
Output: 0
```

---

# Key Concept: Subarray Counting via Sliding Window

```text
[ Window Start (left) ... Window End (right) ]
```

Because all elements in the array are positive integers, multiplying a new element into our window always causes the product to grow monotonically. This allows us to use a **dynamic sliding window** technique to find all valid ranges efficiently.

When a window ending at a specific `right` index is valid, the number of new valid subarrays introduced by adding `nums[right]` is exactly equal to the window's current length:
```text
new_subarrays = right - left + 1
```
This count includes the single element `[nums[right]]`, as well as every larger contiguous combination extending back to the `left` pointer index.

---

# Intuition

We track our active subsegments using two pointers, `left` and `right`, along with a rolling multiplier tracker `prod`.

### Edge Case Handling
If `k <= 1`, it is mathematically impossible to find a valid subarray of positive integers whose product is strictly less than `k`. We can immediately return `0`.

---

### Step-by-Step Window Updates
As the `right` pointer moves forward:
1. Multiply the new element into our running product: `prod *= nums[right]`.
2. **Contraction Phase:** If `prod >= k`, the window is invalid. We continuously divide `prod` by `nums[left]` and advance `left++` until the product falls strictly below `k`.
3. **Accumulation Phase:** Once the window is brought back to a valid state, we calculate its current length (`right - left + 1`) and add it to our cumulative `count` variable.

---

# Visualization

Tracking the dynamic product window on `nums = [10, 5, 2, 6]` with `k = 100`:

```text
1. At right = 0 (Value = 10):
   - prod = 10 (< 100). Valid window:.
   - count += (0 - 0 + 1) -> count = 1.

2. At right = 1 (Value = 5):
   - prod = 10 * 5 = 50 (< 100). Valid window:.
   - Subarrays added: [5] and.
   - count += (1 - 0 + 1) -> count = 1 + 2 = 3.

3. At right = 2 (Value = 2):
   - prod = 50 * 2 = 100 (>= 100). Invalid!
   - Shrink left: prod /= nums[0] (100 / 10 = 10). left becomes 1.
   - Now prod = 10 (< 100). Valid window:.
   - Subarrays added: [2] and.
   - count += (2 - 1 + 1) -> count = 3 + 2 = 5.

4. At right = 3 (Value = 6):
   - prod = 10 * 6 = 60 (< 100). Valid window:.
   - Subarrays added:, [2, 6], and.
   - count += (3 - 1 + 1) -> count = 5 + 3 = 8.

Final Return Value: 8
```

---

# Java Implementation

```java
class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        // Edge case: product of positive numbers cannot be less than or equal to 1
        if (k <= 1) {
            return 0;
        }

        int prod = 1;
        int left = 0;
        int count = 0;

        // Expand the window using the right pointer
        for (int right = 0; right < nums.length; right++) {
            prod *= nums[right];

            // Shrink the window from the left until the product is strictly less than k
            while (prod >= k) {
                prod /= nums[left];
                left++;
            }

            // The number of valid subarrays ending at the right index 
            // is exactly equal to the length of the current window
            count += right - left + 1;
        }

        return count;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total number of elements in the `nums` array. Although a nested `while` loop handles the product reduction step, both the `left` and `right` pointers move strictly forward across the index frames. Each element is processed at most twice, yielding a strict linear runtime profile.
* **Space Complexity:** O(1) auxiliary space. The calculation runs in-place, relying only on a few primitive local integer tracking variables.
