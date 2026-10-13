# Swim in Rising Water

## Problem Statement

You are given an `n x n` integer matrix `grid` where each cell `grid[i][j]` represents the elevation at that coordinate `(i, j)`.

The rain starts to fall. At time `t`, the depth of the water everywhere is `t`. You can swim from a cell to another 4-directionally adjacent cell if and only if both the elevation of the current cell and the adjacent cell are at most `t`. You can swim infinite distances in zero time. Of course, you must stay within the boundaries of the grid during your swim.

You start at the top-left cell `(0, 0)`. Return *the **least time** until you can reach the bottom-right cell `(n - 1, n - 1)`*.

The overall runtime complexity must be strictly bounded by:

```text
O(n² log(n))
```

---

## Example 1

### Input

```text
grid = [,
  [1, 3]
]
```

### Output

```text
3
```

### Explanation

```text
At time 0, you are at cell (0, 0), which has elevation 0.
You cannot move anywhere yet because the surrounding cells have elevations 1 and 2, which are greater than the water level t = 0.
At time 3, the water level is 3 everywhere. All cells in the grid can be visited.
The path to the destination is: (0,0) → (1,0) → (1,1). The maximum height on this path is 3.
```

---

## Example 2

### Input

```text
grid = [,
 ,
 ,
 ,
  [10, 9, 8, 7, 6]
]
```

### Output

```text
16
```

### Explanation

```text
The optimal path is:
(0,0) → (0,1) → (0,2) → (0,3) → (0,4)
                                  ↓
(1,4) ← (1,3) ← (1,2) ← (1,1) ← (1,0 is 24, skipped)
  ↓
(2,4) → (2,3) → (2,2) → (2,1) → (2,0) ... wait, from (2,4) we can go straight down the column block:
The peak bottleneck elevation along the snake path reaching (4,4) is 16.
```

---

# Intuition

This problem can be effectively rephrased as finding a path from `(0,0)` to `(n-1,n-1)` that minimizes the **maximum cell elevation encountered along the path** (a classic Minimax path optimization problem). 

Because the required waiting time is strictly bounded by the highest plateau bottleneck we cross, we can solve this problem optimally by modifying **Dijkstra's Algorithm** to process grid nodes greedily using a **Min-Heap (PriorityQueue)**:

1. **Time Table Matrix:** Create a 2D array `timeTable[n][n]` initialized to infinity (`Integer.MAX_VALUE`) to store the minimum time needed to reach each cell coordinate. Set `timeTable[0][0] = grid[0][0]`.
2. **Min-Heap Configuration:** Initialize a Min-Heap storing cell tuples: `(row, col, timeSeen)`. Seed it with the starting cell `(0, 0, grid[0][0])`.
3. **Greedy Wavefront Expansion:** Pop the cell with the lowest `timeSeen` from the heap. If we hit the destination cell `(n - 1, n - 1)`, its recorded time is guaranteed to be optimal—return it immediately.
4. **Relaxation Condition:** For each of the 4 cardinal neighbors `(nextR, nextC)`:
   * The minimum time required to swim into the neighbor is the **maximum** between the time already spent reaching the current cell (`timeSeen`) and the neighbor's own raw physical elevation (`grid[nextR][nextC]`):
     ```text
     maxTime = max(timeSeen, grid[nextR][nextC])
     ```
   * If `maxTime` is strictly less than the previously recorded value in `timeTable[nextR][nextC]`, we have discovered a more efficient route. Update the table and push `(nextR, nextC, maxTime)` onto the heap.

---

# State Definition

Let:

```text
int[][] timeTable = new int[n][n];                                     // Tracks the minimal time required to reach each cell
PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a[2] - b[2]); // Holds [row, col, time] nodes sorted ascending
```

---

# Recurrence / Relaxation Condition

```text
For each of the 4 cardinal directions (dRow, dCol):
    nextRow = currRow + dRow
    nextCol = currCol + dCol
    If (nextRow, nextCol) is within boundaries:
        maxTime = max(timeSeen, grid[nextRow][nextCol])
        If maxTime < timeTable[nextRow][nextCol]:
            timeTable[nextRow][nextCol] = maxTime
            minHeap.offer([nextRow, nextCol, maxTime])
```

---

# Optimal Structural Graph Shortest-Path Solution

## Idea

By utilizing a Min-Heap to greedily extract the cell path containing the smallest pool bottleneck time and checking coordinates via a standard 4-directional neighborhood sweep, each vertex configuration is relaxed optimally. This completes the graph search execution securely inside **O(n² log(n))** parameters.

---

## Java Code (Modified Dijkstra Paradigm)

