# Surrounded Regions

## Problem Statement

You are given an `m x n` matrix `board` containing letters `'X'` and `'O'`.

A region is **surrounded** if it is completely enclosed by `'X'`s horizontally and vertically. Capturing a surrounded region is done by flipping all `'O'`s into `'X'`s in that surrounded region.

Find all regions of `'O'`s that are completely surrounded by `'X'`s, and **flip them into `'X'`s**. Any `'O'` that is on the border of the board or is connected 4-directionally to a border `'O'` will **not** be flipped.

The overall runtime complexity must be strictly bounded by:

```text
O(m × n)
```

*(where m is the number of rows and n is the number of columns in the grid)*

---

## Example 1

### Input

```text
board = [
  ["X","X","X","X"],
  ["X","O","O","X"],
  ["X","X","O","X"],
  ["X","O","X","X"]
]
```

### Output

```text
[
  ["X","X","X","X"],
  ["X","X","X","X"],
  ["X","X","X","X"],
  ["X","O","X","X"]
]
```

### Explanation

```text
The 'O's at (1,1), (1,2), and (2,2) are completely surrounded by 'X's and are flipped to 'X'.
The 'O' at (3,1) is on the bottom border, so it cannot be surrounded or captured. It remains an 'O'.
```

---

## Example 2

### Input

```text
board = [["X"]]
```

### Output

```text
[["X"]]
```

---

# Intuition

Instead of scanning internal regions to see if they can reach a border, we can invert the strategy: **Start from all border cells containing `'O'` and track inward to protect all reachable, non-surrounded regions.**

Any `'O'` sitting on the 4 outer edges of the matrix cannot be surrounded. Symmetrically, any `'O'` connected 4-directionally to these border cells is also protected from capture.

1. **Border Escape Sweep:** Scan the first row, last row, first column, and last column. Whenever a boundary cell contains an `'O'`, initiate a **Depth-First Search (DFS)**.
2. **Temporary Coloration:** The DFS propagates inward, mutating all connected non-surrounded `'O'` cells into a temporary placeholder symbol like `'T'`. This protects them from being flipped in the next step.
3. **Global Re-Mapping Pass:** Scan the entire grid. 
   - Any remaining `'O'` left intact inside the inner coordinates must be completely surrounded—flip it to `'X'`.
   - Any cell marked as `'T'` belongs to a protected border region—restore it back to `'O'`.

---

# State Definition

The state transformations are managed directly inside the board grid using three distinct markers:
```text
'X' -> Wall / Captured element
'O' -> Trapped element candidate
'T' -> Protected temporary placeholder element
```

---

# Recurrence / Expansion Condition

```text
For a boundary-connected 'O' cell (i, j), lookahead in 4 directions:
    dfs(i - 1, j) // Up
    dfs(i + 1, j) // Down
    dfs(i, j - 1) // Left
    dfs(i, j + 1) // Right
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By isolating the traversal exclusively to border-triggered components and using in-place state mutation, each element is evaluated a constant number of times. This resolves the surrounded region puzzle inside a safe **O(m × n)** linear timeline.

---

## Java Code

```java
class Solution {

