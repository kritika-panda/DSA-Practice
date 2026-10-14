# Minimum Falling Path Sum

## Problem Statement

Given an `n x n` array of integers `matrix`, return *the **minimum sum** of any **falling path** through `matrix`*.

A **falling path** starts at any element in the first row and chooses the element in the next row that is either directly below it or diagonally left/right. Specifically, the next element from position `(row, col)` will be chosen from:
* `(row + 1, col - 1)`
* `(row + 1, col)`
* `(row + 1, col + 1)`

The overall runtime complexity must be:

```text
O(n²)
```

*(where n is the number of rows and columns in the square matrix)*

---

## Example 1

### Input

```text
matrix = [,
 ,
  [7, 8, 9]
]
```

### Output

```text
13
```

### Explanation

```text
There are two distinct falling paths with a minimum sum of 13:
1. 1 → 5 → 7 (Sum = 13)
2. 1 → 4 → 8 (Sum = 13)
```

---

## Example 2

### Input

```text
matrix = [
  [-19, 57],
  [-40, -5]
]
```

### Output

```text
-59
```

### Explanation

```text
The optimal path is: -19 → -40 (Sum = -59).
```

---

# Intuition

To land on a cell `(i, j)` in the current row, the robot/frog could have dropped down from exactly three adjacent cells in the row directly above it (`i - 1`):
1. **From the top-left diagonal cell `(i - 1, j - 1)`**
2. **From the cell directly above `(i - 1, j)`**
3. **From the top-right diagonal cell `(i - 1, j + 1)`**

To minimize the total energy/sum to reach cell `(i, j)`, we greedily take the minimum value among these three source choices and add the current cell's raw value to it:

```text
Minimum Falling Path Sum(i, j)
=
matrix[i][j]
+
min(
    path to (i - 1, j - 1),
    path to (i - 1, j),
    path to (i - 1, j + 1)
)
```

This structural relationship forms a classic grid-based Dynamic Programming puzzle.

---

# State Definition

Let:

```text
f(i, j)
=
Minimum falling path sum from any cell in the 
first row down to cell coordinate (i, j)
```

---

# Recurrence

```text
f(i, j) = matrix[i][j] + min( f(i - 1, j - 1), f(i - 1, j), f(i - 1, j + 1) )
```

whenever the source coordinates stay securely within valid matrix column boundaries.

---

# Recursive Solution

## Base Cases

### First Row Reach

```text
i == 0

return matrix[0][j]
```

---

### Out of Column Bounds

```text
j < 0 || j >= n

return Integer.MAX_VALUE
```

---

## Java Code

```java
class Solution {

    private int solve(int i, int j, int[][] matrix) {
        // Base case: If we drift outside column boundaries
        if (j < 0 || j >= matrix.length) {
            return Integer.MAX_VALUE;
        }

        // Base case: Reached the top row, return the raw value
        if (i == 0) {
            return matrix[0][j];
        }

        // Branch from the three possible parents above
        int upLeft = solve(i - 1, j - 1, matrix);
        int upDirect = solve(i - 1, j, matrix);
        int upRight = solve(i - 1, j + 1, matrix);

        int minParent = Math.min(upDirect, Math.min(upLeft, upRight));

        return matrix[i][j] + minParent;
    }

    public int minFallingPathSum(int[][] matrix) {
        int n = matrix.length;
        int minSum = Integer.MAX_VALUE;

        // Any cell in the final row can be a terminal point
        for (int j = 0; j < n; j++) {
            minSum = Math.min(minSum, solve(n - 1, j, matrix));
        }

        return minSum;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(3^N)
Space Complexity: O(N)
```

---

# Memoization (Top-Down DP)

## Idea

Store intermediate minimum cell path choices inside a 2D cache matrix array `dp[n][n]`.  
Avoid expanding duplicate directional subproblem pathways.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int i, int j, int[][] matrix, int[][] dp) {
        if (j < 0 || j >= matrix.length) {
            return Integer.MAX_VALUE;
        }

        if (i == 0) {
            return matrix[0][j];
        }

        if (dp[i][j] != -10101) { // out of standard range sentinel value
            return dp[i][j];
        }

        int upLeft = solve(i - 1, j - 1, matrix, dp);
        int upDirect = solve(i - 1, j, matrix, dp);
        int upRight = solve(i - 1, j + 1, matrix, dp);

        int minParent = Math.min(upDirect, Math.min(upLeft, upRight));

        return dp[i][j] = matrix[i][j] + minParent;
    }

    public int minFallingPathSum(int[][] matrix) {
        int n = matrix.length;
        int[][] dp = new int[n][n];
        for (int[] row : dp) {
            Arrays.fill(row, -10101);
        }

        int minSum = Integer.MAX_VALUE;
        for (int j = 0; j < n; j++) {
            minSum = Math.min(minSum, solve(n - 1, j, matrix, dp));
        }

        return minSum;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N²)
Space Complexity : O(N²) + O(N) recursion stack
```

---

# Tabulation (Bottom-Up DP)

## DP Definition

```text
dp[i][j] = minimum falling path sum to reach cell (i, j)
```

---

## Base Case

For `i = 0`, initialize all cells with the values of the first row:

```text
dp[0][j] = matrix[0][j]
```

---

## Transition

For any cell `(i, j)` where `i > 0`:

```text
dp[i][j] = matrix[i][j] + min( dp[i-1][j-1], dp[i-1][j], dp[i-1][j+1] )
```

---

## Java Code

```java
class Solution {

