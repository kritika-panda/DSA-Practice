# Non-overlapping Intervals

## Problem Statement

Given an array of intervals `intervals` where `intervals[i] = [starti, endi]`, return *the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping*.

The overall run time complexity should be:

```text
O(n * log(n))
```

*(where n is the total number of intervals)*

---

## Examples

### Example 1

**Input**

```java
intervals = [[1, 2], [2, 3], [3, 4], [1, 3]]
```

**Output**

```java
1
```

**Explanation**

Removing `[1, 3]` leaves the rest of the intervals non-overlapping: `[1, 2]`, `[2, 3]`, and `[3, 4]`.

---

### Example 2

**Input**

```java
intervals = [[1, 2], [1, 2], [1, 2]]
```

**Output**

```java
2
```

**Explanation**

You need to remove two `[1, 2]` intervals to make the remaining interval unique and non-overlapping.

---

### Example 3

**Input**

```java
intervals = [[1, 2], [2, 3]]
```

**Output**

```java
0
```

**Explanation**

The intervals are already non-overlapping, so you do not need to remove any of them.

---

## Brute Force Approach

Generate all possible subsets of intervals, check each subset to see if it contains any overlapping entries, and find the largest valid subset.

### Steps

1. Use recursion or backtracking to generate all \(2^n\) possible subsets of intervals.
2. For each subset, sort the intervals and check if any consecutive pairs overlap.
3. Track the maximum size of a completely valid non-overlapping subset.
4. The minimum number of removals will be `total_intervals - maximum_valid_subset_size`.

### Complexity

```text
Time Complexity: O(2^n * n * log(n))
Space Complexity: O(n) for the recursion stack
```

This exponential combinatorial scaling causes an immediate Time Limit Exceeded (TLE) error for larger inputs. The problem can be re-framed as an Interval Scheduling Maximization problem and solved greedily.

---

# Optimal Approach: Greedy Interval Scheduling (Sort by End Time)

## Key Idea

Finding the *minimum number of intervals to remove* is mathematically equivalent to finding the *maximum number of non-overlapping intervals* you can keep. 

To maximize the number of non-overlapping intervals, we should always prioritize keeping the interval that **ends as early as possible**. By picking the interval with the earliest end time, we leave the maximum possible room for subsequent intervals to fit in the remaining timeline.

1. Sort the `intervals` array by their **end times** in ascending order.
2. Initialize an `end` boundary tracker to `Integer.MIN_VALUE` (or the end time of the first interval) and a `count` variable to 0 to track the number of non-overlapping intervals we keep.
3. Iterate through the sorted intervals sequentially:
   - **If an interval's start time is ≥ our current `end` tracker**, it means it does not overlap. We keep this interval, increment our `count`, and update the `end` tracker to this interval's end time.
   - **If an interval's start time is < our current `end` tracker**, it overlaps with our selection. We must discard it, so we leave the `end` tracker and `count` unchanged.
4. The answer will be the total number of intervals minus the count of intervals we successfully kept: `intervals.length - count`.

---

## Visual Understanding

Suppose `intervals = [[1, 2], [2, 3], [3, 4], [1, 3]]`:

1. **Sort by End Times:**
   - The array rearranges into: `[[1, 2], [2, 3], [1, 3], [3, 4]]`.

2. **Sequential Iteration:**
   - **Interval 1 `[1, 2]`:** First interval. Keep it. `count = 1`. Update `end = 2`.
   - **Interval 2 `[2, 3]`:** Start time `2 >= end (2)`. No overlap. Keep it. `count = 2`. Update `end = 3`.
   - **Interval 3 `[1, 3]`:** Start time `1 < end (3)`. Overlap detected! Discard this interval. `count` and `end` remain unchanged.
   - **Interval 4 `[3, 4]`:** Start time `3 >= end (3)`. No overlap. Keep it. `count = 3`. Update `end = 4`.

Total kept = 3. Total removals = `4 - 3 = 1`.

---

## Partition Variables

Let:

