# Cherry Pickup II (LeetCode 1463)

## Problem Statement

You are given a `rows x cols` matrix `grid` representing a field of cherries where `grid[i][j]` represents the number of cherries that you can collect from the cell `(i, j)`.

You have two robots that can collect cherries for you:
- **Robot #1** is located at the **top-left corner** `(0, 0)`.
- **Robot #2** is located at the **top-right corner** `(0, cols - 1)`.

Return *the maximum number of cherries collection you can obtain by following the rules below*:
- From a cell `(i, j)`, robots can move to cell `(i + 1, j - 1)`, `(i + 1, j)`, or `(i + 1, j + 1)`.
- When any robot passes through a cell, it picks up all cherries, and the cell becomes an empty cell `0`.
- If both robots stay on the same cell at the same time, only one robot takes the cherries and the other takes nothing.
- Both robots must reach the bottom row in the `grid`.

---

## Examples

### Example 1

```java
Input: grid = [,
 ,
 ,
 ,
 
]
Output: 24
```

### Explanation

```text
Path of Robot 1: (0,0) -> (1,1) -> (2,1) -> (3,1) -> (4,1)
Path of Robot 2: (0,2) -> (1,2) -> (2,2) -> (3,2) -> (4,2)

Cherries collected by Robot 1 = 3 + 1 + 2 + 1 + 1 = 8
Cherries collected by Robot 2 = 1 + 5 + 5 + 1 + 5 = 16
Total cherries = 8 + 16 = 24
```

---

### Example 2

```java
Input: grid = [,
 ,
 ,
 
]
Output: 28
```

---

# Intuition

This problem can be structurally modeled using **3D Dynamic Programming (Tabulation)**. 

Since both robots move down exactly **one row at a time**, they always reside on the same row `r` simultaneously. This allows us to sync their movements step-by-step down the grid. 
Instead of tracking each robot independently with separate row parameters, we can define the global system state using a single `row` layer combined with the individual column positions of both robots (`c1` and `c2`).

At row `r`, each robot has 3 valid directional moves for the next row: `j - 1`, `j`, or `j + 1`. This creates $3 \times 3 = 9$ total lookahead combination paths for the next state layer `r + 1`. 

**The Collision Rule:** If `c1 == c2`, both robots land on the exact same tile—we only add the cherry count of that cell once: `grid[r][c1]`. If they are on different tiles (`c1 != c2`), we safely sum both yields: `grid[r][c1] + grid[r][c2]`.

---

# State Definition

Let `dp[r][c1][c2]` be the maximum cherries collected from row `r` down to the bottom row, given Robot #1 is at column `c1` and Robot #2 is at column `c2`.
*   `r`: Active row index layer (from `0` to `rows - 1`).
*   `c1`: Column coordinate of Robot #1 (from `0` to `cols - 1`).
*   `c2`: Column coordinate of Robot #2 (from `0` to `cols - 1`).

---

# Recurrence Relation

For a state layer `r` at column coordinates `c1` and `c2`, we check lookahead combinations at row `r + 1`:

```text
cherriesInCurrentRow = (c1 == c2) ? grid[r][c1] : grid[r][c1] + grid[r][c2]

dp[r][c1][c2] = cherriesInCurrentRow + max( dp[r + 1][nextC1][nextC2] )
for all nextC1 ∈ {c1-1, c1, c1+1} and nextC2 ∈ {c2-1, c2, c2+1}
```

---

# Base Cases

When robots reach the final row (`r == rows - 1`), no subsequent moves exist. The score is simply the value of the cherries on their landing tiles:
```text
dp[rows - 1][c1][c2] = (c1 == c2) ? grid[rows - 1][c1] : grid[rows - 1][c1] + grid[rows - 1][c2]
```

---

# Approach: Space-Optimized Bottom-Up Tabulation

## Algorithm

1. Initialize a 2D matrix `dp[cols][cols]` representing the maximum cherries collected from the *next* row. Initialize with a minimum sentinel value (e.g., `-1`) to filter out invalid paths.
2. **Base Case Setup (Bottom Row):** Fill the baseline layer table for row `rows - 1` by applying the collision summation rule.
3. **Iterative Layer Construction:** Loop backward through rows `r` from `rows - 2` down to `0`:
   - Create a temporary 2D array `nextDp[cols][cols]` initialized to `-1`.
   - Loop through all column positions `c1` and `c2` from `0` to `cols - 1`:
     - Calculate `currentCherries = (c1 == c2) ? grid[r][c1] : grid[r][c1] + grid[r][c2]`.
     - Find the maximum lookahead reward by looping through the 3 choices for Robot 1 (`d1 ∈ {-1, 0, 1}`) and 3 choices for Robot 2 (`d2 ∈ {-1, 0, 1}`):
       - If `c1 + d1` and `c2 + d2` are within column boundaries, and `dp[c1 + d1][c2 + d2] != -1`:
         - Update our tracker: `maxNext = Math.max(maxNext, dp[c1 + d1][c2 + d2])`.
     - If a valid next state was found, store it: `nextDp[c1][c2] = currentCherries + maxNext`.
   - Shift the row layer window forward: `dp = nextDp`.
