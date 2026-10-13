# Path With Minimum Effort

## Problem Statement

You are given a 2D grid `heights` where:

```text
heights[row][col]
```

represents the height of a cell.

You start from:

```text
(0,0)
```

and want to reach:

```text
(m-1,n-1)
```

You can move:

```text
Up
Down
Left
Right
```

The **effort** of a path is defined as:

```text
Maximum absolute height difference
between two consecutive cells
along the path.
```

Return the **minimum effort required** to travel from source to destination.

---

## Example 1

```text
Input:

1 2 2
3 8 2
5 3 5
```

Output:

```text
2
```

---

### Explanation

Path:

```text
1 → 3 → 5 → 3 → 5
```

Differences:

```text
|1-3| = 2
|3-5| = 2
|5-3| = 2
|3-5| = 2
```

Maximum:

```text
2
```

So answer:

```text
2
```

---

## Example 2

```text
Input:

1 2 3
3 8 4
5 3 5
```

Output:

```text
1
```

---

# Key Observation

This is NOT asking:

```text
Minimum Sum Path
```

Instead it asks:

```text
Minimize the maximum edge cost.
```

---

For a path:

```text
1 → 2 → 3 → 8
```

Effort:

```text
max(
|1-2|,
|2-3|,
|3-8|
)

=
max(1,1,5)
=
5
```

---

Another path:

```text
1 → 3 → 5 → 3 → 5
```

Effort:

```text
max(2,2,2,2)

=
2
```

Preferred path:

```text
Effort = 2
```

even if it contains more cells.

---

# Why Dijkstra Works?

Traditional Dijkstra:

```text
dist[v] =
minimum total distance
```

Here:

```text
dist[v] =
minimum effort needed
to reach cell v
```

---

# Modified Relaxation

Normal Dijkstra:

```java
newDist = dist[u] + weight
```

---

Here:

```java
newEffort =
max(
    currentEffort,
    abs(height difference)
)
```

Because path cost is defined as:

```text
Maximum edge encountered so far.
```

---

# State Definition

For every cell:

```text
effort[row][col]
```

stores:

```text
Minimum effort needed
to reach this cell.
```

---

# Algorithm

## Step 1

Create effort matrix.

```java
effort[][] = INF
```

---

## Step 2

Source effort:

```java
effort[0][0] = 0
```

---

## Step 3

Use Min Heap:

```java
(effort,row,col)
```

---

## Step 4

Remove smallest effort cell.

---

## Step 5

Explore neighbors.

Calculate edge weight:

```java
abs(
heights[nr][nc]
-
heights[row][col]
)
```

New effort:

```java
max(currEffort, edgeWeight)
```

---

## Step 6

If better than stored effort:

```java
newEffort < effort[nr][nc]
```

update it.

---

# Dry Run

Grid:

```text
1 2 2
3 8 2
5 3 5
```

---

Start:

```text
(0,0)

effort = 0
```

---

Move right:

```text
1 -> 2

difference = 1

effort = max(0,1)
        = 1
```

---

Move down:

```text
1 -> 3

difference = 2

effort = 2
```

---

Keep expanding the smallest effort cell.

Eventually:

```text
destination effort = 2
```

---

# Visualization

```text
   1 --1--> 2 --0--> 2
   |                  |
   2                  0
   |                  |
   3 --5--> 8 --6--> 2
   |                  |
   2                  3
   |                  |
   5 --2--> 3 --2--> 5
```

Edge labels:

```text
Absolute differences
```

Goal:

```text
Minimize
maximum edge used
```

---

# Java Solution (Dijkstra)

```java
import java.util.*;

class Solution {

    static class Node {
        int row;
        int col;
        int effort;

        Node(int row, int col, int effort) {
            this.row = row;
            this.col = col;
            this.effort = effort;
        }
    }

    public int minimumEffortPath(int[][] heights) {

        int m = heights.length;
        int n = heights[0].length;

        int[][] effort = new int[m][n];

        for (int[] row : effort)
            Arrays.fill(row, Integer.MAX_VALUE);

        PriorityQueue<Node> pq =
                new PriorityQueue<>(
                    (a, b) ->
                        a.effort - b.effort
                );

        effort[0][0] = 0;

        pq.offer(new Node(0, 0, 0));

        int[] dr = {-1, 0, 1, 0};
        int[] dc = {0, 1, 0, -1};

        while (!pq.isEmpty()) {

            Node current = pq.poll();

            int row = current.row;
            int col = current.col;
            int curEffort = current.effort;

            if (row == m - 1 &&
                col == n - 1)
                return curEffort;

            for (int k = 0; k < 4; k++) {

                int nr = row + dr[k];
                int nc = col + dc[k];

                if (nr < 0 ||
                    nc < 0 ||
                    nr >= m ||
                    nc >= n)
                    continue;

                int edgeWeight =
                    Math.abs(
                        heights[nr][nc] -
                        heights[row][col]
                    );

                int newEffort =
                    Math.max(
                        curEffort,
                        edgeWeight
                    );

                if (newEffort < effort[nr][nc]) {

                    effort[nr][nc] =
                        newEffort;

                    pq.offer(
                        new Node(
                            nr,
                            nc,
                            newEffort
                        )
                    );
                }
            }
        }

        return 0;
    }
}
```

---

# Complexity Analysis

Let:

```text
m = rows
n = columns
```

Total cells:

```text
V = m × n
```

Edges:

```text
E ≈ 4mn
```

---

### Time Complexity

Dijkstra:

```text
O(E log V)

O(4mn log(mn))

O(mn log(mn))
```

---

### Space Complexity

Effort Matrix:

```text
O(mn)
```

Priority Queue:

```text
O(mn)
```

Total:

```text
O(mn)
```

---

# Why is this Dijkstra?

Normal Dijkstra stores:

```text
Shortest distance
```

This problem stores:

```text
Minimum effort
```

The greedy property still holds:

```text
Once the smallest effort state
is removed from the min heap,
it is optimal.
```

Hence Dijkstra can be applied with a modified cost function.

---

# Alternative Approach

## Binary Search + BFS/DFS

Observation:

Suppose effort is fixed:

```text
Can we reach destination
using only edges <= effort?
```

This can be checked using:

```text
BFS / DFS
```

---

Binary Search:

```text
0 → 10^6
```

Check feasibility.

Complexity:

```text
O(mn log(10^6))
```

---

# Dijkstra vs Binary Search

### Dijkstra

```text
O(mn log(mn))
```

Most commonly expected solution.

---

### Binary Search + BFS

```text
O(mn log(10^6))
```

Interesting alternative.

---

# Interview Takeaways

### Pattern Recognition

```text
Grid
+
Minimum Maximum Cost
=
Modified Dijkstra
```

---

### Key Relaxation Formula

```java
newEffort =
Math.max(
    currentEffort,
    edgeWeight
);
```

---

### Important Difference

Normal Dijkstra:

```java
dist + weight
```

This Problem:

```java
max(dist, weight)
```

---

### Similar Problems

```text
1631. Path With Minimum Effort

778. Swim in Rising Water

1102. Path With Maximum Minimum Value

1514. Path with Maximum Probability
```

All follow the pattern:

```text
Modified Dijkstra
where path cost is NOT a sum.
```
