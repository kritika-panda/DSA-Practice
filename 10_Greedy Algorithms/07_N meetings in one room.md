# N Meetings in One Room

## Problem Statement

There is **one meeting room** in a firm. There are `n` meetings in the form of `(start[i], end[i])` where `start[i]` is the start time of meeting `i` and `end[i]` is the end time of meeting `i`.

What is the **maximum number of meetings** that can be accommodated in the meeting room when only one meeting can be held in the meeting room at a particular time?

**Note:** The start time of one meeting cannot be equal to or overlap with the end time of another meeting (i.e., if a meeting ends at time `x`, the next meeting can start at the earliest at time `x + 1` or later, depending on the problem variant—on platforms like GeeksforGeeks, meetings can transition immediately at the exact same edge, meaning a meeting can start at `x` if the previous one ended at `x`).

---

## Examples

### Example 1

```java
Input: start = [1, 3, 0, 5, 8, 5], end = [2, 4, 6, 7, 9, 9]
Output: 4
```

### Explanation

```text
Meetings given:
Meeting 1: (1, 2)
Meeting 2: (3, 4)
Meeting 3: (0, 6)
Meeting 4: (5, 7)
Meeting 5: (8, 9)
Meeting 6: (5, 9)

An optimal schedule accommodates 4 meetings:
- Meeting 1 (1, 2) → Finishes at 2.
- Meeting 2 (3, 4) → Finishes at 4.
- Meeting 4 (5, 7) → Finishes at 7.
- Meeting 5 (8, 9) → Finishes at 9.

Maximum meetings = 4
```

---

### Example 2

```java
Input: start = [10, 12, 20], end = [20, 25, 30]
Output: 2
```

### Explanation

```text
- Meeting 1 (10, 20) → Finishes at 20.
- Meeting 3 (20, 30) → Finishes at 30. (Can start right as Meeting 1 ends)
Maximum meetings = 2
```

---

# Intuition

To maximize the total number of meetings held in a single room:

- We must always prioritize scheduling the meeting that **finishes as early as possible**.
- The earlier a meeting ends, the more time remains available in the day to fit subsequent meetings.
- Selecting a meeting based on an early start time or short duration can be deceptive if it blocks multiple other potential events due to a late finish time.

Therefore:

1. Pair the start and end times together into structured objects.
2. Sort all meetings in ascending order of their **end times**.
3. Iterate through the sorted meetings greedily, picking each meeting whose start time does not conflict with the end time of the last accepted meeting.

---

# Greedy Observation

Consider these sorted meetings by finish times:

```text
Meeting A: (1, 2)
Meeting B: (3, 4)
Meeting C: (0, 6)
Meeting D: (5, 7)
```

If we greedily choose meeting A, it frees up the room at time 2. Meeting B can then run from 3 to 4, freeing the room at time 4. Meeting D can then follow from 5 to 7.

If we had instead chosen meeting C (`0 — 6`) because it started earliest, it would have blocked meetings A, B, and D completely, reducing our total count from 3 down to 1. Protecting the earliest finish line is always optimal.

---

# Approach: Sorting + Greedy Interval Selection

## Algorithm

1. Create a custom structure/class `Meeting` holding `start` and `end` times.
2. Populate an array or list of these `Meeting` objects.
3. Sort the list in ascending order based on `meeting.end`. If two meetings have the exact same end time, sort them by their initial index position or start time (though sorting strictly by end time is sufficient for the maximum count).
4. Maintain:
   - `count = 1` → We always accept the absolute earliest finishing meeting.
   - `lastLimit = meetingList.get(0).end` → The finish clock of the active meeting.
5. Iterate from index `1` to `n - 1`:
   - If `meetingList.get(i).start > lastLimit`:
     - This meeting is non-overlapping.
     - Increment `count`.
     - Update the clock: `lastLimit = meetingList.get(i).end`.
6. Return `count`.

---

# Dry Run

```java
start = [1, 3, 0, 5, 8, 5]
end   = [2, 4, 6, 7, 9, 9]
```

After sorting by end times:

```text
Meeting 1: (1, 2)
Meeting 2: (3, 4)
Meeting 3: (0, 6)
Meeting 4: (5, 7)
Meeting 5: (8, 9)
Meeting 6: (5, 9)
```

### Initial State

```text
count = 1
lastLimit = 2 (From Meeting 1)
```

---

### Step 1 (Meeting 2: 3 to 4)

```text
start (3) > lastLimit (2) ? ✅

count++ → 2
lastLimit = 4
```

