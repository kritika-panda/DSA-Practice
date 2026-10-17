# Range Sum Query - Immutable ([LeetCode 303](https://leetcode.com))

## Problem Statement

Given an integer array `nums`, handle multiple queries of the following type:
- Calculate the **sum** of the elements of `nums` between indices `left` and `right` **inclusive** where `left <= right`.

Implement the `NumArray` class:
- `NumArray(int[] nums)` Initializes the object with the integer array `nums`.
- `int sumRange(int left, int right)` Returns the sum of the elements of `nums` between indices `left` and `right` **inclusive** (i.e., `nums[left] + nums[left + 1] + ... + nums[right]`).

**Performance Targets:**
- `sumRange` queries must execute in strictly **O(1) constant time**.

---

## Examples

### Example 1

```text
Input:
["NumArray", "sumRange", "sumRange", "sumRange"]
[[[-2, 0, 3, -5, 2, -1]], [0, 2], [2, 5], [0, 5]]

Output:
[null, 1, -1, -3]
```

### Explanation

```text
NumArray numArray = new NumArray([-2, 0, 3, -5, 2, -1]);
numArray.sumRange(0, 2); // return (-2) + 0 + 3 = 1
numArray.sumRange(2, 5); // return 3 + (-5) + 2 + (-1) = -1
numArray.sumRange(0, 5); // return (-2) + 0 + 3 + (-5) + 2 + (-1) = -3
```

---

# Intuition

If we calculate the range sum naively by iterating from `left` to `right` inside the `sumRange` function, each query will take \(O(N)\) linear time. When the function is called thousands of times, this approach causes an immediate Time Limit Exceeded (TLE) error.

To hit our target of **O(1) constant query time**, we use the **Prefix Sum Array Pattern**:
- We pre-calculate and cache cumulative partial sums inside an array called `prefixSums`.
- Each index `i` in our `prefixSums` array stores the total sum of all elements from the beginning up to index `i-1`:
  \[\text{prefixSums}[i] = \text{nums}[0] + \text{nums}[1] + \dots + \text{nums}[i-1]\]
- By allocating `prefixSums` with a size of `N + 1`, we can use index `0` as a baseline placeholder set to `0`. This eliminates complex edge cases when `left == 0`.
- Armed with this lookup structure, the sum of any arbitrary contiguous subarray from `left` to `right` is derived instantly using a simple arithmetic subtraction step:
  \[\text{Sum}(left, right) = \text{prefixSums}[right + 1] - \text{prefixSums}[left]\]

---

# State Definition

The analytical preprocessing pipeline caches cumulative array states using a flat 1D lookup register:

```java
int[] prefixSums; // 1D array cache storing running prefix totals on the heap
```

---

# Recurrence / Structural Math Condition

```text
Preprocessing Phase:
prefixSums[0] = 0
For i from 0 to nums.length - 1:
    prefixSums[i + 1] = prefixSums[i] + nums[i]

Query Phase:
sumRange(left, right) = prefixSums[right + 1] - prefixSums[left]
```

---

# Optimal Prefix Sum Lookup Solution

## Idea

We configure a single allocation pass inside the constructor to construct our prefix sum lookups. When queries are dispatched to `sumRange`, we extract the slice sums instantly via basic arithmetic array reads in absolute constant time.

---

## Java Code

```java
class NumArray {
    // 1D array cache storing the prefix totals on the heap
    private final int[] prefixSums;

    public NumArray(int[] nums) {
        // Handle degenerate empty input parameters cleanly
        if (nums == null || nums.length == 0) {
            this.prefixSums = new int[1];
            return;
        }

        int n = nums.length;
        // Allocate N + 1 slots to natively map index 0 to a base sum of 0
        this.prefixSums = new int[n + 1];

        // Step 1: Pre-calculate the running prefix totals inside a single pass
        for (int i = 0; i < n; i++) {
            this.prefixSums[i + 1] = this.prefixSums[i] + nums[i];
        }
    }
    
    public int sumRange(int left, int right) {
        // Step 2: Extract range sums in O(1) time using subtraction logic
        // prefixSums[right + 1] gets the total sum from index 0 to right
        // prefixSums[left] gets the total sum from index 0 to left - 1
        return this.prefixSums[right + 1] - this.prefixSums[left];
    }
}
```

---

## Complexity Analysis

### Time Complexity

- **Initialization/Constructor:** \(O(N)\) because it iterates through the \(N\) input array elements exactly once to populate the lookup array.
- **Query (`sumRange`):** \(O(1)\) absolute constant time. The computation resolves via exactly two array indexing operations and a single primitive integer subtraction step, regardless of range width.

---

### Space Complexity

- **Auxiliary Space Complexity:** \(O(N)\) to allocate and hold the `prefixSums` array cache of size \(N + 1\) on the heap.

---

# Key Takeaways

✅ Core fundamental paradigm for processing range accumulation queries, data stream slicing, and static array transformations.

✅ **The N + 1 Offset Advantage:** Allocating your prefix sum lookup structure with a size multiplier of `N + 1` (letting `prefixSums[0] = 0`) is a highly effective design pattern. It ensures that calculating a range starting at the front boundary (`left = 0`) resolves flawlessly using the exact same formula without needing an annoying `if (left == 0)` wrapper check.

✅ Directly drives real-world microarchitectures like **relational database query index caches**, accounting ledger balance aggregators (instantly showing statement windows totals), network telemetry throughput meters, and digital audio signal processing layers.

### Similar Problems

- Range Sum Query 2D - Immutable ([LeetCode 304](https://leetcode.com))
- Subarray Sum Equals K ([LeetCode 560](https://leetcode.com))
- Contiguous Array ([LeetCode 525](https://leetcode.com))
- Product of Array Except Self ([LeetCode 238](https://leetcode.com))
