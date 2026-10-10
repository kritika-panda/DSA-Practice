# Contiguous Array

## Problem Statement

Given a binary array `nums` (containing only `0`s and `1`s), return the **maximum length** of a contiguous subarray with an **equal number of `0`s and `1`s**.

---

## Examples

### Example 1

```text
Input: nums = [0, 1]
Output: 2
```

### Explanation

```text
The entire array [0, 1] is a contiguous subarray with an equal number of 0s and 1s.
Maximum length = 2.
```

---

### Example 2

```text
Input: nums = [0, 1, 0]
Output: 2
```

### Explanation

```text
The valid subarrays are [0, 1] (from index 0 to 1) or [1, 0] (from index 1 to 2).
Both have a length of 2. Maximum length = 2.
```

---

# Key Concept: Balancing and Index Tracking

```text
Treat '0' as -1 and '1' as +1.
The problem then transforms into: Find the longest subarray whose sum equals 0.
```

If we transform the array mentally by treating every `0` as a `-1` and every `1` as a `+1`, a subarray with an equal number of `0`s and `1`s will have a net sum of exactly `0`. 

From prefix sum properties, if `PrefixSum[j] == PrefixSum[i]`, it means the net sum of elements between indices `i` and `j` is exactly `0`. To maximize the length of this window (`j - i`), we only want to track the **earliest index** where each prefix sum configuration was encountered.

---

# Intuition

1. **Initialize Map:** Create a `sumMap` HashMap to record `{prefixSum: earliest_index}`. Add `{0: -1}` to naturally compute the length of valid subarrays that start exactly from index `0`.
2. **Scan Array:** Loop through the array from left to right. Maintain a running `count` variable. Add `+1` if the current number is `1`, and add `-1` if the current number is `0`.
3. **Evaluate State:**
   * If the current `count` value is already present in the map, it means the exact same balance existed at an earlier index `i`. A net sum of `0` was achieved in between. Calculate the window size: `current_index - sumMap.get(count)`. Update our global `maxLen`.
   * If the current `count` value is completely new, store it alongside the current index: `sumMap.put(count, current_index)`. We do *not* overwrite existing entries to ensure we keep the earliest index possible for maximizing the window length.

---

# Java Implementation

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int findMaxLength(int[] nums) {
        // HashMap to store the earliest index of each cumulative count/sum balance
        Map<Integer, Integer> sumMap = new HashMap<>();
        
        // Base case: To handle valid subarrays starting exactly from index 0
        sumMap.put(0, -1);

        int count = 0;
        int maxLen = 0;

        for (int i = 0; i < nums.length; i++) {
            // Treat 1 as +1 and 0 as -1
            if (nums[i] == 1) {
                count += 1;
            } else {
                count -= 1;
            }

            // If this cumulative sum balance has been seen before, a valid subarray exists
            if (sumMap.containsKey(count)) {
                // Calculate window length and update maxLen if it's larger
                maxLen = Math.max(maxLen, i - sumMap.get(count));
            } else {
                // Store only the earliest index of this cumulative sum balance
                sumMap.put(count, i);
            }
        }

        return maxLen;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total length of the `nums` array. The algorithm processes each element exactly once in a single linear pass. Key lookups and insertion operations inside the HashMap execute in O(1) average time.
* **Space Complexity:** O(N) auxiliary space. In the worst-case scenario (such as an array containing all `0`s or all `1`s), the `sumMap` HashMap will store up to N distinct running sum balances.
