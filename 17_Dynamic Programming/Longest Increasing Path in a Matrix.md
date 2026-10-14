# Longest Increasing Path in a Matrix (LeetCode 329)

## Problem Statement

Given an `m x n` integers `matrix`, return *the length of the longest increasing path in `matrix`*.

From each cell, you can either move to four directions: **left, right, up, or down**. You **may not** move diagonally or move outside the boundary (i.e., wrap-around is not allowed).

---

## Examples

### Example 1

```java
Input: matrix = [,
 ,
  [2,1,1]
]
Output: 4
```

### Explanation

```text
The longest increasing path is.
Path tracking cells: (2,1) -> (1,0) -> (0,0) -> (0,1 is 9, but wait: 1 -> 2 -> 6 -> 9)
Visual path: 
  [9, 9, 4]
   ▲
  [6, 6, 8]
   ▲
  [2, 1, 1]
     ▲
Total length = 4.
```

---

### Example 2

```java
Input: matrix = [,
 ,
  [2,2,1]
]
Output: 4
```

### Explanation

```text
The longest increasing path is [3, 4, 5, 6] or.
Total length = 4.
```

---

### Example 3

```java
Input: matrix = [[1]]
Output: 1
```

---

# Intuition

This problem can be elegantly rephrased as finding the longest path in a **Directed Acyclic Graph (DAG)**. 
- Every cell `(r, c)` in the matrix represents a vertex.
- A directed edge exists from cell `A` to cell `B` if `A` and `B` are adjacent neighbors and `matrix[A] < matrix[B]`.
- Because edges strictly point from smaller values to larger values, it is mathematically impossible to have cycles.

A naive DFS exploration would run in exponential time due to overlapping subproblems. To achieve optimal **linear runtime**, we use **DFS with Memoization**:
1. **Cache Initialization:** Maintain a 2D integer array `memo[m][n]` initialized to `0`. A value of `0` means the longest path starting from that specific cell has not been computed yet.
2. **Strict Incremental Lookahead:** From a cell `(r, c)`, look 4-directionally. We only recurse into a neighbor `(nextR, nextC)` if `matrix[nextR][nextC] > matrix[r][c]`.
3. **Memoization Fallback:** If a neighbor has already been calculated previously, we pull its result from `memo` instantly in $O(1)$ time instead of re-traversing its entire tree branch.

---

# State Definition

Let `memo[r][c]` be the maximum length of an increasing path starting at cell `(r, c)`.
*   `r`: Row index of the cell (from `0` to `m - 1`).
*   `c`: Column index of the cell (from `0` to `n - 1`).

---

# Recurrence Relation

For a cell `(r, c)` and its 4 cardinal adjacent neighbors `(nextR, nextC)`:

```text
memo[r][c] = 1 + max( dfs(nextR, nextC) ) 
for all neighbors where matrix[nextR][nextC] > matrix[r][c]
```

If no adjacent neighbor has a strictly larger value, the cell is a local peak, yielding a base path length of `1`.

---

# Approach: DFS + Memoization (Top-Down DP)

## Algorithm

1. Initialize a 2D matrix `memo[m][n]` with `0`.
2. Set a global variable `maxPath = 0`.
3. Loop through every cell `(i, j)` in the grid from `0` to `m - 1` and `0` to `n - 1`:
   - Calculate its peak potential: `len = computePathDFS(i, j, matrix, memo, m, n)`.
   - Update `maxPath = Math.max(maxPath, len)`.
4. Within `computePathDFS(r, c, ...)`:
   - If `memo[r][c] != 0`, return `memo[r][c]` immediately.
   - Set an initial local max path tracker `currentMax = 1`.
   - Loop through the 4 direction vectors (Up, Down, Left, Right):
     - Calculate neighbor coordinates `(nextR, nextC)`.
     - Check grid boundaries and verify if `matrix[nextR][nextC] > matrix[r][c]`.
     - If valid, recurse: `currentMax = Math.max(currentMax, 1 + computePathDFS(nextR, nextC, ...))`.
   - Store the result back into cache: `memo[r][c] = currentMax`.
   - Return `memo[r][c]`.
5. Return `maxPath`.

---

# Dry Run

```java
matrix = [,
  [2, 1]
]
```

### Initial State

```text
memo = [[0, 0], [0, 0]]
maxPath = 0
```

---

