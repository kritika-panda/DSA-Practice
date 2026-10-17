# 3Sum Closest ([LeetCode 16](https://leetcode.com))

## Problem Statement

Given an integer array `nums` of length `n` and an integer `target`, find three integers in `nums` such that the sum is **closest** to `target`.

Return *the sum of the three integers*. You may assume that each input would have **exactly one solution**.

---

## Examples

### Example 1

```text
Input: nums = [-1,2,1,-4], target = 1
Output: 2
```

### Explanation

```text
The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```

---

### Example 2

```text
Input: nums =, target = 1
Output: 0
```

### Explanation

```text
The only possible triplet sums to 0, which is the closest value to 1.
```

---

# Intuition

A brute-force solution checking every possible triplet combination using three nested loops takes a highly inefficient O(N³) cubic runtime penalty.

To optimize the search down to a fast **quadratic O(N²) time complexity with O(1) constant auxiliary space**, we can reuse the sorting and **Two-Pointer (Meet-in-the-Middle)** paradigm from the standard 3Sum problem:
1. **Sort the array upfront.** Sorting structures the integers monotonically, allowing us to adjust pointer positions systematically based on mathematical feedback loops.
2. **Fix a Pivot Element:** We iterate across the array using an outer cursor `i` to fix the first element of our triplet. 
3. **Bilateral Boundary Squeezing:** For each fixed `nums[i]`, we anchor two pointers: `left = i + 1` at the lower search bound and `right = N - 1` at the upper search bound.
   - Calculate the running sum: `currentSum = nums[i] + nums[left] + nums[right]`.
   - If `currentSum == target`, we have found the absolute closest sum possible. We can short-circuit out of the program and return `target` immediately.
   - If `currentSum < target`, the running total falls short of our goal. We move `left++` to increase our sum value.
   - If `currentSum > target`, the running total overshoots our goal. We move `right--` to decrease our sum value.
4. **Proximity Synchronization:** At each step, we calculate the absolute delta distance to our target (`Math.abs(currentSum - target)`). If this delta is strictly smaller than the absolute delta of our historical record, we synchronize and update our global tracker register (`closestSum = currentSum`).

---

# State Definition

The multi-pointer reduction pipeline tracks spatial boundaries and proximity records using four reference registers:

```java
int closestSum = nums[0] + nums[1] + nums[2]; // Global register tracking the closest sum achieved
int i = 0;                                   // Pivot pointer tracking the first element
int left = i + 1;                            // Squeezing pointer anchored at the lower bound
int right = nums.length - 1;                 // Squeezing pointer anchored at the upper bound
```

---

# Recurrence / Structural Squeezing Condition

```text
Sort(nums)
closestSum = nums[0] + nums[1] + nums[2]
For i from 0 to n - 3:
    left = i + 1, right = n - 1
    While left < right:
        sum = nums[i] + nums[left] + nums[right]
        if sum == target -> Return sum
        
        if abs(sum - target) < abs(closestSum - target):
            closestSum = sum
            
        if sum < target -> left++
        else -> right--
Return closestSum
```

---

# Optimal Two-Pointer Sorting Reduction Solution

## Idea

We sort the array upfront to configure direction-based search loops. By combining a single pivot-tracking outer loop with an inner meet-in-the-middle proximity synchronization pass, we calculate the optimal target triplet completely in-place.

---

## Java Code

```java
import java.util.Arrays;

class Solution {
    public int threeSumClosest(int[] nums, int target) {
        // Guard Condition: Arrays with less than three elements cannot form a valid triplet
        if (nums == null || nums.length < 3) {
            return 0;
        }

        int n = nums.length;

        // Step 1: Sort the array upfront to configure structural monotonicity
        Arrays.sort(nums);

        // Seed our global tracking register with the initial triplet combination
        int closestSum = nums[0] + nums[1] + nums[2];

        // Step 2: Fix the pivot element 'i' sequentially
        for (int i = 0; i < n - 2; i++) {
            // Duplicate Pruning Guard: Skip identical pivot elements to isolate unique root loops
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }

            // Step 3: Initialize the Meet-in-the-Middle Two-Pointer pointers
            int left = i + 1;
            int right = n - 1;

            while (left < right) {
                int currentSum = nums[i] + nums[left] + nums[right];

                // Optimization Short-Circuit: If we land exactly on target, return it immediately
                if (currentSum == target) {
                    return target;
                }

                // Proximity Check: If the active currentSum is closer to target than our historical peak
                if (Math.abs(currentSum - target) < Math.abs(closestSum - target)) {
                    closestSum = currentSum; // Synchronize and update the global tracker record
                }

                // Balance adjustment track based on directional feedback metrics
                if (currentSum < target) {
                    left++; // Sum is too small -> shift lower boundary rightward to a larger number
                } else {
                    right--; // Sum is too large -> shift upper boundary leftward to a smaller number
                }
            }
        }

        return closestSum; // Return the absolute closest triplet sum discovered
    }
}
```

---

## Complexity Analysis

Let N be the total length of the input array `nums`.

### Time Complexity

```text
Time Complexity: O(N²)
```
*Explanation:* Sorting the array takes \(O(N \log N)\) time. The primary outer loop runs N times. For each iteration, the inner meet-in-the-middle `while` loop checks the remaining search space linearly via the left and right pointers in O(N) steps. This nested \(O(N \times N)\) execution bounds the total runtime quadratically to O(N²), resolving complex proximity searches smoothly.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. The state tracking mutations and proximity evaluations execute completely in-place directly on the source array references using a fixed set of local variable registers, preserving a completely flat constant space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for processing multi-pointer proximity lookups, array sorting reductions, and real-time delta tracking.

✅ **The Proximity Synchronization Rule:** When tracking values based on target *closeness*, your conditional evaluations must be framed around absolute differences (`Math.abs(currentSum - target)`). Comparing numbers directly will break the tracking logic, whereas delta synchronization tracks precision perfectly across positive and negative bounds.

✅ Directly drives real-world microarchitectures like **automated portfolio proximity rebalancers** (finding asset combinations whose net sum aligns closest to a target investment allocation percentage quota), signal routing trackers, coordinate proximity processors, and predictive modeling filters.

### Similar Problems

- Two Sum ([LeetCode 1](https://leetcode.com))
- 3Sum ([LeetCode 15](https://leetcode.com))
- 3Sum Smaller ([LeetCode 259](https://leetcode.com))
- 4Sum ([LeetCode 18](https://leetcode.com))
