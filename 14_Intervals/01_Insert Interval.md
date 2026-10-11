# Insert Interval

## Problem Statement

You are given an array of non-overlapping intervals `intervals` where `intervals[i] = [starti, endi]` represent the start and the end of the `i`-th interval and `intervals` is sorted in ascending order by `starti`. You are also given an interval `newInterval = [start, end]` that represents the start and end of another interval.

Insert `newInterval` into `intervals` such that `intervals` is still sorted in ascending order by `starti` and `intervals` still does not have any overlapping intervals (merge overlapping intervals if necessary).

Return `intervals` after the insertion.

The overall run time complexity should be:

```text
O(n)
```

*(where n is the total number of intervals)*

---

## Examples

### Example 1

**Input**

```java
intervals = [, ]
newInterval = [2, 5]
```

**Output**

```java
[, ]
```

**Explanation**

Because the new interval `[2, 5]` overlaps with `[1, 3]` and `[6, 9]`, they are merged into `[1, 5]` and `[6, 9]`. Note that `[2, 5]` does not actually overlap with `[6, 9]`, but it bridges the gaps correctly to leave `[1, 5]` and `[6, 9]`.

---

### Example 2

**Input**

```java
intervals = [, , , , ]
newInterval = [4, 8]
```

**Output**

```java
[, , ]
```

**Explanation**

Because the new interval `[4, 8]` overlaps with `[3, 5]`, `[6, 7]`, `[8, 10]`, it merges them into a single interval `[3, 10]`.

---

### Example 3

**Input**

```java
intervals = []
newInterval = [5, 7]
```

**Output**

```java
[[5, 7]]
```

---

## Brute Force Approach

Append the new interval to the existing list, re-sort all intervals by their start times from scratch, and then perform a standard interval merging pass.

### Steps

1. Create a new expandable list and add all elements from `intervals` plus `newInterval`.
2. Sort the entire collection using a comparison-based sorting algorithm based on start coordinates.
3. Iterate through the sorted list sequentially. If the current interval overlaps with the previous one, merge them by updating the end time boundary to the maximum of both.
4. Convert the list back to a primitive 2D array and return it.

### Complexity

```text
Time Complexity: O(n * log(n))
Space Complexity: O(n)
```

This approach ignores the fact that the original `intervals` array is **already sorted** and non-overlapping. Re-sorting the whole pool wastes processing cycles. We can complete this in a single linear pass.

---

# Optimal Approach: Three-Phase Linear Pass

## Key Idea

Since the initial array is pre-sorted and clean, we can construct our result by iterating through the array exactly once. We can split the insertion logic into three sequential phases relative to `newInterval`:

1. **Phase 1 (Before Overlap):** Add all intervals that end *before* the `newInterval` starts (`intervals[i][1] < newInterval[0]`). These can be copied directly to the output without any modification.
2. **Phase 2 (Merge Overlaps):** For any interval that overlaps with `newInterval` (meaning it doesn't end before `newInterval` starts and doesn't start after `newInterval` ends), we merge it into `newInterval`. We expand the bounds of `newInterval` dynamically:
   - `newInterval[0] = min(newInterval[0], intervals[i][0])`
   - `newInterval[1] = max(newInterval[1], intervals[i][1])`
   Once the overlap loop finishes, we insert this single, fully merged `newInterval`.
3. **Phase 3 (After Overlap):** Add all remaining intervals that start *after* the `newInterval` ends (`intervals[i][0] > newInterval[1]`). These are appended directly without any changes.

---

## Visual Understanding

Suppose `intervals = [, ]` and `newInterval = [2, 5]`:

- **Phase 1 (`intervals[i][1] < 2`):** 
  - `i = 0 ([1, 3])`: `3 < 2` is false. Phase 1 completes. Output = `[]`.
- **Phase 2 (`intervals[i][0] <= 5`):**
  - `i = 0 ([1, 3])`: `1 <= 5` is true. Overlap detected! Merge: `newInterval[0] = min(2, 1) = 1`, `newInterval[1] = max(5, 3) = 5`. `newInterval` becomes `[1, 5]`.
  - `i = 1 ([6, 9])`: `6 <= 5` is false. Overlap loop finishes. Add `[1, 5]` to the output. Output = `[[1, 5]]`.
- **Phase 3 (Remaining items):**
  - `i = 1 ([6, 9])`: Append directly to output. Output = `[, ]`.

