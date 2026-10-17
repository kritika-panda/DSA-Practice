# Longest Subarray with Sum K

**GFG / Classic Interview Problem - Longest Subarray with Sum K**

## Problem Statement

Given an array of integers `nums` (which may contain **negative numbers, zeroes, and positive numbers**) and an integer `k`, return the **length of the longest subarray** whose elements sum to exactly `k`.

A **subarray** is a contiguous, non-empty sequence of elements within the array.

---

## Example

```java
int[] nums = {10, 5, 2, 7, 1, 9};
int k = 15;

Solution sol = new Solution();
sol.longestSubarrayWithSumK(nums, k); // Returns 4
```

**Explanation:** The longest subarray whose elements sum to `15` is `[5, 2, 7, 1]` (indices `1` to `4`). Therefore, its length is `4`.

---

# Intuition

A brute force approach would compute the sum of every possible subarray by running two nested loops over all start and end index pairs. This costs `O(N²)` time — unacceptable for large inputs.

For arrays containing **only non-negative numbers**, a **sliding window** (two-pointer) technique achieves `O(N)` time by expanding the right boundary and shrinking the left boundary whenever the running sum overshoots `k`. However, this breaks down the moment **negative numbers** appear — shrinking the window no longer guarantees a smaller sum, making it impossible to reason about boundary movements correctly.

To handle all cases — including negatives — in linear time, we use a **Prefix Sum with a HashMap**.

The key observation is:

> If the prefix sum up to index `j` minus the prefix sum up to index `i` equals `k`, then the subarray from index `i + 1` to index `j` has sum exactly `k`.

Formally:

$$\text{prefixSum}[j] - \text{prefixSum}[i] = k \implies \text{sum}(nums[i+1 \ldots j]) = k$$

By storing each **first-seen prefix sum value and its index** inside a HashMap, we can check in `O(1)` whether the required complementary prefix (`prefixSum - k`) was seen before. The earlier that complement was seen, the longer the valid subarray — so we always record the **first** occurrence of each prefix sum to maximize subarray length.

---

# Approach: Prefix Sum + HashMap Optimization

## Data Structure

```java
Map<Long, Integer> prefixMap
```
Used to store the **first index** at which each prefix sum value was seen, allowing O(1) lookups to find valid subarray left boundaries.

---

## Algorithm Steps

1. Initialize `prefixSum = 0` and seed the map with `prefixMap.put(0, -1)` — representing that a prefix sum of `0` exists before the array starts (at virtual index `-1`).
2. Iterate through each index `i` from `0` to `N - 1`, accumulating `prefixSum += nums[i]`.
3. At each index `i`, check if `prefixSum - k` exists in `prefixMap`:
   - If it **does**, a valid subarray ends at `i` and starts at `prefixMap.get(prefixSum - k) + 1`. Compute its length as `i - prefixMap.get(prefixSum - k)` and update `maxLength` if this is larger.
   - If it **does not**, no valid subarray ending at `i` with sum `k` has been found yet.
4. If `prefixSum` has **not been seen before**, store it in `prefixMap` with its current index `i`. We do **not** overwrite existing entries — the earlier (smaller-index) occurrence always yields a longer subarray.
5. Return `maxLength` after the full traversal.

---

# Dry Run

### Input
```java
nums = [10, 5, 2, 7, 1, 9],  k = 15
```

### Execution Trace

- **Initialization:** `prefixSum = 0`, `maxLength = 0`, `prefixMap = {0: -1}`

| Index `i` | `nums[i]` | `prefixSum` | `prefixSum - k` | In Map? | Action | `maxLength` |
|---|---|---|---|---|---|---|
| — | — | `0` | — | — | Seed map: `{0: -1}` | `0` |
| `0` | `10` | `10` | `10 - 15 = -5` | No | Store `10 → 0`. Map: `{0:-1, 10:0}` | `0` |
| `1` | `5` | `15` | `15 - 15 = 0` | ✅ Yes (at -1) | Length = `1 - (-1) = 2`. Store `15 → 1`. | `2` |
| `2` | `2` | `17` | `17 - 15 = 2` | No | Store `17 → 2`. | `2` |
| `3` | `7` | `24` | `24 - 15 = 9` | No | Store `24 → 3`. | `2` |
| `4` | `1` | `25` | `25 - 15 = 10` | ✅ Yes (at 0) | Length = `4 - 0 = 4`. Store `25 → 4`. | `4` |
| `5` | `9` | `34` | `34 - 15 = 19` | No | Store `34 → 5`. | `4` |