```java
Arrays.sort(intervals, (a, b) -> Integer.compare(a[1], b[1]));
int count = 0; // Number of valid non-overlapping intervals kept
int end = Integer.MIN_VALUE; // Trailing edge of the active timeline window
```

---

### Border Elements

The scanning loop parses the array from left to right to build the optimal timeline:

```java
for (int i = 0; i < intervals.length; i++)
```

---

## Correct Partition Condition

If the current interval starts after or exactly at the conclusion of the previous non-overlapping segment, it is safely locked into the selection pool:

```java
if (intervals[i][0] >= end) {
    end = intervals[i][1];
    count++;
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with an initial end-coordinate sorting pass followed by an adjacent greedy linear scan to achieve optimal log-linear runtimes).*

---

## Java Solution

```java
import java.util.Arrays;

class Solution {

    public int eraseOverlapIntervals(int[][] intervals) {
        if (intervals == null || intervals.length == 0) {
            return 0;
        }

        // Step 1: Sort the intervals by their end times in ascending order
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[1], b[1]));

        int nonOverlappingCount = 0;
        // Using a long variable initialization to shield against initial boundary edge cases safely
        long endLimit = Long.MIN_VALUE; 

        // Step 2: Iterate through the intervals and greedily select non-overlapping slots
        for (int[] interval : intervals) {
            // If the current interval starts at or after the previous end limit, keep it
            if (interval[0] >= endLimit) {
                endLimit = interval[1]; // Update our active timeline boundary
                nonOverlappingCount++;
            }
        }

        // Step 3: Total removals = total intervals - valid intervals kept
        return intervals.length - nonOverlappingCount;
    }
}
```

---

## Dry Run

### Input

```java
intervals = [[1, 2], [2, 3], [3, 4], [1, 3]];
```

---

### Step Execution Traversal

- **Sorting Pass:** Sorted by index 1 \(\implies\) `[[1, 2], [2, 3], [1, 3], [3, 4]]`.
- **Iteration 1:** `interval = [1, 2]`. `1 >= Long.MIN_VALUE` (True). `endLimit = 2`. `nonOverlappingCount = 1`.
- **Iteration 2:** `interval = [2, 3]`. `2 >= 2` (True). `endLimit = 3`. `nonOverlappingCount = 2`.
- **Iteration 3:** `interval = [1, 3]`. `1 >= 3` (False). Discarded.
- **Iteration 4:** `interval = [3, 4]`. `3 >= 3` (True). `endLimit = 4`. `nonOverlappingCount = 3`.

Loop terminates. Returns `4 - 3 = 1`.

---

### Answer

```java
1
```

---

## Why Is the Time Complexity Log-Linear?

The array sorting pass based on end times governs the overall processing timeline, scaling at an \(O(n \log n)\) factor. The subsequent evaluation loop sweeps through the sorted layout in a single linear pass (O(n)) without any index regression or recursive steps, which fits comfortably inside log-linear thresholds.

This sorting-driven framework yields:

```text
O(n * log(n))
```

which satisfies the optimal complexity constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n * log(n))
```

Where `n` is the total number of intervals in the input array. Sorting the items takes \(O(n \log n)\) time, while the single-pass greedy selection loop takes O(n) time.

---

### Space Complexity

```text
O(1) or O(n)
```

Depending on the underlying sorting algorithm used by the language runtime environment (e.g., Primitive collections use Quick Sort variants which consume \(O(\log n)\) implicit stack memory, while object array structures use Timsort which can scale up to O(n) space bounds).

---

## Key Insight

Sorting by end times ensures we always lock in the interval that finishes earliest. This maximizes the leftover space available on the timeline for subsequent items, allowing us to find the largest possible set of non-overlapping intervals in a single pass.

```text
Time  : O(n * log(n)) runtime path
Space : O(1) auxiliary operational footprint
```

---

## Similar Problems

1. Merge Intervals (56)
2. Insert Interval (57)
3. Meeting Rooms (252)
4. Meeting Rooms II (253)
5. Minimum Number of Arrows to Burst Balloons (452)
