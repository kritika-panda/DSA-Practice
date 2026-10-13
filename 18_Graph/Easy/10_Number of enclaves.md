# Number of Enclaves

## Problem Statement

You are given an `m x n` binary matrix `grid`, where `0` represents a water cell and `1` represents a land cell.

A **move** consists of walking from one land cell to another adjacent (4-directionally) land cell or walking off the boundary of the grid.

Return *the number of land cells in `grid` for which we **cannot** walk off the boundary of the grid in any number of moves*.

The overall runtime complexity must be strictly bounded by:

```text
O(m × n)
```

*(where m is the number of rows and n is the number of columns in the grid)*

---

## Example 1

### Input

```text
grid = [
  [0, 0, 0, 0],
  [1, 0, 1, 0],
  [0, 1, 1, 0],
  [0, 0, 0, 0]
]
```

### Output

```text
3
```

### Explanation

```text
There are three 1s that are enclosed by 0s as enclaves. 
The 1 at position (1, 0) is on the boundary and can walk off the grid directly.
The remaining three 1s at (1, 2), (2, 1), and (2, 2) cannot walk off the boundary.
```

---

## Example 2

### Input

```text
grid = [
  [0, 1, 1, 0],
  [0, 0, 1, 0],
  [0, 0, 1, 0],
  [0, 0, 0, 0]
]
```

### Output

```text
0
```

### Explanation

```text
All 1s are connected to at least one boundary land cell and can walk off the grid.
```

---

# Intuition

Instead of searching from internal land cells outward to see if they can leak onto a boundary, we can invert the strategy: **Start from all boundary land cells and flood-fill inward to eliminate land reachable from the outside.**

Any land cell (`1`) sitting directly on the 4 outer borders of the matrix can instantly step off the grid. Furthermore, any land cell connected 4-directionally to these border cells can eventually find its way out. These cells are fundamentally *not* enclaves.

1. **Boundary Sweep:** Scan the first row, last row, first column, and last column. Whenever a boundary cell contains a `1`, initiate a **Depth-First Search (DFS)**.
2. **Flooding/Sinking Pass:** The DFS propagates inward, mutating all connected land cells from `1` to `0`. This sinks the non-enclave land masses in-place, neutralizing them so they won't confuse our downstream count.
3. **Enclave Collection Pass:** After all boundary-connected land cells have been flooded, scan the entire grid a second time. Any remaining `1` left intact inside the inner coordinates must be an enclave because it has zero connection routes to the outer rim. Sum up these remaining land cells.

---

# State Definition

The state transformations are managed directly inside the grid itself by sinking non-enclave land cells into water:
```text
grid[i][j] = 0 // Turning boundary-connected land masses to water to filter them out
```

---

# Recurrence / Expansion Condition

```text
For a boundary-connected land cell (i, j), lookahead in 4 directions:
    dfs(i - 1, j) // Up
    dfs(i + 1, j) // Down
    dfs(i, j - 1) // Left
    dfs(i, j + 1) // Right
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By isolating the traversal to border-triggered components and using in-place state mutation, each element is evaluated a constant number of times. This resolves the enclave tracking puzzle inside a safe **O(m × n)** linear timeline.

---

## Java Code

```java
class Solution {

    public int numEnclaves(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }

        int m = grid.length;
        int n = grid[0].length;

        // Step 1: Scan left and right vertical borders
        for (int i = 0; i < m; i++) {
            if (grid[i][0] == 1) {
                sinkBoundaryConnectedLand(i, 0, grid, m, n);
            }
            if (grid[i][n - 1] == 1) {
                sinkBoundaryConnectedLand(i, n - 1, grid, m, n);
            }
        }

        // Step 2: Scan top and bottom horizontal borders
        for (int j = 0; j < n; j++) {
            if (grid[0][j] == 1) {
                sinkBoundaryConnectedLand(0, j, grid, m, n);
            }
            if (grid[m - 1][j] == 1) {
                sinkBoundaryConnectedLand(m - 1, j, grid, m, n);
            }
        }

