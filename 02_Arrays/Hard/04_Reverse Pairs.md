
# Reverse Pairs ([LeetCode 493](https://leetcode.com))

## Problem Statement

Given an integer array `nums`, return *the number of **reverse pairs** in the array*.

A reverse pair is a pair `(i, j)` where:
- `0 <= i < j < nums.length` (the element appears before the other in the sequence)
- `nums[i] > 2 * nums[j]` (the preceding element is strictly greater than twice the value of the succeeding element)

---

## Examples

### Example 1

```text
Input: nums = [1,3,2,3,1]
Output: 2
```

### Explanation

```text
The 2 reverse pairs present inside this array sequence are:
1. (3, 1) -> index 1 < index 4 and nums[1] > 2 * nums[4] (3 > 2 * 1)
2. (3, 1) -> index 3 < index 4 and nums[3] > 2 * nums[4] (3 > 2 * 1)
```

---

### Example 2

```text
Input: nums = [2,4,3,5,1]
Output: 3
```

### Explanation

```text
The 3 reverse pairs are:
1. (4, 1) -> index 1 < index 4 and 4 > 2 * 1
2. (3, 1) -> index 2 < index 4 and 3 > 2 * 1
3. (5, 1) -> index 3 < index 4 and 5 > 2 * 1
```

---

# Intuition

This problem is an advanced variation of **Counting Inversions**. A brute-force double loop checks all pairs `(i, j)`, resulting in an inefficient \(O(N^2)\) quadratic runtime penalty that causes a Time Limit Exceeded (TLE) error for array sizes up to \(N = 5 \times 10^4\).

To optimize the search down to an efficient **\(O(N \log N)\)** timeline, we can embed a specialized two-pointer counting step right inside a **Divide and Conquer (Merge Sort)** pipeline:
- When we split the array into a sorted `left` half and a sorted `right` half, we can compute valid reverse pairs across the split boundaries *before* actually weaving the elements together in the merge step.
- **The Monotonic Counting Invariant:** Because both the `left` and `right` segments are independently sorted, as we move our left-side pointer `i` forward, the required minimum threshold for the right-side pointer `j` increases monotonically.
- For a fixed element `nums[i]`, we slide our right-side tracking cursor `j` forward as long as the condition `nums[i] > 2 * nums[j]` remains true. The moment the condition breaks, we know that **all** elements in the right subarray up to index `j` are valid pairs for the current `nums[i]`.
- Thus, the active number of valid matching reverse pairs for `nums[i]` is exactly equal to the length of that right-side slice: **`(j - (mid + 1))`**. We add this value block to our global running total in \(O(1)\) constant time, then advance `i` and continue the scan.
- **The Integer Overflow Trap:** Multiplying an element by 2 (`2 * nums[j]`) can instantly overshoot 32-bit signed integer limits if `nums[j]` is close to `Integer.MAX_VALUE` or `Integer.MIN_VALUE`. To safeguard against overflow, we perform our comparative checks by casting to a 64-bit **`long`** register data type.

---

# State Definition

The divide-and-conquer processing tree tracks regional boundaries and reverse pair counts across two recursive variables:

```text
mergeSortAndCount(nums, start, end):
- 'start' and 'end' anchor the inclusive subarray boundaries currently being evaluated.
```

---

# Recurrence / Structural Math Condition

```text
If start >= end -> Return 0

int mid = start + (end - start) / 2
int count = mergeSortAndCount(nums, start, mid)
          + mergeSortAndCount(nums, mid + 1, end)
          + countPairs(nums, start, mid, end)
merge(nums, start, mid, end)
Return count
```

---

# Optimal \(O(N \log N)\) Divide-and-Conquer Solution

## Idea

