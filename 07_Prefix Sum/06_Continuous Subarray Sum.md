# Continuous Subarray Sum

## Problem Statement

Given an integer array `nums` and an integer `k`, return `true` if `nums` has a **good subarray**, or `false` otherwise.

A **good subarray** is defined as a contiguous part of the array that meets the following conditions:
1. Its length is **at least two**.
2. The sum of the elements in the subarray is a **multiple of `k`** (i.e., the sum is an integer multiple of `k`, including `0` if the sum is `0`).

---

## Examples

### Example 1

```text
Input: nums =, k = 6
Output: true
```

### Explanation

```text
The subarray [2, 4] is a continuous subarray of length 2 whose elements sum up to 6.
Since 6 is a multiple of 6 (6 * 1 = 6), the condition is satisfied.
```

---

### Example 2

```text
Input: nums =, k = 6
Output: true
```

### Explanation

```text
The subarray [23, 2, 6, 4, 7] has a sum of 42.
Since 42 is a multiple of 6 (6 * 7 = 42), the entire array forms a valid subarray of length 5.
```

---

### Example 3

```text
Input: nums =, k = 13
Output: false
```

---

# Key Mathematical Property

```text
If PrefixSum[j] % k == PrefixSum[i] % k and (j - i) >= 2, a valid continuous multiple subarray exists.
```

This is derived from modular arithmetic:
If a cumulative sum up to index `j` leaves the exact same remainder as a cumulative sum up to index `i`, subtracting the two prefixes cancels out the remainder. The remaining subsegment `nums[i+1...j]` must sum up to a perfect multiple of `k`. 

Unlike standard subarray sum problems, we do not track the *frequency* of the remainders. Instead, we map each remainder to its **earliest seen index** to verify that the length constraint of `(j - i) >= 2` is honored.

---

# Intuition

Instead of evaluating all combinations in O(N²) time, we use a **Prefix Sum paired with an Index HashMap** to track remainders in a single linear pass.

1. **Initialize Map:** Add `{0: -1}` to a `remainderMap` HashMap. Setting the index of remainder `0` to `-1` naturally handles valid multiple subarrays that start exactly from index `0`.
2. **Scan Array:** Loop through the elements while tracking a running `prefixSum`.
3. **Compute Remainder:** Calculate the remainder using `prefixSum % k`. If `k` can be negative, normalize the remainder to a positive range `[0, |k| - 1]`.
4. **Evaluate Match:** 
   * If the remainder is already present in our map, it means the same remainder occurred at an earlier index `i`. Check if `current_index - i >= 2`. If true, return `true` immediately.
   * If the remainder is new, store it along with the current index: `remainderMap.put(remainder, current_index)`. We do *not* overwrite existing entries, as we want to preserve the earliest index to maximize the window length.

---

# Java Implementation

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public boolean checkSubarraySum(int[] nums, int k) {
        // HashMap to store the earliest index of each prefix sum remainder
        Map<Integer, Integer> remainderMap = new HashMap<>();
        
        // Base case: To handle valid subarrays starting from index 0
        remainderMap.put(0, -1);

        int prefixSum = 0;

        for (int i = 0; i < nums.length; i++) {
            prefixSum += nums[i];
            
            // Calculate remainder
            int remainder = prefixSum;
            if (k != 0) {
                remainder = prefixSum % k;
                // Handle negative remainders if input contains negative values
                if (remainder < 0) {
                    remainder += Math.abs(k);
                }
            }

            // If the remainder has been seen before, check the length constraint
            if (remainderMap.containsKey(remainder)) {
                if (i - remainderMap.get(remainder) >= 2) {
                    return true;
                }
            } else {
                // Store only the earliest occurrence of this remainder
                remainderMap.put(remainder, i);
            }
        }

        return false;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total length of the `nums` array. The algorithm processes each element exactly once in a single linear pass. Map lookup and insertion operations run in O(1) average time.
* **Space Complexity:** O(min(N, |k|)) auxiliary space. The `remainderMap` stores at most `|k|` unique remainders or `N` entries if the array size is smaller than `k`.
