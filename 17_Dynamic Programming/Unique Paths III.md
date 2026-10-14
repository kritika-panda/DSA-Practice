# Unique Paths III (LeetCode 980)

## Problem Statement

You are given an `m x n` integer grid `grid` where `grid[i][j]` could be:

- `1`: The starting square. There is exactly one starting square.
- `2`: The ending square. There is exactly one ending square.
- `0`: Empty squares we can walk over.
- `-1`: Obstacles that we cannot walk over.

Return *the number of **4-directional walks** from the starting square to the ending square, that walk over every non-obstacle square exactly once*.

---

## Examples

### Example 1

```java
Input: grid = [[1,0,0,0],[0,0,0,0],[0,0,2,-1]]
Output: 2
```

### Explanation

```text
We have the following two paths:
1. (0,0) -> (0,1) -> (0,2) -> (0,3) -> (1,3) -> (1,2) -> (1,1) -> (1,0) -> (2,0) -> (2,1) -> (2,2)
2. (0,0) -> (1,0) -> (2,0) -> (2,1) -> (1,1) -> (0,1) -> (0,2) -> (0,3) -> (1,3) -> (1,2) -> (2,2)

Both paths visit every single empty square exactly once before finishing at the target cell '2'.
```

---

### Example 2

```java
Input: grid = [[1,0,0,0],[0,0,0,0],[0,0,0,2]]
Output: 4
```

---

### Example 3

```java
Input: grid = [[0,1],[2,0]]
Output: 0
```

### Explanation

```text
There is no path that can visit all empty squares exactly once because the starting square 
is right next to the ending square, making full coverage impossible.
```

---

# Intuition

This problem can be structurally rephrased as finding a **Hamiltonian Path on a 2D Grid Matrix**. 

Unlike standard path-finding algorithms or simple dynamic programming grid questions (like *Unique Paths I & II*) where you can only move down and right, this problem allows you to move **4-directionally** (Up, Down, Left, Right). Furthermore, the condition that we must visit **every single traversable cell exactly once** means that regular subproblems overlap unpredictably, making tracking states via simple memoization tables impossible. 

Instead, the optimal approach is an explicit **Backtracking DFS (Depth-First Search)** exploration:
1. **Count Walkable Nodes:** Scan the grid up front to locate the starting coordinates `(startX, startCol)` and count the total number of non-obstacle cells (`0`, `1`, and `2`) that *must* be included in our path. Let this value be `emptyCellsCount`.
2. **Backtracking Wave Pass:** Start our DFS from the `1` cell, tracking the number of cells visited along the current active branch path.
3. **The Visited Lock Mechanism:** As the DFS steps onto a valid cell, we mutate its value in-place to `-1` (turning it into a temporary wall) and increment our path step counter. When backtracking out of the recursive frame, we restore its original value back to `0`. This tracks visited routes with zero extra space overhead.
4. **Terminal Evaluation:** When the DFS reaches the target cell `2`:
   * If our accumulated path step counter perfectly matches `emptyCellsCount`, then a completely valid Hamiltonian path has been constructed. Return `1`.
   * If the step count falls short, it means some open squares were left unvisited. Return `0`.

---

# State Definition

The unique route mutations are tracked using in-place state modifications on the input layout grid:
```text
grid[r][c] = -1 // Turning an active cell into a wall to block retrospective loops
```

---

# Recurrence / Expansion Condition

```text
For each cell (r, c) stepped onto:
    If grid[r][c] == 2 AND steps == emptyCellsCount -> Found valid path! Return 1
    
    Look ahead 4-directionally to (nextR, nextC):
        If neighbor cell == 0 or 2:
            paths += dfs(nextR, nextC, steps + 1)
```

---

# Optimal Structural Backtracking Solution

## Idea

By pre-calculating the exact number of squares requiring coverage and implementing an elegant in-place backtracking lock-and-restore cycle, the path search space is exhaustively explored. It checks valid permutations precisely and handles grid topologies up to size 20 smoothly.

---

## Java Code

