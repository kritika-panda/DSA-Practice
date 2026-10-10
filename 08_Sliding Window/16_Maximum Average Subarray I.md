# Maximum Average Subarray I

## Problem Statement

You are given an integer array `nums` consisting of `n` elements, and an integer `k`.

Find a contiguous subarray whose **length is equal to `k`** that has the **maximum average value** and return this value. Any answer with a calculation error less than \(10^{-5}\) will be accepted.

---

## Examples

### Example 1

```text
Input: nums = [1, 12, -5, -6, 50, 3], k = 4
Output: 12.75000
```

### Explanation

```text
The subarray [12, -5, -6, 50] has the maximum sum of 51.
The maximum average value is 51 / 4 = 12.75.
```

---

### Example 2

```text
Input: nums =, k = 1
Output: 5.00000
```

---

# Key Concept: Fixed-Size Sliding Window

Instead of recalculating the sum of \(k\) consecutive elements from scratch for every possible starting position, which would take \(O(n \times k)\) time, we use a **fixed-size sliding window**. 

As the window moves one step to the right, the new sum can be derived in \(O(1)\) time by adding the element entering the window and subtracting the element leaving the window.

```text
[ Element Leaving ] <-- [ Active Window of Size K ] <-- [ Element Entering ]
```

---

# Intuition

Since \(k\) is fixed, maximizing the average is mathematically identical to **maximizing the sum** of the subarray (\(\text{Average} = \text{Sum} / k\)). 

1. **Phase 1 (Baseline):** Compute the sum of the first \(k\) elements to establish our initial window and set it as `maxSum`.
2. **Phase 2 (Sliding):** Slide the window across the rest of the array from index `k` to the end. For each new element at index `i`, update the rolling sum by adding `nums[i]` and subtracting the leftmost element of the old window (`nums[i - k]`).
3. **Phase 3 (Result):** Cast `maxSum` to a double and divide it by `k` to return the maximum average.

---

# Visualization

Tracking the rolling sum on `nums = [1, 12, -5, -6, 50, 3]` with `k = 4`:

```text
1. Initial Baseline Window (First 4 elements):
   [1, 12, -5, -6], 50, 3
   sum = 1 + 12 + (-5) + (-6) = 2
   maxSum = 2

2. Slide Window to Index 4 (Add 50, Eject 1):
   1, [12, -5, -6, 50], 3
   sum = 2 + 50 - 1 = 51
   maxSum = max(2, 51) = 51

3. Slide Window to Index 5 (Add 3, Eject 12):
   1, 12, [-5, -6, 50, 3]
   sum = 51 + 3 - 12 = 42
   maxSum = max(51, 42) = 51

Final Return Value: 51 / 4 = 12.75
```

---

# Java Implementation

```java
class Solution {
    public double findMaxAverage(int[] nums, int k) {
        int sum = 0;
        
        // Step 1: Calculate the sum of the initial baseline window
        for (int i = 0; i < k; i++) {
            sum += nums[i];
        }
        
        int maxSum = sum;
        
        // Step 2: Slide the window across the remaining elements of the array
        for (int i = k; i < nums.length; i++) {
            // Add the incoming element and remove the outgoing element
            sum += nums[i] - nums[i - k];
            maxSum = Math.max(maxSum, sum);
        }
        
        // Step 3: Compute and return the maximum average value
        return (double) maxSum / k;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total number of elements in the `nums` array. The first loop runs \(k\) times to compute the initial sum, and the second loop slides the window across the remaining \(N - k\) elements. Each element is visited at most twice, resulting in a strict linear runtime profile.
* **Space Complexity:** O(1) auxiliary space. The calculation maintains only a few primitive local tracking variables (`sum` and `maxSum`), using no extra data structures or memory buffers.
