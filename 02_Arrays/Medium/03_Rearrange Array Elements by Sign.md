# Rearrange Array Elements by Sign ([LeetCode 2149](https://leetcode.com))

## Problem Statement

You are given a **0-indexed** integer array `nums` of **even** length containing an equal number of positive and negative integers.

You should **rearrange** the elements of `nums` such that the modified array follows the given conditions:
1. Every consecutive pair of integers have **opposite signs**.
2. For all integers with the same sign, the **relative order** in which they were present in `nums` must be **preserved**.
3. The rearranged array must begin with a **positive** integer.

Return *the modified array after rearranging the elements*.

---

## Examples

### Example 1

```text
Input: nums = [3,1,-2,-5,2,-4]
Output: [3,-2,1,-5,2,-4]
```

### Explanation

```text
The positive integers in nums are. The negative integers are [-2, -5, -4].
The only possible arrangement that starts with a positive number and keeps the relative order is [3, -2, 1, -5, 2, -4].
```

---

### Example 2

```text
Input: nums = [-1,1]
Output: [1,-1]
```

### Explanation

```text
1 is the only positive integer and -1 is the only negative integer.
The array must start with a positive integer, so [1, -1] is returned.
```

---

# Intuition

Since the array contains an equal number of positive and negative integers and must alternate signs starting with a positive number, we know exactly where each type of number should land in the final array:
- All **positive integers** must occupy the **even indices** (`0, 2, 4, ...`).
- All **negative integers** must occupy the **odd indices** (`1, 3, 5, ...`).

To satisfy the strict condition of **preserving the relative order** in a single pass without sorting, we can deploy a **Two-Pointer Interleaving Pattern**:
1. Allocate a brand new result array `ans` of the exact same size as `nums`.
2. Maintain two independent placement tracking index pointers:
   - `posIdx` initialized to `0` (tracks the next slot for a positive integer).
   - `negIdx` initialized to `1` (tracks the next slot for a negative integer).
3. Scan through the input `nums` array from left to right.
   - If `nums[i] > 0`, write it down to `ans[posIdx]` and advance `posIdx += 2`.
   - If `nums[i] < 0`, write it down to `ans[negIdx]` and advance `negIdx += 2`.

---

# State Definition

The sequential generation pipeline tracks targeted placement anchors using two local index cursors:

```java
int posIdx = 0; // Insertion index register for positive elements (even boundaries)
int negIdx = 1; // Insertion index register for negative elements (odd boundaries)
```

---

# Recurrence / Structural State Condition

```text
For each num in nums:
    If num > 0:
        ans[posIdx] = num
        posIdx += 2
    Else:
        ans[negIdx] = num
        negIdx += 2
Return ans
```

---

# Optimal Single-Pass Two-Pointer Solution

## Idea

We allocate an output array workspace. We sweep across the original list exactly once. By routing incoming items straight to their targeted even or odd slots based on value signs, we sort out the structure cleanly inside a single pass.

---

## Java Code

```java
class Solution {
    public int[] rearrangeArray(int[] nums) {
        // Guard Condition: Degenerate arrays or empty limits return directly
        if (nums == null || nums.length == 0) {
            return nums;
        }

        int n = nums.length;
        int[] ans = new int[n];

        // Track targeted placement slots for alternating signs
        int posIdx = 0;
        int negIdx = 1;

        // Core Step: Single forward scan interleaved routing
        for (int num : nums) {
            if (num > 0) {
                ans[posIdx] = num; // Route positive number to its active even slot position
                posIdx += 2;       // Advance cursor past the next negative slot
            } else {
                ans[negIdx] = num; // Route negative number to its active odd slot position
                negIdx += 2;       // Advance cursor past the next positive slot
            }
        }

        return ans; // Return the perfectly interleaved array structure
    }
}
```

---

## Complexity Analysis

Let N be the total length of the input array `nums`.

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The processing loop scans linearly through the N input elements exactly once. Inside the conditional block, it performs simple constant-time O(1) integer pointer jumps and element copies, locking total execution runtime strictly relative to the data length.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. Excluding the allocation of the output array `ans` required to compile and return the final answer (which scales as an inherent O(N) constraint), the algorithm coordinates tracking completely inside local registers using no helper tables or collection structures, preserving a flat constant auxiliary footprint.

---

# Key Takeaways

✅ Core baseline template for stream demultiplexing, element interleaving, and sign-partitioning.

✅ **The Positional Determinism Rule:** When an alternating pattern has fixed target dimensions (such as evens for positive and odds for negative), do not use slow intermediate list arrays to segregate numbers. Mapping state destinations upfront using independent leapfrogging indices allows you to write the final structure directly inside a single single pass.

✅ Directly drives real-world microarchitectures like **audio data signal de-interleavers** (separating left and right channel audio packet streams from a multiplexed line stream), alternating sequence formatters, hardware bit-stream routing layers, and telemetric data pipeline filters.

### Similar Problems

- Sort Array By Parity ([LeetCode 905](https://leetcode.com))
- Sort Array By Parity II ([LeetCode 922](https://leetcode.com))
- Move Zeroes ([LeetCode 283](https://leetcode.com))
- Wiggle Sort ([LeetCode 280](https://leetcode.com))