4. The maximum cherries collection will be located at `dp[0][cols - 1]` (the starting columns of both robots at row 0).

---

# Java Solution

```java
import java.util.Arrays;

class Solution {
    public int cherryPickup(int[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;

        // dp[c1][c2] stores the max cherries collected from the current row down to the bottom
        int[][] dp = new int[cols][cols];
        for (int[] row : dp) {
            Arrays.fill(row, -1); // -1 represents an unreachable/invalid path state
        }

        // Step 1: Base Case Setup for the absolute bottom row
        for (int c1 = 0; c1 < cols; c1++) {
            for (int c2 = 0; c2 < cols; c2++) {
                dp[c1][c2] = (c1 == c2) ? grid[rows - 1][c1] : grid[rows - 1][c1] + grid[rows - 1][c2];
            }
        }

        // Step 2: Build state layers backward from row rows-2 down to row 0
        for (int r = rows - 2; r >= 0; r--) {
            int[][] nextDp = new int[cols][cols];
            for (int[] row : nextDp) {
                Arrays.fill(row, -1);
            }

            for (int c1 = 0; c1 < cols; c1++) {
                for (int c2 = 0; c2 < cols; c2++) {
                    int currentCherries = (c1 == c2) ? grid[r][c1] : grid[r][c1] + grid[r][c2];
                    int maxNext = -1;

                    // Evaluate all 9 possible lookahead move combinations for both robots
                    for (int d1 = -1; d1 <= 1; d1++) {
                        for (int d2 = -1; d2 <= 1; d2++) {
                            int nextC1 = c1 + d1;
                            int nextC2 = c2 + d2;

                            // Ensure destination columns stay safely within matrix boundaries
                            if (nextC1 >= 0 && nextC1 < cols && nextC2 >= 0 && nextC2 < cols) {
                                if (dp[nextC1][nextC2] != -1) {
                                    maxNext = Math.max(maxNext, dp[nextC1][nextC2]);
                                }
                            }
                        }
                    }

                    // If a valid lookahead connection exists, save the step score
                    if (maxNext != -1) {
                        nextDp[c1][c2] = currentCherries + maxNext;
                    }
                }
            }
            // Move the rolling DP window matrix forward to the preceding row layer
            dp = nextDp;
        }

        // Step 3: Final result is at row 0 with Robot 1 at col 0 and Robot 2 at col cols-1
        return dp[0][cols - 1];
    }
}
```

---

## Complexity Analysis

Let:
*   R: Number of rows in the matrix field (`rows`).
*   C: Number of columns in the matrix field (`cols`).

### Time Complexity
The algorithm processes exactly R row layers. Inside each layer, it runs a two-tier nested loop over all columns $C \times C$, and evaluates 9 constant directional combinations. This bounds execution runtime to predictable polynomial dimensions:
```text
Time Complexity: O(R × C²)
```

### Space Complexity
By implementing a space-optimized tabulation layout where the previous row's matrix `nextDp` overwrites the active tracking matrix `dp`, we avoid allocating a full 3D $R \times C \times C$ matrix container. The memory footprint scales strictly bounding the column parameters:
```text
Space Complexity: O(C²)
```

---

# Key Pattern
This problem belongs to the **Synchronized Dual-Agent Layered Grid Tabulation** template inside dynamic programming.

Common indicators:
- Two independent agents moving down a grid matrix simultaneously at a uniform rate.
- Simultaneous turn transitions where state validation requires checking if agents collide onto the same element coordinates.
- Turn-based or row-based dependencies where layer `r` maps its outputs directly from the computed values of layer `r + 1`.

---

# Similar Problems
1. Cherry Pickup I (LeetCode 741)
2. Minimum Path Sum (LeetCode 64)
3. Unique Paths II (LeetCode 63)
4. Dungeon Game (LeetCode 174)

---

# Takeaway
```text
Synchronize dual-agent steps by using a single row layer loop parameter.

Deduplicate item allocation values immediately inside an conditional check whenever coordinates collide.

Traverse the grid rows backwards to build state dependencies from the terminal boundaries.
```

**Pattern:** Synchronized Dual-Agent Layered Grid Tabulation DP

<FollowUp>
We can now map out another advanced synchronized matrix or path tracking dynamic programming pattern. If you want, let me know if we should prepare the raw markdown for **Cherry Pickup I (LeetCode 741)** or transition over to an independent matrix routing model like **Dungeon Game (LeetCode 174)**!
</FollowUp>
