# Sudoku Solver ([LeetCode 37](https://leetcode.com))

## Problem Statement

Write a program to solve a Sudoku puzzle by filling the empty cells.

A sudoku solution must satisfy **all of the following rules**:
1. Each of the digits `1-9` must occur exactly once in each row.
2. Each of the digits `1-9` must occur exactly once in each column.
3. Each of the digits `1-9` must occur exactly once in each of the 9 `3x3` sub-boxes of the grid.

The empty cells are indicated by the character `'.'`.

**Constraints:**
- The input board is always a `9x9` matrix.
- The given board contains only digits `1-9` and `'.'`.
- It is guaranteed that the given puzzle has **exactly one unique solution**.
- The modifications must be executed **in-place** directly on the input `char[][]` board array.

---

## Examples

### Example 1

```text
Input: board = 
[["5","3",".",".","7",".",".",".","."],
 ["6",".",".","1","9","5",".",".","."],
 [".","9","8",".",".",".",".","6","."],
 ["8",".",".",".","6",".",".",".","3"],
 ["4",".",".","8",".","3",".",".","1"],
 ["7",".",".",".","2",".",".",".","6"],
 [".","6",".",".",".",".","2","8","."],
 [".",".",".","4","1","9",".",".","5"],
 [".",".",".",".","8",".",".","7","9"]]

Output: 
[["5","3","4","6","7","8","9","1","2"],
 ["6","7","2","1","9","5","3","4","8"],
 ["1","9","8","3","4","2","5","6","7"],
 ["8","5","9","7","6","1","4","2","3"],
 ["4","2","6","8","5","3","7","9","1"],
 ["7","1","3","9","2","4","8","5","6"],
 ["9","6","1","5","3","7","2","8","4"],
 ["2","8","7","4","1","9","6","3","5"],
 ["3","4","5","2","8","6","1","7","9"]]
```

---

# Intuition

Sudoku is a classic **Constraint Satisfaction Problem (CSP)** that maps onto a dense state-space decision tree. We must search for a valid assignment of characters for every single open cell.

We can systematically find the solution using **Depth-First Search (DFS) with Backtracking**:
1. **Locate an Empty Cell:** We scan the `9x9` board sequentially from top-left to bottom-right. The moment we land on an unassigned cell marked by `'.'`, we pause our scan.
2. **Constraint-Guided Multi-Way Branching:** For this open cell `(r, c)`, we try placing every possible character `d` from `'1'` to `'9'`. Before writing it down, we run an inline lookahead validation check to make sure `d` does not cause any immediate conflicts:
   - Check row `r` to ensure `d` is unique.
   - Check column `c` to ensure `d` is unique.
   - Check the specific localized `3x3` sub-grid row/column block containing cell `(r, c)` to ensure `d` is unique.
3. **Recursive Exploration:** If the character `d` satisfies all three constraints, we write it into the cell (`board[r][c] = d`) and recursively call our solver to process the remaining empty cells on the board.
4. **The Backtrack Reset:** If exploring downstream hits a dead end (returns `false`), it means our choice of `d` was structurally incorrect for the long-term layout. We undo this choice by resetting the cell back to empty (`board[r][c] = '.'`) and continue our loop to evaluate the next character choice.
5. **Base Case Victory:** If our board scan completes entirely without running into any more empty cells, the puzzle is solved, and we return `true` to cascade success all the way back up the call stack.

---

# State Definition (Optimal Backtracking Approach)

To trace our grid transformations completely in-place using the execution stack frames:

```text
solve(board):
- Scans the mutable board for unassigned '.' blocks.
- Uses inline loops to evaluate choices dynamically from '1' to '9'.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Function solve(board):
    For r from 0 to 8:
        For c from 0 to 8:
            If board[r][c] == '.':
                For digit from '1' to '9':
                    If isSafe(r, c, digit, board):
                        board[r][c] = digit
                        If solve(board) -> Return true
                        board[r][c] = '.' // Backtrack step (Undo choice)
                Return false // All 9 choices exhausted, pull back
    Return true // No empty cells left, puzzle completed!
```

---

# Optimal Backtracking Solution

## Idea

The algorithm implements a full grid scan. When an empty slot is encountered, it cycles through characters `'1'` to `'9'`. By utilizing a clever modulo and integer division trick (`3 * (r / 3) + i / 3` and `3 * (c / 3) + i % 3`), the row, column, and local `3x3` sub-box constraints are verified simultaneously inside a single high-speed linear check pass.

