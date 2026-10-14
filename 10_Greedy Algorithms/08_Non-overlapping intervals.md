# Non-Overlapping Intervals (LeetCode 435)

## Problem Statement

Given an array of intervals where:

```java
intervals[i] = [starti, endi]
```

Return the minimum number of intervals you need to remove so that the remaining intervals are non-overlapping.

---

## Examples

### Example 1

```java
Input:
intervals = [[1,2],[2,3],[3,4],[1,3]]

Output:
1
```

### Explanation

Remove:

```text
[1,3]
```

Remaining:

```text
[1,2] [2,3] [3,4]
```

No overlaps.

---

### Example 2

```java
Input:
intervals = [[1,2],[1,2],[1,2]]

Output:
2
```

### Explanation

Keep one interval.

Remove:

```text
2 intervals
```

---

### Example 3

```java
Input:
intervals = [[1,2],[2,3]]

Output:
0
```

### Explanation

```text
[1,2] and [2,3] do not overlap
because touching endpoints is allowed.
```

---

# Key Observation

Instead of thinking:

```text
How many intervals should I remove?
```

Think:

```text
How many intervals can I keep?
```

The more intervals we keep without overlapping, the fewer we remove.

This becomes:

```text
Find Maximum Number of Non-Overlapping Intervals
```

which is the classic **Activity Selection Problem**.

---

# Greedy Intuition

Suppose we have:

```text
[1,100]
[2,3]
[3,4]
[4,5]
```

If we keep:

```text
[1,100]
```

we lose all other intervals.

If we keep:

```text
[2,3]
```

we can also keep:

```text
[3,4]
[4,5]
```

Thus:

```text
We should finish as early as possible.
```

---

# Greedy Rule

Sort intervals by:

```text
Ending time (ascending)
```

Always choose the interval that finishes earliest.

This leaves maximum room for future intervals.

---

# Approach

## Step 1

Sort intervals by end time.

```java
Arrays.sort(intervals, (a, b) -> a[1] - b[1]);
```

---

## Step 2

Pick the first interval.

```java
prevEnd = intervals[0][1];
```

---

## Step 3

Traverse remaining intervals.

### Case 1: No Overlap

```java
currentStart >= prevEnd
```

Keep interval.

Update:

```java
prevEnd = currentEnd;
```

---

### Case 2: Overlap

```java
currentStart < prevEnd
```

Remove current interval.

Increase removal count.

---

# Dry Run

## Input

```java
[[1,2],[2,3],[3,4],[1,3]]
```

---

### Sort By End Time

```text
[1,2]
[2,3]
[1,3]
[3,4]
```

---

### Keep [1,2]

```text
prevEnd = 2
remove = 0
```

---

### Interval [2,3]

```text
2 >= 2
```

No overlap.

Keep.

```text
prevEnd = 3
```

---

### Interval [1,3]

```text
1 < 3
```

Overlap.

Remove.

```text
remove = 1
```

---

### Interval [3,4]

```text
3 >= 3
```

No overlap.

Keep.

---

Final:

```text
remove = 1
```

Answer:

```java
1
```

---

# Java Solution

```java
import java.util.Arrays;

class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {

        Arrays.sort(intervals, (a, b) -> a[1] - b[1]);

        int removals = 0;
        int prevEnd = intervals[0][1];

        for (int i = 1; i < intervals.length; i++) {

            if (intervals[i][0] < prevEnd) {

                removals++;

            } else {

                prevEnd = intervals[i][1];
            }
        }

        return removals;
    }
}
```

---

# Alternative View

Maximum intervals kept:

```text
Activity Selection Problem
```

Then:

```text
Removals = Total Intervals - Kept Intervals
```

Both approaches lead to the same answer.

---

# Correctness Proof

## Greedy Choice

Choose the interval with the earliest end time.

Why?

```text
It leaves the maximum space
for future intervals.
```

Suppose we choose an interval that ends later.

Then:

```text
Available time for future intervals decreases.
```

Replacing it with an earlier-ending interval can never reduce the number of intervals selected.

Thus the greedy choice is optimal.

---

## Optimal Substructure

After selecting an interval:

```text
All intervals ending before it are irrelevant.
```

The remaining problem becomes:

```text
Select maximum non-overlapping intervals
from the remaining intervals.
```

Same problem recursively.

Therefore greedy works.

---

# Complexity Analysis

Let:

```text
N = number of intervals
```

### Time Complexity

Sorting:

```text
O(N log N)
```

Traversal:

```text
O(N)
```

Overall:

```text
O(N log N)
```

---

### Space Complexity

```text
O(1)
```

Ignoring sorting space.

---

# Visualization

```text
Intervals:

[1,2]
[2,3]
[1,3]
[3,4]

Sorted by end:

[1,2] ---- keep

[2,3] ---- keep

[1,3] ---- overlap -> remove

[3,4] ---- keep
```

---

# Why Sort By End Time Instead of Start Time?

Consider:

```text
[1,100]
[2,3]
[3,4]
```

Sorting by start:

```text
[1,100]
[2,3]
[3,4]
```

Bad choice.

Keeping:

```text
[1,100]
```

removes two intervals.

---

Sorting by end:

```text
[2,3]
[3,4]
[1,100]
```

Keep:

```text
[2,3]
[3,4]
```

Remove:

```text
[1,100]
```

Optimal answer.

---

# Pattern Recognition

Use this greedy approach when:

```text
✓ Intervals are given
✓ Need maximum non-overlapping intervals
✓ Need minimum removals
✓ Need room for future intervals
✓ Earliest finishing interval is beneficial
```

---

# Similar Problems

1. Activity Selection Problem
2. Meeting Rooms II
3. Insert Interval
4. Merge Intervals
5. Minimum Number of Arrows to Burst Balloons
6. Maximum Meetings in One Room
7. Attend Maximum Events
8. Employee Free Time

---

# Takeaway

```text
Sort intervals by ending time.

Always keep the interval
that finishes earliest.

Whenever overlap occurs,
remove the current interval.
```

**Pattern:** Greedy + Interval Scheduling

**Time Complexity:** O(N log N)

**Space Complexity:** O(1)
