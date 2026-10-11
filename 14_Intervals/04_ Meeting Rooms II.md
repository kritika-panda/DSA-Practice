# Meeting Rooms II

## Problem Statement

Given an array of meeting time intervals `intervals` where `intervals[i] = [starti, endi]`, find the **minimum number of conference rooms** required.

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
2
```

**Explanation**

- Meeting `` takes place first. Room 1 is assigned.
- Meeting `` starts at 5. Since Room 1 is busy until 30, a new room (Room 2) is needed.
- Meeting `` starts at 15. Both rooms are busy. However, Room 2 frees up at 10. Thus, this meeting can reuse Room 2.
The maximum number of concurrent rooms needed is 2.

---

### Example 2

**Input**

```java
intervals = [, ]
```

**Output**

```java
1
```

**Explanation**

The second meeting `` starts exactly when the first meeting `` ends, so they can share a single room.

---

### Example 3

**Input**

```java
intervals = []
```

**Output**

```java
0
```

---

## Brute Force Approach

Track every timestamp uniquely or use nested loops to count how many meetings overlap with each current interval globally.

### Steps

1. Find the absolute minimum start time and maximum end time across all meetings to define a timeline range.
2. Create an array representing this timeline or check each interval manually against all other intervals using nested loops.
3. Count the number of concurrent overlaps for each step.
4. Return the maximum overlap count found.

### Complexity

```text
Time Complexity: O(n^2)
Space Complexity: O(1)
```

Comparing every pair yields an inefficient quadratic time scaling. We can optimize this by sorting the intervals and using a priority tracking mechanism.

---

# Optimal Approach: Min-Heap Tracking End Times

## Key Idea

If we sort the meetings by their **start times** in ascending order, we can process them sequentially as they occur on the timeline. 

To determine if an existing room can be reused, we need to quickly find the room that frees up the **earliest**. This can be tracked efficiently using a **Min-Heap (Priority Queue)** that stores the **end times** of active meetings:
1. Sort the `intervals` array by start times in ascending order.
2. Push the end time of the first meeting into the Min-Heap. The size of the heap represents the number of rooms currently active.
3. For each subsequent meeting, look at the root of the Min-Heap (`peek()`), which tells us the earliest time a room will become empty:
   - If `current_meeting.start >= heap.peek()`, it means an existing room has already freed up. We can reuse this room, so we pop the old end time off the heap (`poll()`).
   - If `current_meeting.start < heap.peek()`, all occupied rooms are still busy. We must allocate a new room, so we leave the heap elements intact.
4. Push the current meeting's end time into the heap.

After checking all meetings, the absolute maximum size the heap reached (or its final size) corresponds to the minimum number of conference rooms required.

---

## Visual Understanding

Suppose `intervals = [, , ]`:

1. **Sort by Start Times:**
   - The array rearranges into: `[, , ]`.

2. **Heap Timeline Simulation:**
   - **Meeting 1 ():** Allocate Room 1. Push end time `30` -> Heap: `[30]`. Max rooms = 1.
   - **Meeting 2 ():** Start time `5 < 30`. Room 1 is busy. Allocate Room 2. Push end time `10` -> Heap: `[10, 30]`. Max rooms = 2.
   - **Meeting 3 ():** Start time `15 >= 10` (Heap root). Room 2 has freed up! Reuse Room 2 \(\implies\) Pop `10`. Push new end time `20` -> Heap: `[15, 30]`. Max rooms = 2.

The total maximum rooms required is `2`.

---

## Partition Variables

Let:

```java
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
```

---

### Border Elements

The boundary comparison checks whether the current meeting's starting index clears the oldest trailing timeline deadline:

```java
if (intervals[i][0] >= minHeap.peek()) {
    minHeap.poll(); // Evict the cleared deadline slot to allow structural room reuse
}
```

---

## Correct Partition Condition

The running maximum size constraint tracked by the active heap elements defines the total conference rooms needed:

```java
return minHeap.size();
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with an initial sorting pass followed by an optimized Min-Heap priority simulation loop to achieve log-linear runtimes).*

---

## Java Solution

```java
import java.util.Arrays;
import java.util.PriorityQueue;

class Solution {

    public int minMeetingRooms(int[][] intervals) {
        if (intervals == null || intervals.length == 0) {
            return 0;
        }

        // Step 1: Sort the intervals by their start times in ascending order
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

        // Step 2: Initialize Min-Heap to track the end times of active meetings
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();

        // Allocate a room for the first meeting
        minHeap.offer(intervals[0][1]);

        // Step 3: Iterate through the remaining meetings and allocate rooms dynamically
        for (int i = 1; i < intervals.length; i++) {
            // If the current meeting starts after or when the earliest meeting ends, reuse the room
            if (intervals[i][0] >= minHeap.peek()) {
                minHeap.poll(); // Free up the room
            }

            // Allocate the current room slot by offering its end time to the heap
            minHeap.offer(intervals[i][1]);
        }

        // The size of the heap represents the total number of concurrent rooms needed
        return minHeap.size();
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
- **Initialization:** `minHeap.offer(30)`. Heap = `[30]`.
- **Iteration 1 (`i = 1`):** Current interval ``, Heap root `30`.
  - Check: `5 >= 30` (False) \(\implies\) Room busy. Heap pushes `10`. Heap = `[10, 30]`.
- **Iteration 2 (`i = 2`):** Current interval ``, Heap root `10`.
  - Check: `15 >= 10` (True) \(\implies\) Room free! `minHeap.poll()` removes `10`.
  - Heap pushes `20`. Heap = `[20, 30]`.

Loop terminates. `minHeap.size()` returns `2`.

---

### Answer

```java
2
```

---

## Why Is the Time Complexity Log-Linear?

Sorting the intervals array takes \(O(n \log n)\) time. The subsequent loop processes `n` elements sequentially, where each iteration performs a Min-Heap extraction and insertion that scales dynamically based on tree height rules (\(O(\log n)\)). This keeps the overall operation safely within log-linear execution thresholds.

This sorting and tree filtering framework yields:

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

Where `n` is the total number of meetings. Sorting the array takes \(O(n \log n)\) time, and the traversal loop performs up to `n` heap operations, each taking at most \(O(\log n)\) time.

---

### Space Complexity

```text
O(n)
```

In the worst-case scenario where all meetings overlap completely at a single point in time, the Min-Heap must hold up to `n` end-time integer keys simultaneously.

---

## Key Insight

Sorting by start times orders the timeline timeline entry flow, while a Min-Heap dynamically isolates the earliest room release events, converting a complex concurrent scheduling problem into a simple greedy lookup pass.

```text
Time  : O(n * log(n)) runtime path
Space : O(n) heap memory allocation tracker
```

---

## Similar Problems

1. Meeting Rooms (252)
2. Merge Intervals (56)
3. Insert Interval (57)
4. Non-overlapping Intervals (435)
5. Car Pooling (1094)