### Result
```java
maxLength = 4   // Subarray [5, 2, 7, 1] at indices [1..4] sums to 15
```

---

# Java Solution

```java
import java.util.*;

class Solution {
    public int longestSubarrayWithSumK(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return 0;
        }

        // Stores first-seen index of each prefix sum value
        // Using Long to safely handle large cumulative sums without overflow
        Map<Long, Integer> prefixMap = new HashMap<>();

        // Seed with prefix sum 0 at virtual index -1 (before the array starts)
        // Handles the case where the entire prefix from index 0 to i sums to k
        prefixMap.put(0L, -1);

        long prefixSum = 0;
        int maxLength = 0;

        for (int i = 0; i < nums.length; i++) {
            prefixSum += nums[i];

            // Check if a subarray ending at i with sum k exists
            // That subarray starts right after the index where (prefixSum - k) was first seen
            long complement = prefixSum - k;
            if (prefixMap.containsKey(complement)) {
                int subarrayLength = i - prefixMap.get(complement);
                maxLength = Math.max(maxLength, subarrayLength);
            }

            // Only record the FIRST occurrence of each prefix sum
            // An earlier index gives a longer valid subarray for any future complement match
            if (!prefixMap.containsKey(prefixSum)) {
                prefixMap.put(prefixSum, i);
            }
        }

        return maxLength;
    }
}
```

---

# Complexity Analysis

### Time Complexity

```java
O(N)
```
The array is traversed exactly **once** in a single left-to-right pass. At each index, computing the running prefix sum costs `O(1)`, checking the HashMap for the complement costs `O(1)` average time, and inserting a new prefix sum entry costs `O(1)` amortized time. No nested loops or repeated revisits occur at any index, keeping the full traversal strictly linear.

### Space Complexity

```java
O(N)
```
In the worst case, every prefix sum encountered across the `N` indices is distinct (no repeated prefix sum values), requiring `N + 1` entries inside the HashMap (including the seeded `{0: -1}` entry). The auxiliary space usage therefore scales linearly with the input size.

---

# Comparison of Approaches

| Approach | Time Complexity | Space Complexity | Handles Negatives? | Pros / Cons |
|---|---|---|---|---|
| **Brute Force** (nested loops) | \(O(N^2)\) | \(O(1)\) | ✅ Yes | Simple to implement, but fails for large inputs due to quadratic runtime. |
| **Sliding Window** (two pointers) | \(O(N)\) | \(O(1)\) | ❌ No | Optimal time and space, but **only valid for non-negative arrays**. Breaks when negatives are present. |
| **Prefix Sum + HashMap** (Optimal) | \(O(N)\) | \(O(N)\) | ✅ Yes | Meets optimal time bound for all input types. Requires auxiliary memory. **Preferred interview answer.** |

---

# Key Takeaways

- Use a **Prefix Sum HashMap** to convert the expensive \(O(N^2)\) subarray-sum search into an immediate `O(1)` complement lookup, achieving full linear-time correctness across all integer types — including negatives.
- **Seed `prefixMap` with `{0: -1}` before the loop.** This single initialization handles the critical edge case where the valid subarray begins at index `0` — without it, subarrays spanning from the very start of the array would be missed entirely.
- **Never overwrite existing map entries.** Storing only the **first** occurrence of each prefix sum is what guarantees we always recover the longest valid subarray for every future complement match.
- Core concepts:
  - Prefix Sums
  - HashMap Complement Lookups
  - Subarray Boundary Recovery via Index Arithmetic
- Expected interview preference: **Prefix Sum + HashMap Approach**.

