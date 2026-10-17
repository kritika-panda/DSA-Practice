# Wiggle Sort II ([LeetCode 324](https://leetcode.com))

## Problem Statement

Given an integer array `nums`, reorder it such that `nums[0] < nums[1] > nums[2] < nums[3]...`.

You may assume the input array always has a valid answer.

**Performance Targets:**
- **Time Complexity:** strictly bounded by **O(N)**.
- **Auxiliary Space Complexity:** highly optimal at **O(1)**.

---

## Examples

### Example 1

```text
Input: nums = [1,5,1,1,6,4]
Output: [1,6,1,5,1,4]
```

### Explanation

```text
[1,4,1,5,1,6] is also an acceptable valid configuration output.
```

---

### Example 2

```text
Input: nums = [1,3,2,2,3,1]
Output: [2,3,1,3,1,2]
```

---

# Intuition

Wiggle Sort II requires a strict inequality chain: `nums[0] < nums[1] > nums[2] < nums[3]...`. This is much harder than *Wiggle Sort I* (which allows `>=` and `<=`), because simple adjacent element swaps will cause infinite collision traps when identical **duplicate elements** cluster together.

To solve this problem optimally in **O(N) time and O(1) auxiliary space**:
1. **Find the Median:** A valid wiggle configuration requires placing elements smaller than the median at even slots (`0, 2, 4...`) and elements larger than the median at odd slots (`1, 3, 5...`). We can locate the absolute median of the array in linear time using a **QuickSelect** algorithm (or `nth_element`).
2. **Three-Way Partitioning (Dutch National Flag):** Once the median value is pinned, we partition the elements into three categories: those greater than the median, those equal to the median, and those smaller than the median.
3. **Virtual Index Mapping:** To avoid allocating a secondary array to rearrange these elements, we use a brilliant bitwise structural trick called **Virtual Indexing**. We map standard linear indices `0, 1, 2...` to interleaved slots using the formula:
   \[\text{VirtualIndex}(i) = (1 + 2 \times i) \% (N \mid 1)\]

This mapping naturally routes the front indices to odd slots (`1, 3, 5...`) and the tail indices to even slots (`0, 2, 4...`). By wrapping our standard Dutch National Flag algorithm with this virtual index converter function, the elements arrange themselves into the target wiggle configuration completely in-place.

---

# State Definition

The space-optimized virtual three-way partitioning pipeline tracks three dynamic bounding cursors:

```java
int low = 0;        // Insertion boundary tracking large values at virtual odd slots
int mid = 0;        // Active scanning cursor moving horizontally across virtual indices
int high = n - 1;   // Insertion boundary tracking small values at virtual even slots
```

---

# Recurrence / Structural Virtual State Condition

```text
VirtualIndex mapping function: (1 + 2 * i) % (n | 1)

While mid <= high:
    If nums[VirtualIndex(mid)] > median:
        Swap(nums[VirtualIndex(low)], nums[VirtualIndex(mid)])
        low++, mid++
    Else If nums[VirtualIndex(mid)] < median:
        Swap(nums[VirtualIndex(mid)], nums[VirtualIndex(high)])
        high--
    Else:
        mid++
```

---

# Optimal Single-Pass Virtual Indexing Solution

## Idea

We isolate the median element using an inline QuickSelect routine. We then launch a modified three-way Dutch National Flag partition loop. By translating index read and write addresses on-the-fly using bitwise modulo lookups, we achieve the target wiggle configuration using zero auxiliary heap allocations.

---

## Java Code

```java
import java.util.Random;

class Solution {
    public void wiggleSort(int[] nums) {
        // Guard Condition: Arrays with less than two elements are implicitly wiggle sorted
        if (nums == null || nums.length <= 1) {
            return;
        }

        int n = nums.length;

        // Step 1: Locate the median element in linear O(N) time using QuickSelect
        int median = findKthLargest(nums, (n + 1) / 2);

        // Step 2: Three-way partitioning with Virtual Index Mapping
        int low = 0;
        int mid = 0;
        int high = n - 1;

        // Bitwise or operator 'n | 1' elegantly computes the closest odd integer >= n
        int oddMaxBoundary = n | 1;

        while (mid <= high) {
            // Translate the standard linear tracking counters to virtual interleaved addresses
            int virtualMid = (1 + 2 * mid) % oddMaxBoundary;

            if (nums[virtualMid] > median) {
                int virtualLow = (1 + 2 * low) % oddMaxBoundary;
                swap(nums, virtualLow, virtualMid);
                low++;
                mid++;
            } else if (nums[virtualMid] < median) {
                int virtualHigh = (1 + 2 * high) % oddMaxBoundary;
                swap(nums, virtualMid, virtualHigh);
                // Decrement high. Leave mid stationary to evaluate the uninspected incoming element
                high--;
            } else {
                mid++;
            }
        }
    }

    // High-speed array reference element swapper
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }

    // Standard QuickSelect selection framework to find the median in linear time
    private int findKthLargest(int[] nums, int k) {
        int targetIdx = nums.length - k;
        int left = 0;
        int right = nums.length - 1;
        Random rand = new Random();

        while (left < right) {
            int pivotIdx = left + rand.nextInt(right - left + 1);
            int finalPivotIdx = partition(nums, left, right, pivotIdx);
            
            if (finalPivotIdx == targetIdx) {
                break;
            } else if (finalPivotIdx < targetIdx) {
                left = finalPivotIdx + 1;
            } else {
                right = finalPivotIdx - 1;
            }
        }
        return nums[targetIdx];
    }

    private int partition(int[] nums, int left, int right, int pivotIdx) {
        int pivotValue = nums[pivotIdx];
        swap(nums, pivotIdx, right);
        int writeIdx = left;
        
        for (int i = left; i < right; i++) {
            if (nums[i] < pivotValue) {
                swap(nums, i, writeIdx);
                writeIdx++;
            }
        }
        swap(nums, writeIdx, right);
        return writeIdx;
    }
}
```

---

## Complexity Analysis

Let N be the total length of the input array `nums`.

### Time Complexity

```text
Time Complexity: O(N) average case
```
*Explanation:* The QuickSelect routine finds the median element in expected linear O(N) time via random pivot partitioning. The subsequent virtual Dutch National Flag loop sweeps across the N virtual addresses exactly once, executing simple constant-time O(1) variable swaps. This keeps total time parameters strictly linear.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. By applying index-mapping arithmetic directly to address transformations on-the-fly, the algorithm bypasses secondary collection lists or helper arrays completely, keeping auxiliary storage completely flat.

---

# Key Takeaways

✅ Peak-tier advanced sorting architecture representing the pinnacle of in-place address transformations and index abstractions.

✅ **The Virtual Index Converter Pattern:** The mathematical expression `(1 + 2 * i) % (n | 1)` is exceptionally powerful. It abstracts data retrieval layers entirely. By decoupling physical memory slots from logical array indices, you can run classical partitioning algorithms natively over interleaved patterns without allocating extra memory buffers.

✅ Directly drives real-world microarchitectures like **digital signal processing interleavers** (re-shuffling telemetry frames or communication stream data packets into high/low frequency wave formats to reduce data collision risk), hardware memory defragmentation registers, and color grid shaders.

### Similar Problems

- Sort Colors / Dutch National Flag ([LeetCode 75](https://leetcode.com))
- Wiggle Sort I ([LeetCode 280](https://leetcode.com))
- Rearrange Array Elements by Sign ([LeetCode 2149](https://leetcode.com))
- Kth Largest Element in an Array ([LeetCode 215](https://leetcode.com))

