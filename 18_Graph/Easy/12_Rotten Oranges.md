# Rotten Oranges

## Problem Statement

You are given an `m x n` grid where:

```text
0 = Empty Cell
1 = Fresh Orange
2 = Rotten Orange
```

Every minute:

```text
Any fresh orange adjacent
(up, down, left, right)
to a rotten orange
becomes rotten.
```

Return:

```text
Minimum time required
to rot all oranges.
```

If impossible:

```text
Return -1
```

---

# Example 1

Input

```text
2 1 1
1 1 0
0 1 1
```

Output

```text
4
```

---

# Example 2

Input

```text
2 1 1
0 1 1
1 0 1
```

Output

```text
-1
```

Explanation:

```text
Bottom-left orange
can never be reached.
```

---

# Example 3

Input

```text
0 2
```

Output

```text
0
```

---

# Intuition

Imagine rot spreading like:

```text
Fire
Virus
Infection
```

At every minute:

```text
One rotten orange
infects all neighboring fresh oranges.
```

This is exactly:

```text
Level Order Traversal
```

which means:

```text
BFS
```

---

# Why Normal BFS Works?

Suppose:

```text
2 1 1
1 1 1
1 1 1
```

Minute 0:

```text
2 1 1
1 1 1
1 1 1
```

Minute 1:

```text
2 2 1
2 1 1
1 1 1
```

Minute 2:

```text
2 2 2
2 2 1
2 1 1
```

Every BFS level:

```text
= One Minute
```

---

# Key Observation

Initially there may be:

```text
Multiple rotten oranges.
```

Example:

```text
2 1 1

1 2 1

1 1 2
```

All rotten oranges spread infection simultaneously.

Therefore:

```text
Multi Source BFS
```

---

# Pattern Recognition

Whenever you see:

```text
Spread
Infection
Fire
Virus
Propagation
Nearest Source
Shortest Time
```

Think:

```text
Multi Source BFS
```

---

# Multi Source BFS

Initially:

```text
Put ALL rotten oranges
into queue.
```

Then perform BFS.

Each BFS layer:

```text
Represents one minute.
```

---

# Algorithm

### Step 1

Traverse grid.

Count:

```text
Fresh oranges
```

Store:

```text
Rotten oranges
```

inside queue.

---

### Step 2

Run BFS.

Every rotten orange infects:

```text
Up
Down
Left
Right
```

neighbors.

---

### Step 3

Whenever a fresh orange rots:

```text
freshCount--
```

Push into queue.

---

### Step 4

Track:

```text
Time (Minutes)
```

using BFS levels.

---

### Step 5

After BFS:

If fresh oranges remain:

```text
return -1
```

Else:

```text
return time
```

---

# Dry Run

Input

```text
2 1 1
1 1 0
0 1 1
```

---

## Initial

Queue:

```text
[(0,0)]
```

Fresh:

```text
6
```

Time:

```text
0
```

---

## Minute 1

Rot:

```text
(0,1)
(1,0)
```

Grid:

```text
2 2 1
2 1 0
0 1 1
```

Fresh:

```text
4
```

Time:

```text
1
```

---

## Minute 2

Rot:

```text
(0,2)
(1,1)
```

Grid:

```text
2 2 2
2 2 0
0 1 1
```

Fresh:

```text
2
```

Time:

```text
2
```

---

## Minute 3

Rot:

```text
(2,1)
```

Grid:

```text
2 2 2
2 2 0
0 2 1
```

Fresh:

```text
1
```

Time:

```text
3
```

---

## Minute 4

Rot:

```text
(2,2)
```

Grid:

```text
2 2 2
2 2 0
0 2 2
```

Fresh:

```text
0
```

Time:

```text
4
```

Answer:

```text
4
```

---

# BFS Visualization

Minute 0

```text
R F F
F F .
. F F
```

---

Minute 1

```text
R R F
R F .
. F F
```

---

Minute 2

```text
R R R
R R .
. F F
```

---

Minute 3

```text
R R R
R R .
. R F
```

---

Minute 4

```text
R R R
R R .
. R R
```

---

# Java Solution

