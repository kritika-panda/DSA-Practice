# Maximum Number of Non-Overlapping Subarrays With Sum Equals Target (LeetCode 1546)

## Problem Statement

Given an array `nums` and an integer `target`, return *the maximum number of **non-overlapping non-empty** subarrays such that the sum of the elements in each subarray is equal to `target`*.

---

## Examples

### Example 1

```java
Input: nums =, target = 2
Output: 2
```

### Explanation

```text
We can form at most 2 non-overlapping subarrays with a sum equal to 2:
- Subarray 1: nums[0...1] → 1 + 1 = 2 ✅
- Subarray 2: nums[2...3] → 1 + 1 = 2 ✅

The element at index 4 is left over, yielding a maximum count of 2.
```

---

### Example 2

```java
Input: nums = [-1,3,5,1,4,2,-9], target = 6
Output: 2
```

### Explanation

```text
The valid non-overlapping subarrays with a sum equal to 6 are:
- Subarray 1: nums[2...3] → 5 + 1 = 6 ✅
- Subarray 2: nums[4...5] → 4 + 2 = 6 ✅

These 2 subarrays do not overlap, so return 2.
```

---

# Intuition

To find the maximum number of non-overlapping subarrays that sum up to `target`:

- We should use a **Greedy + Prefix Sum** approach. 
- The earlier we can finish a valid subarray, the more room we leave open to fit subsequent valid subarrays down the line. 
- We can track cumulative prefix sums as we loop through the array. If we find a past prefix sum such that `currentPrefixSum - pastPrefixSum == target`, it indicates that the subarray between those two indices sums to `target`.
- **The Greedy Reset:** Once a valid subarray is locked in, we increment our global count. To enforce the **non-overlapping** rule, we must prevent any future subarray from using elements inside the segment we just claimed. We achieve this by instantly wiping our tracking memory history cache and restarting our prefix sum search from scratch.

---

# Greedy Observation

Consider the array:

```text
nums =, target = 2
```

Let's track the running prefix sums and historical caches:

```text
i = 0 → prefix = 1. Cache = {0, 1}
i = 1 → prefix = 2. Check: 2 - 2 = 0. Found in cache! 
        Subarray formed. Count = 1.
        Reset Cache = {2} to lock out index 0 and 1.
i = 2 → prefix = 3. Cache = {2, 3}
i = 3 → prefix = 4. Check: 4 - 2 = 2. Found in cache!
        Subarray formed. Count = 2.
        Reset Cache = {4}.
i = 4 → prefix = 5. Cache = {4, 5}. Loop ends.
```

By immediately finalizing a subarray as early as its boundary closes, we leave the maximum possible room for remaining suffixes.

---

# Approach: Prefix Sums + Hashing + Greedy Reset

## Algorithm

1. Initialize:
   - `count = 0` → Global tracker recording the number of non-overlapping valid subarrays.
   - `prefixSum = 0` → Running accumulator of the current cumulative prefix sum.
   - `visitedPrefixes` → A `HashSet` used to cache prefix sums seen *within the current search window*. Seed it with `0`.
2. Iterate through each `num` inside `nums`:
   - Add `num` to `prefixSum`.
   - **The Target Validation Check:** If `visitedPrefixes.contains(prefixSum - target)`:
     - A valid subarray ending at the current index has been found!
     - Increment `count`.
     - **The Greedy Reset Step:** Clear `visitedPrefixes` completely and seed it with only the current `prefixSum`. This acts as a fire-wall boundary, preventing future segments from overlapping back into the current completed block.
   - If the target condition was not met:
     - Save the current `prefixSum` into `visitedPrefixes` and continue.
3. Return `count`.

---

# Dry Run

```java
nums = [1,1,1,1,1]
target = 2
```

### Initial State

```text
count = 0
prefixSum = 0
visitedPrefixes = {0}
```

---

### Step 1 (num = 1)

```text
prefixSum = 0 + 1 = 1
Check: prefixSum - target = 1 - 2 = -1. 
Is -1 in visitedPrefixes? ❌

Add 1 to set. visitedPrefixes = {0, 1}
```

---

### Step 2 (num = 1)

```text
prefixSum = 1 + 1 = 2
Check: prefixSum - target = 2 - 2 = 0.
Is 0 in visitedPrefixes? ✅ (Subarray found!)

count++ → 1
Clear and Reset Set: visitedPrefixes = {2}
```

---

### Step 3 (num = 1)

