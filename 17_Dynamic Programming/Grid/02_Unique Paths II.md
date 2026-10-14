# Unique Paths II

## Problem Statement

You are given an `m x n` integer array `obstacleGrid`. There is a robot initially located at the **top-left corner** (i.e., `obstacleGrid[0][0]`). The robot tries to move to the **bottom-right corner** (i.e., `obstacleGrid[m - 1][n - 1]`). The robot can only move either **down** or **right** at any point in time.

An obstacle and space are marked as `1` and `0` respectively in the grid. A path that the robot takes cannot include **any** square that is an obstacle.

Return *the number of possible unique paths that the robot can take to reach the bottom-right corner*.

The test cases are generated so that the answer will be less than or equal to 2 × 10⁹.

---

## Example 1

### Input

```text
obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
```

### Output

```text
2
```

### Explanation

```text
There is one obstacle in the middle of the 3x3 grid above.
There are two ways to reach the bottom-right corner:
1. Right → Right → Down → Down
2. Down → Down → Right → Right
```

---

## Example 2

### Input

```text
obstacleGrid = [[0,1],[0,0]]
```

### Output

```text
1
```

---

# Intuition

This problem builds directly upon the foundational *Unique Paths* setup. At any given cell `(i, j)`, the robot can enter it from two directions:
1. **From the top cell `(i - 1, j)`:** Moving downward.
2. **From the left cell `(i, j - 1)`:** Moving rightward.

However, we must introduce an additional operational constraint: **if the current square `(i, j)` contains an obstacle (`obstacleGrid[i][j] == 1`), the robot cannot step onto it.** Therefore, the number of unique ways to reach or pass through an obstacle square drops to exactly `0`. 

```text
Unique Paths(i, j)
=
If cell is an obstacle -> 0
Else -> paths from (i - 1, j) + paths from (i, j - 1)
```

---

# State Definition

Let:

```text
f(i, j)
=
Number of unique paths from the top-left 
origin corner (0, 0) to cell (i, j) without touching obstacles
```

---

# Recurrence

```text
If obstacleGrid[i][j] == 1 -> f(i, j) = 0
Else -> f(i, j) = f(i - 1, j) + f(i, j - 1)
```

whenever both source coordinates fall within valid grid boundaries.

---

# Recursive Solution

## Base Cases

### Obstacle Encountered

```text
obstacleGrid[i][j] == 1

return 0
```

---

### Origin Point

```text
i == 0 && j == 0

return 1
```

---

### Out of Bounds

```text
i < 0 || j < 0

return 0
```

---

## Java Code

```java
class Solution {

    private int solve(int i, int j, int[][] obstacleGrid) {
        // Base case: If we drift outside grid boundary coordinates
        if (i < 0 || j < 0) {
            return 0;
        }

        // Base case: If the current cell is blocked by an obstacle
        if (obstacleGrid[i][j] == 1) {
            return 0;
        }

        // Base case: Start origin position holds exactly 1 initial path config
        if (i == 0 && j == 0) {
            return 1;
        }

        // Branch paths dynamically from top and left neighbors
        int up = solve(i - 1, j, obstacleGrid);
        int left = solve(i, j - 1, obstacleGrid);

        return up + left;
    }

    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        return solve(m - 1, n - 1, obstacleGrid);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(2^(M + N))
Space Complexity: O(M + N)
```

---

# Memoization (Top-Down DP)

## Idea

Store intermediate grid coordination values inside a 2D cache matrix array `dp[m][n]`.  
Avoid expanding duplicate directional subproblem pathways.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int i, int j, int[][] obstacleGrid, int[][] dp) {
        if (i < 0 || j < 0) {
            return 0;
        }

        if (obstacleGrid[i][j] == 1) {
            return 0;
        }

        if (i == 0 && j == 0) {
            return 1;
        }

        // Return cached state instantly if previously evaluated
        if (dp[i][j] != -1) {
            return dp[i][j];
        }

        int up = solve(i - 1, j, obstacleGrid, dp);
        int left = solve(i, j - 1, obstacleGrid, dp);

        return dp[i][j] = up + left;
    }

    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        
        int[][] dp = new int[m][n];
        for (int[] row : dp) {
            Arrays.fill(row, -1);
        }

        return solve(m - 1, n - 1, obstacleGrid, dp);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(M × N)
Space Complexity : O(M × N) + O(M + N) recursion stack
```

---

# Tabulation (Bottom-Up DP)

## DP Definition

```text
dp[i][j] = unique paths from the origin to cell (i, j) avoiding obstacles
```

---

## Base Case

```text
dp[0][0] = obstacleGrid[0][0] == 1 ? 0 : 1
```

---

## Transition

For any cell `(i, j)` where `i > 0` or `j > 0`:

```text
If obstacleGrid[i][j] == 1 -> dp[i][j] = 0
Else -> dp[i][j] = (i > 0 ? dp[i - 1][j] : 0) + (j > 0 ? dp[i][j - 1] : 0)
```

---

## Java Code

```java
class Solution {

    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        
        // If the origin or destination itself is blocked, no path is possible
        if (obstacleGrid[0][0] == 1 || obstacleGrid[m - 1][n - 1] == 1) {
            return 0;
        }