```java
import java.util.*;

class Solution {

    static class Pair {

        int row;
        int col;

        Pair(int row, int col) {

            this.row = row;
            this.col = col;
        }
    }

    public int orangesRotting(int[][] grid) {

        int n = grid.length;
        int m = grid[0].length;

        Queue<Pair> queue =
                new LinkedList<>();

        int fresh = 0;

        // Collect all rotten oranges

        for (int i = 0; i < n; i++) {

            for (int j = 0; j < m; j++) {

                if (grid[i][j] == 2) {

                    queue.offer(
                        new Pair(i, j)
                    );
                }

                else if (grid[i][j] == 1) {

                    fresh++;
                }
            }
        }

        int[] dr = {-1, 0, 1, 0};
        int[] dc = {0, 1, 0, -1};

        int minutes = 0;

        while (!queue.isEmpty() &&
                fresh > 0) {

            int size = queue.size();

            minutes++;

            for (int i = 0; i < size; i++) {

                Pair current =
                        queue.poll();

                int row = current.row;
                int col = current.col;

                for (int k = 0; k < 4; k++) {

                    int nr = row + dr[k];
                    int nc = col + dc[k];

                    if (nr >= 0 &&
                        nr < n &&
                        nc >= 0 &&
                        nc < m &&
                        grid[nr][nc] == 1) {

                        grid[nr][nc] = 2;

                        fresh--;

                        queue.offer(
                            new Pair(nr, nc)
                        );
                    }
                }
            }
        }

        return fresh == 0
                ? minutes
                : -1;
    }
}
```

---

# Complexity Analysis

Let:

```text
N = Rows
M = Columns
```

---

### Time Complexity

```text
O(N × M)
```

Each cell enters queue once.

---

### Space Complexity

```text
O(N × M)
```

Queue can contain all cells.

---

# Why BFS Gives Minimum Time?

BFS processes nodes level by level.

```text
Level 0 → Minute 0

Level 1 → Minute 1

Level 2 → Minute 2
```

Since all rotten oranges spread simultaneously:

```text
First reach
=
Minimum time
```

Exactly the same principle as:

```text
Shortest Path in Unweighted Graph
```

---

# Alternative Approach Using Time in Queue

Store:

```java
(row, col, time)
```

Example:

```java
queue.offer(
    new int[]{row,col,time}
);
```

Then:

```java
answer =
max(time)
```

Works fine but uses extra space.

Level-order BFS is cleaner.

---

# Similar Problems

## Multi Source BFS

1. Rotten Oranges
2. Distance of Nearest Cell Having 1
3. 01 Matrix
4. Walls and Gates
5. Map of Highest Peak
6. Escape the Spreading Fire

---

# Interview Follow-Up Questions

### Q1: Why BFS and not DFS?

DFS:

```text
Does not guarantee
minimum time.
```

BFS:

```text
Processes level by level.

Each level = one minute.
```

---

### Q2: Why Multi Source BFS?

Because:

```text
Multiple rotten oranges
spread simultaneously.
```

Starting BFS from only one source:

```text
Wrong answer.
```

---

### Q3: What does one BFS level represent?

```text
One Minute
```

Very common interviewer question.

---

# Common Mistakes

## Mistake 1

Running BFS separately from every rotten orange.

```text
O((N*M)^2)
```

Unnecessary.

---

## Mistake 2

Not counting fresh oranges.

Then impossible to know:

```text
Some oranges never rotted.
```

---

## Mistake 3

Incrementing time for every cell.

Wrong.

Time increases after:

```text
One complete BFS level.
```

---

# FAANG Cheat Sheet

```text
Problem:
Rotten Oranges

Pattern:
Multi Source BFS

Sources:
All Rotten Oranges (2)

Target:
Fresh Oranges (1)

One BFS Level:
One Minute

Queue:
Initially contains all rotten oranges

Time:
O(N*M)

Space:
O(N*M)

Key Formula:

Fresh becomes Rotten
→ Add to Queue

If fresh > 0 after BFS
→ Return -1

Else
→ Return minutes
```

# Key Insight

This problem is the **most important Multi-Source BFS problem** and is frequently used as a template for many advanced graph/grid questions.

```text
Rotten Oranges
        ↓
Multi Source BFS
        ↓
Level Order Traversal
        ↓
Each Level = One Minute
        ↓
Answer = Total Levels
```

Once you recognize **"spread from multiple sources simultaneously"**, the solution is almost always **Multi-Source BFS**.
