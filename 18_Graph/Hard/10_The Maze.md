# The Maze

## Problem Statement

There is a ball in a `maze` with empty spaces (represented as `0`) and walls (represented as `1`). The ball can go through the empty spaces by rolling **up, down, left, or right**, but it won't stop rolling until it **hits a wall**. When the ball stops, it could choose the next direction.

Given the `m x n` matrix `maze`, the ball's `start` position, and the `destination` position where `start = [start_row, start_col]` and `destination = [destination_row, destination_col]`, return `true` *if the ball can stop at the destination, otherwise return `false`*.

The overall runtime complexity must be strictly bounded by:

```text
O(m × n)
```

*(where m is the number of rows and n is the number of columns in the maze)*

---

## Example 1

### Input

```text
maze = [,
 ,
 ,
 ,
  [0, 0, 0, 0, 0]
]
start = [0, 4]
destination = [4, 4]
```

### Output

```text
true
```

### Explanation

```text
One possible path is: 
- Roll left:   (0, 4) -> (0, 3)
- Roll down:   (0, 3) -> (1, 3) -> (2, 3) -> hit wall at (3, 3) -> stop at (2, 3)
- Roll left:   (2, 3) -> (2, 2) -> (2, 1) -> (2, 0)
- Roll down:   (2, 0) -> (3, 0 is wall) -> stop at (4, 0)
- Roll right:  (4, 0) -> (4, 1) -> (4, 2) -> (4, 3) -> (4, 4) -> stop at (4, 4)
The ball successfully stopped at the destination.
```

---

## Example 2

### Input

```text
maze = [,
 ,
 ,
 ,
  [0, 0, 0, 0, 0]
]
start = [0, 4]
destination = [3, 2]
```

### Output

```text
false
```

### Explanation

```text
The ball can pass through the destination cell, but it cannot stop there because it won't hit a wall.
```

---

# Intuition

This problem can be modeled as a graph traversal task across a 2D matrix grid. However, unlike standard grid search problems (like *Number of Islands* or *Flood Fill*) where the ball can stop on any adjacent cell, **the ball can only change directions when it stops moving at a wall boundary**.

This means our valid **graph nodes are only the stopping coordinates** (cells right next to a wall or maze edge).

We can solve this problem elegantly using **Breadth-First Search (BFS)** or **Depth-First Search (DFS)**:
1. **Visited Tracking State:** Maintain a 2D boolean array `visited[m][n]` to keep track of stopping positions we have already evaluated. This prevents processing identical branch paths over and over again.
2. **Directional Rolling Loops:** When evaluating a starting or stopping coordinate `(currRow, currCol)`, try rolling in all 4 cardinal directions (Up, Down, Left, Right).
3. **The Wall Collision Condition:** Instead of stepping one cell out, use a nested `while` loop to advance the coordinate in the chosen direction continuously until it hits a wall (`1`) or goes out of the matrix bounds.
4. **Step Back Verification:** Because the `while` loop terminates only *after* stepping onto a wall or running out of bounds, we must step back exactly one cell to find the true, safe stopping coordinate.
5. **Destination Match:** If this true stopping position matches our target `destination`, return `true` immediately. If it hasn't been visited before, mark it as visited and push it onto our queue to explore next.

---

# State Definition

Let:

```text
boolean[][] visited = new boolean[m][n];   // Matrix tracking validated stopping coordinates
Queue<int[]> queue = new LinkedList<>();   // BFS queue driving rolling coordinate sweeps
```

---

# Recurrence / Directional Roll Condition