### Step 1 (Evaluating cell 0,0 → Val: 1)
- Neighbor (0,1) with Val 2 > 1 → Valid! Call `dfs(0,1)`.
  - At (0,1) Val 2: Neighbors are 1 (smaller) and 1 (smaller). No valid moves. 
  - `memo[0][1] = 1`. Returns 1.
- Neighbor (1,0) with Val 2 > 1 → Valid! Call `dfs(1,0)`.
  - At (1,0) Val 2: No larger valid neighbors.
  - `memo[1][0] = 1`. Returns 1.
- Backtrack to (0,0): `memo[0][0] = 1 + max(1, 1) = 2`.
- `maxPath = max(0, 2) = 2`.

---

### Step 2 (Evaluating cell 0,1 → Val: 2)
- `memo[0][1]` is already `1` (not 0). Returns `1` instantly from cache.
- `maxPath = max(2, 1) = 2`.

The remaining sweeps verify cache hits cleanly. Final result returns `2`.

---

# Java Solution

```java
class Solution {
    // 4 cardinal direction vectors: Up, Down, Left, Right
    private final int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

    public int longestIncreasingPath(int[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return 0;
        }

        int m = matrix.length;
        int n = matrix[0].length;
        int[][] memo = new int[m][n];
        int maxPath = 0;

        // Sweep every coordinate cell inside the matrix grid
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int pathLength = dfs(i, j, matrix, memo, m, n);
                maxPath = Math.max(maxPath, pathLength);
            }
        }

        return maxPath;
    }

    private int dfs(int r, int c, int[][] matrix, int[][] memo, int m, int n) {
        // Memoization check: If already computed, return cached value instantly
        if (memo[r][c] != 0) {
            return memo[r][c];
        }

        int currentMax = 1; // Base case: a cell can always form a path of length 1

        // Explore all 4 adjacent directions
        for (int[] dir : directions) {
            int nextR = r + dir[0];
            int nextC = c + dir[1];

            // Verify boundaries and ensure the adjacent step is strictly increasing
            if (nextR >= 0 && nextR < m && nextC >= 0 && nextC < n && matrix[nextR][nextC] > matrix[r][c]) {
                int subPathLength = 1 + dfs(nextR, nextC, matrix, memo, m, n);
                currentMax = Math.max(currentMax, subPathLength);
            }
        }

        // Cache the computed maximum value for the current state cell
        memo[r][c] = currentMax;
        return currentMax;
    }
}
```

---

## Complexity Analysis

Let:
*   M: Number of rows in the matrix grid.
*   N: Number of columns in the matrix grid.

### Time Complexity
Thanks to memoization caching, each state cell `(r, c)` computes its longest increasing branch exactly once. Subsequent lookups resolve in $O(1)$ constant time. For each cell, we check 4 constant direction neighbors, bounding the total operational footprint linearly:
```text
Time Complexity: O(M × N)
```

### Space Complexity
The auxiliary storage is dominated by the 2D `memo` tracking grid matrix of size $M \times N$, along with the recursive execution call stack frames which can reach a depth of $M \times N$ in worst-case sequential graph layouts:
```text
Space Complexity: O(M × N)
```

---

# Key Pattern
This problem belongs to the **Grid Graph DAG / Top-Down Memoization** dynamic programming template.

Common indicators:
- Finding maximum path tracking sequences across a 2D matrix structure.
- Local values must satisfy monotonic, strictly increasing, or strictly decreasing constraints.
- Overlapping recursive sub-branches that can be easily cached via coordinate keys.

---

# Similar Problems
1. Longest Increasing Subsequence (1D variant - LeetCode 300)
2. Number of Increasing Paths in a Grid (LeetCode 2328)
3. Out of Boundary Paths (LeetCode 576)
4. Unique Paths (LeetCode 62)

---

# Takeaway
```text
Reframe matrix cells as vertices in a Directed Acyclic Graph.

Isolate path directions strictly from smaller numbers to larger numbers to prevent cyclic loops.

Cache computed tree branches inside a memo grid to reduce execution time to O(N).
```

**Pattern:** Grid Graph DAG / Top-Down Memoization DP

<FollowUp>
We can now map out another critical matrix-based dynamic programming or graph problem. If you'd like, let me know if we should generate the raw markdown code for **Number of Increasing Paths in a Grid (LeetCode 2328)** or explore the 1D classic **Longest Increasing Subsequence (LeetCode 300)**!
</FollowUp>
