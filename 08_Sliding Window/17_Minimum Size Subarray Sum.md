# Minimum Size Subarray Sum

## Problem Statement

Given an array of positive integers `nums` and a positive integer `target`, return the **minimal length** of a contiguous subarray whose sum is **greater than or equal to** `target`. If there is no such subarray, return `0` instead.

---

## Examples

### Example 1

```text
Input: target = 7, nums = [2, 3, 1, 2, 4, 3]
Output: 2
```

### Explanation

```text
The subarray [4, 3] has the minimal length of 2 under the problem constraints 
because its sum is 7, which is >= target.
```

---

### Example 2

```text
Input: target = 4, nums = [1, 4, 4]
Output: 1
```

### Explanation

```text
The subarrays [4] or [4] have a length of 1 and their sum satisfies the criteria.
```

---

### Example 3

```text
Input: target = 11, nums = [1, 1, 1, 1, 1, 1, 1, 1]
Output: 0
```

### Explanation

```text
The sum of the entire array is 8, which is less than 11. No valid subarray exists.
```

---

# Key Concept: Dynamic Sliding Window

Unlike fixed-size sliding windows, this problem requires a **variable-size / dynamic sliding window**. 

We expand the window until a certain criteria is achieved, and then try to aggressively contract it from the left to find the absolute minimal size that still honors the given condition.

```text
[ Window Start (left) ... Window End (right) ] ---> Expands right, shrinks left
```

---

# Intuition

We traverse the array using the `right` pointer to expand our window step-by-step:
1. Include the current element in our rolling `sum`: `sum += nums[right]`.

### Case 1: Sum is less than target
If `sum < target`, our current window doesn't satisfy the condition. We do nothing and let the `right` pointer continue expanding the window in the next iteration.

---

### Case 2: Sum is greater than or equal to target
The moment `sum >= target`, the window becomes valid. 
1. We calculate the active window length: `right - left + 1`.
2. Update our global running minimum length tracking boundary: `minLen`.
3. Try to optimize and shrink the window from the left by subtracting `nums[left]` from `sum` and advancing the `left` pointer forward (`left++`).
4. We repeat this check using a `while` loop, contracting as much as possible until the rolling sum drops below the target threshold.

---

# Visualization

Tracking the dynamic window on `nums = [2, 3, 1, 2, 4, 3]` with `target = 7`:

```text
1. Expand right (indices 0 to 3):, 4, 3  -> sum = 8 (>= 7). Valid!
   - minLen = min(MAX, 3 - 0 + 1) = 4
   - Shrink left: remove 2 -> sum = 6 (< 7). Loop breaks. left pointer is now at 1.

2. Expand right to index 4 (Value = 4):
   2, [3, 1, 2, 4], 3  -> sum = 10 (>= 7). Valid!
   - minLen = min(4, 4 - 1 + 1) = 4
   - Shrink left: remove 3 -> sum = 7 (>= 7). Still Valid!
   - minLen = min(4, 4 - 2 + 1) = 3
   - Shrink left: remove 1 -> sum = 6 (< 7). Loop breaks. left pointer is now at 3.

3. Expand right to index 5 (Value = 3):
   2, 3, 1, [2, 4, 3]  -> sum = 9 (>= 7). Valid!
   - minLen = min(3, 5 - 3 + 1) = 3
   - Shrink left: remove 2 -> sum = 7 (>= 7). Still Valid!
   - minLen = min(3, 5 - 4 + 1) = 2  <-- New Minimum Found!
   - Shrink left: remove 4 -> sum = 3 (< 7). Loop breaks.

Final Return Value: 2
```

---

# Java Implementation

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int left = 0, sum = 0, minLen = Integer.MAX_VALUE;

        // Traverse the array to expand the window using the right pointer
        for (int right = 0; right < nums.length; right++) {
            sum += nums[right];

            // Aggressively shrink the window from the left as long as the condition is satisfied
            while (sum >= target) {
                minLen = Math.min(minLen, right - left + 1);
                sum -= nums[left];
                left++;
            }
        }

        // If minLen was never updated, it means no valid subarray exists
        return minLen == Integer.MAX_VALUE ? 0 : minLen;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total length of the `nums` array. Although a nested `while` loop exists inside the linear `for` loop, each element is visited at most twice—once by the `right` pointer expanding the window, and at most once by the `left` pointer contracting it. This ensures a strict linear runtime profile.
* **Space Complexity:** O(1) auxiliary space. The computation maintains an in-place window structure requiring only a few primitive integer counters.