```java
import java.util.Arrays;
import java.util.PriorityQueue;

class Solution {

    public int swimInWater(int[][] grid) {
        if (grid == null || grid.length == 0) {
            return 0;
        }

        int n = grid.length;

        // Step 1: Pre-allocate and initialize the time tracking matrix table
        int[][] timeTable = new int[n][n];
        for (int[] row : timeTable) {
            Arrays.fill(row, Integer.MAX_VALUE);
        }
        
        // Base case origin initialization
        timeTable[0][0] = grid[0][0];

        // Min-Heap sorting elements in ascending order: [row, col, current_accumulated_time]
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> Integer.compare(a[2], b[2]));
        minHeap.offer(new int[]{0, 0, grid[0][0]});

        // Direction vectors for 4 cardinal movements: Up, Down, Left, Right
        int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

        // Step 2: Run the greedy Dijkstra extraction wave
        while (!minHeap.isEmpty()) {
            int[] curr = minHeap.poll();
            int r = curr[0];
            int c = curr[1];
            int timeSeen = curr[2];

            // Case A: Reached the terminal bottom-right cell target anchor!
            if (r == n - 1 && c == n - 1) {
                return timeSeen;
            }

            // Optimization Check: If an alternate path found a lower time for this cell already, skip
            if (timeSeen > timeTable[r][c]) {
                continue;
            }

            // Step 3: Relax outgoing paths 4-directionally to all neighboring fields
            for (int[] dir : directions) {
                int nextR = r + dir[0];
                int nextC = c + dir[1];

                // Ensure neighbor coordinates reside securely within matrix boundaries
                if (nextR >= 0 && nextR < n && nextC >= 0 && nextC < n) {
                    // The required time to cross into the next cell is throttled by the highest peak crossed
                    int maxTime = Math.max(timeSeen, grid[nextR][nextC]);

                    // If this route minimizes the target cell's required time, update it
                    if (maxTime < timeTable[nextR][nextC]) {
                        timeTable[nextR][nextC] = maxTime;
                        minHeap.offer(new int[]{nextR, nextC, maxTime});
                    }
                }
            }
        }

        return 0;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(n² log(n)) -> The total number of matrix vertices V is n². In the worst-case scenario, every vertex is pushed and popped from the Min-Heap once, where each heap transaction costs a logarithmic balance step bounded by O(log(n²)) = O(2 log(n)) = O(log(n)).
Space Complexity: O(n²)        -> Memory allocated to house the 2D primitive timeTable matrix array and tracking nodes inside the PriorityQueue container.
```

---

# Dry Run

## Input

```text
grid = [,
  [1, 3]
]
```

---

### Execution Trace

```text
Initial State: timeTable[][] = [[0, INF], [INF, INF]], minHeap = [[0, 0, time=0]]

1. Pop [0, 0, time=0]:
   - check Right neighbor (0,1) [elev=2]: maxTime = max(0, 2) = 2. 2 < INF -> timeTable = 2. minHeap = [[0,1, time=2]]
   - check Down neighbor (1,0) [elev=1]: maxTime = max(0, 1) = 1. 1 < INF -> timeTable = 1. minHeap = [[1,0, time=1], [0,1, time=2]]

2. Pop [1, 0, time=1] (Lowest time item extracted greedily):
   - check Down neighbor (1,1) [elev=3]: maxTime = max(1, 3) = 3. 3 < INF -> timeTable = 3. minHeap = [[0,1, time=2], [1,1, time=3]]

3. Pop [0, 1, time=2]:
   - check Down neighbor (1,1) [elev=3]: maxTime = max(2, 3) = 3. 3 is not < timeTable (3) -> Skip.

4. Pop [1, 1, time=3]:
   - Matches destination coordinate (1,1)! Returns 3.

The routine successfully extracts the path requiring the minimal water-level height.
```

---

# Visualization

## Minimax Water Elevation Wave

```text
     (0,0)[Elev=0] ─────────────► (0,1)[Elev=2]
         │        max(0,2) = 2         │
         │ max(0,1) = 1                │ max(2,3) = 3
         ▼                             ▼
     (1,0)[Elev=1] ─────────────► (1,1)[Elev=3] [Destination]
                  max(1,3) = 3
     
  Path 1: (0,0) -> (0,1) -> (1,1) -> Peak water bottleneck level = 3
  Path 2: (0,0) -> (1,0) -> (1,1) -> Peak water bottleneck level = 3
  Both options yield a minimum effort time boundary of 3.
```

---

# Graph Paradigm Pattern

## Identify State

```text
Single-source minimax bottleneck path optimization resolved via a maximum cell-elevation tracking Dijkstra matrix model.
```

## Choices

```text
Extract wavefront elements greedily via a Min-Heap. Maximize path node elevation weights internally. Minimize global cell limits across the active tracker matrix.
```

Key Takeaways

✅ Advanced, high-tier variation of shortest path graph algorithms.

✅ Shifting the edge relaxation equation from a standard summation operator (dist[u] + w) to a maximum/bottleneck comparison operator (Math.max(dist[u], grid[v])) allows Dijkstra's framework to resolve minimax constraints seamlessly.

✅ Perfect real-world systems model used for network bandwidth optimization (routing data along paths maximizing the minimum available pipe throughput), topography mapping frameworks, and autonomous navigation vehicles tracking safe inclines.

✅ Common Graph Design Pattern:
```text 
Minimax Bottleneck Path Tracking via Greedy Dijkstra Relaxation
```
