# Longest Continuous Increasing Subsequence (LeetCode 674)

## Problem Statement

Given an unsorted array of integers `nums`, return *the length of the longest **continuous increasing subsequence** (i.e., subarray)*.

A **subsequence** is a sequence that can be derived from an array by deleting some or no elements without changing the order of the remaining elements.

A **continuous increasing subsequence** is defined by two indices `l` and `r` (`l <= r`) such that it is a subarray `nums[l], nums[l+1], ..., nums[r]` and for all `l <= i < r`, `nums[i] < nums[i+1]`.

---

## Examples

### Example 1

```java
Input: nums = [1,3,5,4,7]
Output: 3
```

### Explanation

```text
The longest continuous increasing subsequence is [1,3,5] with a length of 3.
Even though [1,3,5,7] is an increasing subsequence, it is not continuous because elements 5 and 7 are split by 4.
```

---

### Example 2

```java
Input: nums = [2,2,2,2,2]
Output: 1
```

### Explanation

```text
The longest continuous increasing subsequence is [2] with a length of 1. 
Note that the sequence must be strictly increasing.
```

---

# Intuition

To find the longest continuous increasing subarray:

- We can track the current sequence length as we scan the array from left to right.
- Since the sequence must be **continuous**, the moment we encounter an element that is less than or equal to its immediate predecessor (`nums[i] <= nums[i-1]`), our current increasing streak breaks completely.
- When a streak breaks, we don't need to backtrack or look old positions up; we can greedily reset our active count to `1` and use the current element as the anchor for a brand-new streak.

---

# Greedy Observation

Consider the array:

```text
Indices: 0  1  2  3  4
nums   : [1, 3, 5, 4, 7]
```

Let's track the active streak counter and max records linearly:

```text
i = 0 → Start. Count = 1, Max = 1
i = 1 → 3 > 1. Count = 2, Max = 2
i = 2 → 5 > 3. Count = 3, Max = 3
i = 3 → 4 <= 5. Streak Breaks! Reset Count = 1, Max stays 3
i = 4 → 7 > 4. Count = 2, Max stays 3
```

By immediately checking adjacent neighbors linearly, we capture peak streak boundaries without using nested tracking lookups.

---

# Approach: Single-Pass Greedy Traversal

## Algorithm

1. If the length of `nums` is `0`, return `0`.
2. Initialize:
   - `maxLen = 1` → Global tracker recording the maximum sequence length found.
   - `currentLen = 1` → Active counter tracking the length of the current increasing subarray streak.
3. Loop through the array from index `i = 1` to `nums.length - 1`:
   - If `nums[i] > nums[i - 1]`:
     - The strictly increasing condition is maintained.
     - Increment `currentLen`.
   - Otherwise (`nums[i] <= nums[i - 1]`):
     - The streak is broken.
     - Reset `currentLen = 1`.
   - Update `maxLen = Math.max(maxLen, currentLen)`.
4. Return `maxLen`.

---

# Dry Run

```java
nums = [1,3,5,4,7]
```

### Initial State

```text
maxLen = 1
currentLen = 1
```

---

### i = 1 (nums[1] = 3, nums[0] = 1)

```text
3 > 1 ✅

currentLen++ → 2
maxLen = max(1, 2) = 2
```

---

### i = 2 (nums[2] = 5, nums[1] = 3)

```text
5 > 3 ✅

currentLen++ → 3
maxLen = max(2, 3) = 3
```

---

### i = 3 (nums[3] = 4, nums[2] = 5)

```text
4 > 5 ❌ (Streak breaks!)

currentLen = 1
maxLen = max(3, 1) = 3
```

---

### i = 4 (nums[4] = 7, nums[3] = 4)

```text
7 > 4 ✅

currentLen++ → 2
maxLen = max(3, 2) = 3
```

Loop ends.

Answer:

```text
3
```

---

# Java Solution

```java
class Solution {
    public int findLengthOfLCIS(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }

        int maxLen = 1;
        int currentLen = 1;

        // Traverse the array linearly from the second element onwards
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] > nums[i - 1]) {
                // Condition holds, increment active streak length
                currentLen++;
            } else {
                // Streak broken, reset local counter instantly
                currentLen = 1;
            }
            
            // Record the global maximum length found so far
            maxLen = Math.max(maxLen, currentLen);
        }

        return maxLen;
    }
}
```

---

# Optimized Understanding

This problem is a streamlined variation of the **Maximum Subarray (Kadane's Algorithm)** paradigm. 

Instead of tracking maximum arithmetic sums, we track continuous boolean properties (`nums[i] > nums[i-1]`). Because the sequence constraint requires elements to be contiguous, any local failure drops the future utility of the previous prefix to exactly 0, allowing an optimal O(1) space runtime model.

---

# Correctness Proof

### Greedy Choice

We reset our tracking length back to `1` the exact moment `nums[i] <= nums[i-1]`. 

Suppose there existed a longer increasing subarray that started somewhere inside the previous streak and crossed past index `i`. Since `nums[i] <= nums[i-1]`, any sequence that includes both `nums[i-1]` and `nums[i]` is automatically invalid by definition. 

Therefore, resetting the anchor immediately to `nums[i]` is a safe and globally optimal decision.

---

### Optimal Substructure

After resetting `currentLen = 1` at a break point, the problem of finding the longest continuous increasing subsequence within the remaining suffix `nums[i...n-1]` is completely independent of the elements before index `i`. This matches the identical subproblem structure.

---

# Complexity Analysis

Let:

```text
n = nums.length
```

### Time Complexity

Single-pass traversal:

```text
O(n)
```

Overall:

```text
O(n)
```

---

### Space Complexity

```text
O(1)
```

Requires only two scalar integer tracking registers, completely avoiding extra collection list allocations.

---

# Key Pattern

This problem belongs to:

```text
Greedy + Continuous Streak Tracking + Kadane's Variant
```

Common indicators:

- Maximum or minimum length of a *continuous* subarray meeting a property
- Comparison of adjacent, consecutive pairs (`i` vs `i-1`)
- O(1) space local streak resets upon conditional breaks

---

# Similar Problems

1. Maximum Subarray (LeetCode 53)
2. Longest Increasing Subsequence (Non-continuous - LeetCode 300)
3. Partition Array Into Three Parts With Equal Sum (LeetCode 1013)
4. Longest Turbulent Subarray (LeetCode 978)
5. Check If Array Is Sorted and Rotated (LeetCode 1752)

---

# Takeaway

```text
Scan the array linearly from left to right.

Increment your active counter as long as adjacent items 
are strictly increasing.

Reset your streak counter instantly to 1 upon any conditional breach 
and log the global peak.
```

**Pattern:** Greedy + Continuous Streak Tracking + Kadane's Variant
