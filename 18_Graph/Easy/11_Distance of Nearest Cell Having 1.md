# Distance of Nearest Cell Having 1

## Problem Statement

Given a binary matrix `grid[][]` of size `N x M`:

```text
0 → Empty Cell
1 → Source Cell
```

For every cell, find the **minimum distance to the nearest cell containing 1**.

Distance is calculated using the 4 directions:

```text
Up
Down
Left
Right
```

Return the distance matrix.

---

# Example

Input

```text
1 0 1
1 1 0
1 0 0
```

Output

```text
0 1 0
0 0 1
0 1 2
```

---

# Visualization

Input:

```text
1 0 1
1 1 0
1 0 0
```

All cells having:

```text
1
```

act as sources.

```text
S 0 S
S S 0
S 0 0
```

Distance matrix:

```text
0 1 0
0 0 1
0 1 2
```

---

# Naive Approach

For every cell:

```text
Run BFS
Find nearest 1
```

Time Complexity:

```text
O((N*M)^2)
```

Too slow.

---

# Key Observation

Instead of:

```text
For every 0
Find nearest 1
```

Think:

```text
Start from all 1s simultaneously
Spread outward
```

This is a classic:

```text
Multi Source BFS
```

---

# Why Multi-Source BFS?

Suppose:

```text
0 0 0

0 1 0

0 0 0
```

If BFS starts from:

```text
Center 1
```

then

```text
Level 0 = Distance 0

Level 1 = Distance 1

Level 2 = Distance 2
```

Every cell automatically gets the minimum distance.

---

# Multi-Source BFS Idea

Put all cells containing:

```text
1
```

into the queue initially.

Then run BFS normally.

Each BFS layer represents:

```text
Distance from nearest 1
```

---

# Algorithm

### Step 1

Create:

```java
Queue
visited[][]
distance[][]
```

---

### Step 2

Insert all cells containing 1.

```java
grid[i][j] == 1
```

into queue.

Mark:

```java
visited[i][j] = true

distance[i][j] = 0
```

---

### Step 3

Run BFS.

Whenever we visit a neighbor:

```java
distance[newRow][newCol]
       =
distance[row][col] + 1
```

---

### Step 4

Return distance matrix.

---

# Dry Run

Input:

```text
1 0 1
1 1 0
1 0 0
```

---

## Initial Queue

```text
(0,0)
(0,2)
(1,0)
(1,1)
(2,0)
```

Distance Matrix:

```text
0 - 0
0 0 -
0 - -
```

---

## Process Layer

From:

```text
(0,0)
```

Visit:

```text
(0,1)
```

Distance:

```text
1
```

---

From:

```text
(0,2)
```

Visit:

```text
(1,2)
```

Distance:

```text
1
```

---

From:

```text
(2,0)
```

Visit:

```text
(2,1)
```

Distance:

```text
1
```

---

Distance Matrix:

```text
0 1 0
0 0 1
0 1 -
```

---

Process next layer.

Visit:

```text
(2,2)
```

Distance:

```text
2
```

Final:

```text
0 1 0
0 0 1
0 1 2
```

---

# BFS Visualization

```text
Level 0

1 . 1
1 1 .
1 . .
```

---

```text
Level 1

1 1 1
1 1 1
1 1 .
```

---

```text
Level 2

1 1 1
1 1 1
1 1 1
```

Distance:

```text
0 1 0
0 0 1
0 1 2
```

---

# Java Solution

```java
import java.util.*;

class Solution {

    static class Pair {

        int row;
        int col;
        int dist;

        Pair(int row, int col, int dist) {
            this.row = row;
            this.col = col;
            this.dist = dist;
        }
    }

    public int[][] nearest(int[][] grid) {

        int n = grid.length;
        int m = grid[0].length;

        int[][] distance = new int[n][m];
        boolean[][] visited = new boolean[n][m];

        Queue<Pair> queue = new LinkedList<>();

        // Add all 1's into queue

        for (int i = 0; i < n; i++) {

            for (int j = 0; j < m; j++) {

                if (grid[i][j] == 1) {

                    queue.offer(new Pair(i, j, 0));

                    visited[i][j] = true;
                }
            }
        }

        int[] delRow = {-1, 0, 1, 0};
        int[] delCol = {0, 1, 0, -1};

        while (!queue.isEmpty()) {

            Pair current = queue.poll();

            int row = current.row;
            int col = current.col;
            int dist = current.dist;

            distance[row][col] = dist;

            for (int i = 0; i < 4; i++) {

                int newRow = row + delRow[i];
                int newCol = col + delCol[i];

                if (newRow >= 0 &&
                    newRow < n &&
                    newCol >= 0 &&
                    newCol < m &&
                    !visited[newRow][newCol]) {

                    visited[newRow][newCol] = true;

                    queue.offer(
                        new Pair(
                            newRow,
                            newCol,
                            dist + 1
                        )
                    );
                }
            }
        }

        return distance;
    }
}
```

---

# Optimized Version (Without Storing Distance in Queue)

```java
import java.util.*;

class Solution {

    public int[][] nearest(int[][] grid) {

        int n = grid.length;
        int m = grid[0].length;

        int[][] dist = new int[n][m];

        Queue<int[]> queue =
                new LinkedList<>();

        for (int i = 0; i < n; i++) {

            for (int j = 0; j < m; j++) {

                if (grid[i][j] == 1) {

                    queue.offer(new int[]{i, j});
                } else {

                    dist[i][j] = Integer.MAX_VALUE;
                }
            }
        }

        int[] dr = {-1, 0, 1, 0};
        int[] dc = {0, 1, 0, -1};

        while (!queue.isEmpty()) {

            int[] current = queue.poll();

            int row = current[0];
            int col = current[1];

            for (int k = 0; k < 4; k++) {

                int nr = row + dr[k];
                int nc = col + dc[k];

                if (nr >= 0 &&
                    nr < n &&
                    nc >= 0 &&
                    nc < m &&
                    dist[nr][nc] >
                    dist[row][col] + 1) {

                    dist[nr][nc] =
                            dist[row][col] + 1;

                    queue.offer(
                            new int[]{nr, nc});
                }
            }
        }

        return dist;
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

For:

```text
Queue
Visited Array
Distance Matrix
```

---

# Why BFS Gives Minimum Distance?

BFS processes nodes level by level.

```text
Level 0 → Distance 0
Level 1 → Distance 1
Level 2 → Distance 2
```

First time reaching a cell:

```text
Guaranteed shortest distance
```

This is exactly why BFS is used for:

```text
Shortest Path in Unweighted Graph
```

---

# Similar Problems

## Multi Source BFS Pattern

1. Distance of Nearest Cell Having 1
2. Rotten Oranges
3. 01 Matrix
4. Walls and Gates
5. Escape the Spreading Fire
6. Map of Highest Peak

---

# Interview Pattern Recognition

Whenever you hear:

```text
Nearest
Minimum Distance
Closest Source
Shortest Distance from Multiple Sources
```

Think immediately:

```text
Multi Source BFS
```

---

# FAANG Cheat Sheet

```text
Problem:
Distance of Nearest Cell Having 1

Pattern:
Multi Source BFS

Source Nodes:
All cells containing 1

Traversal:
BFS

Queue Initially:
All 1's

Distance:
distance[neighbor]
=
distance[current] + 1

Time:
O(N*M)

Space:
O(N*M)

Key Insight:
Don't run BFS from every 0.
Run one BFS from all 1's simultaneously.
```