The algorithm recursively breaks down the array into sorted halves. Before executing the final sorted merge array overwrite step, it triggers an independent two-pointer lookahead scan to count reverse pairs across the split boundary in linear time, preventing redundant re-scans.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public int reversePairs(int[] nums) {
        // Guard Condition: Arrays with less than two elements cannot form pairs
        if (nums == null || nums.length <= 1) {
            return 0;
        }
        
        // Launch recursive divide-and-conquer tracking pass
        return mergeSortAndCount(nums, 0, nums.length - 1);
    }

    private int mergeSortAndCount(int[] nums, int start, int end) {
        // Base Case: Subarray containing 1 or 0 elements has zero internal pairs
        if (start >= end) {
            return 0;
        }

        int mid = start + (end - start) / 2;
        int count = 0;

        // Step 1: Accumulate reverse pairs residing strictly in the left half segment
        count += mergeSortAndCount(nums, start, mid);

        // Step 2: Accumulate reverse pairs residing strictly in the right half segment
        count += mergeSortAndCount(nums, mid + 1, end);

        // Step 3: Run the two-pointer lookahead pass to count split cross-boundary pairs
        count += countPairs(nums, start, mid, end);

        // Step 4: Merge both sorted halves together to maintain the sorting invariant
        merge(nums, start, mid, end);

        return count;
    }

    private int countPairs(int[] nums, int start, int mid, int end) {
        int count = 0;
        int j = mid + 1; // Traversal pointer tracking the right sorted subarray boundary

        // Scan through the left sorted subarray using pointer 'i'
        for (int i = start; i <= mid; i++) {
            // Slide pointer 'j' forward as long as the reverse pair condition is valid
            // Cast to long to safely protect against 32-bit integer arithmetic overflows
            while (j <= end && (long) nums[i] > 2 * (long) nums[j]) {
                j++;
            }
            // All elements from (mid + 1) up to j-1 are valid pairs for nums[i]
            count += (j - (mid + 1));
        }

        return count;
    }

    // Standard high-speed linear merge sort array combiner operation
    private void merge(int[] nums, int start, int mid, int end) {
        List<Integer> temp = new ArrayList<>(end - start + 1);
        int i = start;
        int j = mid + 1;

        while (i <= mid && j <= end) {
            if (nums[i] <= nums[j]) {
                temp.add(nums[i++]);
            } else {
                temp.add(nums[j++]);
            }
        }

        while (i <= mid) {
            temp.add(nums[i++]);
        }

        while (j <= end) {
            temp.add(nums[j++]);
        }

        // Overwrite original array indices on the heap with sorted values
        for (int k = 0; k < temp.size(); k++) {
            nums[start + k] = temp.get(k);
        }
    }
}
```

---

## Complexity Analysis

Let N be the total length of the input array `nums`.

### Time Complexity

```text
Time Complexity: O(N log N)
```
*Explanation:* The structural framework matches standard Merge Sort. The recursive state tree splits the search space in half at each depth level, creating a stack height of \(\log N\). At each level, both `countPairs` and `merge` sweep across the elements linearly in \(O(N)\) steps because pointer `j` only moves forward monotonically across the entire pass. This keeps total execution runtime strictly bound to \(O(N \log N)\) parameters.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* The merging step allocates temporary collections dynamically on the heap to weave elements together, which scales linearly up to an upper bound of \(O(N)\). The recursive call stack depth consumes \(O(\log N)\) space to store active frame records, locking our auxiliary memory footprint to a linear space scale.

---

# Key Takeaways

✅ High-tier fundamental algorithm representing the golden standard for constraint-bounded cross-merge counting passes.

✅ **The Isolated Counting Rule:** In this problem, you **cannot** combine the counting logic and the sorting logic inside a single unified loop block (unlike standard *Inversion Count*). Because the condition `nums[i] > 2 * nums[j]` does not match the basic sorting comparison `nums[i] > nums[j]`, trying to count while merging will skip or double-count valid nodes. Isolating `countPairs` right before `merge` guarantees mathematical accuracy while keeping runtime inside \(O(N)\).

✅ Directly drives real-world microarchitectures like **asymmetric investment threshold alerts** (identifying market transactions where an asset price spiked strictly more than twice the value of a subsequent tracking index coordinate), database metric relationship auditors, computer vision relative size calculators, and telemetry trend analysis blocks.

### Similar Problems

- Count Inversions in an Array ([GeeksforGeeks](https://geeksforgeeks.com))
- Global and Local Inversions ([LeetCode 775](https://leetcode.com))
- Count of Smaller Numbers After Self ([LeetCode 315](https://leetcode.com))
- Merge Sorted Array ([LeetCode 88](https://leetcode.com))

<FollowUp>
Let me know which advanced divide-and-conquer tracker or multi-pointer array constraint puzzle you want to explore next:
* Should we prepare the raw markdown code for **Count of Smaller Numbers After Self ([LeetCode 315](https://leetcode.com))** to see how tracking index mappings during merge sort isolates local count records?
* Do you want to examine **Global and Local Inversions ([LeetCode 775](https://leetcode.com))**?