The process completes in a single linear pass.

---

## Partition Variables

Let:

```java
List<int[]> result = new ArrayList<>();
int i = 0;
int n = intervals.length;
```

---

### Border Elements

The traversal pointers strictly shift forward through individual indices, guarding the array limits securely:

```java
while (i < n && intervals[i][1] < newInterval[0])
```

---

## Correct Partition Condition

The overlapping bounds collapse continuously into a single merged interval as long as the current interval's start time sits inside the active window threshold:

```java
while (i < n && intervals[i][0] <= newInterval[1]) {
    newInterval[0] = Math.min(newInterval[0], intervals[i][0]);
    newInterval[1] = Math.max(newInterval[1], intervals[i][1]);
    i++;
}
result.add(newInterval);
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range division with a sequential three-phase partitioning pass over pre-sorted coordinate sets to guarantee true linear runtimes).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    public int[][] insert(int[][] intervals, int[] newInterval) {
        if (intervals == null || newInterval == null) {
            return new int[0][0];
        }

        List<int[]> result = new ArrayList<>();
        int i = 0;
        int n = intervals.length;

        // Phase 1: Add all intervals that end before the newInterval starts
        while (i < n && intervals[i][1] < newInterval[0]) {
            result.add(intervals[i]);
            i++;
        }

        // Phase 2: Merge all overlapping intervals into newInterval
        while (i < n && intervals[i][0] <= newInterval[1]) {
            newInterval[0] = Math.min(newInterval[0], intervals[i][0]);
            newInterval[1] = Math.max(newInterval[1], intervals[i][1]);
            i++;
        }
        // Add the fully merged newInterval
        result.add(newInterval);

        // Phase 3: Add all remaining intervals that start after the newInterval ends
        while (i < n) {
            result.add(intervals[i]);
            i++;
        }

        // Convert the list back into a primitive 2D array
        return result.toArray(new int[result.size()][]);
    }
}
```

---

## Dry Run

### Input

```java
intervals = [, , , , ];
newInterval =;
```

---

### Step Execution Traversal

- **Phase 1 (`intervals[i][1] < 4`):**
  - `i = 0 ([1, 2])`: `2 < 4` is true. Added. `result = [[1, 2]]`. `i = 1`.
  - `i = 1 ([3, 5])`: `5 < 4` is false. Phase 1 ends.
- **Phase 2 (`intervals[i][0] <= 8`):**
  - `i = 1 ([3, 5])`: `3 <= 8` is true. Merge: `newInterval = [min(4,3), max(8,5)] = [3, 8]`. `i = 2`.
  - `i = 2 ([6, 7])`: `6 <= 8` is true. Merge: `newInterval = [min(3,6), max(8,7)] = [3, 8]`. `i = 3`.
  - `i = 3 ([8, 10])`: `8 <= 8` is true. Merge: `newInterval = [min(3,8), max(8,10)] = [3, 10]`. `i = 4`.
  - `i = 4 ([12, 16])`: `12 <= 8` is false. Phase 2 ends. Add `[3, 10]`. `result = [, [3, 10]]`.
- **Phase 3 (Remaining elements):**
  - `i = 4 ([12, 16])`: Added directly. `result = [, , ]`. `i = 5`. Loop terminates.

---

### Answer

```java
[, , ]
```

---

## Why Is the Time Complexity Linear?

The algorithm scans the sorted array precisely once from left to right using a single index pointer `i`. Since no interval is ever re-evaluated or comparison-sorted globally, the processing overhead scales natively in proportion to the input size.

This single-pass workflow yields:

```text
O(n)
```

which satisfies the optimal time constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Where `n` is the total number of intervals in the input array. The loop index increments exactly `n` times across the three phases.

---

### Space Complexity

```text
O(n)
```

An auxiliary array list is utilized to compile the final collection of non-overlapping intervals before converting it back to the output structure.

---

## Key Insight

Dividing interval processing into three sequential zones (before, during, and after the target window) leverages the pre-sorted input array to skip comparison-based sorting costs entirely.

```text
Time  : O(n) execution pass
Space : O(n) dynamic array allocation
```

---

## Similar Problems

1. Merge Intervals (56)
2. Non-overlapping Intervals (435)
3. Meeting Rooms (252)
4. Meeting Rooms II (253)
5. Range Module (715)