```text
For each of the 4 cardinal direction vectors (dRow, dCol):
    newRow = currRow
    newCol = currCol
    While (newRow + dRow, newCol + dCol) is inside bounds and is empty space (0):
        newRow += dRow
        newCol += dCol
    If !visited[newRow][newCol]:
        queue.offer([newRow, newCol])
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By utilizing a nested `while` loop to slide directly across empty paths until hitting a boundary, and tracking stopping coordinates using a boolean visited bitmask, each valid state configuration is evaluated a constant number of times. This resolves the path routing problem securely within an optimal **O(m × n)** timeline.

---

## Java Code (BFS Rolling Variant)

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    public boolean hasPath(int[][] maze, int[] start, int[] destination) {
        if (maze == null || maze.length == 0 || maze[0].length == 0) {
            return false;
        }

        int m = maze.length;
        int n = maze[0].length;

        // 2D boolean array to track visited stopping positions
        boolean[][] visited = new boolean[m][n];
        
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(start);
        visited[start[0]][start[1]] = true;

        // Direction vectors for: Up, Down, Left, Right
        int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

        // Launch the rolling BFS traversal wave
        while (!queue.isEmpty()) {
            int[] curr = queue.poll();
            int currRow = curr[0];
            int currCol = curr[1];

            // Case A: The ball successfully stopped exactly at the target destination
            if (currRow == destination[0] && currCol == destination[1]) {
                return true;
            }

            // Step 1: Attempt to roll the ball in all 4 cardinal directions
            for (int[] dir : directions) {
                int r = currRow;
                int c = currCol;
                int dRow = dir[0];
                int dCol = dir[1];

                // Step 2: Keep sliding continuously until hitting a wall or edge boundary
                while (r + dRow >= 0 && r + dRow < m && c + dCol >= 0 && c + dCol < n && maze[r + dRow][c + dCol] == 0) {
                    r += dRow;
                    c += dCol;
                }

                // Step 3: Check if this new stopping coordinate is unvisited
                if (!visited[r][c]) {
                    visited[r][c] = true;
                    queue.offer(new int[]{r, c});
                }
            }
        }

        // Step 4: All valid stopping positions evaluated with zero routes reaching the target
        return false;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(m × n) -> In the worst-case scenario, the ball may traverse each empty cell a constant number of times during the rolling loops, but it will only trigger a 4-direction branching step from any specific stopping cell once.
Space Complexity: O(m × n) -> Memory allocated to house the 2D boolean visited matrix and tracking coordinates within the BFS queue framework.
```

---

# Dry Run

## Input

```text
maze = [[0, 0, 1], [0, 0, 0], [1, 0, 0]]
start =, destination = [1, 2]
```

---

### BFS Execution Trace

```text
Initial Queue: [[0, 0]]. visited[0][0] = true.

1. Pop:
   - Roll Down: slides through (1,0) until hitting wall at (2,0). True Stop: (1,0). 
     visited[1][0] = true -> queue = [[1, 0]]
   - Roll Right: slides through (0,1) until hitting wall at (0,2). True Stop: (0,1).
     visited[0][1] = true -> queue = [[1, 0], [0, 1]]

2. Pop:
   - Roll Right: slides through (1,1) then (1,2) until hitting right edge boundary. True Stop: (1,2).
   - Check: (1,2) == destination [1, 2] -> TRUE!
   - Returns true immediately.

The routine successfully isolates the correct rolling path configuration.
```

---

# Visualization

## Rolling Trajectory Matrix Sweep

```text
   [Start]
    (S) ────► ────► [Wall]
     │
     ▼
     ▼
  [Stop] ───► ───► [Destination]
```

---

# Graph Paradigm Pattern

## Identify State

```text
Grid-based coordinate path-finding tracking utilising momentum-sliding rolling checks across a linear BFS queue.
```

---

## Choices

```text
Slide iteratively along 4 structural vectors until hitting a grid boundary or value block. Evaluate only true stop positions.
```

---

## Key Takeaways

✅ Advanced variation of 2D grid matrix maze-routing algorithms.

✅ Reframing graph nodes strictly as **stopping coordinates** rather than adjacent step cells simplifies the state exploration search space, keeping runtime metrics linear.

✅ The nested sliding `while` loop provides an excellent real-world implementation model for calculating **momentum-based movements**, sliding block puzzles, or physics raycasts.

✅ Common Graph Design Pattern:

```text
Momentum-Based Node Traversal via Matrix Boundary Scanning
```

### Similar Problems

- The Maze II (Find shortest distance value - LeetCode 505)
- The Maze III (Find shortest distance with rolling ball instructions - LeetCode 490)
- Hit Bricks (LeetCode 803)
- Shortest Path in Binary Matrix (LeetCode 1091)
