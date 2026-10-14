# Shortest Job First (SJF) Scheduling

## Problem Statement

Given an array of integers `bt` representing the **burst times** (execution times) of `n` different independent tasks or processes, find the **average waiting time** for all tasks when scheduled using the non-preemptive **Shortest Job First (SJF)** scheduling algorithm.

Assume all processes arrive at the same time (Arrival Time = 0). 

The waiting time for a process is the total time it spends waiting in the scheduling queue before its execution begins.

Return the average waiting time as an integer (rounded down by taking the floor value).

---

## Examples

### Example 1

```java
Input: bt = [4, 1, 3, 7, 2]
Output: 4
```

### Explanation

```text
Processes sorted by burst time (Shortest Job First):
- Process 2 (bt = 1) → Starts at time 0. Waiting Time = 0.
- Process 5 (bt = 2) → Starts at time 1. Waiting Time = 1.
- Process 3 (bt = 3) → Starts at time 3. Waiting Time = 3.
- Process 1 (bt = 4) → Starts at time 6. Waiting Time = 6.
- Process 4 (bt = 7) → Starts at time 10. Waiting Time = 10.

Individual Waiting Times: [6, 0, 3, 10, 1]
Total Waiting Time = 0 + 1 + 3 + 6 + 10 = 20
Average Waiting Time = 20 / 5 = 4
```

---

### Example 2

```java
Input: bt = [1, 2, 3, 4]
Output: 2
```

### Explanation

```text
Processes are already sorted:
- P1 (bt=1) → Waits 0.
- P2 (bt=2) → Waits 1.
- P3 (bt=3) → Waits 1 + 2 = 3.
- P4 (bt=4) → Waits 3 + 3 = 6.

Total Waiting Time = 0 + 1 + 3 + 6 = 10
Average Waiting Time = 10 / 4 = 2.5 → Floor value = 2
```

---

# Intuition

To minimize the global average waiting time:

- Execute the task that takes the least amount of time first.
- The shorter a job is, the less time it adds to the waiting penalty of *all subsequent jobs* waiting in the execution queue.
- Leaving a long job for the end ensures that its large burst time doesn't hold up smaller tasks (preventing the classic **Convoy Effect**).

Therefore:

1. Sort the process burst times in ascending order.
2. Maintain a running timeline clock to track the start time of each job.
3. Sum up the individual waiting times and compute the average.

---

# Greedy Observation

Consider the sorted burst times:

```text
Tasks : 1  2  3  7
```

Let's look at how a task's duration impacts the timeline:

```text
Task 1 runs from 0 to 1.  (Adds 1 to the wait time of all 3 remaining tasks)
Task 2 runs from 1 to 3.  (Adds 2 to the wait time of all 2 remaining tasks)
Task 3 runs from 3 to 6.  (Adds 3 to the wait time of the last task)
Task 7 runs from 6 to 13.
```

By prioritizing the shortest tasks, the cumulative wait time overhead added to the system is minimized.

---

# Approach: Sorting + Cumulative Summation

## Algorithm

1. Sort the burst time array `bt` in ascending order.
2. Initialize:
   - `waitingTime = 0` → Accumulates the current start time for the next process.
   - `totalWaitingTime = 0` → Accumulates the sum of all individual waiting times.
3. Iterate through the sorted `bt` array:
   - Add the current `waitingTime` to `totalWaitingTime`.
   - Update `waitingTime` by adding the current process's burst time (`waitingTime += bt[i]`).
4. Average waiting time = `totalWaitingTime / n`.

---

# Dry Run

```java
bt = [4, 1, 3, 7, 2]
```

After sorting:

```text
bt = [1, 2, 3, 4, 7]
```

### Initial State

```text
totalWaitingTime = 0
waitingTime = 0
```

---

### Step 1 (bt[0] = 1)

```text
totalWaitingTime += 0 → 0
waitingTime += 1 → 1
```

---

### Step 2 (bt[1] = 2)

```text
totalWaitingTime += 1 → 1
waitingTime += 2 → 3
```

---

### Step 3 (bt[2] = 3)

```text
totalWaitingTime += 3 → 4
waitingTime += 3 → 6
```

---

### Step 4 (bt[3] = 4)

```text
totalWaitingTime += 6 → 10
waitingTime += 4 → 10
```

---

### Step 5 (bt[4] = 7)

```text
totalWaitingTime += 10 → 20
waitingTime += 7 → 17
```

Loop ends.

Answer: `20 / 5 = 4`

---

# Java Solution

```java
import java.util.Arrays;

class Solution {
    public int solve(int[] bt) {
        // Step 1: Sort the burst times to implement Shortest Job First
        Arrays.sort(bt);
        
        int n = bt.length;
        int totalWaitingTime = 0;
        int waitingTime = 0;
        
        // Step 2: Accumulate individual waiting times
        for (int i = 0; i < n; i++) {
            totalWaitingTime += waitingTime;
            waitingTime += bt[i];
        }
        
        // Step 3: Return the floor value of the average waiting time
        return totalWaitingTime / n;
    }
}
```

---

# Optimized Understanding

Whenever a process at index `i` runs, its waiting time is exactly equal to the sum of all burst times of the processes before it.

Mathematically, the i-th process (0-indexed) is waited on by exactly (n - 1 - i) subsequent processes. We can also calculate the total waiting time by considering how many times each burst time is added to the system penalty:

\[\text{Total Waiting Time} = \sum_{i=0}^{n-1} bt[i] \times (n - 1 - i)\]

Since (n - 1 - i) is a strictly decreasing multiplier weight, assigning the largest burst times to the smallest multipliers (at the end of the array) minimizes the mathematical product. This proves why sorting is globally optimal.

---

# Correctness Proof

### Greedy Choice

We always schedule the shortest available task next. 

Suppose we swapped a shorter job X and a longer job Y such that Y runs before X. Every job scheduled between Y and X, including X itself, will have its waiting time increased by exactly \((\text{burst time}(Y) - \text{burst time}(X))\). 

Since this delta is strictly positive, the alternative schedule yields a worse average waiting time. Thus, the greedy choice of shortest first is always optimal.

---

### Optimal Substructure

After selecting the absolute shortest task and advancing our timeline clock:

```text
Remaining unscheduled tasks
```

presents a subproblem of the exact same nature. Solving this subproblem optimally builds the global solution.

---

# Complexity Analysis

Let:

```text
n = bt.length
```

### Time Complexity

Sorting:

```text
O(n log n)
```

Linear Traversal:

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
O(1)
```

The algorithm operates completely in-place, ignoring internal auxiliary space used by standard sorting implementations.

---

# Key Pattern

This problem belongs to:

```text
Greedy + Scheduling + Task Ordering
```

Common indicators:

- Optimization of average wait time, completion time, or penalty overheads
- Non-preemptive scheduling tasks with simultaneous arrival times
- Sorting components down to minimize cumulative prefix sums

---

# Similar Problems

1. Assign Cookies
2. Minimum Number of Arrows to Burst Balloons
3. Lemonade Change
4. Task Scheduler
5. Meeting Rooms II
6. Job Sequencing Problem (with Deadlines)
7. Huffman Coding

---

# Takeaway

```text
Sort the process burst times in ascending order.

Track the current timeline clock to calculate individual
waiting times on-the-fly.

Accumulate the total system wait penalty and compute the average.
```

**Pattern:** Greedy + Scheduling + Task Ordering
