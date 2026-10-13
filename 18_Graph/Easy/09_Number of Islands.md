# Number of Islands

## Problem Statement

Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return *the total number of **islands***.

An **island** is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

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
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
```

### Output

```text
1
```

---

## Example 2

### Input

```text
grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
```

### Output

```text
3
```

---

# Intuition

The grid can be modeled as an undirected graph where each land cell `'1'` is a node, and its connections to adjacent land cells (up, down, left, right) represent edges. Finding the total number of islands is equivalent to counting the **Connected Components** of this graph.

We can achieve this by scanning the grid cell by cell. When we find an unvisited land cell `'1'`:
1. It marks the discovery of a **brand-new island**—we increment our global island counter.
2. To avoid counting any piece of this island again, we must immediately find and sink all connected land cells belonging to it.
3. We trigger a **Depth-First Search (DFS)** traversal starting from that cell. The DFS recursively radiates outwards in all 4 cardinal directions.
4. **Sinking Mechanism:** As the DFS visits each land cell, we mutate its value in-place from `'1'` to `'0'` (turning land into water). This serves as an embedded "visited" marker with zero extra memory overhead.

Once the DFS finishes flooding that connected piece of land, the search loop resumes looking for the next remaining `'1'`.

---

# State Definition

The state transitions are managed inside the grid itself by transforming elements in-place:
```text
grid[i][j] = '0' // Mutating visited land cells into water to prevent cycles
```

---

# Recurrence / Expansion Condition

```text
For a land cell (i, j), lookahead in 4 directions:
    dfs(i - 1, j) // Up
    dfs(i + 1, j) // Down
    dfs(i, j - 1) // Left
    dfs(i, j + 1) // Right
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By scanning every cell in the grid and using in-place mutation to sink discovered islands, each node coordinate is evaluated a constant number of times. This processes the entire matrix inside a highly stable **O(m × n)** linear timeline.

---

## Java Code

```java
class Solution {

    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }

        int m = grid.length;
        int n = grid[0].length;
        int islandCount = 0;

        // Step 1: Scan every cell in the 2D grid matrix
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                
                // If a land cell '1' is hit, a new independent component is found
                if (grid[i][j] == '1') {
                    islandCount++;
                    // Step 2: Flood/sink the entire island to mark it as visited
                    sinkIsland(i, j, grid, m, n);
                }
            }
        }

        return islandCount;
    }

    private void sinkIsland(int i, int j, char[][] grid, int m, int n) {
        // Base case: out of bounds or current cell is water '0'
        if (i < 0 || i >= m || j < 0 || j >= n || grid[i][j] == '0') {
            return;
        }

        // Sink the current land cell in-place to serve as our visited marker
        grid[i][j] = '0';

        // Step 3: Radiate outwards recursively in all 4 cardinal directions
        sinkIsland(i - 1, j,     grid, m, n); // Up
        sinkIsland(i + 1, j,     grid, m, n); // Down
        sinkIsland(i,     j - 1, grid, m, n); // Left
        sinkIsland(i,     j + 1, grid, m, n); // Right
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(m × n) -> Where m is the number of rows and n is the number of columns. Every cell is inspected at most a constant number of times.
Space Complexity: O(m × n) -> In the worst-case scenario where the entire grid is a single giant island, the recursive call stack depth can grow up to m × n.
```

---

# Dry Run

## Input

```text
grid = [
  ["1", "1", "0"],
  ["0", "1", "0"],
  ["0", "0", "1"]
]
```

---

### Initial State

```text
islandCount = 0
```

---

### Grid Sweep Execution

```text
1. i = 0, j = 0 -> grid == '1'. islandCount = 1. Trigger sinkIsland(0, 0):
   - grid becomes '0'.
   - Recurse right -> sinkIsland(0, 1):
     - grid becomes '0'.
     - Recurse down -> sinkIsland(1, 1):
       - grid becomes '0'. All surrounding directions are now '0' or out of bounds. Returns up.
2. The entire connected top-left block has been inverted to '0'.
3. Iterations skip filled cells until i = 2, j = 2 -> grid == '1'. islandCount = 2.
   - sinkIsland(2, 2) turns it to '0'.

Returns 2.
```

---

# Visualization

## Connected Component Flood Sinking

```text
  Initial Grid      Discovered Island 1   Discovered Island 2
   ┌───┬───┬───┐       ┌───┬───┬───┐         ┌───┬───┬───┐
   │ 1 │ 1 │ 0 │       │ 0 │ 0 │ 0 │         │ 0 │ 0 │ 0 │
   ├───┼───┼───┤  ──►  ├───┼───┼───┤   ──►   ├───┼───┼───┤
   │ 0 │ 1 │ 0 │       │ 0 │ 0 │ 0 │         │ 0 │ 0 │ 0 │
   ├───┼───┼───┤       ├───┼───┼───┤         ├───┼───┼───┤
   │ 0 │ 0 │ 1 │       │ 0 │ 0 │ 1 │         │ 0 │ 0 │ 0 │
   └───┴───┴───┘       └───┴───┴───┘         └───┴───┴───┘
                       Island Count = 1      Island Count = 2
```

---

# DP Pattern / Graph Paradigm

## Identify State

```text
Grid-based Connected Components counter using localized in-place DFS graph mutation.
```

---

## Choices

```text
Traverse 4-directionally to flood-fill contiguous lands. Sink nodes to block cyclic loops.
```

---

## Key Takeaways

✅ Standard 2D grid matrix connected components graph traversal problem.

✅ Overwriting the grid arrays directly (`'1' -> '0'`) provides an elegant space optimization step that completely skips allocating an explicit boolean visited tracker array.

✅ Universal model representing **Flood Fill tools** inside graphics software, cluster grouping, or region segmenting algorithms.

✅ Common Graph Design Pattern:

```text
In-Place Matrix Mutation for Sub-Graph Component Eviction
```

### Similar Problems

- Max Area of Island (LeetCode 695)
- Flood Fill (LeetCode 733)
- Surrounded Regions (LeetCode 130)
- Island Perimeter (LeetCode 463)
