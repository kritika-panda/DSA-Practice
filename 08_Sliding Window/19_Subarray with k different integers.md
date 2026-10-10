# Subarrays with K Different Integers

## Problem Statement

Given an integer array `nums` and an integer `k`, return the number of **good subarrays** of `nums`.

A **good subarray** is defined as a contiguous part of the array that contains **exactly** `k` distinct integers.

---

## Examples

### Example 1

```text
Input: nums =, k = 2  
Output: 7
```

### Explanation

```text
There are 7 subarrays containing exactly 2 distinct integers:, [2, 1], [1, 2], [2, 3], [1, 2, 1], [2, 1, 2], and.
```

---

### Example 2

```text
Input: nums =, k = 3  
Output: 3
```

### Explanation

```text
There are 3 subarrays containing exactly 3 distinct integers:, [2, 1, 3], and.
```

---

# Key Concept: Subarray Arithmetic via At-Most Strategy

Counting subarrays with **exactly** `k` distinct elements using a standard sliding window is challenging. As the window expands, the number of distinct elements grows monotonically, but a window can remain valid with an exact number of distinct elements even as it shrinks or shifts, which makes calculating exact boundaries complex.

Instead, we solve this using the **At-Most Strategy**:
```text
Exact(k) = AtMost(k) - AtMost(k - 1)
```

* `AtMost(k)` counts all subarrays containing anywhere from `0` up to `k` distinct integers.
* `AtMost(k - 1)` counts all subarrays containing anywhere from `0` up to `k - 1` distinct integers.
* The difference between these two values gives the exact count of subarrays with precisely `k` distinct integers.

---

# Intuition

We implement a helper function `atMostK(nums, k)` that manages a dynamic sliding window bounded by `left` and `right` pointers. 

As the `right` pointer expands the window forward:
1. We add `nums[right]` to a frequency **HashMap** tracking unique elements.

### Window Contraction Phase
If `freq.size() > k`, the window contains too many distinct integers. We continuously shrink the window from the left by decrementing the frequency of `nums[left]`. If an element's frequency drops to `0`, we remove it entirely from the map. We advance `left` until `freq.size() <= k`.

### Subarray Contribution
Once the window ending at index `right` becomes valid, the total number of valid subarrays ending at `right` is exactly equal to the length of the window:
```text
res += right - left + 1
```
This adds the single element at `right`, along with every valid extension reaching back to the current `left` pointer index.

---

# Java Implementation

```java
import java.util.*;

class Solution {
    public int subarraysWithKDistinct(int[] nums, int k) {
        // The difference yields the exact count matching target k
        return atMostK(nums, k) - atMostK(nums, k - 1);
    }

    // Helper method to count subarrays with at most k distinct integers
    private int atMostK(int[] nums, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        int left = 0, res = 0;

        // Traverse the array to expand the window using the right pointer
        for (int right = 0; right < nums.length; right++) {
            freq.put(nums[right], freq.getOrDefault(nums[right], 0) + 1);

            // Shrink the window from the left if distinct element count exceeds k
            while (freq.size() > k) {
                freq.put(nums[left], freq.get(nums[left]) - 1);
                if (freq.get(nums[left]) == 0) {
                    freq.remove(nums[left]);
                }
                left++;
            }

            // The number of valid subarrays ending at right is the window size
            res += right - left + 1;
        }
        return res;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total length of the `nums` array. The algorithm calls `atMostK` twice, resulting in two independent scans. Within each call, both the `left` and `right` pointers move strictly forward across the index frames, ensuring that every element is added and removed from the HashMap at most once.
* **Space Complexity:** O(N) auxiliary space. In the worst-case scenario, the HashMap inside `atMostK` can store up to N distinct elements if all numbers in the input array are unique.