        int enclaveCount = 0;

        // Step 3: Count all remaining enclosed land cells inside the inner grid
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    enclaveCount++;
                }
            }
        }

        return enclaveCount;
    }

    private void sinkBoundaryConnectedLand(int i, int j, int[][] grid, int m, int n) {
        // Base case: Out of bounds or current cell is water/already processed
        if (i < 0 || i >= m || j < 0 || j >= n || grid[i][j] == 0) {
            return;
        }

        // Sink the non-enclave land cell in-place
        grid[i][j] = 0;

        // Propagate outward 4-directionally to claim connected land cells
        sinkBoundaryConnectedLand(i - 1, j,     grid, m, n); // Up
        sinkBoundaryConnectedLand(i + 1, j,     grid, m, n); // Down
        sinkBoundaryConnectedLand(i,     j - 1, grid, m, n); // Left
        sinkBoundaryConnectedLand(i,     j + 1, grid, m, n); // Right
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(m × n) -> Where m is the number of rows and n is the number of columns. Every grid cell is visited a constant number of times across the sinking and counting passes.
Space Complexity: O(m × n) -> In the worst-case scenario where the entire matrix is a contiguous grid of land, the recursive stack can reach a depth of m × n.
```

---

# Dry Run

## Input

```text
grid = [
  [0, 0, 0],
  [1, 0, 1],
  [0, 0, 0]
]
```

---

### Boundary Sinking Phase

```text
1. Left border scan hits (1, 0) == 1. Trigger sinkBoundaryConnectedLand(1, 0):
   - grid[1][0] becomes 0. Neighbors are all 0 or out of bounds. Returns.
2. All other boundary scans find 0.
Grid becomes:
[
  [0, 0, 0],
  [0, 0, 1],
  [0, 0, 0]
]
(Notice the cell at (1, 2) was on the right border, but our scan configuration skips it? 
Wait, the right border scan covers i=1, j=2, which is 1! It gets sunk to 0 during step 1.)
```

---

### Inner Counting Phase

```text
Scanning the final grid matrix finds all 0s. 
enclaveCount = 0.
Returns 0.
```

---

# Visualization

## Border-Anchored Inward Sinking

```text
   Initial Grid        Boundary Sunk        Enclaves Counted
   ┌───┬───┬───┐       ┌───┬───┬───┐         ┌───┬───┬───┐
   │ 0 │ 0 │ 0 │       │ 0 │ 0 │ 0 │         │ 0 │ 0 │ 0 │
   ├───┼───┼───┤  ──►  ├───┼───┼───┤   ──►   ├───┼───┼───┤
   │ 1 │ 1 │ 0 │       │ 0 │ 0 │ 0 │         │ 0 │ 0 │ 0 │
   └───┴───┴───┘       └───┴───┴───┘         └───┴───┴───┘
 (1,0 leaks out)       Component erased       Final Sum = 0
```

---

# Graph Paradigm Pattern

## Identify State

```text
Grid-based boundary isolation using multi-source in-place DFS component filtering.
```

---

## Choices

```text
Initiate flood paths exclusively from border limits. Neutralize connected chains. Tally survivors.
```

---

## Key Takeaways

✅ Advanced 2D grid matrix component filtering layout.

✅ Starting the traversal from the boundary nodes simplifies the exploration path, turning an ambiguous structural containment puzzle into a binary connected-component exclusion sweep.

✅ Reusing the input array space to flag visited components drops secondary overhead completely.

✅ Common Graph Design Pattern:

```text
Boundary-Anchored Sub-Graph Component Neutralization
```

### Similar Problems

- Surrounded Regions (LeetCode 130)
- Number of Islands (LeetCode 200)
- Max Area of Island (LeetCode 695)
- Pacific Atlantic Water Flow (LeetCode 417)