```java
class Solution {
    private final int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

    public int uniquePathsIII(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }

        int m = grid.length;
        int n = grid[0].length;

        int startRow = 0;
        int startCol = 0;
        int emptyCellsCount = 0;

        // Step 1: Scan the grid matrix to find the starting location and total cells to visit
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] != -1) {
                    emptyCellsCount++; // Count 0, 1, and 2 as part of the path criteria
                }
                if (grid[i][j] == 1) {
                    startRow = i;
                    startCol = j;
                }
            }
        }

        // Step 2: Launch the backtracking path search pass, starting with 1 active step
        return backtrack(startRow, startCol, 1, emptyCellsCount, grid, m, n);
    }

    private int backtrack(int r, int c, int steps, int emptyCellsCount, int[][] grid, int m, int n) {
        // Base case: If we reach the target cell '2'
        if (grid[r][c] == 2) {
            // A path is valid if and only if we have walked over every target cell exactly once
            return (steps == emptyCellsCount) ? 1 : 0;
        }

        // Save original value before modifying to restore later during backtracking
        int originalValue = grid[r][c];
        
        // Mutate current cell to an obstacle to act as an implicit 'visited' tracker
        grid[r][c] = -1;
        int totalPaths = 0;

        // Step 3: Branch out 4-directionally to all adjacent neighbors
        for (int[] dir : directions) {
            int nextR = r + dir[0];
            int nextC = c + dir[1];

            // Validate boundaries and check if the cell is traversable (empty space '0' or target '2')
            if (nextR >= 0 && nextR < m && nextC >= 0 && nextC < n && (grid[nextR][nextC] == 0 || grid[nextR][nextC] == 2)) {
                totalPaths += backtrack(nextR, nextC, steps + 1, emptyCellsCount, grid, m, n);
            }
        }

        // Backtrack Step: Restore the current cell's original value before returning up the call stack
        grid[r][c] = originalValue;

        return totalPaths;
    }
}
```

---

## Complexity Analysis

Let:
*   M: Number of rows in the matrix grid.
*   N: Number of columns in the matrix grid.

### Time Complexity
Since this is a Hamiltonian Path problem on a grid graph, the algorithm performs a full backtracking search. From each cell, we can branch out in at most 3 directions (excluding the cell we just came from). In the worst-case scenario where there are zero obstacles, the recursion tree can explore up to:
```text
Time Complexity: O(3^(M × N))
```
*(Note: Because the problem limits grid size strictly to a maximum of 20 total cells ($M \times N \le 20$), this exponential boundary remains small and processes instantly inside LeetCode execution runtime thresholds).*

### Space Complexity
The auxiliary memory is determined entirely by the maximum depth of the recursive execution call stack, which can grow up to the total number of cells in the worst-case open matrix path layout:
```text
Space Complexity: O(M × N)
```

---

# Key Pattern
This problem belongs to the **Grid Backtracking / Hamiltonian Path Matrix Search** template.

Common indicators:
- Exploring grid maps 4-directionally where paths are *not* restricted to monotonic moves (down/right).
- Constraint criteria require visiting *every* node or element exactly once before closing the path.
- Counting all possible distinct valid paths instead of just optimizing for a minimum/maximum cost value.

---

# Similar Problems
1. Unique Paths (LeetCode 62)
2. Unique Paths II (LeetCode 63)
3. Word Search (LeetCode 79)
4. Sudoku Solver (LeetCode 37)
5. N-Queens (LeetCode 51)

---

# Takeaway
```text
Count all traversable squares up front to establish a strict coverage baseline target.

Mutate cells in-place to an obstacle value (-1) to serve as a high-speed implicit visited tracker.

Validate your step counter against the total target cell count only when hitting the terminal state marker.
```

**Pattern:** Grid Backtracking / Hamiltonian Path Matrix Search

<FollowUp>
We can now map out another critical path-finding or backtracking matrix template from your pool. If you want, let me know if we should explore **Word Search (LeetCode 79)** to see similar coordinate backtracking rules, or look at the dynamic programming models of **Unique Paths II (LeetCode 63)**!
</FollowUp>
