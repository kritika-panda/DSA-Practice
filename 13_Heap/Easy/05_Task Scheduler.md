# Task Scheduler

## Problem Statement

Given a characters array `tasks`, representing the tasks a CPU needs to do, where each character represents a different task. Tasks could be done in any order. Each task is done in one unit of time. For each unit of time, the CPU could complete either one task or just be idle.

However, there is a non-negative integer `n` that represents the cooldown period between two **same tasks** (the same letter in the input), that is, there must be at least `n` units of time between any two same tasks.

Return the *minimum number of units of time* that the CPU will take to finish all the given tasks.

The overall run time complexity should be:

```text
O(t.length())
```

*(where t is the tasks array length, optimizable to O(1) auxiliary operational logic post frequency extraction)*

---

## Examples

### Example 1

**Input**

```java
tasks = ['A', 'A', 'A', 'B', 'B', 'B']
n = 2
```

**Output**

```java
8
```

**Explanation**

A possible optimal schedule is:

```text
A -> B -> idle -> A -> B -> idle -> A -> B
```

There are 3 units of time between the first 'A' and second 'A', satisfying the cooldown rule. The total units of time used is 8.

---

### Example 2

**Input**

```java
tasks = ['A', 'A', 'A', 'B', 'B', 'B']
n = 0
```

**Output**

```java
6
```

**Explanation**

Since there is no cooldown constraint (`n = 0`), the tasks can be executed sequentially in any order without idle periods:

```text
A -> B -> A -> B -> A -> B
```

---

### Example 3

**Input**

```java
tasks = ['A', 'A', 'A', 'A', 'A', 'A', 'B', 'C', 'D', 'E', 'F', 'G']
n = 2
```

**Output**

```java
16
```

**Explanation**

One possible optimal arrangement is:

```text
A -> B -> C -> A -> D -> E -> A -> F -> G -> A -> idle -> idle -> A -> idle -> idle -> A
```

---

## Brute Force Approach

Simulate the timeline explicitly by tracking remaining tasks and cooldown restrictions using a Priority Queue and an active waiting line queue.

### Steps

1. Count the frequency of each task using a Hash Map or fixed array.
2. Store the non-zero frequencies in a Max-Heap (Priority Queue) to always pick the highest remaining task first.
3. For each unit of time, pop the most frequent task from the heap, decrement its count, and append it to a temporary wait-list queue along with its release timestamp.
4. If the wait-list elements clear their cooldown intervals, push them back into the Max-Heap.
5. If the heap is empty but tasks remain in the wait-list, count an idle cycle. Repeat until all tasks finish.

### Complexity

```text
Time Complexity: O(tasks.length * log(26)) -> O(tasks.length)
Space Complexity: O(26) -> O(1)
```

While simulation tracks the timeline directly, it can be slow when many idle slots occur. We can calculate the total time instantly using a greedy mathematical counting framework.

---

# Optimal Approach: Greedy Interval Scheduling Math

## Key Idea

Instead of simulating the exact execution steps, we can compute the minimum timeline mathematically by focusing on the **most frequent task(s)**.

Suppose the task with the highest frequency appears `maxFreq` times. To separate these identical tasks by at least `n` units of time, we can group them into `maxFreq - 1` horizontal block slots. Each slot contains exactly `n + 1` execution positions (the task itself plus `n` cooling spaces).

```text
Block 1:  A  _  _  ... (size n+1)
Block 2:  A  _  _  ... (size n+1)
...
Final:    A
```

1. Find the maximum frequency `maxFreq` among all tasks.
2. Count how many distinct tasks share this absolute highest frequency. Let this be `maxFreqCount`.
3. Compute the minimum time required to arrange these baseline structures:
   ```text
   baseIntervals = (maxFreq - 1) * (n + 1)
   minimumTime = baseIntervals + maxFreqCount
   ```
4. Fill the remaining empty inner slots greedily with other less frequent tasks. If the total number of tasks is larger than our calculated `minimumTime`, it means we have enough tasks to fill all idle spaces completely without needing any padding. In that case, the answer is simply the absolute size of the `tasks` array.

---

## Visual Understanding

Suppose `tasks = ['A', 'A', 'A', 'B', 'B', 'B']` and `n = 2`:

- `maxFreq = 3` (for task 'A' or 'B')
- `maxFreqCount = 2` (both 'A' and 'B' appear 3 times)

We frame the `maxFreq - 1 = 2` empty structural slots:

