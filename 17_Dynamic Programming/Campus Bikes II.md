# 1066. Campus Bikes II

🔗 **Problem Link:**  
https://leetcode.ca/all/1066.html

---

# Problem Statement

On a campus there are:

- `n` workers
- `m` bikes

represented as coordinates:

```text
workers[i] = [x, y]
bikes[j] = [x, y]
```

Assign exactly one bike to each worker such that:

- Each bike can be assigned to at most one worker.
- Every worker must receive a bike.

Return the **minimum possible total Manhattan distance**.

---

## Manhattan Distance

For:

```text
worker = (x1, y1)
bike   = (x2, y2)
```

Distance:

```text
|x1 - x2| + |y1 - y2|
```

---

# Example

```text
workers = [[0,0],[2,1]]
bikes   = [[1,2],[3,3]]
```

Possible assignments:

```text
Worker0 → Bike0 = 3
Worker1 → Bike1 = 3

Total = 6
```

Another assignment:

```text
Worker0 → Bike1 = 6
Worker1 → Bike0 = 2

Total = 8
```

Answer:

```text
6
```

---

# Brute Force Approach

For every worker:

```text
Try every available bike.
```

This generates all possible assignments.

If:

```text
n = 10
m = 10
```

Complexity becomes roughly:

```text
10!
```

which is too large.

---

# Key Observation

To describe a state we only need:

```text
1. Which worker are we assigning?
2. Which bikes have already been used?
```

The actual assignment history does not matter.

---

# State Definition

## Worker Index

```text
workerIndex
```

Current worker being assigned.

---

## Used Bikes Mask

A bitmask representing assigned bikes.

Example:

```text
bikes = 4
```

Mask:

```text
0101
```

means:

```text
Bike 0 → Used
Bike 1 → Free
Bike 2 → Used
Bike 3 → Free
```

---

# DP State

```text
dfs(workerIndex, usedMask)
```

returns:

```text
Minimum distance needed
to assign bikes to

workers[workerIndex...n-1]
```

given the bikes already used in `usedMask`.

---

# Memoization Insight

Notice:

```text
workerIndex = number of set bits in usedMask
```

because:

```text
One bike is assigned
for every processed worker.
```

Therefore:

```text
workerIndex can be derived
from usedMask.
```

This allows memoization using only:

```text
memo[usedMask]
```

instead of:

```text
memo[workerIndex][usedMask]
```

---

# Recursive Choices

For current worker:

```text
workerIndex
```

Try every bike:

```text
bikeIndex = 0...m-1
```

that is not already used.

---

## Transition

```text
distance(current worker, bike)

+

best assignment for
remaining workers
```

Mathematically:

```text
dfs(workerIndex, usedMask)

=

min(
    distance +
    dfs(workerIndex + 1, newMask)
)
```

---

# Code

```java
class Solution {

    public int assignBikes(int[][] workers,
                           int[][] bikes) {

        int m = bikes.length;

        int[] memo = new int[1 << m];

        Arrays.fill(
                memo,
                Integer.MAX_VALUE
        );

        return dfs(
                workers,
                bikes,
                0,
                0,
                memo
        );
    }

    private int dfs(
            int[][] workers,
            int[][] bikes,
            int workerIndex,
            int usedMask,
            int[] memo) {

        if (workerIndex == workers.length) {
            return 0;
        }

        if (memo[usedMask]
                != Integer.MAX_VALUE) {
            return memo[usedMask];
        }

        int minDist = Integer.MAX_VALUE;

        for (int bikeIndex = 0;
             bikeIndex < bikes.length;
             bikeIndex++) {

            if ((usedMask &
                    (1 << bikeIndex)) == 0) {

                int dist =
                        Math.abs(
                                workers[workerIndex][0]
                                        - bikes[bikeIndex][0])
                                +
                                Math.abs(
                                        workers[workerIndex][1]
                                                - bikes[bikeIndex][1]);

                int nextMask =
                        usedMask |
                                (1 << bikeIndex);

                minDist = Math.min(
                        minDist,
                        dist +
                        dfs(
                                workers,
                                bikes,
                                workerIndex + 1,
                                nextMask,
                                memo
                        )
                );
            }
        }

        memo[usedMask] = minDist;

        return minDist;
    }
}
```

