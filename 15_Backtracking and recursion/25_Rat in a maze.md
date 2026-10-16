# Rat in a Maze ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Consider a rat placed at position `(0, 0)` in a square matrix `mat` of order `n x n`. It has to reach the destination at position `(n - 1, n - 1)`. Find all possible distinct paths that the rat can take to travel from source to destination. 

The directions in which the rat can move are **'U' (up)**, **'D' (down)**, **'L' (left)**, and **'R' (right)**. Value 0 in a cell of the matrix represents a blocked cell that the rat cannot enter, while value 1 represents an open cell that the rat can walk through.

**Constraints:**
- The rat cannot step on a cell multiple times along any single path track.
- If the source cell `mat[0][0]` or the destination cell `mat[n-1][n-1]` is 0, return an empty list because no path can structurally exist.
- Return the list of unique paths sorted in **lexicographical (dictionary) order** (i.e., paths starting with 'D' before 'L', 'R', 'U').

---

## Examples

### Example 1

```text
Input: mat = [[1, 0, 0, 0],
,
 ,
 ]
Output: ["DDRDRR", "DRDDRR"]
```

### Explanation

```text
The rat can reach the final destination using exactly 2 unique path configurations:
Path 1: (0,0) -> (1,0) -> (2,0) -> (2,1) -> (3,1) -> (3,2) -> (3,3) => "DDRDRR"
Path 2: (0,0) -> (1,0) -> (1,1) -> (2,1) -> (3,1) -> (3,2) -> (3,3) => "DRDDRR"
```

---

### Example 2

```text
Input: mat = [[1, 0],
]
Output: []
```

### Explanation

```text
The destination coordinate (1,1) is completely blocked (0), making it impossible to finish the trail.
```

---

# Intuition

This problem represents a classical **Grid-Based Backtracking Exploration Search**. We need to find every single valid path from the top-left corner to the bottom-right corner.

We can systematically find all paths using a **Depth-First Search (DFS) with Backtracking**:
1.  **Lexicographical Ordering Optimization:** To guarantee that our paths are collected in sorted dictionary order natively without running an expensive post-sort operation, we can force our recursive exploration loop to visit directions in strict alphabetical order: **'D' (Down), 'L' (Left), 'R' (Right), 'U' (Up)**.
2.  **Boundary & Value Blocks:** At each step `(r, c)`, we verify our safety invariants:
    - Are the grid indexes out of bounds?
    - Is the cell blocked (`mat[r][c] == 0`)?
    - Has this cell already been stepped on in our active path?
3.  **In-Place Visited State Isolation:** To prevent the rat from walking in circles, we must flag cells as visited. Instead of allocating a separate `boolean[][]` grid, we can perform **in-place tracking** by temporarily setting `mat[r][c] = 0` before moving forward.
4.  **The Backtrack Restoration:** Once the 4-directional explorations from the current cell complete, we pop the trailing character off our string buffer and restore `mat[r][c] = 1`. This resets the cell, allowing other independent path tracks to utilize this coordinate frame during subsequent choices.

---

# State Definition (Optimal Backtracking Approach)

To trace our grid transformations completely in-place using the execution stack frames:

```text
dfs(r, c, currentPath):
- 'r' and 'c' track the active row and column coordinates inside the matrix.
- 'currentPath' acts as a shared mutable string tracker accumulating directional tokens.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Case:
If r == n - 1 AND c == n - 1 -> result.add(currentPath.toString()); return

Recursive Controlled Branching:
mat[r][c] = 0 // Mask cell as visited

// Try 'D' -> dfs(r + 1, c, currentPath + "D")
// Try 'L' -> dfs(r, c - 1, currentPath + "L")
// Try 'R' -> dfs(r, c + 1, currentPath + "R")
// Try 'U' -> dfs(r - 1, c, currentPath + "U")

mat[r][c] = 1 // Backtrack step (Undo choice)
```

---

# Optimal Backtracking Solution

## Idea

The algorithm launches a depth-first search from `(0, 0)`. By prioritizing the directional steps in **'D', 'L', 'R', 'U'** order and resetting the cell values inline during stack unwinding, we map out all unique valid paths inside a constant auxiliary space envelope.