    public int minFallingPathSum(int[][] matrix) {
        int n = matrix.length;
        int[][] dp = new int[n][n];

        // Base case setup: copy the first row over unchanged
        for (int j = 0; j < n; j++) {
            dp[0][j] = matrix[0][j];
        }

        // Iterate through all remaining rows sequentially
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < n; j++) {
                
                int upLeft = (j > 0) ? dp[i - 1][j - 1] : Integer.MAX_VALUE;
                int upDirect = dp[i - 1][j];
                int upRight = (j < n - 1) ? dp[i - 1][j + 1] : Integer.MAX_VALUE;

                dp[i][j] = matrix[i][j] + Math.min(upDirect, Math.min(upLeft, upRight));
            }
        }

        // Extract the absolute minimum from the final row cells
        int minSum = Integer.MAX_VALUE;
        for (int j = 0; j < n; j++) {
            minSum = Math.min(minSum, dp[n - 1][j]);
        }

        return minSum;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N²)
Space Complexity : O(N²)
```

---

# Space Optimized Solution

## Observation

To compute any cell value inside the current row index `i`:

```text
dp[i][j] = matrix[i][j] + min(dp[i-1][j-1], dp[i-1][j], dp[i-1][j+1])
```

We only ever read values from the **immediate row right above it** (`dp[i-1]`). 

Therefore, storing a full 2D grid matrix is redundant. We can compress our memory environment allocation down to a single 1D rolling array `prev` of size `n` representing the row above, and a `temp` array representing the current row.

---

## Java Code

```java
class Solution {

    public int minFallingPathSum(int[][] matrix) {
        int n = matrix.length;
        
        // Array representing the row immediately above our active workspace row
        int[] prev = new int[n];
        for (int j = 0; j < n; j++) {
            prev[j] = matrix[0][j];
        }

        for (int i = 1; i < n; i++) {
            int[] temp = new int[n];
            for (int j = 0; j < n; j++) {
                
                int upLeft = (j > 0) ? prev[j - 1] : Integer.MAX_VALUE;
                int upDirect = prev[j];
                int upRight = (j < n - 1) ? prev[j + 1] : Integer.MAX_VALUE;

                temp[j] = matrix[i][j] + Math.min(upDirect, Math.min(upLeft, upRight));
            }
            prev = temp; // Roll variables forward
        }

        // Locate minimum sum from the final row cache register
        int minSum = Integer.MAX_VALUE;
        for (int j = 0; j < n; j++) {
            minSum = Math.min(minSum, prev[j]);
        }

        return minSum;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N²)
Space Complexity : O(N)
```

---

# Dry Run

## Input

```text
matrix = [,
 ,
  [7, 8, 9]
]
```

---

### Initialize (i = 0 Row Configuration)

```text
prev = [2, 1, 3]
```

---

### i = 1 (Row 1 Calculation Sweep)

```text
j = 0: temp[0] = 6 + min(INF, 2, 1) = 6 + 1 = 7
j = 1: temp[1] = 5 + min(2, 1, 3) = 5 + 1 = 6
j = 2: temp[2] = 4 + min(1, 3, INF) = 4 + 1 = 5

prev updates to: [7, 6, 5]
```

---

### i = 2 (Row 2 Calculation Sweep)

```text
j = 0: temp[0] = 7 + min(INF, 7, 6) = 7 + 6 = 13
j = 1: temp[1] = 8 + min(7, 6, 5) = 8 + 5 = 13
j = 2: temp[2] = 9 + min(6, 5, INF) = 9 + 5 = 14

prev updates to: [13, 13, 14]
```

---

Answer
```text
min(13, 13, 14) = 13
```

Visualization


## Falling Path Routing Combinations Grid

```text 
[2]      [1]      [3] \     /   \     / \   /     \   / [7]      [6]      [5] \     /   \     / \   /     \   / [13]     [13]     [14] 
```

## DP Pattern


## Identify State


```text
f(i, j)
Minimum falling path sum from row 0 to grid cell location (i, j)
```

## Choices

```text
To arrive at cell (i, j):
1. Travel diagonally right-down from (i - 1, j - 1)
2. Travel directly down from (i - 1, j)
3. Travel diagonally left-down from (i - 1, j + 1)
```

## Recurrence

text f(i, j) = matrix[i][j] + min( f(i - 1, j - 1), f(i - 1, j), f(i - 1, j + 1) ) 

## Base Cases

text f(0, j) = matrix[0][j] f(out of bounds column) = Integer.MAX_VALUE 

## Key Takeaways

✅ Standard 2D dynamic programming grid minimization framework.
✅ Unlike fixed single-target destination cells (like bottom-right in Unique Paths), the answer must be evaluated across every cell in the final terminal row.
✅ Space optimization compresses memory layouts from $O(N^2)$ straight down to an absolute linear 1D buffer size $O(N)$.
✅ Common Interview Pattern:
```text 
Minimize Matrix Paths with Variable Boundaries and Multi-Choice Moves
```

##Similar Problems

• Unique Paths (LeetCode 62)
• Minimum Path Sum (LeetCode 64)
• Triangle (LeetCode 120)
• Minimum Falling Path Sum incentive variant II (LeetCode 1289)