    public void solve(char[][] board) {
        if (board == null || board.length == 0 || board[0].length == 0) {
            return;
        }

        int m = board.length;
        int n = board[0].length;

        // Step 1: Scan left and right vertical borders to protect border-connected regions
        for (int i = 0; i < m; i++) {
            if (board[i][0] == 'O') {
                protectBorderRegions(i, 0, board, m, n);
            }
            if (board[i][n - 1] == 'O') {
                protectBorderRegions(i, n - 1, board, m, n);
            }
        }

        // Step 2: Scan top and bottom horizontal borders to protect border-connected regions
        for (int j = 0; j < n; j++) {
            if (board[0][j] == 'O') {
                protectBorderRegions(0, j, board, m, n);
            }
            if (board[m - 1][j] == 'O') {
                protectBorderRegions(m - 1, j, board, m, n);
            }
        }

        // Step 3: Iterate through the entire board to apply final re-mappings
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 'O') {
                    board[i][j] = 'X'; // Surrounded component -> Captured!
                } else if (board[i][j] == 'T') {
                    board[i][j] = 'O'; // Protected component -> Restored!
                }
            }
        }
    }

    private void protectBorderRegions(int i, int j, char[][] board, int m, int n) {
        // Base case: Out of bounds or current cell is not a target 'O' candidate
        if (i < 0 || i >= m || j < 0 || j >= n || board[i][j] != 'O') {
            return;
        }

        // Mark the current non-surrounded cell with a temporary placeholder
        board[i][j] = 'T';

        // Propagate outward 4-directionally to claim connected 'O' cells
        protectBorderRegions(i - 1, j,     board, m, n); // Up
        protectBorderRegions(i + 1, j,     board, m, n); // Down
        protectBorderRegions(i,     j - 1, board, m, n); // Left
        protectBorderRegions(i,     j + 1, board, m, n); // Right
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(m × n) -> Where m is the number of rows and n is the number of columns. Every cell is visited at most a constant number of times during the protection and re-mapping passes.
Space Complexity: O(m × n) -> In the worst-case scenario where the board is fully populated with 'O's, the recursive stack can reach a depth of m × n.
```

---

# Dry Run

## Input

```text
board = [
  ["X","X","X","X"],
  ["X","O","O","X"],
  ["X","X","O","X"],
  ["X","O","X","X"]
]
```

---

### Boundary Sinking Phase

```text
1. Bottom border scan hits (3, 1) == 'O'. Trigger protectBorderRegions(3, 1):
   - board[3][1] becomes 'T'. All adjacent neighbor directions are 'X' or out of bounds. Returns.
2. All other boundary scans find 'X'.
Board state after protection pass:
[
  ["X","X","X","X"],
  ["X","O","O","X"],
  ["X","X","O","X"],
  ["X","T","X","X"]
]
```

---

### Global Re-Mapping Phase

```text
Scanning the full board matrix:
- Cells at (1,1), (1,2), and (2,2) are still 'O' -> Flipped to 'X'.
- Cell at (3,1) is 'T' -> Restored to 'O'.

Final output matches example 1.
```

---

# Visualization

## Border-Anchored Protection Sinking

```text
   Initial Board        Boundary Sunk        Final Re-mapping
   ┌───┬───┬───┐        ┌───┬───┬───┐         ┌───┬───┬───┐
   │ X │ X │ X │        │ X │ X │ X │         │ X │ X │ X │
   ├───┼───┼───┤   ──►  ├───┼───┼───┤   ──►   ├───┼───┼───┤
   │ X │ O │ X │        │ X │ O │ X │         │ X │ X │ X │
   ├───┼───┼───┤        ├───┼───┼───┤         ├───┼───┼───┤
   │ X │ O │ O │        │ X │ T │ T │         │ X │ O │ O │
   └───┴───┴───┘        └───┴───┴───┘         └───┴───┴───┘
 (Bottom cells protect)  Inward collection     'O' -> 'X', 'T' -> 'O'
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
Initiate flood paths exclusively from border limits. Protect connected chains. Overwrite survivors.
```

---

## Key Takeaways

✅ Advanced 2D grid matrix component filtering layout.

✅ Starting the traversal from the boundary nodes simplifies the exploration path, turning an ambiguous structural containment puzzle into a binary connected-component exclusion sweep.

✅ Reusing the input array space to flag visited components drops secondary memory overhead completely.

✅ Common Graph Design Pattern:

```text
Boundary-Anchored Sub-Graph Component Neutralization
```

### Similar Problems

- Number of Enclaves (LeetCode 1020)
- Number of Islands (LeetCode 200)
- Max Area of Island (LeetCode 695)
- Pacific Atlantic Water Flow (LeetCode 417)