---

## Java Code

```java
import java.util.ArrayList;

class Solution {
    public ArrayList<String> findPath(int[][] mat) {
        ArrayList<String> result = new ArrayList<>();
        
        // Guard Condition: If source or destination is blocked, no path can exist
        if (mat == null || mat.length == 0 || mat[0][0] == 0 || mat[mat.length - 1][mat.length - 1] == 0) {
            return result;
        }

        int n = mat.length;
        StringBuilder currentPath = new StringBuilder();

        // Launch our recursive backtracking pass from the source cell (0, 0)
        solveMaze(0, 0, n, mat, currentPath, result);
        return result;
    }

    private void solveMaze(int r, int c, int n, int[][] mat, StringBuilder currentPath, ArrayList<String> result) {
        // Base Case: If the rat successfully reaches the destination cell, a path is finalized
        if (r == n - 1 && c == n - 1) {
            result.add(currentPath.toString());
            return;
        }

        // Cache and mask the current cell to prevent circular path loops
        int originalValue = mat[r][c];
        mat[r][c] = 0; // Mark as visited by temporarily blocking it

        // Coordinate shift mappings matching strict lexicographical order: D, L, R, U
        int[] dRow = {1, 0, 0, -1};
        int[] dCol = {0, -1, 1, 0};
        char[] directions = {'D', 'L', 'R', 'U'};

        // Controlled Branching: Explore all 4 cardinal direction choices sequentially
        for (int i = 0; i < 4; i++) {
            int nextRow = r + dRow[i];
            int nextCol = c + dCol[i];

            // Validation Guard: Check grid boundaries and ensure the target cell is unvisited/open
            if (nextRow >= 0 && nextRow < n && nextCol >= 0 && nextCol < n && mat[nextRow][nextCol] == 1) {
                currentPath.append(directions[i]); // Choice Step: Append directional step
                
                // Recurse deeper into the maze
                solveMaze(nextRow, nextCol, n, mat, currentPath, result);
                
                // Backtrack Step: Remove trailing step token to restore state context for the next branch
                currentPath.deleteCharAt(currentPath.length() - 1);
            }
        }

        // Restore original contextual value back onto the grid layout
        mat[r][c] = originalValue;
    }
}
```

---

## Complexity Analysis

Let N × N be the total board dimension layout size.

### Time Complexity

```text
Time Complexity: O(3^(N²)) worst case
```
*Explanation:* From any cell inside the grid layout, the rat can choose up to 4 directions. However, since the cell it just came from is masked as blocked, the number of choices drops to at most 3 options per step. In the absolute worst case where the entire grid consists of `1` entries with deeply winding paths, the upper bound state size scales exponentially up to \(O(3^{N^2})\).

---

### Space Complexity

```text
Space Complexity: O(N²) auxiliary space
```
*Explanation:* Highly optimized. By changing cell values inline directly inside the source matrix array, we completely bypass the extra O(N²) memory overhead of an external boolean tracking matrix. Auxiliary storage space scales linearly to accommodate the maximum depth height of the recursive call stack, bounded strictly by N².

---

# Key Takeaways

✅ Core fundamental paradigm for processing matrix path searches and graph maze traversals.

✅ **The Alphabetical Traversal Rule:** Arranging the array mappings matching strict alphabetical order (**'D', 'L', 'R', 'U'**) is a vital design technique. It lets the algorithm construct paths that are automatically sorted, removing the need to run an expensive \(O(K \log K)\) string sorting pass over the results.

✅ Directly drives real-world microarchitectures like **autonomous robotic route explorers**, text maze game engines, automated industrial circuit board trace routing tools, and packet stream routers.

✅ Common Architectural Paradigm:

```text
Cardinal Direction Grid Branching with Synchronized Inline State Inversions
```

### Similar Problems

- Word Search ([LeetCode 79](https://leetcode.com))
- Number of Islands ([LeetCode 200](https://leetcode.com))
- Unique Paths II ([LeetCode 63](https://leetcode.com))
- Robot Room Cleaner ([LeetCode 489](https://leetcode.com))
