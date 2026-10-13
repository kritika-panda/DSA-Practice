# Course Schedule III

## Problem Statement

There are `n` different online courses numbered from `1` to `n`. You are given an array `courses` where `courses[i] = [duration_i, lastDay_i]` indicate that the `i`-th course should be taken continuously for `duration_i` days and must be finished before or on `lastDay_i`.

You will start on the `1`-st day. You cannot take two or more courses simultaneously.

Return *the **maximum number of courses** you can take*.

The overall runtime complexity must be strictly bounded by:

```text
O(N * log(N))
```

*(where N is the total number of courses available)*

---

## Example 1

### Input

```text
courses = [[100, 200], [200, 1300], [1000, 1250], [2000, 3200]]
```

### Output

```text
3
```

### Explanation

```text
There are a total of 4 courses, but you can take at most 3 courses:
1. Take the 1st course [100, 200]: costs 100 days, finished on day 100. (Valid, 100 <= 200)
2. Take the 2nd course [200, 1300]: costs 200 days, finished on day 300. (Valid, 300 <= 1300)
3. Take the 4th course [2000, 3200]: costs 2000 days, finished on day 2300. (Valid, 2300 <= 3200)

Note: The 3rd course cannot be taken because by day 1300 you would have overshot its deadline (1250).
```

---

## Example 2

### Input

```text
courses = [[1, 2], [3, 2]]
```

### Output

```text
1
```

---

# Intuition

Unlike *Course Schedule I & II*, which focus on structural graph dependencies, **Course Schedule III** is actually a **Greedy Scheduling problem optimized via a Max-Heap**.

1. **Greedy Deadline Sorting:** We must always consider courses with an **earlier deadline (`lastDay`) first**. Sorting our array in ascending order of deadlines ensures that we don't accidentally lock ourselves out of early opportunities down the road.
2. **Streaming Schedule Pass:** Maintain a running clock counter variable `time = 0` to track the current calendar day. As we scan the sorted courses:
   - Tentatively accept the course by adding its duration to our clock: `time += duration`.
   - Push this course's duration onto a **Max-Heap (PriorityQueue)**. The heap keeps track of all currently accepted courses, with the longest duration floating to the root.
   - **The Deadline Overwrite Check:** If our clock overshoots the current course's deadline (`time > lastDay`), our tentative schedule is invalid. To recover, we look back at all courses we've accepted so far and **greedily evict the course with the absolute longest duration**.
   - We pop the maximum duration off the Max-Heap and subtract it from our clock: `time -= maxHeap.poll()`.
3. **The Swap Benefit:** By swapping out a longer course for a shorter one with the same or earlier deadline, we successfully compress our running `time` footprint. This leaves more breathing room open on our timeline to claim upcoming classes, without hurting our total count of completed slots.
4. At termination, the size of our Max-Heap corresponds exactly to the maximum number of courses we can survive.

---

# State Definition

Let:

```text
PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a); // Tracks durations of accepted courses
int time = 0;                                                         // Running clock tracking active day
```

---

# Recurrence / Pointer Transition Condition

```text
For each course (duration, lastDay) in sorted timeline:
    time += duration
    maxHeap.offer(duration)
    If time > lastDay:
        time -= maxHeap.poll() // Evict the longest time bottleneck
```

---

# Optimal Structural Scheduling Solution

## Idea

By sorting the array by deadline filters first and utilizing a Max-Heap to evict greedy duration bottlenecks on the fly, array re-sorting operations take \(O(N \log N)\) time, and the stream pass executes inside tightly bounded heap constraints. This solves the tracking puzzle cleanly inside **O(N * log(N))** parameters.

---

## Java Code

```java
import java.util.Arrays;
import java.util.PriorityQueue;

class Solution {

    public int scheduleCourse(int[][] courses) {
        if (courses == null || courses.length == 0) {
            return 0;
        }

        // Step 1: Sort the courses in ascending order based on their deadlines (lastDay)
        Arrays.sort(courses, (a, b) -> Integer.compare(a[1], b[1]));

        // Max-Heap to track the durations of all courses currently accepted into our schedule
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> Integer.compare(b, a));
        
        int time = 0; // Tracks the current running calendar day location

        // Step 2: Stream through the sorted courses greedily
        for (int[] course : courses) {
            int duration = course[0];
            int lastDay = course[1];

            // Tentatively add the course to our active schedule timeline
            time += duration;
            maxHeap.offer(duration);

            // Step 3: If our timeline breaches the deadline, evict the longest duration bottleneck
            if (time > lastDay) {
                // Polling drops the absolute largest duration out of our lookahead options pool
                int longestDuration = maxHeap.poll();
                time -= longestDuration; // Reclaim clock space safely
            }
        }

        // Step 4: The total number of safe elements remaining matches the heap size
        return maxHeap.size();
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N * log(N)) -> Sorting the N courses costs O(N log N) time. The sequential streaming loop runs N times, where each insertion or removal from the Max-Heap takes O(log N) balance steps.
Space Complexity: O(N)          -> Space utilized to house up to N unique course duration elements inside the priority queue container.
```

---

# Dry Run

## Input

```text
courses = [[100, 200], [1000, 1250], [200, 1300]] 
(Already sorted by deadline parameters)
```

---

### Execution Trace

```text
Initial: time = 0, maxHeap = []

1. Course 0 [100, 200]:
   - time = 0 + 100 = 100. maxHeap = [100].
   - Check: time (100) <= deadline (200) -> Safe!

2. Course 1 [1000, 1250]:
   - time = 100 + 1000 = 1100. maxHeap = [1000, 100].
   - Check: time (1100) <= deadline (1250) -> Safe!

3. Course 2 [200, 1300]:
   - time = 1100 + 200 = 1300. maxHeap = [1000, 200, 100].
   - Check: time (1300) <= deadline (1300) -> Safe!

Final maxHeap size = 3. Returns 3.
```

---

# Visualization

## Greedy Duration Bottleneck Eviction Swap

```text
Time Grid:
0 ─────────── 100 ────────────────────────────────────────────── 1100 ────► (Tentative: 1300)
  [ C1: 100 ]                     [ C2: 1000 ]                     [ C3: 200 ]
  
  If a deadline was breached at the edge, popping out C2 (1000) and replacing it 
  with C3 (200) compresses the clock back to day 300, leaving extensive free space!
```

---

# Scheduling Design Pattern

## Identify State

```text
Deadline-ordered stream scheduling utilizing a Max-Heap to prune time-duration bottlenecks on structural collisions.
```

---

## Choices

```text
Sort by deadlines. Append durations forward. Evict the maximal value from the active heap layout whenever a boundary constraint is overshot.
```

---

## Key Takeaways

✅ Advanced Greedy resource optimization algorithm.

✅ Using a Max-Heap to backward-evict the largest duration bottleneck provides a powerful "regret" mechanic—allowing the system to fix suboptimal past choices dynamically without altering the global permutation space.

✅ Standard real-world systems model used inside **Operating System Task Schedulers** (such as Earliest Deadline First - EDF frameworks), data processing stream limiters, or logistics assembly channels.

✅ Common Scheduling Design Pattern:

```text
Greedy Timeline Compression via Max-Heap Resource Eviction
```

### Similar Problems

- Course Schedule (LeetCode 207)
- Course Schedule II (LeetCode 210)
- Meeting Rooms II (LeetCode 253)
- Task Scheduler (LeetCode 621)
- IPO (LeetCode 502)
