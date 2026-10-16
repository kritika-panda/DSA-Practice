# Word Search ([LeetCode 79](https://leetcode.com))

## Problem Statement

Given an `m x n` grid of characters `board` and a string `word`, return `true` *if `word` exists in the grid*.

The word can be constructed from letters of sequentially adjacent cells, where "adjacent" cells are horizontally or vertically neighboring. The same letter cell **may not be used more than once** along any single path combination track.

---

## Examples

### Example 1

```text
Input: board = [["A","B","C","E"],
                ["S","F","C","S"],
                ["A","D","E","E"]], word = "ABCCED"
Output: true
```

### Explanation

```text
The word "ABCCED" can be traced successfully across the grid paths:
Row 0: 'A' -> 'B' -> 'C' 
Row 1:               'C' (drop down)
Row 2:        'D' <- 'E' (left and down)
```

---

### Example 2

```text
Input: board = [["A","B","C","E"],
                ["S","F","C","S"],
                ["A","D","E","E"]], word = "SEE"
Output: true
```

---

### Example 3

```text
Input: board = [["A","B","C","E"],
                ["S","F","C","S"],
                ["A","D","E","E"]], word = "ABCB"
Output: false
```

### Explanation

```text
The trail path "ABCB" attempts to reuse the cell at Row 0, Column 1 ('B') twice, 
which violates the node single-selection invariant rule.
```

---

# Intuition

This problem represents a classical **Grid-Based Backtracking Exploration Search**. We need to find if any cell coordinate can serve as a starting origin point to match our target string.

We can systematically evaluate path selections using a **Depth-First Search (DFS) with Backtracking**:
1. **Grid Entry:** We sweep across every `(r, c)` cell in the grid. If `board[r][c] == word.charAt(0)`, we launch our recursive DFS matching algorithm from that spot.
2. **Boundary & Value Checks:** At each recursive step tracking string index position `k`, we verify our safety invariants:
   - Are the coordinate indexes out of bounds?
   - Does the character at `board[r][c]` match `word.charAt(k)`?
   - If any check fails, the current path is invalid, so we return `false` (Pruning).
3. **In-Place Visited State Isolation:** To prevent reusing the same grid cell multiple times within the active path track without paying a memory penalty for a separate `boolean[][] visited` grid, we can perform **in-place character masking**. We temporarily overwrite `board[r][c]` with a placeholder non-alphabet character flag (like `'#'`) before exploring further downstream.
4. **Directional Exploration:** We recursively explore all four cardinal directions (up, down, left, right) to match the subsequent character `k + 1`.
5. **The Backtrack Restorations:** Once the directional tracks return their matching evaluations, we undo our character mask by restoring the original letter back into `board[r][c]`. This step clears the cell state, allowing other independent search paths to utilize this node during subsequent turns.

---

# State Definition (Optimal Backtracking Approach)

To trace our combinatorial search steps systematically inside a grid decision matrix tree, we manage states via four parameters:

```text
dfs(r, c, k):
- 'r' and 'c' track the active row and column coordinates inside the board grid.
- 'k' tracks our progress index pointer inside the target word string sequence.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Cases:
- If k == word.length() -> Return true (Word completely matched)
- If r, c out of bounds OR board[r][c] != word.charAt(k) -> Return false

Recursive Controlled Branching:
char temp = board[r][c]
board[r][c] = '#' // Mask cell as visited

boolean found = dfs(r + 1, c, k + 1) ||
                dfs(r - 1, c, k + 1) ||
                dfs(r, c + 1, k + 1) ||
                dfs(r, c - 1, k + 1)

board[r][c] = temp // Backtrack step (Restore original cell value)
Return found
```

---

# Optimal Backtracking Solution

## Idea

We combine an explicit double matrix loop with a 4-directional short-circuit logical `OR` recursive engine. By leveraging hardware bit or character swapping directly inside the grid cells, we execute path searches completely in-place.

---

## Java Code

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        if (board == null || board.length == 0 || word == null || word.length() == 0) {
            return false;
        }

        int rows = board.length;
        int cols = board[0].length;

        // Step 1: Sweep across every grid coordinate to locate valid path origins
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                // Launch depth-first backtracking search if the initial character matches
                if (board[r][c] == word.charAt(0)) {
                    if (dfs(r, c, 0, board, word)) {
                        return true; // Match located, short-circuit out instantly!
                    }
                }
            }
        }

        return false; // Grid completely exhausted without finding a match
    }

    private boolean dfs(int r, int c, int k, char[][] board, String word) {
        // Base Case 1: The index pointer reaches word length, confirming a complete match
        if (k == word.length()) {
            return true;
        }

        // Base Case 2: Boundary validation guards and target mismatch filters
        if (r < 0 || r >= board.length || c < 0 || c >= board[0].length || board[r][c] != word.charAt(k)) {
            return false;
        }

        // Step 2: Cache active cell character data and mask it to prevent path collisions
        char temp = board[r][c];
        board[r][c] = '#'; // Overwrite with a sentinel token to flag it as visited

        // Step 3: Recursively explore all four cardinal direction choices
        // Short-circuiting evaluation stops searching early the moment any direction returns true
        boolean found = dfs(r + 1, c, k + 1, board, word) || // Down
                        dfs(r - 1, c, k + 1, board, word) || // Up
                        dfs(r, c + 1, k + 1, board, word) || // Right
                        dfs(r, c - 1, k + 1, board, word);   // Left

        // Step 4: Backtrack Step - Restore original contextual character value back onto the heap
        board[r][c] = temp;

        return found;
    }
}
```

---

## Complexity Analysis

Let M × N be the total grid dimension size, and L be the absolute character length of the target string `word`.

### Time Complexity

```text
Time Complexity: O(M × N × 3^L)
```
*Explanation:* The outer nested loop pairs scan the grid exactly M × N times. At the starting cell, the backtracking DFS engine branches in up to 4 directions. For all subsequent character steps down the path up to length L, since the direction we just came from is masked as visited (`'#'`), we choose from at most 3 remaining directional tracks. This restricts our recursive tree growth to an asymptotic upper bound of \(O(M \times N \times 3^L)\).

---

### Space Complexity

```text
Space Complexity: O(L) auxiliary space
```
*Explanation:* Highly optimized. Because our masking strategy changes cell data inline directly on the board array, we bypass the \(O(M \times N)\) extra memory cost of a dedicated visited tracking matrix. Auxiliary storage space scales linearly to accommodate the maximum depth height of the recursive call stack, bounded strictly by the word length L.

---

# Key Takeaways

✅ Core fundamental paradigm for processing matrix path searches and state tracking layouts.

✅ **The Value of In-Place Inversions:** Modifying data in-place during the downward recursive phase (`board[r][c] = '#'`) and restoring it during the upward stack unwinding phase (`board[r][c] = temp`) is a powerful backtracking optimization. This keeps memory costs tightly clamped, avoiding the allocation of temporary helper grids.

✅ Directly drives real-world microarchitectures like **matrix text maze word games**, robotic navigation routing systems, geographic map layout analyzers, automated circuit path routing traces, and grid packet routing controllers.

✅ Common Architectural Paradigm:

```text
Cardinal Direction Grid Branching with Stateful Inline Character Masking
```

### Similar Problems

- Word Search II (Multi-word concurrent Trie-backtracking - [LeetCode 212](https://leetcode.com))
- Number of Islands ([LeetCode 200](https://leetcode.com))
- N-Queens ([LeetCode 51](https://leetcode.com))
- Robot Room Cleaner ([LeetCode 489](https://leetcode.com))
