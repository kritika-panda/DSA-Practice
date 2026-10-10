# Subarray Sum Equals K

## Problem Statement

Given an array of integers `nums` and an integer `k`, return the *total number of contiguous subarrays* whose sum is exactly equal to `k`.

A **subarray** is a contiguous part of an array.

---

## Examples

### Example 1

```text
Input: nums =, k = 2
Output: 2
```

### Explanation

```text
The two valid subarrays that sum up to 2 are:
1. [1, 1, _] (from index 0 to 1)
2. [_, 1, 1] (from index 1 to 2)
```

---

### Example 2

```text
Input: nums =, k = 3
Output: 2
```

### Explanation

```text
The two valid subarrays that sum up to 3 are:
1. [1, 2, _] (sum = 3)
2. [_, _, 3] (sum = 3)
```

---

# Key Mathematical Property

```text
If PrefixSum[j] - PrefixSum[i] == k, then the sum of the subarray nums[i+1...j] is exactly equal to k.
```

Rearranging this algebraic equation gives:
```text
PrefixSum[i] = PrefixSum[j] - k
```
This means if we are standing at index `j` with a cumulative sum of `PrefixSum[j]`, we can look backward to see how many times a target sum of `PrefixSum[j] - k` occurred in the past. Every such occurrence marks the left boundary of a valid subarray ending at `j`.

---

# Intuition

Instead of evaluating all possible subarrays using an O(N²) nested loop configuration, we use a **Prefix Sum paired with a Frequency Map** to check for matching segments in a single linear pass.

1. **Initialize Map:** Add `{0: 1}` to a `prefixSumCount` HashMap. This natively handles instances where the cumulative `prefixSum` matches `k` perfectly from the very beginning (index 0).
2. **Scan Array:** Traverse the array while maintaining a running `prefixSum`.
3. **Check Target:** At each element, calculate the required target value: `prefixSum - k`. If this target exists in the map, add its recorded frequency to our global `count`.
4. **Update Map:** Store the current `prefixSum` inside the HashMap or increment its frequency by 1 if it has been seen before.

---

# Visualization

Tracking lookups on `nums = [1, 2, 3]` with `k = 3`:

```text
Initial: prefixSumCount = {0: 1}, prefixSum = 0, count = 0

1. At index 0 (Value = 1):
   - prefixSum = 0 + 1 = 1
   - target = prefixSum - k = 1 - 3 = -2
   - Is -2 in map? No.
   - prefixSumCount = {0: 1, 1: 1}, count = 0

2. At index 1 (Value = 2):
   - prefixSum = 1 + 2 = 3
   - target = prefixSum - k = 3 - 3 = 0
   - Is 0 in map? Yes (frequency = 1).
   - count += 1 -> count = 1
   - prefixSumCount = {0: 1, 1: 1, 3: 1}, count = 1

3. At index 2 (Value = 3):
   - prefixSum = 3 + 3 = 6
   - target = prefixSum - k = 6 - 3 = 3
   - Is 3 in map? Yes (frequency = 1).
   - count += 1 -> count = 2
   - prefixSumCount = {0: 1, 1: 1, 3: 1, 6: 1}, count = 2

Final Return Value: 2
```

---

# Java Implementation

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int subarraySum(int[] nums, int k) {
        // HashMap to store the frequency of each prefix sum encountered
        Map<Integer, Integer> prefixSumCount = new HashMap<>();
        
        // Base case: To count subarrays that sum up to k directly from index 0
        prefixSumCount.put(0, 1);

        int prefixSum = 0;
        int count = 0;

        for (int num : nums) {
            // Update the running prefix sum
            prefixSum += num;

            // If (prefixSum - k) exists in the map, it represents a valid subarray ending here
            if (prefixSumCount.containsKey(prefixSum - k)) {
                count += prefixSumCount.get(prefixSum - k);
            }

            // Record the current prefix sum in the frequency map
            prefixSumCount.put(prefixSum, prefixSumCount.getOrDefault(prefixSum, 0) + 1);
        }

        return count;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total length of the `nums` array. The algorithm traverses the array in a single linear pass. Retrieving and updating the entries inside the HashMap takes O(1) average time per element.
* **Space Complexity:** O(N) auxiliary space. In the worst-case scenario (such as an array with all positive numbers), the `prefixSumCount` HashMap will store up to N distinct prefix sum entries.
