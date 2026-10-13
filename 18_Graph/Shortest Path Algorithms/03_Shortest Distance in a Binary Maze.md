# Shortest Distance in a Binary Maze

## Problem Statement

Given an `m x n` binary matrix `grid` representing a maze, where `0` represents an empty traversable cell and `1` represents a wall that blocks movement, find the **shortest distance** from a given `source` cell to a `destination` cell. 

You can move **up, down, left, or right** to an adjacent empty cell. The distance is defined as the total number of steps/cells traversed along the path. If the destination is completely unreachable, return `-1`.

Both `source` and `destination` are given as integer coordinate arrays of size 2: `[row, col]`.

The overall runtime complexity must be strictly bounded by:

```text
O(m × n)
```

*(where m is the number of rows and n is the number of columns in the grid)*

---

## Example 1

### Input

```text
grid = [,
 ,
 ,
  [0, 1, 1, 0]
]
source = [0, 0]
destination = [2, 3]
```

### Output

```text
5
```

### Explanation

```text
One shortest path is: (0,0) → (0,1) → (0,2) → (1,2) → (2,2) → (2,3).
Total steps taken = 5.
```

---

## Example 2

### Input

```text
grid = [,
  [1, 0]
]
source = [0, 0]
destination = [1, 1]
```

### Output

```text
-1
```

### Explanation

```text
The path is completely blocked by walls, making it impossible to reach the destination.
```

---

# Intuition

Since all valid steps between adjacent empty cells have a uniform weight of exactly **1 unit**, finding the absolute shortest path can be solved optimally using a standard **Breadth-First Search (BFS)** traversal. 

BFS expands outward from the source cell radially, layer by layer. The very first time the BFS wave steps onto the `destination` cell, it is mathematically guaranteed to have found the path with the minimal number of steps.

1. **Queue Configuration:** Maintain a FIFO queue storing the coordinate states paired with their current distance: `(row, col, currentDistance)`.
2. **Infinite Loop Prevention:** To prevent the BFS wave from stepping backward and turning in infinite cycles, we mark cells as visited. We can do this with zero extra memory overhead by mutating visited cells in the `grid` from `0` to `1` in-place, treating them as temporary walls.
3. **Cardinal Wave Expansion:** For each cell popped from the queue, if it matches the `destination` coordinates, return its associated `currentDistance`. Otherwise, branch out in all 4 cardinal directions (Up, Down, Left, Right). 
4. If a neighbor is inside the grid boundaries and contains a `0`, it's an unvisited empty cell. Push it onto the queue with a distance of `currentDistance + 1` and immediately flip its grid cell value to `1`.

---

# State Definition

Let:

```text
Queue<int[]> queue = new LinkedList<>(); // BFS queue storing [row, col, distance] tuples
grid[row][col] = 1;                      // In-place mutation of visited coordinates
```

---

# Recurrence / Distance Update Condition

```text
For each of the 4 cardinal directions (dRow, dCol):
    nextRow = currRow + dRow
    nextCol = currCol + dCol
    If (nextRow, nextCol) is inside grid boundaries AND grid[nextRow][nextCol] == 0:
        if (nextRow, nextCol) == destination -> Return currentDistance + 1
        queue.offer([nextRow, nextCol, currentDistance + 1])
        grid[nextRow][nextCol] = 1 // Mark visited instantly
```

---

# Optimal Structural Graph Shortest-Path Solution

## Idea

By processing grid cells layer by layer using a simple FIFO queue sweep and marking visited indices in-place inside the primitive matrix array, every coordinate is evaluated a constant number of times. This evaluates the shortest path inside a highly optimized **O(m × n)** timeline.

---