```text
Frame 1:  A  _  _
Frame 2:  A  _  _
Tail   :  A  B
```

Now, fit the second frequent task 'B' into the frames:

```text
Frame 1:  A  B  _
Frame 2:  A  B  _
Tail   :  A  B
```

Using our math formula:

```text
baseIntervals = (3 - 1) * (2 + 1) = 2 * 3 = 6
minimumTime = 6 + 2 = 8
```

The calculated timeline is `8`. Since `8 > tasks.length (6)`, we need 2 idle spaces, making `8` the final minimum time units.

---

## Partition Variables

Let:

```java
int[] frequencies = new int[26];
int maxFreq = 0;
int maxFreqCount = 0;
```

---

### Border Elements

The task counts are extracted using a compact uppercase indexing offset array block:

```java
for (char task : tasks) {
    frequencies[task - 'A']++;
    maxFreq = Math.max(maxFreq, frequencies[task - 'A']);
}
```

---

## Correct Partition Condition

The ultimate threshold boundary returns either the mathematically padded block interval sum or the raw count of total element slots depending on whether idle slots remain:

```java
return Math.max(tasks.length, (maxFreq - 1) * (n + 1) + maxFreqCount);
```

---

## How to Move Binary Search

*(Note: This optimal method trades traditional binary search ranges for a deterministic math formula to evaluate execution timeline limits in linear time).*

---

## Java Solution

```java
class Solution {

    public int leastInterval(char[] tasks, int n) {
        if (tasks == null || tasks.length == 0) {
            return 0;
        }

        // Step 1: Count frequency of each character task
        int[] frequencies = new int[26];
        int maxFreq = 0;

        for (char task : tasks) {
            frequencies[task - 'A']++;
            maxFreq = Math.max(maxFreq, frequencies[task - 'A']);
        }

        // Step 2: Count how many distinct tasks share the maximum frequency
        int maxFreqCount = 0;
        for (int freq : frequencies) {
            if (freq == maxFreq) {
                maxFreqCount++;
            }
        }

        // Step 3: Compute the optimal mathematical interval allocation
        int baseIntervals = maxFreq - 1;
        int slotSize = n + 1;
        int calculatedTime = (baseIntervals * slotSize) + maxFreqCount;

        // Step 4: Return the larger value between calculated slots and raw array length
        return Math.max(tasks.length, calculatedTime);
    }
}
```

---

## Dry Run

### Input

```java
tasks = ['A', 'A', 'A', 'B', 'B', 'B'];
n = 2;
```

---

### Step Execution Traversal

- **Frequency Mapping:** `frequencies['A'-'A'] = 3`, `frequencies['B'-'A'] = 3`. `maxFreq` evaluates to `3`.
- **Count Max Tier:** Loop checks elements. Both index 0 ('A') and index 1 ('B') match `3`, so `maxFreqCount = 2`.
- **Math Extraction:**
  - `baseIntervals = 3 - 1 = 2`
  - `slotSize = 2 + 1 = 3`
  - `calculatedTime = (2 * 3) + 2 = 6 + 2 = 8`
- **Final Evaluation:** `Math.max(6, 8)` evaluates to `8`.

---

### Answer

```java
8
```

---

## Why Is the Time Complexity Linear?

The algorithm parses the input array exactly once to count frequencies. The subsequent loops run over a fixed alphabet array of size 26, which executes in constant time O(1). This replaces complex timeline simulations with an instant mathematical calculation.

This non-comparison frequency extraction yields:

```text
O(tasks.length)
```

which satisfies the optimal complexity constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(tasks.length)
```

Where `tasks.length` is the total number of elements in the task array. The initialization takes linear time, and the frequency sorting evaluation steps take a constant O(26) operations.

---

### Space Complexity

```text
O(1)
```

The algorithm utilizes a fixed-size integer array of length 26 to store character tallies. No dynamic heap objects or timeline data lists are allocated.

---

## Key Insight

By prioritizing the most frequent elements and treating all other tasks as flexible fillers for intermediate cooling blocks, we transform a complex scheduling simulation into a simple constant-time math equation.

```text
Time  : O(tasks.length) runtime path
Space : O(1) auxiliary storage footprint
```

---

## Similar Problems

1. Rearrange String k Distance Apart (358)
2. Reorganize String (767)
3. Longest Happy String (1405)
4. Top K Frequent Elements (347)
5. Car Pooling (1094)
