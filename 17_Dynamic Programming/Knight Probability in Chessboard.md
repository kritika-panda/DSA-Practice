# Knight Probability in Chessboard (LeetCode 688)

## Problem Statement

On an `n x n` chessboard, a knight starts at the cell `(row, column)` and attempts to make exactly `k` moves. The rows and columns are 0-indexed, so the top-left cell is `(0, 0)`, and the bottom-right cell is `(n - 1, n - 1)`.

A chess knight has eight possible moves it can make, as illustrated below. Each move is two cells horizontally and one cell vertically, or one cell horizontally and two cells vertically.

Every time the knight is to make a move, it chooses one of eight possible moves uniformly at random (even if the piece would go off the chessboard) and moves there.

The knight continues moving until it has made exactly `k` moves or has moved off the chessboard.

Return *the probability that the knight remains on the board after making `k` moves*.

---

## Examples

### Example 1

```java
Input: n = 3, k = 2, row = 0, column = 0
Output: 0.06250
```

### Explanation

```text
There are two moves to make.
From (0,0), after 1 move, the knight has exactly 2 valid destinations: (1,2) and (2,1).
The probability of stepping on each is 1/8 = 0.125.

From (1,2), after the 2nd move, the knight has 2 valid destinations: (0,0) and (2,0).
From (2,1), after the 2nd move, the knight has 2 valid destinations: (0,0) and (0,2).

Total successful landing states = 2 + 2 = 4 paths out of 64 total path permutations.
Probability = 4 / 64 = 0.0625
```

---

### Example 2

```java
Input: n = 1, k = 0, row = 0, column = 0
Output: 1.00000
```

---

# Intuition

This problem can be effectively modeled as a **State-Space Layered Dynamic Programming (DP)** problem.

When a knight is at a cell `(r, c)` at move `m`, it has eight possible moves. Each move has an equal probability of \(\frac{1}{8}\) of being selected. The probability of the knight remaining on the board from cell `(r, c)` at move `m` is simply the sum of the survival probabilities of its 8 lookahead destinations divided by 8.

Instead of tracking raw path counts which can grow exponentially up to \(8^{k}\) and trigger integer arithmetic overflow exceptions, we can track the **survival probability directly** as a floating-point matrix state.

---

# State Definition

Let `dp[m][r][c]` be the probability that the knight is on the board at cell `(r, c)` after exactly `m` moves.
*   `m`: The current move number (from `0` to `k`).
*   `r`: The row coordinate on the chessboard (from `0` to `n - 1`).
*   `c`: The column coordinate on the chessboard (from `0` to `n - 1`).

---

# Recurrence Relation

To compute the probability state for move `m` at coordinate `(r, c)`, we aggregate the probabilities from the previous layer `m - 1`:

```text
For each of the 8 knight directional vectors (dr, dc):
    prevR = r - dr
    prevC = c - dc
    If (prevR, prevC) is inside the chessboard boundaries:
        dp[m][r][c] += dp[m - 1][prevR][prevC] / 8.0
```

---

# Base Cases

Before any moves are made (`m == 0`), the knight is guaranteed to be at its starting position `(startRow, startCol)` with a 100% probability:
```text
dp[startRow][startCol] = 1.0
All other dp[r][c] = 0.0
```

---

# Approach: Layer-by-Layer 2D Space-Optimized Tabulation

## Algorithm

1. Initialize a 2D matrix `dp[n][n]` to track the *current* move layer's probabilities.
2. Seed the base case: set `dp[row][column] = 1.0`.
3. Run an outer loop tracking the move index `m` from `1` up to `k`:
   - Create a temporary 2D array `nextDp[n][n]` filled with `0.0`.
   - Loop through every cell `(r, c)` on the chessboard:
     - If `dp[r][c] > 0.0`, it means the knight can reach this cell at the current layer.
     - Look ahead to all 8 possible knight moves `(nextR, nextC)`.
     - If the destination cell is inside the board boundaries, add the transitioned probability share:
       ```text
       nextDp[nextR][nextC] += dp[r][c] / 8.0
       ```
   - Shift the layer window forward: `dp = nextDp`.
4. Sum all probability values inside the finalized `dp` matrix to compute the total survival rate.

---

# Java Solution

```java
class Solution {
    public double knightProbability(int n, int k, int row, int column) {
        // Direction vectors representing the 8 classic L-shaped knight moves
        int[][] directions = {
            {-2, -1}, {-2, 1}, {-1, -2}, {-1, 2},
            {1, -2}, {1, 2}, {2, -1}, {2, 1}
        };

        // dp[r][c] stores the probability of the knight being at cell (r, c)
        double[][] dp = new double[n][n];
        
        // Base Case: At move 0, the knight is at the starting cell with 100% probability
        dp[row][column] = 1.0;

        // Iterate move by move up to k layers
        for (int m = 1; m <= k; m++) {
            double[][] nextDp = new double[n][n];

            for (int r = 0; r < n; r++) {
                for (int c = 0; c < n; c++) {
                    if (dp[r][c] == 0.0) {
                        continue;
                    }

                    // Spread the probability share 8-directionally
                    for (int[] dir : directions) {
                        int nextR = r + dir;
                        int nextC = c + dir;

                        // Verify if the move lands securely within the chessboard boundaries
                        if (nextR >= 0 && nextR < n && nextC >= 0 && nextC < n) {
                            nextDp[nextR][nextC] += dp[r][c] / 8.0;
                        }
                    }
                }
            }
            // Transition the matrix window to the next move layer
            dp = nextDp;
        }

        // Sum up all probabilities remaining on the board after k moves
        double totalProbability = 0.0;
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                totalProbability += dp[r][c];
            }
        }

        return totalProbability;
    }
}
```

---

## Complexity Analysis

Let:
*   N: The side length of the chessboard grid (`n`).
*   K: The total number of target execution moves (`k`).

### Time Complexity
The algorithm loops through exactly K moves. Inside each move layer, it sweeps through all N × N cells and processes 8 constant directional vectors. This keeps the execution runtime securely inside linear-layered bounds:
```text
Time Complexity: O(K × N²)
```

### Space Complexity
By utilizing a rolling layer technique where the next move grid `nextDp` overwrites the active tracking matrix `dp`, we avoid allocating a 3D matrix. The runtime memory footprint scales strictly bounding the chessboard parameters:
```text
Space Complexity: O(N²)
```

---

# Key Pattern
This problem belongs to the **Layered State-Space Probability Propagation** template inside dynamic programming.

Common indicators:
- Finding a floating-point probability metric across sequential turns or clock cycles.
- Grid-based navigation tracking fixed directional move parameters.
- Turn-based dependencies where layer M relies exclusively on the finalized state outcomes of layer M-1.

---

# Similar Problems
1. Out of Boundary Paths (LeetCode 576)
2. Knight Dialer (LeetCode 935)
3. Dice Roll Simulation (LeetCode 1223)
4. Champagne Tower (LeetCode 799)

---

# Takeaway
```text
Model stochastic state cascades using rolling 2D floating-point probability tables.

Filter out unreached matrix states to avoid redundant calculation cycles.

Divide the parent cell's probability share evenly among its valid target endpoints 
at every turn boundary.
```

**Pattern:** Layered State-Space Probability Propagation