---

### Step 2 (Meeting 3: 0 to 6)

```text
start (0) > lastLimit (4) ? ❌ (Overlaps, skip)
```

---

### Step 3 (Meeting 4: 5 to 7)

```text
start (5) > lastLimit (4) ? ✅

count++ → 3
lastLimit = 7
```

---

### Step 4 (Meeting 5: 8 to 9)

```text
start (8) > lastLimit (7) ? ✅

count++ → 4
lastLimit = 9
```

---

### Step 5 (Meeting 6: 5 to 9)

```text
start (5) > lastLimit (9) ? ❌ (Overlaps, skip)
```

Loop ends.

Answer:

```text
4
```

---

# Java Solution

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

class Solution {
    // Custom internal class to represent meeting intervals
    private static class Meeting {
        int start;
        int end;

        Meeting(int start, int end) {
            this.start = start;
            this.end = end;
        }
    }

    public int maxMeetings(int[] start, int[] end, int n) {
        if (n == 0) return 0;

        List<Meeting> meetings = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            meetings.add(new Meeting(start[i], end[i]));
        }

        // Greedy Core Step: Sort meetings based on their finish (end) times
        Collections.sort(meetings, (a, b) -> Integer.compare(a.end, b.end));

        // Always accommodate the absolute earliest finishing meeting
        int count = 1;
        int lastLimit = meetings.get(0).end;

        // Traverse remaining meetings sequentially
        for (int i = 1; i < n; i++) {
            // If the start time is strictly greater than the previous end time 
            // (Use >= if immediate transition at the same edge is allowed)
            if (meetings.get(i).start > lastLimit) {
                count++;
                lastLimit = meetings.get(i).end; // Update room availability clock
            }
        }

        return count;
    }
}
```

---

# Optimized Understanding

This problem is a direct implementation of the classic **Activity Selection Problem**, which serves as the foundational proof for Greedy algorithm behaviors. 

By sorting exclusively by the right endpoint (`end`), we ensure that we make the most locally optimal decision at each step. This leaves the largest possible remaining time slice for any upcoming requests, which leads to a globally optimal solution.

---

# Correctness Proof

### Greedy Choice

Suppose there exists an optimal schedule $O$ that does not choose the meeting with the earliest finish time $E$. Let the first meeting in $O$ be $K$. 

Since $E$ has the earliest possible finish time of all meetings, we know that:
$$\text{finish time}(E) \le \text{finish time}(K)$$

If we replace $K$ with $E$ inside $O$, the new schedule remains valid because $E$ finishes even earlier than $K$, meaning it cannot conflict with any of the subsequent meetings already established in $O$. 

This prove that picking the earliest finish time is always a safe, optimal choice.

---

### Optimal Substructure

Once the earliest finishing meeting is locked in and its duration interval is cleared out, the problem reduces to finding the maximum number of mutually exclusive meetings within the remaining time domain. This is an identical subproblem, confirming optimal substructure.

---

# Complexity Analysis

Let:

```text
n = number of meetings
```

### Time Complexity

Building collection list:

```text
O(n)
```

Sorting intervals:

```text
O(n log n)
```

Greedy Traversal pass:

```text
O(n)
```

Overall:

```text
O(n log n)
```

---

### Space Complexity

```text
O(n)
```

Required to store the paired `Meeting` interval structures inside our container list.

---

# Key Pattern

This problem belongs to:

```text
Greedy + Interval Scheduling + Activity Selection
```

Common indicators:

- Single resource allocation with overlapping time blocks
- Maximize the total count of non-colliding tasks
- Sorting by right interval endpoints (`end` / `deadline`)

---

# Similar Problems

1. Non-overlapping Intervals (LeetCode 435)
2. Minimum Number of Arrows to Burst Balloons (LeetCode 452)
3. Meeting Rooms (LeetCode 252)
4. Meeting Rooms II (LeetCode 253)
5. Boats to Save People (LeetCode 881)
6. Job Sequencing Problem

---

# Takeaway

```text
Pair start and end intervals together.

Sort all intervals in ascending order based on their finish times.

Iterate through the list greedily, picking each non-overlapping
meeting that starts after the current finish clock.
```

**Pattern:** Greedy + Interval Scheduling + Activity Selection

<FollowUp>
We can now map out another critical interval or greedy puzzle. Let me know if you would like to generate the raw markdown code for **Non-overlapping Intervals (LeetCode 435)** or **Job Sequencing Problem with Deadlines**!
</FollowUp>
