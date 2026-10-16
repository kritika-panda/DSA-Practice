# N-Queens ([LeetCode 51](https://leetcode.com))

## Problem Statement

The **n-queens** puzzle is the problem of placing `n` queens on an `n x n` chessboard such that no two queens attack each other.

Given an integer `n`, return *all distinct solutions to the **n-queens puzzle***. You may return the answer in **any order**.

Each solution contains a distinct board configuration of the n-queens' placement, where `'Q'` and `'.'` both indicate a queen and an empty space, respectively.

---

## Examples

### Example 1

```text
Input: n = 4
Output: [[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
```

### Explanation

```text
There exist two distinct solutions for a 4x4 board:
Solution 1:
. Q . .
. . . Q
Q . . .
. . Q .

Solution 2:
. . Q .
Q . . .
. . . Q
. Q . .
```

---

### Example 2

```text
Input: n = 1
Output: [["Q"]]
```

---

# Intuition

This problem represents a classic **Constraint Satisfaction Problem (CSP)** over a 2D matrix layout. By exploiting the rule that each row can contain exactly one queen, we can reduce the 2D grid search space into a sequential **Row-by-Row Selection** process.

We step through rows from `0` to `n - 1`. For each row `r`, we iterate across all possible columns `c` from `0` to `n - 1`. Before placing a queen at `(r, c)`, we must check three safety vectors:
1.  **Vertical Check:** Is there a queen already positioned anywhere in column `c`?
2.  **Main Diagonal Check (\):** Is there a queen on the backward diagonal? Notice that for any cell on a given `\` diagonal, the value **`r - c`** is a constant.
3.  **Anti-Diagonal Check (/):** Is there a queen on the forward diagonal? Notice that for any cell on a given `/` diagonal, the value **`r + c`** is a constant.

### The Bitmask/Lookup Optimization
Instead of running a linear scan up the board to check diagonals at every single step, we can use boolean lookup arrays or **bitmasks** to log occupied paths. This drops our validity checks down to an ultra-fast **O(1) constant time**.

---

# State Definition (Optimal Backtracking Approach)

To trace our grid transformations systematically using an optimized constraint backtracking framework:

```text
backtrack(r, colMask, diagMask, antiDiagMask):
- 'r' tracks our active row index pointer from 0 to n-1.
- 'colMask', 'diagMask', and 'antiDiagMask' act as boolean tracking arrays or bitwise integers logging occupied paths.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Function backtrack(r, board):
    If r == n -> result.add(format(board)); return

    For c from 0 to n - 1:
        If col[c] OR diag[r - c] OR antiDiag[r + c] -> Continue (Prune Attacked Cells)

        // Place Queen
        Set constraints true
        board[r][c] = 'Q'
        
        backtrack(r + 1, board) // Recurse to next row
        
        // Backtrack Step (Undo Choice)
        board[r][c] = '.'
        Set constraints false
```

---

# Optimal Backtracking Solution with O(1) Lookups

## Idea

We allocate three boolean tracking arrays to log occupied lines. The main diagonal lookup array uses a padding offset index (`r - c + n`) to avoid negative index keys in memory blocks. We launch our recursive depth pass from row 0, managing state allocations inline directly on a temporary board matrix workspace.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> result = new ArrayList<>();
        if (n <= 0) {
            return result;
        }

        // Initialize our structural 2D board workspace with empty '.' characters
        char[][] board = new char[n][n];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                board[i][j] = '.';
            }
        }

        // O(1) Lookup directories tracking attack vectors
        boolean[] cols = new boolean[n];
        boolean[] diags = new boolean[2 * n];     // For tracking r - c constant lines
        boolean[] antiDiags = new boolean[2 * n]; // For tracking r + c constant lines

        // Launch recursive backtracking solver from row 0
        backtrack(0, n, board, cols, diags, antiDiags, result);
        return result;
    }

    private void backtrack(int r, int n, char[][] board, boolean[] cols, boolean[] diags, boolean[] antiDiags, List<List<String>> result) {
        // Base Case: If the row cursor reaches n, all queens are placed safely
        if (r == n) {
            result.add(constructBoardLayout(board));
            return;
        }

        // Iterate through each column candidate choice for the current row 'r'
        for (int c = 0; c < n; c++) {
            // Calculate structural constant index flags for diagonals
            int diagIdx = r - c + n; // Add an offset padding of 'n' to completely prevent negative index keys
            int antiDiagIdx = r + c;

            // Pruning Invariant: If the cell is under attack from any direction, skip it early!
            if (cols[c] || diags[diagIdx] || antiDiags[antiDiagIdx]) {
                continue;
            }

            // Choice Step: Place the queen and flag attack paths as occupied
            board[r][c] = 'Q';
            cols[c] = true;
            diags[diagIdx] = true;
            antiDiags[antiDiagIdx] = true;

            // Recurse deeper to place the next queen on the subsequent row layout below
            backtrack(r + 1, n, board, cols, diags, antiDiags, result);

            // Backtrack Step: Undo choices to restore contextual alignment for sibling branches
            board[r][c] = '.';
            cols[c] = false;
            diags[diagIdx] = false;
            antiDiags[antiDiagIdx] = false;
        }
    }

    // Helper utility to translate our mutable char[][] workspace into the final required string list
    private List<String> constructBoardLayout(char[][] board) {
        List<String> layout = new ArrayList<>();
        for (char[] row : board) {
            layout.add(new String(row));
        }
        return layout;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N!)
```
*Explanation:* In the first row, we have N possible placement choices. In the second row, we have at most N-2 column choices available due to attack vectors. Moving downwards, choices narrow sequentially down to a maximum search tree state space bounded securely by the factorial ceiling O(N!).

---

### Space Complexity

```text
Space Complexity: O(N²)
```
*Explanation:* The final output collection matrices take up size variables. Excluding that required output storage, our auxiliary workspace uses O(N²) space to allocate the `char[][]` board array template. The boolean tracking registries and the recursive call stack depth scale strictly linearly at O(N), keeping the footprint bounded.

---

# Key Takeaways

✅ Core fundamental paradigm for processing multi-constraint geometric optimization paths.

✅ **The Diagonal Parity Formulas:** The structural mathematical invariants **`r - c`** and **`r + c`** are incredibly powerful. Recognizing that cells sharing the same diagonal return identical arithmetic combinations allows us to replace slow O(N) coordinate scanning loops with high-speed O(1) constant-time boolean array flag lookups.

✅ Directly drives real-world microarchitectures like **automated spatial layout organizers**, VLSI chip circuit trace routers (preventing intersecting wire paths across multiple layers), scheduling routers with complex multi-way blockers, and game theory engine analyzers.

✅ Common Architectural Paradigm:

```text
Row-Indexed Metric Pruning via Symmetric Diagonal Invariant Maskings
```

### Similar Problems

- Sudoku Solver ([LeetCode 37](https://leetcode.com))
- m-Coloring Problem ([GeeksforGeeks](https://geeksforgeeks.com))
- N-Queens II (Only counting total solution metrics - [LeetCode 52](https://leetcode.com))
- Grid Illumination ([LeetCode 1001](https://leetcode.com))