        int[][] dp = new int[m][n];
        dp[0][0] = 1;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // Skip the origin point since it is initialized
                if (i == 0 && j == 0) {
                    continue;
                }

                // If the current cell is an obstacle, paths to it are 0
                if (obstacleGrid[i][j] == 1) {
                    dp[i][j] = 0;
                    continue;
                }

                int up = 0;
                int left = 0;

                if (i > 0) up = dp[i - 1][j];
                if (j > 0) left = dp[i][j - 1];

                dp[i][j] = up + left;
            }
        }

        return dp[m - 1][n - 1];
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(M × N)
Space Complexity : O(M × N)
```

---

# Space Optimized Solution

## Observation

To compute any cell value inside the current row index `i`:

```text
dp[i][j] = obstacleGrid[i][j] == 1 ? 0 : dp[i-1][j] + dp[i][j-1]
```

We only ever read values from the **immediate preceding cell in the current row** (`dp[i][j-1]`) and the **matching column cell of the row right above it** (`dp[i-1][j]`). 

Therefore, storing a full 2D grid matrix is redundant. We can compress the entire environment allocation down to a single 1D rolling array `dp` of size `n`.

---

## Java Code

```java
class Solution {

    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;

        // Fast-fail if the origin or target destination is blocked
        if (obstacleGrid[0][0] == 1 || obstacleGrid[m - 1][n - 1] == 1) {
            return 0;
        }

        // 1D rolling array representing states for the current row
        int[] dp = new int[n];
        dp[0] = 1; // Base Case origin initialization

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // If current cell is blocked, reset its running path value to 0
                if (obstacleGrid[i][j] == 1) {
                    dp[j] = 0;
                } 
                // Skip updating the baseline origin cell on the first iteration
                else if (j > 0) {
                    // new_dp[j] = old_dp[j] (from top neighbor) + new_dp[j-1] (from left neighbor)
                    dp[j] = dp[j] + dp[j - 1];
                }
            }
        }

        return dp[n - 1];
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(M × N)
Space Complexity : O(N)
```

---

# Dry Run

## Input

```text
obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
```

---

### Initialize (i = 0 Row Configuration)

```text
dp = [1, 0, 0]

j = 0 -> skip
j = 1 -> dp[1] = dp[1] (0) + dp[0] (1) = 1
j = 2 -> dp[2] = dp[2] (0) + dp[1] (1) = 1

dp array after row 0: [1, 1, 1]
```

---

### i = 1 (Row 1 Calculation Sweep)

```text
j = 0 -> dp[0] remains 1 (inherited from top)
j = 1 -> obstacleGrid[1][1] == 1 -> dp[1] = 0 (reset!)
j = 2 -> dp[2] = dp[2] (1 from top) + dp[1] (0 from left) = 1

dp array after row 1: [1, 0, 1]
```

---

### i = 2 (Row 2 Calculation Sweep)

```text
j = 0 -> dp[0] remains 1
j = 1 -> dp[1] = dp[1] (0 from top) + dp[0] (1 from left) = 1
j = 2 -> dp[2] = dp[2] (1 from top) + dp[1] (1 from left) = 2
dp array after row 2: [1, 1, 2]
```


Answer

text 2 

Visualization


Unique Path Routing Combinations Grid

text [0] ───► [1] ───► [1] │                 │ ▼                 ▼ [1]      [X]      [1] │                 │ ▼                 ▼ [1] ───► [1] ───► [2] 

DP Pattern


Identify State


```text
f(i, j)

Total unique path variations from origin to cell coordinate (i, j) avoiding obstacles
```

Choices

```text
To arrive at cell (i, j):
1. Travel downward from cell (i - 1, j) if not an obstacle
2. Travel rightward from cell (i, j - 1) if not an obstacle
```

Recurrence

```text 
If grid[i][j] == 1 -> f(i, j) = 0 Else -> f(i, j) = f(i - 1, j) + f(i, j - 1)
```

Base Cases

```text 
f(0, 0) = grid[0][0] == 1 ? 0 : 1 f(out of bounds) = 0 
```

Key Takeaways

✅ Advanced 2D Grid Coordinate matrix optimization with explicit obstacle pruning logic
✅ Space optimization compresses memory layout down to a single rolling 1D array of size O(N) by dynamically overwriting cells in-place
✅ Common Interview Pattern:
text Count Matrix Paths with Obstacles and Movement Constraints 

Similar Problems

• Unique Paths (LeetCode 62)
• Minimum Path Sum (LeetCode 64)
• Out of Boundary Paths (LeetCode 576)
• Path with Maximum Gold (LeetCode 1219)
