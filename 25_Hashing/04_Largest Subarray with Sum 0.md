# Largest Subarray with Sum 0

**GFG / Classic Interview Problem - Largest Subarray with Sum 0**

## Problem Statement

Given an array of integers `nums` (which may contain **negative numbers, zeroes, and positive numbers**), return the **length of the longest subarray** whose elements sum to exactly `0`.

A **subarray** is a contiguous, non-empty sequence of elements within the array.

---

## Example

```java
int[] nums = {15, -2, 2, -8, 1, 7, 10, 23};

Solution sol = new Solution();
sol.maxLen(nums); // Returns 5
```

**Explanation:** The longest subarray whose elements sum to `0` is `[-2, 2, -8, 1, 7]` (indices `1` to `5`). Therefore, its length is `5`.

---

# Intuition

A brute force approach would compute the sum of every possible subarray by running two nested loops over all start and end index pairs. This costs `O(N²)` time — unacceptable for large inputs.

For arrays containing only non-negative numbers, a sliding window (two-pointer) technique is commonly used to find sum-based subarrays. However, since the target sum is `0` and the array elements can be negative, shrinking or expanding a window does not provide a monotonic, predictable relationship with the sum. Thus, a sliding window breaks down.

To handle all cases — including negatives and zeroes — in linear time, we use a **Prefix Sum with a HashMap**.

The key observation is:

> If the prefix sum up to index `j` equals the prefix sum up to index `i` (where `i < j`), then the subarray from index `i + 1` to index `j` has a sum exactly equal to `0`.

Formally:

\[\text{prefixSum}[j] - \text{prefixSum}[i] = 0 \implies \text{prefixSum}[j] = \text{prefixSum}[i] \implies \text{sum}(nums[i+1 \ldots j]) = 0\]

By storing each **first-seen prefix sum value and its index** inside a HashMap, we can check in `O(1)` whether the current prefix sum has been encountered before. The earlier that prefix sum was seen, the longer the valid subarray — so we always record the **first** occurrence of each prefix sum to maximize subarray length.

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
3. At each index `i`, check if `prefixSum` exists in `prefixMap`:
   - If it **does**, a valid 0-sum subarray ends at `i` and starts at `prefixMap.get(prefixSum) + 1`. Compute its length as `i - prefixMap.get(prefixSum)` and update `maxLength` if this is larger.
   - If it **does not**, no valid subarray ending at `i` with sum `0` has been found yet.
4. If `prefixSum` has **not been seen before**, store it in `prefixMap` with its current index `i`. We do **not** overwrite existing entries — the earlier (smaller-index) occurrence always yields a longer subarray.
5. Return `maxLength` after the full traversal.

---

# Dry Run

### Input
```java
nums = [15, -2, 2, -8, 1, 7, 10, 23]
```

### Execution Trace

- **Initialization:** `prefixSum = 0`, `maxLength = 0`, `prefixMap = {0: -1}`

| Index `i` | `nums[i]` | `prefixSum` | In Map? | Action | `maxLength` |
|---|---|---|---|---|---|
| — | — | `0` | — | Seed map: `{0: -1}` | `0` |
| `0` | `15` | `15` | No | Store `15 → 0`. Map: `{0:-1, 15:0}` | `0` |
| `1` | `-2` | `13` | No | Store `13 → 1`. Map: `{0:-1, 15:0, 13:1}` | `0` |
| `2` | `2` | `15` | ✅ Yes (at 0) | Length = `2 - 0 = 2`. Do not overwrite `15`. | `2` |
| `3` | `-8` | `7` | No | Store `7 → 3`. | `2` |
| `4` | `1` | `8` | No | Store `8 → 4`. | `2` |
| `5` | `7` | `15` | ✅ Yes (at 0) | Length = `5 - 0 = 5`. Update `maxLength`. | `5` |
| `6` | `10` | `25` | No | Store `25 → 6`. | `5` |
| `7` | `23` | `48` | No | Store `48 → 7`. | `5` |

### Result
```java
maxLength = 5   // Subarray [-2, 2, -8, 1, 7] at indices [1..5] sums to 0
```

---

# Java Solution

```java
import java.util.*;

class Solution {
    public int maxLen(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }

        // Stores first-seen index of each prefix sum value
        // Using Long to safely handle large cumulative sums without overflow
        Map<Long, Integer> prefixMap = new HashMap<>();

        // Seed with prefix sum 0 at virtual index -1 (before the array starts)
        // Handles the case where the entire prefix from index 0 to i sums to 0
        prefixMap.put(0L, -1);

        long prefixSum = 0;
        int maxLength = 0;

        for (int i = 0; i < nums.length; i++) {
            prefixSum += nums[i];

            // Check if this prefix sum has been encountered before
            if (prefixMap.containsKey(prefixSum)) {
                int subarrayLength = i - prefixMap.get(prefixSum);
                maxLength = Math.max(maxLength, subarrayLength);
            }

            // Only record the FIRST occurrence of each prefix sum
            // An earlier index gives a longer valid subarray for any future matching prefix sum
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
The array is traversed exactly **once** in a single left-to-right pass. At each index, computing the running prefix sum costs `O(1)`, checking the HashMap for the entry costs `O(1)` average time, and inserting a new prefix sum entry costs `O(1)` amortized time. No nested loops or repeated revisits occur at any index, keeping the full traversal strictly linear.

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
| **Prefix Sum + HashMap** (Optimal) | \(O(N)\) | \(O(N)\) | ✅ Yes | Meets optimal time bound for all input types. Requires auxiliary memory. **Preferred interview answer.** |

---

# Key Takeaways

- Use a **Prefix Sum HashMap** to convert the expensive \(O(N^2)\) subarray-sum search into an immediate `O(1)` lookup, achieving full linear-time correctness across all integer types — including negatives.
- **Seed `prefixMap` with `{0: -1}` before the loop.** This single initialization handles the critical edge case where the valid subarray begins at index `0` (i.e., the total sum up to index `i` is exactly `0`) — without it, subarrays spanning from the very start of the array would be missed entirely.
- **Never overwrite existing map entries.** Storing only the **first** occurrence of each prefix sum is what guarantees we always recover the longest valid subarray for every future match.
- Core concepts:
  - Prefix Sums
  - HashMap Frequency/Index Tracking
  - Subarray Boundary Recovery via Index Arithmetic
- Expected interview preference: **Prefix Sum + HashMap Approach**.