## Java Code (BFS Layer Propagation)

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    public int shortestDistance(int[][] grid, int[] source, int[] destination) {
        // Edge Case: If the source or destination itself is blocked by a wall
        if (grid == null || grid.length == 0 || grid[0].length == 0 || 
            grid[source[0]][source[1]] == 1 || grid[destination[0]][destination[1]] == 1) {
            return -1;
        }

        // Shortcut: If source and destination coordinates are identical
        if (source[0] == destination[0] && source[1] == destination[1]) {
            return 0;
        }

        int m = grid.length;
        int n = grid[0].length;

        // BFS Queue storing arrays of size 3: [row, col, current_distance]
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{source[0], source[1], 0});
        
        // Mutate the source cell in-place to 1 to mark it as visited
        grid[source[0]][source[1]] = 1;

        // Direction vectors for 4 cardinal movements: Up, Down, Left, Right
        int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

        // Launch the radial level-order BFS expansion wave
        while (!queue.isEmpty()) {
            int[] curr = queue.poll();
            int r = curr[0];
            int c = curr[1];
            int dist = curr[2];

            // Explore all 4 adjacent directions
            for (int[] dir : directions) {
                int nextR = r + dir[0];
                int nextC = c + dir[1];

                // Check grid boundaries and confirm the cell is an unvisited empty space (0)
                if (nextR >= 0 && nextR < m && nextC >= 0 && nextC < n && grid[nextR][nextC] == 0) {
                    
                    // Case A: Destination cell hit! Return the incremented distance instantly
                    if (nextR == destination[0] && nextC == destination[1]) {
                        return dist + 1;
                    }

                    // Case B: Legitimate path cell; push onto queue and mark visited in-place
                    queue.offer(new int[]{nextR, nextC, dist + 1});
                    grid[nextR][nextC] = 1; 
                }
            }
        }

        // Step 4: Queue exhausted with zero paths breaking through to the target
        return -1;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(m × n) -> Where m is the number of rows and n is the number of columns. The level-order BFS visits each cell at most once and inspects its neighbors a constant number of times.
Space Complexity: O(m × n) -> Memory utilized by the BFS queue to track active wavefront elements in a worst-case open matrix environment.
```

---

# Dry Run

## Input

```text
grid = [[0, 0], [1, 0]], source =, destination = [1, 1]
```

---

### Execution Trace

```text
Initial state: queue = [[0,0, dist=0]], grid[0][0] mutated to 1

1. Pop [0, 0, dist=0]:
   - check direction Right (0,1): grid[0][1] == 0 -> valid!
     - Check: (0,1) == destination ([1,1]) -> False.
     - queue = [[0,1, dist=1]], grid[0][1] mutated to 1.
   - check direction Down (1,0): grid[1][0] == 1 -> blocked wall, skip.

2. Pop [0, 1, dist=1]:
   - check direction Down (1,1): grid[1][1] == 0 -> valid!
     - Check: (1,1) == destination ([1,1]) -> MATCH ENCOUNTERED!
     - Returns dist + 1 = 1 + 1 = 2.

Output returns exactly 2.
```

---

# Visualization

## Layer-by-Layer Wave Front Expansion

```text
   Layer 0 (Dist 0)      Layer 1 (Dist 1)      Layer 2 (Target Match!)
       ┌───┬───┐             ┌───┬───┐             ┌───┬───┐
       │ 0 │ 0 │             │ 1 │ 1 │             │ 1 │ 1 │
       ├───┼───┤   ──►       ├───┼───┤   ──►       ├───┼───┤
       │ 1 │ 0 │             │ 1 │ 0 │             │ 1 │ 1 │
       └───┴───┘             └───┴───┘             └───┴───┘
     Start at (0,0)        Waves hits (0,1)       Destination unified!
```

---

# Graph Paradigm Pattern

## Identify State

```text
Single-source shortest-path matrix cell expansion driven by a flat, uniform level-order BFS wave pipeline.
```

---

## Choices

```text
Radiate outward 4-directionally. Flip empty spaces to wall values on first-touch to block retrospective paths. Catch target matches at the gate.
```

---

## Key Takeaways

✅ Baseline fundamental grid matrix shortest path framework.

✅ Overwriting the grid cells in-place (`0 -> 1`) provides an elegant space optimization step that avoids allocating an explicit boolean `visited[][]` tracking layout.

✅ Universal concept representing **pathfinding nodes** inside game maps, navigating warehouse routing grids for robotics, and driving simple pixel processing kernels.

✅ Common Graph Design Pattern:

```text
Grid Matrix Shortest Path via Level-Order BFS Wave Propagation
```

### Similar Problems

- Shortest Path in Binary Matrix (8-directional variant - LeetCode 1091)
- 01 Matrix (LeetCode 542)
- Rotting Oranges (LeetCode 994)
- Shortest Path in an Undirected Graph with Unit Weights