---

## Java Code

```java
class Solution {
    public void solveSudoku(char[][] board) {
        if (board == null || board.length == 0) {
            return;
        }
        // Launch our in-place recursive backtracking solver
        solve(board);
    }

    private boolean solve(char[][] board) {
        // Step 1: Scan the 9x9 matrix layout to find an unassigned cell
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                if (board[r][c] == '.') {
                    
                    // Step 2: Multi-Way Branching - Attempt to place digits '1' through '9'
                    for (char d = '1'; d <= '9'; d++) {
                        // Validate row, column, and sub-grid constraints lookahead bounds
                        if (isValid(board, r, c, d)) {
                            board[r][c] = d; // Choice Step: Tentatively assign the digit

                            // Recurse deeper to attempt solving the rest of the board layout
                            if (solve(board)) {
                                return true; // Solution located, short-circuit out instantly!
                            }

                            // Backtrack Step: Undo the assignment to restore contextual alignment
                            board[r][c] = '.';
                        }
                    }
                    
                    // Trigger Pruning: If all 9 digits fail, this sub-tree is dead. Return false!
                    return false;
                }
            }
        }
        return true; // Base Case: Checked the entire board and found zero empty slots. Victory!
    }

    // High-speed synchronized lookahead constraint validator checking 3 zones concurrently
    private boolean isValid(char[][] board, int row, int col, char d) {
        for (int i = 0; i < 9; i++) {
            // Constraint 1: Check if digit already exists anywhere in the active row
            if (board[row][i] == d) {
                return false;
            }
            // Constraint 2: Check if digit already exists anywhere in the active column
            if (board[i][col] == d) {
                return false;
            }
            // Constraint 3: Check if digit already exists inside the active local 3x3 box partition
            // rowOffset identifies sub-box top row, colOffset identifies sub-box leftmost column
            int rowBoxIndex = 3 * (row / 3) + i / 3;
            int colBoxIndex = 3 * (col / 3) + i % 3;
            if (board[rowBoxIndex][colBoxIndex] == d) {
                return false;
            }
        }
        return true; // Digit passes all constraint validations safely
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: Constant Upper Bound O(1) -> Max upper limit bounded by 9^81 operations
```
*Explanation:* Since the board dimension size is strictly fixed at a static `9x9` footprint, the absolute maximum number of empty cells is 81. In the absolute worst-case scenario, every slot could check up to 9 paths, yielding a constant upper bound operations timeline of \(O(9^{81})\). However, because the rigorous constraint check clips out invalid paths instantly at each depth level, the actual number of states explored is highly optimized, resolving puzzles in just a few milliseconds.

---

### Space Complexity

```text
Space Complexity: Constant Upper Bound O(1) -> Max depth height bounded by 81 stack frames
```
*Explanation:* Highly optimal. The grid states are managed completely in-place directly inside the input board memory allocations. Auxiliary memory overhead is driven exclusively by the nested recursive execution call stack, which is capped at a strict maximum height limit of 81 nested stack records.

---

# Key Takeaways

✅ Core fundamental paradigm for processing complex, multi-zone constraint satisfaction problems (CSPs).

✅ **The 3x3 Box Matrix Coordinate Formula:** The index expressions `3 * (row / 3) + i / 3` and `3 * (col / 3) + i % 3` are highly strategic. They map a linear tracking variable `i` from `0` to `8` directly onto a 2D `3x3` sub-grid cell layout based on the row/column coordinates. This allows you to check rows, columns, and sub-boxes simultaneously inside a single `for` loop pass.

✅ Directly drives real-world microarchitectures like **automated scheduling managers** (e.g., flight timeline planners satisfying overlapping terminal constraints), hardware register assignment compilers, automated network layout routers, and puzzle engines.

✅ Common Architectural Paradigm:

```text
Multi-Zone Matrix Constraint Branching with Symmetric Trailing Inversions
```

### Similar Problems

- N-Queens ([LeetCode 51](https://leetcode.com))
- m-Coloring Problem ([GeeksforGeeks](https://geeksforgeeks.com))
- Word Search ([LeetCode 79](https://leetcode.com))
- Valid Sudoku ([LeetCode 36](https://leetcode.com))

<FollowUp>
