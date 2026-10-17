# Count Subarrays with Given Sum

**LeetCode 560 - Subarray Sum Equals K**

## Problem Statement

Given an array of integers `nums` (which may contain **negative numbers, zeroes, and positive numbers**) and an integer `k`, return the **total number of subarrays** whose elements sum to exactly `k`.

A **subarray** is a contiguous, non-empty sequence of elements within the array.

---

## Example

```java
int[] nums = {1, 1, 1};
int k = 2;

Solution sol = new Solution();
sol.subarraySum(nums, k); // Returns 2
```

**Explanation:** The valid subarrays whose elements sum to `2` are `[1, 1]` from index 0 to 1, and `[1, 1]` from index 1 to 2. Therefore, the total count is `2`.

---

# Intuition

A brute force approach would compute the sum of every possible subarray by running two nested loops over all start and end index pairs. This costs `O(N²)` time — unacceptable for large inputs.

For arrays containing only non-negative numbers, a sliding window (two-pointer) technique is commonly used to find sum-based subarrays. However, since the array elements can be negative, shrinking or expanding a window does not provide a monotonic, predictable relationship with the sum. Thus, a sliding window breaks down.

To handle all cases — including negatives and zeroes — in linear time, we use a **Prefix Sum with a HashMap**.

The key observation is:

> If the prefix sum up to index `j` minus the prefix sum up to index `i` equals `k`, then the subarray from index `i + 1` to index `j` has a sum exactly equal to `k`.

Formally:

\[\text{prefixSum}[j] - \text{prefixSum}[i] = k \implies \text{prefixSum}[j] - k = \text{prefixSum}[i]\]

By storing each **prefix sum value and its frequency** inside a HashMap, we can check in `O(1)` how many times the required complementary prefix (`prefixSum - k`) has occurred before. Every time that complement was seen in the past, it marks the start of a valid subarray ending at the current index. We add that frequency count to our total count.

---

# Approach: Prefix Sum + HashMap Optimization

## Data Structure

```java
Map<Long, Integer> prefixMap
```
Used to store the **frequency** of each prefix sum value encountered so far, allowing O(1) lookups to instantly count matching valid subarray left boundaries.

---

## Algorithm Steps

1. Initialize `prefixSum = 0`, `count = 0`, and seed the map with `prefixMap.put(0L, 1)` — representing that a prefix sum of `0` has occurred exactly once before the array starts (at virtual index `-1`).
2. Iterate through each index `i` from `0` to `N - 1`, accumulating `prefixSum += nums[i]`.
3. At each index `i`, check if `prefixSum - k` exists in `prefixMap`:
   - If it **does**, add the frequency of `prefixSum - k` from the map directly to `count`. This represents the number of valid subarrays ending at `i` that sum to `k`.
4. Update the map by adding the current `prefixSum` value to it (increment its existing frequency by `1`, or set it to `1` if it is seen for the first time).
5. Return `count` after completing the full traversal.

---

# Dry Run

### Input
```java
nums = [3, 4, 7, 2, -3, 1, 4, 2],  k = 7
```

### Execution Trace

- **Initialization:** `prefixSum = 0`, `count = 0`, `prefixMap = {0: 1}`

| Index `i` | `nums[i]` | `prefixSum` | `prefixSum - k` | Found in Map? | Count Update | Map State (`prefixSum : freq`) |
|---|---|---|---|---|---|---|
| — | — | `0` | — | — | — | Seed map: `{0: 1}` |
| `0` | `3` | `3` | `3 - 7 = -4` | No | `0` | `{0:1, 3:1}` |
| `1` | `4` | `7` | `7 - 7 = 0` | ✅ Yes (freq: 1) | `0 + 1 = 1` | `{0:1, 3:1, 7:1}` |
| `2` | `7` | `14` | `14 - 7 = 7` | ✅ Yes (freq: 1) | `1 + 1 = 2` | `{0:1, 3:1, 7:1, 14:1}` |
| `3` | `2` | `16` | `16 - 7 = 9` | No | `2` | `{0:1, 3:1, 7:1, 14:1, 16:1}` |
| `4` | `-3` | `13` | `13 - 7 = 6` | No | `2` | `{0:1, 3:1, 7:1, 14:1, 16:1, 13:1}` |
| `5` | `1` | `14` | `14 - 7 = 7` | ✅ Yes (freq: 1) | `2 + 1 = 3` | `{0:1, 3:1, 7:1, 14:2, 16:1, 13:1}` |
| `6` | `4` | `18` | `18 - 7 = 11` | No | `3` | `{0:1, 3:1, 7:1, 14:2, 16:1, 13:1, 18:1}` |
| `7` | `2` | `20` | `20 - 7 = 14` | ✅ Yes (freq: 2) | `3 + 2 = 5` | `{0:1, 3:1, 7:1, 14:2, ... 20:1}` |

