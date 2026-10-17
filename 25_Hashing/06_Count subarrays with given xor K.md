# Count Subarrays with Given XOR K

**InterviewBit / Classic Advanced Interview Problem - Subarrays with Given XOR**

## Problem Statement

Given an array of integers `nums` and an integer `k`, return the **total number of subarrays** whose bitwise XOR sum is exactly equal to `k`.

A **subarray** is a contiguous, non-empty sequence of elements within the array.

---

## Example

```java
int[] nums = {4, 2, 2, 6, 4};
int k = 6;

Solution sol = new Solution();
sol.subarrayXor(nums, k); // Returns 4
```

**Explanation:** The valid subarrays whose elements XOR up to `6` are:
1. `[4, 2]` from index 0 to 1 (`4 ^ 2 = 6`)
2. `[6]` from index 3 to 3 (`6`)
3. `[2, 2, 6]` from index 1 to 3 (`2 ^ 2 ^ 6 = 6`)
4. `[4, 2, 2, 6, 4]` from index 0 to 4 (`4 ^ 2 ^ 2 ^ 6 ^ 4 = 6`)

Therefore, the total count is `4`.

---

# Intuition

A brute force approach would compute the XOR sum of every possible subarray by running two nested loops over all start and end index pairs. This costs `O(N²)` time — unacceptable for large inputs.

To optimize this to linear time, we can apply an idea similar to the **Prefix Sum** technique. Instead of keeping a prefix addition sum, we keep a **Prefix XOR** sum. 

The bitwise XOR operator has a powerful mathematical property:
* If \(A \oplus B = C\), then \(A \oplus C = B\) and \(B \oplus C = A\).

Let `xr` be the cumulative XOR sum from index `0` up to the current index `j`. If there exists a prefix subarray from index `0` to `i` (where `i < j`) with a cumulative XOR sum of `Y`, then the XOR sum of the remaining subarray from index `i + 1` to `j` will be equal to `k` if and only if:

\[\text{xr} \oplus Y = k \implies Y = \text{xr} \oplus k\]

By storing each **Prefix XOR value and its frequency** inside a HashMap, we can check in `O(1)` how many times the required complementary prefix XOR (`xr ^ k`) has occurred before. Every time that complement was seen in the past, it marks the start of a valid subarray ending at the current index.

---

# Approach: Prefix XOR + HashMap Optimization

## Data Structure

```java
Map<Integer, Integer> xorMap
```
Used to store the **frequency** of each prefix XOR value encountered so far, allowing O(1) lookups to instantly count matching valid subarray left boundaries.

---

## Algorithm Steps

1. Initialize `xr = 0`, `count = 0`, and seed the map with `xorMap.put(0, 1)` — representing that a prefix XOR of `0` has occurred exactly once before the array starts (at virtual index `-1`).
2. Iterate through each index `i` from `0` to `N - 1`, updating the cumulative XOR: `xr = xr ^ nums[i]`.
3. At each index `i`, calculate the required target complement: `target = xr ^ k`.
4. Check if `target` exists in `xorMap`:
   - If it **does**, add the frequency of `target` from the map directly to `count`.
5. Update the map by adding the current `xr` value to it (increment its existing frequency by `1`, or set it to `1` if it is seen for the first time).
6. Return `count` after completing the full traversal.

---

# Dry Run

### Input
```java
nums =,  k = 6
```

### Execution Trace

- **Initialization:** `xr = 0`, `count = 0`, `xorMap = {0: 1}`

| Index `i` | `nums[i]` | `xr` (Prefix XOR) | `target = xr ^ k` | Found in Map? | Count Update | Map State (`xr : freq`) |
|---|---|---|---|---|---|---|
| — | — | `0` | — | — | — | Seed map: `{0: 1}` |
| `0` | `4` | `4` | `4 ^ 6 = 2` | No | `0` | `{0:1, 4:1}` |
| `1` | `2` | `4 ^ 2 = 6` | `6 ^ 6 = 0` | ✅ Yes (freq: 1) | `0 + 1 = 1` | `{0:1, 4:1, 6:1}` |
| `2` | `2` | `6 ^ 2 = 4` | `4 ^ 6 = 2` | No | `1` | `{0:1, 4:2, 6:1}` |
| `3` | `6` | `4 ^ 6 = 2` | `2 ^ 6 = 4` | ✅ Yes (freq: 2) | `1 + 2 = 3` | `{0:1, 4:2, 6:1, 2:1}` |
| `4` | `4` | `2 ^ 4 = 6` | `6 ^ 6 = 0` | ✅ Yes (freq: 1) | `3 + 1 = 4` | `{0:1, 4:2, 6:2, 2:1}` |

### Result
```java
count = 4   // Subarrays match our analytical list exactly!
```

---

# Java Solution

```java
import java.util.*;

class Solution {
    public int subarrayXor(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return 0;
        }

        // Stores the frequency of each prefix XOR value
        Map<Integer, Integer> xorMap = new HashMap<>();

        // Seed with prefix XOR 0 with a frequency of 1
        // Handles cases where the prefix from index 0 to i yields exactly k
        xorMap.put(0, 1);

        int xr = 0;
        int count = 0;

        for (int i = 0; i < nums.length; i++) {
            xr = xr ^ nums[i];

            // Check if a complementary prefix XOR exists
            int target = xr ^ k;
            if (xorMap.containsKey(target)) {
                count += xorMap.get(target);
            }

            // Record/Increment the frequency of the current prefix XOR
            xorMap.put(xr, xorMap.getOrDefault(xr, 0) + 1);
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
The array is traversed exactly **once** in a single pass. At each index, calculating the cumulative bitwise XOR costs `O(1)`, checking the HashMap costs `O(1)` average time, and updating map frequencies takes `O(1)` amortized time. This keeps the execution strictly linear.

### Space Complexity

```java
O(N)
```
In the worst case where every prefix XOR combination evaluates to a distinct integer value, the map can store up to `N + 1` entries. The auxiliary space usage scales linearly with input size.

---

# Comparison of Approaches

| Approach | Time Complexity | Space Complexity | Pros / Cons |
|---|---|---|---|
| **Brute Force** (nested loops) | O(N²) | O(1) | Simple to implement, but encounters TLE on large arrays. |
| **Prefix XOR + HashMap** (Optimal) | O(N) | O(N) | Leverages XOR self-inversion properties to run optimally in linear time. |

---

# Key Takeaways

- **XOR Identity Property:** The underlying math relies on \(A \oplus B = C \iff A \oplus C = B\). This property allows us to convert searching for elements into tracking past prefix boundaries.
- **Seed `xorMap` with `{0: 1}`:** Essential for acknowledging a full subarray starting cleanly from index `0`.
- **Track Frequencies:** Just like subsegment summation counts, always accumulate frequency metrics because multiple past indices could hold matching prefix properties due to canceling XOR loops.
- Core concepts:
  - Bitwise XOR Operations
  - Prefix Bitmanipulation Trackers
  - HashMap Complement Inversions
- Expected interview preference: **Prefix XOR + HashMap Approach**.
