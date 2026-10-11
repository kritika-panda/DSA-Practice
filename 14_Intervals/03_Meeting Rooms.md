# Meeting Rooms

## Problem Statement

Given an array of meeting time intervals `intervals` where `intervals[i] = [starti, endi]`, determine if a person could attend all meetings.

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
intervals = [, , ]
```

**Output**

```java
false
```

**Explanation**

The first meeting `` and the second meeting `` overlap during the time segment `[5, 10)`. Therefore, a person cannot attend both meetings.

---

### Example 2

**Input**

```java
intervals = [, ]
```

**Output**

```java
true
```

**Explanation**

The meeting `` ends at 10, and the next meeting `` starts exactly at 10. There is no overlapping window, so a person can attend all meetings.

---

### Example 3

**Input**

```java
intervals = []
```

**Output**

```java
true
```

**Explanation**

An empty schedule has no meetings to attend, which naturally evaluates to `true`.

---

## Brute Force Approach

Compare every single pair of meetings using nested loops to verify if any two intervals overlap.

### Steps

1. Run an outer loop with index `i` from `0` to `n - 1`.
2. Run an inner loop with index `j` from `i + 1` to `n - 1`.
3. For each pair, check if they overlap by checking if `Math.max(intervals[i][0], intervals[j][0]) < Math.min(intervals[i][1], intervals[j][1])`.
4. If an overlap is detected, return `false`.
5. If all pairs are checked without any overlaps found, return `true`.

### Complexity

```text
Time Complexity: O(n^2)
Space Complexity: O(1)
```

Comparing every single pair yields an inefficient quadratic time scaling. We can optimize this by sorting the intervals first.

---

# Optimal Approach: Sorting by Start Coordinates

## Key Idea

If we sort the meetings by their **start times** in ascending order, we transform a global pair-matching problem into a simple sequential comparison. 

Once the schedule is sorted, any potential overlap can only happen between **consecutive meetings**. We iterate through the sorted list starting from index 1 and compare the current meeting's start time with the previous meeting's end time:

```text
If current_meeting.start < previous_meeting.end
```

If this condition evaluates to true, an overlap exists, and it is impossible to attend all meetings. If the loop completes without finding any overlaps, the schedule is completely clear.

---

## Visual Understanding

Suppose `intervals = [, , ]`:

1. **Sort by Start Times:**
   - The array rearranges into: `[, , ]`.

2. **Sequential Iteration:**
   - **i = 1:** Compare current `` with previous ``.
     - Check: `5 < 30` holds **true**.
     - An overlap is intercepted immediately. Return `false`.

We completely skipped checking any subsequent combinations.

---

## Partition Variables

Let:

```java
int n = intervals.length;
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
```

---

### Border Elements

The scanning pointer loops sequentially starting directly from the second node element to verify adjacent links cleanly:

```java
for (int i = 1; i < intervals.length; i++)
```

---

## Correct Partition Condition

If a meeting attempts to commence prior to the conclusion of the immediate preceding interval, the structural timeline collapses:

```java
if (intervals[i][0] < intervals[i - 1][1]) {
    return false; // Time conflict identified
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with an initial sorting pass followed by an adjacent linear scan to achieve optimal log-linear runtimes).*

---

## Java Solution

```java
import java.util.Arrays;

class Solution {

    public boolean canAttendMeetings(int[][] intervals) {
        if (intervals == null || intervals.length <= 1) {
            return true;
        }

        // Step 1: Sort the intervals by their start times in ascending order
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

        // Step 2: Check for overlapping boundaries between consecutive meetings
        for (int i = 1; i < intervals.length; i++) {
            // If the current meeting starts before the previous one ends, an overlap exists
            if (intervals[i][0] < intervals[i - 1][1]) {
                return false;
            }
        }

        return true;
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
- **Iteration 1 (`i = 1`):** Current is ``, Previous is ``.
  - Check condition: `5 < 10` evaluates to **true**.
  - Conflict found. Method returns `false`.

---

### Answer

```java
false
```

---

## Why Is the Time Complexity Log-Linear?

The initial array ordering phase governs the overall processing timeline, scaling at an \(O(n \log n)\) factor. The subsequent validation loop sweeps through the sorted layout in a single linear pass (O(n)) without index regression, which fits comfortably inside the log-linear limit.

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

Where `n` is the total number of meetings. Sorting the array takes \(O(n \log n)\) time, while the subsequent linear loop takes O(n) time.

---

### Space Complexity

```text
O(1) or O(n)
```

Depending on the underlying sorting algorithm used by the language implementation (e.g., dual-pivot Quick Sort uses \(O(\log n)\) recursive stack space, while Timsort allocations can scale to O(n) space).

---

## Key Insight

Sorting the array transforms a distributed multi-interval overlap inspection into a simple, single-pass check between adjacent elements.

```text
Time  : O(n * log(n)) execution runtime
Space : O(1) auxiliary operational allocations
```

---

## Similar Problems

1. Meeting Rooms II (253)
2. Merge Intervals (56)
3. Insert Interval (57)
4. Non-overlapping Intervals (435)
5. Car Pooling (1094)
