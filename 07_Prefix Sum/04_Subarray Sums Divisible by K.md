# Subarray Sums Divisible by K

## Problem Statement

Given an integer array `nums` and an integer `k`, return the *number of non-empty subarrays* that have a sum divisible by `k`.

A **subarray** is a contiguous part of an array.

---

## Examples

### Example 1

```text
Input: nums = [4, 5, 0, -2, -3, 1], k = 5
Output: 7
```

### Explanation

```text
There are 7 subarrays with a sum divisible by k = 5:
1. [4, 5, 0, -2, -3, 1] (sum = 5)
2. [5] (sum = 5)
3. [5, 0] (sum = 5)
4. [5, 0, -2, -3] (sum = 0)
5. [0] (sum = 0)
6. [0, -2, -3] (sum = -5)
7. [-2, -3] (sum = -5)
```

---

### Example 2

```text
Input: nums = [-1, 2, 9], k = 2
Output: 2
```

### Explanation

```text
The 2 valid subarrays are [2] (sum = 2) and [-1, 2, 9] (sum = 10).
```

---

# Key Mathematical Property

```text
If PrefixSum[j] % k == PrefixSum[i] % k, then the sum of subarray nums[i+1...j] is divisible by k.
```

This is derived from modular arithmetic:
If a cumulative sum up to index `j` leaves the same remainder as a cumulative sum up to index `i`, subtracting the two sums cancels out the remainder. The remaining difference (the sum of the elements between `i` and `j`) must be perfectly divisible by `k`.

---

# Intuition

Instead of checking every subarray combination in \(O(N^2)\) time, we use a **Prefix Sum paired with a Frequency Map** to track remainders in a single linear pass.

### Handling Negative Remainders
In Java, the modulo operator can return a negative value if the prefix sum is negative (e.g., `-2 % 5 = -2`). Mathematically, a remainder of `-2` under modulo `5` is equivalent to a positive remainder of `3` (since \(-2 + 5 = 3\)). To normalize all remainders to a positive range `[0, k - 1]`, we apply the adjustment formula:
```text
remainder = ((prefixSum % k) + k) % k;
```

---

# Step-by-Step Algorithm
1. **Initialize Map:** Add `{0: 1}` to a `remainderCount` HashMap to natively capture valid subarrays that start exactly from index `0`.
2. **Scan Array:** Loop through the elements while tracking a running `prefixSum`.
3. **Normalize Remainder:** Calculate the normalized positive remainder of the current `prefixSum`.
4. **Accumulate & Update:** Check how many times this specific remainder has been seen before. Add that frequency to `count`, then increment the remainder's frequency inside the HashMap.

---

# Java Implementation

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int subarraysDivByK(int[] nums, int k) {
        // HashMap to store frequencies of prefix sum remainders
        Map<Integer, Integer> remainderCount = new HashMap<>();
        
        // Base case: To handle subarrays starting from index 0
        remainderCount.put(0, 1); 

        int prefixSum = 0, count = 0;

        for (int num : nums) {
            prefixSum += num;
            
            // Normalize to handle negative remainders properly in Java
            int remainder = ((prefixSum % k) + k) % k; 

            // If this remainder was seen before, it forms valid divisible subarrays
            count += remainderCount.getOrDefault(remainder, 0);
            
            // Update the frequency map with the current remainder
            remainderCount.put(remainder, remainderCount.getOrDefault(remainder, 0) + 1);
        }

        return count;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total length of the `nums` array. The algorithm traverses the array in a single linear pass. Checking and updating the HashMap keys takes O(1) average time per element.
* **Space Complexity:** O(min(N, k)) auxiliary space. The HashMap stores at most `k` unique remainder records (ranging from `0` to `k-1`) or `N` unique entries if the array length is shorter than `k`.
