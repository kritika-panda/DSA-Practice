# Count Number of Nice Subarrays

## Problem Statement

Given an array of integers `nums` and an integer `k`. A continuous subarray is called **nice** if there are exactly `k` odd numbers in it.

Return the number of **nice subarrays**.

---

## Examples

### Example 1

```text
Input: nums =, k = 3
Output: 2
```

Because:

```text
The only subarrays containing exactly 3 odd numbers are:
1. [1, 1, 2, 1] (from index 0 to 3)
2. [1, 2, 1, 1] (from index 1 to 4)
```

---

### Example 2

```text
Input: nums =, k = 1
Output: 0
```

Because:

```text
There are no odd numbers present in the array, making it impossible to form a nice subarray.
```

---

### Example 3

```text
Input: nums =, k = 2
Output: 16
```

---

# Key Concept: Subarray Arithmetic via At-Most Strategy

Finding a subarray with **exactly** `k` properties using a standard sliding window is difficult because matching exact targets does not yield a monotonic condition. 

Instead, we can solve this using the **At-Most Strategy**:
```text
Exact(k) = AtMost(k) - AtMost(k - 1)
```

* `AtMost(k)` counts all subarrays that contain anywhere from `0` up to `k` odd numbers.
* `AtMost(k - 1)` counts all subarrays that contain anywhere from `0` up to `k - 1` odd numbers.
* Subtracting the two gives the exact count of subarrays containing precisely `k` odd numbers.

---

# Intuition

We implement a helper function `countAtMost(nums, k)` that manages a classic two-pointer sliding window (`left` and `right`).

As the `right` pointer expands the window forward:

### Case 1: Odd element added
If `nums[right]` is an odd number (`nums[right] % 2 != 0`), it consumes our budget of allowed odd numbers. We decrement our remaining capacity tracking limit: `k--`.

---

### Case 2: Window becomes invalid (Too many odd numbers)
If `k < 0`, the current window contains more than the allowed amount of odd numbers. 

We contract the window from the left by advancing the `left` pointer. If the element leaving the window at index `left` is odd, it frees up our capacity budget, so we increment `k++`. We repeat this until `k >= 0` and the window is valid again.

---

### Counting Subarrays
Once the window ending at index `right` is valid, the total number of valid subarrays ending at `right` is exactly equal to the length of the window:
```text
res += (right - left + 1)
```

---

# Java Implementation

```java
import java.util.*;

class Solution {
    // Helper function to count subarrays with at most k odd numbers
    public int countAtMost(int[] nums, int k) {
        int left = 0, res = 0;

        // Traverse through the array using right pointer
        for (int right = 0; right < nums.length; right++) {
            // If current number is odd, reduce allowed budget k
            if (nums[right] % 2 != 0) {
                k--;
            }

            // Shrink the window from left until k becomes valid (k >= 0)
            while (k < 0) {
                if (nums[left] % 2 != 0) {
                    k++;
                }
                left++;
            }

            // Add the count of all valid subarrays ending at the right pointer
            res += (right - left + 1);
        }

        return res;
    }

    // Function to return number of subarrays with exactly k odd numbers
    public int numberOfSubarrays(int[] nums, int k) {
        // The difference yields the exact count matching target k
        return countAtMost(nums, k) - countAtMost(nums, k - 1);
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the length of the `nums` array. The algorithm runs `countAtMost` twice, resulting in two independent linear scans. Within each scan, both `left` and `right` pointers move strictly forward, visiting each element at most once.
* **Space Complexity:** O(1) auxiliary space. The calculation uses only a few primitive integer tracking indicators, making its memory footprint completely constant regardless of input size.
