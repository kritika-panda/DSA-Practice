# Job Sequencing Problem

## Problem Statement

You are given `N` jobs where each job has:

- `id` → Job ID
- `deadline` → Last time slot in which the job can be completed
- `profit` → Profit earned if the job is completed before or on its deadline

Each job takes **exactly 1 unit of time**.

Return:

```text
1. Maximum number of jobs done
2. Maximum profit earned
```

---

## Example

### Input

```text
Job    Deadline   Profit

a         2         100
b         1          19
c         2          27
d         1          25
e         3          15
```

### Output

```text
Jobs Done = 3
Profit    = 142
```

---

## Visualization

Available slots:

```text
Time: 1  2  3
```

After scheduling:

```text
Slot 1 → c (27)
Slot 2 → a (100)
Slot 3 → e (15)
```

Profit:

```text
27 + 100 + 15 = 142
```

---

# Intuition

Since every job takes:

```text
1 unit time
```

To maximize profit:

```text
Perform high-profit jobs first.
```

However, we must also satisfy deadlines.

Therefore:

```text
Sort jobs by profit in descending order.
```

Then try to place each job in the latest possible free slot before its deadline.

---

# Greedy Observation

Suppose:

```text
Job A -> Profit = 100, Deadline = 2
Job B -> Profit = 25,  Deadline = 2
```

If slot 2 is available:

```text
Place Job A in slot 2.
```

Why latest slot?

Because:

```text
Earlier slots remain available
for future jobs with smaller deadlines.
```

This increases the chances of scheduling more jobs.

---

# Approach

## Step 1

Sort jobs by descending profit.

```text
Highest profit first
```

---

## Step 2

Find the maximum deadline.

```java
maxDeadline
```

Create slots:

```java
slot[maxDeadline + 1]
```

Initialize:

```text
All slots = Empty
```

---

## Step 3

For every job:

Try placing it in:

```text
deadline
deadline - 1
deadline - 2
...
1
```

Find the latest free slot.

If available:

```text
Schedule job
Add profit
Increase job count
```

---

# Dry Run

## Input

```text
Job    Deadline Profit

a         2       100
b         1        19
c         2        27
d         1        25
e         3        15
```

---

## Sort By Profit

```text
a -> 100
c -> 27
d -> 25
b -> 19
e -> 15
```

---

### Job a

```text
Deadline = 2

Slot 2 free
```

Schedule:

```text
[_, a, _]
```

Profit:

```text
100
```

---

### Job c

```text
Deadline = 2

Slot 2 occupied
Slot 1 free
```

Schedule:

```text
[c, a, _]
```

Profit:

```text
127
```

---

### Job d

```text
Deadline = 1

Slot 1 occupied
```

Cannot schedule.

---

### Job b

```text
Deadline = 1

Slot 1 occupied
```

Cannot schedule.

---

### Job e

```text
Deadline = 3

Slot 3 free
```

Schedule:

```text
[c, a, e]
```

Profit:

```text
142
```

Jobs:

```text
3
```

---

# Java Solution

```java
import java.util.*;

class Job {
    int id;
    int deadline;
    int profit;

    Job(int id, int deadline, int profit) {
        this.id = id;
        this.deadline = deadline;
        this.profit = profit;
    }
}

class Solution {

    int[] JobScheduling(Job arr[], int n) {

        Arrays.sort(arr, (a, b) -> b.profit - a.profit);

        int maxDeadline = 0;

        for (Job job : arr) {
            maxDeadline = Math.max(maxDeadline, job.deadline);
        }

        int[] slots = new int[maxDeadline + 1];

        Arrays.fill(slots, -1);

        int jobsDone = 0;
        int totalProfit = 0;

        for (Job job : arr) {

            for (int j = job.deadline; j >= 1; j--) {

                if (slots[j] == -1) {

                    slots[j] = job.id;

                    jobsDone++;
                    totalProfit += job.profit;

                    break;
                }
            }
        }

        return new int[]{jobsDone, totalProfit};
    }
}
```

---

# Correctness Proof

## Greedy Choice

We always select the highest-profit remaining job.

Suppose a lower-profit job is selected before a higher-profit job.

```text
Profit decreases.
```

Replacing the lower-profit job with the higher-profit one can only increase total profit.

Therefore choosing jobs in descending profit order is optimal.

---

## Why Place At Latest Slot?

When a job has deadline:

```text
d
```

placing it at slot:

```text
d
```

keeps earlier slots free.

This maximizes opportunities for jobs having smaller deadlines.

Hence scheduling a job at the latest available slot before its deadline is always optimal.

---

# Complexity Analysis

Let:

```text
N = number of jobs
D = maximum deadline
```

### Time Complexity

Sorting:

```text
O(N log N)
```

Scheduling:

```text
O(N × D)
```

Overall:

```text
O(N log N + N × D)
```

---

### Space Complexity

```text
O(D)
```

for slot array.

---

# Optimized Solution (Disjoint Set Union)

Instead of scanning slots linearly:

```text
deadline
deadline-1
deadline-2 ...
```

Use DSU (Union Find).

Slot lookup becomes:

```text
Nearly O(1)
```

Overall complexity:

```text
O(N log N)
```

after sorting.

This is the interview-optimized version when deadlines are large.

---

# Why This Is a Greedy Problem?

Because:

```text
✓ We make a locally optimal choice
✓ Choose highest profit job first
✓ Place it as late as possible
✓ Local decisions lead to global optimum
```

No DP is required.

---

# Pattern Recognition

Use Job Sequencing Greedy when:

```text
✓ Every task takes 1 unit time
✓ Each task has a deadline
✓ Each task has a profit/value
✓ Need maximum profit
✓ Need optimal scheduling
```

---

# Similar Problems

1. Fractional Knapsack
2. Assign Cookies
3. Maximum Meetings in One Room
4. Minimum Platforms
5. Huffman Encoding
6. Course Schedule III
7. IPO (LeetCode 502)
8. Maximum Units on a Truck

---

# Takeaway

```text
1. Sort jobs by profit descending.
2. Pick highest-profit job first.
3. Schedule it in the latest possible slot.
4. Repeat for all jobs.
```

**Pattern:** Greedy + Sorting + Scheduling

**Time Complexity:** O(N log N + N × D)

**Space Complexity:** O(D)