### Result
```java
count = 5   // Subarrays are: [3, 4], [7], [7, 2, -3, 1], [-3, 1, 4, 2], [1, 4, 2]
```

---

# Java Solution

```java
import java.util.*;

class Solution {
    public int subarraySum(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return 0;
        }

        // Stores the frequency of each prefix sum value
        // Using Long to safely handle large cumulative sums without overflow
        Map<Long, Integer> prefixMap = new HashMap<>();

        // Seed with prefix sum 0 with a frequency of 1
        // Handles cases where the prefix from index 0 to i sums perfectly to k
        prefixMap.put(0L, 1);

        long prefixSum = 0;
        int count = 0;

        for (int i = 0; i < nums.length; i++) {
            prefixSum += nums[i];

            // Check if a complementary prefix sum exists
            long complement = prefixSum - k;
            if (prefixMap.containsKey(complement)) {
                count += prefixMap.get(complement);
            }

            // Record/Increment the frequency of the current prefix sum
            prefixMap.put(prefixSum, prefixMap.getOrDefault(prefixSum, 0) + 1);
        }

        return count;
    }
}
```

---

# Complexity Analysis

### Time Complexity

```java
O(N)
```
The array is traversed exactly **once** in a single left-to-right pass. At each index, computing the running prefix sum costs `O(1)`, checking the HashMap for the entry costs `O(1)` average time, and updating the frequency maps costs `O(1)` amortized time. No nested loops or repeated revisits occur at any index, keeping the full traversal strictly linear.

### Space Complexity

```java
O(N)
```
In the worst case, every prefix sum encountered across the `N` indices is distinct (no repeated prefix sum values), requiring `N + 1` entries inside the HashMap (including the seeded `{0: 1}` entry). The auxiliary space usage therefore scales linearly with the input size.

---

# Comparison of Approaches

| Approach | Time Complexity | Space Complexity | Handles Negatives? | Pros / Cons |
|---|---|---|---|---|
| **Brute Force** (nested loops) | \(O(N^2)\) | \(O(1)\) | ✅ Yes | Simple to implement, but fails for large inputs due to quadratic runtime. |
| **Prefix Sum + HashMap** (Optimal) | \(O(N)\) | \(O(N)\) | ✅ Yes | Meets optimal time bound for all input types. Requires auxiliary memory. **Preferred interview answer.** |

---

# Key Takeaways

- Use a **Prefix Sum HashMap** to convert the expensive \(O(N^2)\) subarray search into an immediate `O(1)` complement lookup, achieving full linear-time correctness across all integer types — including negatives.
- **Seed `prefixMap` with `{0: 1}` before the loop.** This single initialization handles the critical edge case where the valid subarray begins at index `0` (i.e., `prefixSum == k`) — without it, subarrays spanning from the very start of the array would be missed entirely.
- **Track Frequencies, Not Just Existence.** Unlike finding the *longest* subarray where we only save the first index, here we track *frequencies* because multiple historical indices can share the same prefix sum, each opening up a unique valid subarray path.
- Core concepts:
  - Prefix Sums
  - HashMap Complement Lookups
  - Frequency Counting Matrix Algorithms
- Expected interview preference: **Prefix Sum + HashMap Frequency Approach**.
