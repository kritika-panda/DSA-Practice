# Merge Intervals

## Problem Statement

Given an array of `intervals` where `intervals[i] = [starti, endi]`, merge all overlapping intervals, and return an array of the non-overlapping intervals that cover all the intervals in the input.

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
intervals = [, , , ]
```

**Output**

```java
[, , ]
```

**Explanation**

Since intervals `` and `` overlap, they are merged into ``.

---

### Example 2

**Input**

```java
intervals = [, ]
```

**Output**

```java
```

**Explanation**

Intervals `` and `` overlap, so they merge into a single continuous interval ``.

---

### Example 3

**Input**

```java
intervals = []
```

**Output**

```java
[]
```

---

## Brute Force Approach

Compare every interval with every other interval using nested loops, and merge them if they share any overlapping regions. Repeat this process until no more merges can be made.

### Steps

1. Run an outer loop with index `i` from `0` to `n - 1`.
2. Run an inner loop with index `j` from `i + 1` to `n - 1`.
3. If `intervals[i]` and `intervals[j]` overlap, update `intervals[i]` to represent the merged boundary:
   - `start = min(intervals[i][0], intervals[j][0])`
   - `end = max(intervals[i][1], intervals[j][1])`
4. Delete the duplicated interval `j` and restart the outer evaluation loop from scratch since the bounds have shifted.

### Complexity

```text
Time Complexity: O(n^2) or worse due to repetitive deletions and restarts
Space Complexity: O(n) to manage elements dynamically
```

This brute-force strategy ignores ordering, leading to expensive repetitive scanning. We can optimize this by sorting the intervals first.

---

# Optimal Approach: Sorting by Start Coordinates

## Key Idea

If we sort all intervals by their **start times** in ascending order, we ensure that any overlapping intervals will sit **consecutively** next to each other.

1. Sort the `intervals` array by their start coordinates (`intervals[i][0]`) in non-decreasing order.
2. Initialize an expandable output list and insert the first interval into it. This element acts as our active merging window candidate.
3. Iterate through the remaining intervals from index 1 to `n - 1` and compare the current interval with the last interval appended to our output list:
   - **If an overlap exists** (`current.start <= last_inserted.end`), it means they merge. We expand the trailing boundary of the last inserted interval to the maximum end time between both: `last_inserted.end = Math.max(last_inserted.end, current.end)`.
   - **If no overlap exists** (`current.start > last_inserted.end`), the active window is closed. We append the current interval to the output list as a fresh new merging candidate window.

---

## Visual Understanding

Suppose `intervals = [, , , ]`:

1. **Sort by Start Coordinates:**
   - The array rearranges into: `[, , , ]`.

2. **Sequential Iteration:**
   - **Initialize:** Add `[1, 3]` to output. Current active window = `[1, 3]`.
   - **i = 1 (`[2, 6]`):** `2 <= 3` (True). Overlap! Merge: `end = max(3, 6) = 6`. Window becomes `[1, 6]`.
   - **i = 2 (`[8, 10]`):** `8 > 6` (True). No overlap. Append `[8, 10]` directly. New active window = `[8, 10]`.
   - **i = 3 (`[15, 18]`):** `15 > 10` (True). No overlap. Append `[15, 18]` directly. New active window = `[15, 18]`.

The final non-overlapping result array is built cleanly in a single tracking pass.

---

## Partition Variables

Let:

```java
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
List<int[]> mergedList = new ArrayList<>();
```

---

### Border Elements

The boundary check verifies whether the upcoming interval's starting point conflicts with the existing window's trailing dead-end:

```java
if (currentInterval[0] <= lastMergedInterval[1]) {
    lastMergedInterval[1] = Math.max(lastMergedInterval[1], currentInterval[1]);
}
```

---

## Correct Partition Condition

If an interval completely clears the active merged end-threshold boundary, the current window is finalized, and a new one opens:

```java
if (currentInterval[0] > lastMergedInterval[1]) {
    mergedList.add(currentInterval); // Open a brand new isolated interval block
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with an initial sorting pass followed by an adjacent linear scan to achieve optimal log-linear runtimes).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

class Solution {

    public int[][] merge(int[][] intervals) {
        if (intervals == null || intervals.length <= 1) {
            return intervals;
        }

        // Step 1: Sort the intervals by their start times in ascending order
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

        List<int[]> mergedList = new ArrayList<>();
        
        // Initialize our tracking list with the first interval candidate
        mergedList.add(intervals[0]);

        // Step 2: Traverse and merge intervals sequentially in a single pass
        for (int i = 1; i < intervals.length; i++) {
            int[] currentInterval = intervals[i];
            int[] lastMergedInterval = mergedList.get(mergedList.size() - 1);

            // If the current interval overlaps with the last merged interval, merge them
            if (currentInterval[0] <= lastMergedInterval[1]) {
                lastMergedInterval[1] = Math.max(lastMergedInterval[1], currentInterval[1]);
            } 
            // Otherwise, there is no overlap, so append it as a new candidate window
            else {
                mergedList.add(currentInterval);
            }
        }

        // Convert the list back into a primitive 2D array structure
        return mergedList.toArray(new int[mergedList.size()][]);
    }
}
```

---

## Dry Run

### Input

```java
intervals = [, , ];
```

---

### Step Execution Traversal

- **Sorting Pass:** Sorted by index 0 \(\implies\) `[, , ]`.
- **Initialization:** Add `[1, 4]` to list. `mergedList = [[1, 4]]`.
- **Iteration 1 (`i = 1`):** `currentInterval = [4, 5]`. `lastMergedInterval = [1, 4]`.
  - Check condition: `4 <= 4` evaluates to **true**.
  - Overlap confirmed. Merge: `lastMergedInterval[1] = max(4, 5) = 5`.
  - `mergedList` tracks: `[[1, 5]]`.
- **Iteration 2 (`i = 2`):** `currentInterval = [8, 10]`. `lastMergedInterval = [1, 5]`.
  - Check condition: `8 <= 5` evaluates to **false**.
  - No overlap. Append directly. `mergedList` expands: `[[1, 5], [8, 10]]`.

Loop finishes completely.

---

### Answer

```java
[, ]
```

---

## Why Is the Time Complexity Log-Linear?

The array sorting pass dominates the performance curve, scaling at an \(O(n \log n)\) factor. The subsequent evaluation loop sweeps through the sorted layout in a single linear pass (O(n)) without any index regression or tracking rewind steps, which fits comfortably inside log-linear bounds.

This sorting-driven layout yields:

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

Where `n` is the total number of intervals in the input array. Sorting the items takes \(O(n \log n)\) time, while the single-pass merging loop takes O(n) time.

---

### Space Complexity

```text
O(n)
```

An auxiliary list structure is used to dynamically group and compile the final non-overlapping interval sets before transforming them back to the output structure.

---

## Key Insight

Sorting by start coordinates aligns the flow of intervals sequentially along the timeline, turning a complex distributed overlapping search into a simple check between adjacent blocks.

```text
Time  : O(n * log(n)) runtime path
Space : O(n) auxiliary list tracking allocations
```

---

## Similar Problems

1. Insert Interval (57)
2. Non-overlapping Intervals (435)
3. Meeting Rooms (252)
4. Meeting Rooms II (253)
5. Interval List Intersections (986)