---

# Understanding the Bitmask

Assume:

```text
3 bikes
```

Mask representation:

| Mask | Binary | Used Bikes |
|--------|--------|------------|
| 0 | 000 | None |
| 1 | 001 | Bike 0 |
| 2 | 010 | Bike 1 |
| 3 | 011 | Bike 0,1 |
| 7 | 111 | All Bikes |

---

## Check if Bike is Used

```java
(usedMask & (1 << bikeIndex)) != 0
```

Example:

```text
usedMask = 0101
bikeIndex = 2
```

```text
0101
0100
----
0100
```

Bike 2 is already assigned.

---

## Mark a Bike as Used

```java
nextMask = usedMask | (1 << bikeIndex);
```

Example:

```text
usedMask = 0001

Assign Bike 2
```

```text
0001
0100
----
0101
```

---

# Dry Run

## Input

```text
workers = [[0,0],[2,1]]

bikes = [[1,2],[3,3]]
```

---

### State

```text
dfs(0, 00)
```

Worker 0 can choose:

```text
Bike 0
Bike 1
```

---

### Choose Bike 0

Distance:

```text
|0-1| + |0-2|
=
3
```

Next:

```text
dfs(1, 01)
```

Worker 1 must take Bike 1.

Distance:

```text
|2-3| + |1-3|
=
3
```

Total:

```text
3 + 3 = 6
```

---

### Choose Bike 1

Distance:

```text
|0-3| + |0-3|
=
6
```

Next:

```text
dfs(1,10)
```

Worker 1 takes Bike 0.

Distance:

```text
|2-1| + |1-2|
=
2
```

Total:

```text
6 + 2 = 8
```

---

### Answer

```text
min(6, 8)
=
6
```

---

# Why Memoization Works

Without memoization:

```text
Same bike configurations
are explored repeatedly.
```

Example:

```text
usedMask = 10101
```

can be reached through multiple assignment orders.

Instead:

```text
Compute once
Store result
Reuse later
```

---

# Complexity Analysis

## Number of States

A state is represented by:

```text
usedMask
```

For `m` bikes:

```text
2^m
```

possible masks.

---

## Transition Per State

For each state:

```text
Try every bike
```

```text
O(m)
```

work.

---

## Time Complexity

```text
O(m × 2^m)
```

---

## Space Complexity

Memoization:

```text
O(2^m)
```

Recursion stack:

```text
O(n)
```

Overall:

```text
O(2^m)
```

---

# Visual Representation

Suppose:

```text
m = 4
```

Bitmask tree:

```text
0000
├── 0001
├── 0010
├── 0100
└── 1000
```

Each level:

```text
Assign one worker
```

Each bit:

```text
Bike assigned
```

---

# Pattern Recognition

This problem belongs to:

```text
DP + Bitmask
```

Common clues:

✅ Small number of entities (`n <= 10`)

✅ Need to assign items uniquely

✅ Each choice affects future choices

✅ Minimize or maximize cost

---

# Similar Problems

| Problem | Pattern |
|----------|----------|
| 1066. Campus Bikes II | DP + Bitmask |
| 526. Beautiful Arrangement | DP + Bitmask |
| 847. Shortest Path Visiting All Nodes | BFS + Bitmask |
| 698. Partition to K Equal Sum Subsets | DP + Bitmask |
| Traveling Salesman Problem | DP + Bitmask |

---

# Key Takeaways

| Observation | Benefit |
|------------|----------|
| State only depends on used bikes | Enables memoization |
| Use bitmask to represent assigned bikes | Compact state representation |
| Worker index grows with assigned bikes | Can derive worker from mask |
| Explore every unused bike | Guarantees optimal assignment |
| Store results for each mask | Avoids exponential recomputation |

---

# Complexity Summary

| Approach | Time | Space |
|-----------|--------|--------|
| Memoization + Bitmask DP | O(m × 2^m) | O(2^m) |

✅ **Optimal Approach:** DP + Bitmask Memoization using `usedMask` as the DP state.