```text
prefixSum = 2 + 1 = 3
Check: 3 - 2 = 1. Is 1 in visitedPrefixes? ❌

Add 3 to set. visitedPrefixes = {2, 3}
```

---

### Step 4 (num = 1)

```text
prefixSum = 3 + 1 = 4
Check: 4 - 2 = 2. Is 2 in visitedPrefixes? ✅ (Subarray found!)

count++ → 2
Clear and Reset Set: visitedPrefixes = {4}
```

---

### Step 5 (num = 1)

```text
prefixSum = 4 + 1 = 5
Check: 5 - 2 = 3. Is 3 in visitedPrefixes? ❌

Add 5 to set. visitedPrefixes = {4, 5}
```

Loop ends.

Answer:

```text
2
```

---

# Java Solution

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public int maxNonOverlapping(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return 0;
        }

        int count = 0;
        int prefixSum = 0;
        
        // HashSet to store prefix sums seen within the active non-overlapping window
        Set<Integer> visitedPrefixes = new HashSet<>();
        visitedPrefixes.add(0); // Base case: an empty prefix has a sum of 0

        for (int num : nums) {
            prefixSum += num;

            // Greedy Core Check: Look for a past prefix that completes the target sum
            if (visitedPrefixes.contains(prefixSum - target)) {
                count++;
                
                // Non-overlapping protection: Reset history instantly 
                // to lock out past indices from being reused
                visitedPrefixes.clear();
                visitedPrefixes.add(prefixSum);
            } else {
                // Save the current prefix sum to look up in future cycles
                visitedPrefixes.add(prefixSum);
            }
        }

        return count;
    }
}
```

---

# Optimized Understanding

This problem acts as a bridge between **Subarray Sum Equals K (LeetCode 560)** and the interval-packing concepts of **N Meetings in One Room**.

In LeetCode 560, we want to count *all* overlapping combinations, so we use a map frequency cache and never clear it. Here, because overlapping is strictly forbidden, any completed interval makes its interior elements completely useless for future steps. Wiping the `HashSet` acts as a localized window reset, executing the entire evaluation in a high-speed linear O(N) pass.

---

# Correctness Proof

### Greedy Choice

We always close and finalize a valid subarray the absolute earliest moment it becomes available.

Suppose we deliberately bypassed a valid subarray ending at index `R1` to wait for an alternative valid subarray that ends later at index `R2` (`R1 < R2`). 

```text
Bypassed Interval: [L1...R1]
Alternative Interval: [L2...R2]
```

Choosing the alternative interval finishes later at `R2`, which leaves *less* remaining space in the rest of the array to fit subsequent non-overlapping segments. Closing the interval as early as possible at `R1` minimizes resource footprint, proving the greedy choice is always optimal.

---

### Optimal Substructure

Once an optimal valid subarray is closed and the tracking cache is wiped, the problem of finding the maximum number of non-overlapping target subarrays within the remaining suffix `nums[i+1...n-1]` is completely independent. It presents an identical subproblem over a smaller range.

---

# Complexity Analysis

Let:

```text
N = nums.length
```

### Time Complexity

Single-pass loop:

```text
O(N)
```

Inside the loop, `HashSet` operations (`add`, `contains`, `clear`) execute in O(1) amortized time.

Overall:

```text
O(N)
```

---

### Space Complexity

```text
O(N)
```

In the worst-case scenario where no subarrays match the target, the `HashSet` stores up to N unique prefix sum integers.

---

# Key Pattern

This problem belongs to:

```text
Greedy + Prefix Sum Hashing + Interval Reset
```

Common indicators:

- Maximum number of *non-overlapping* continuous subarrays meeting an exact condition
- Subarray summation problems combined with counting optimization limits
- In-place tracking memory cache clears upon satisfying local constraints

---

# Similar Problems

1. Subarray Sum Equals K (Overlapping allowed - LeetCode 560)
2. Non-overlapping Intervals (LeetCode 435)
3. Continuous Subarray Sum (LeetCode 523)
4. Maximum Length of Subarray With Positive Product (LeetCode 1567)
5. Split Array into Consecutive Subsequences (LeetCode 659)

---

# Takeaway

```text
Accumulate cumulative prefix sums linearly.

Look up history using a HashSet to capture the exact target delta.

Wipe and reset the tracking history map completely the moment a target is struck 
to prevent future components from overlapping backwards.
```

**Pattern:** Greedy + Prefix Sum Hashing + Interval Reset
