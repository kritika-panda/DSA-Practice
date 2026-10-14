# Maximal Square

## Problem Statement

Given an `m x n` binary `matrix` filled with `'0'`s and `'1'`s, find the largest square containing only `'1'`s and return *its area*.

The overall runtime complexity must be strictly bounded by:

```text
O(m × n)
```

*(where m is the number of rows and n is the number of columns in the grid)*

---

## Example 1

### Input

```text
matrix = [
  ["1","0","1","0","0"],
  ["1","0","1","1","1"],
  ["1","1","1","1","1"],
  ["1","0","0","1","0"]
]
```

### Output

```text
4
```

### Explanation

```text
The largest square containing only '1's is highlighted below (size 2x2):
  1  0  1  0  0
  1  0 [1][1] 1
  1  1 [1][1] 1
  1  0  0  1  0

Area = 2 × 2 = 4.
```

---

## Example 2

### Input

```text
matrix = [
  ["0","1"],
  ["1","0"]
]
```

### Output

```text
1
```

---

# Intuition

If we consider any cell `(i, j)` containing a `'1'`, it can form the **bottom-right corner of a square**. The maximum size of the square it can anchor depends directly on its three immediate neighbors:
1. **The top neighbor `(i - 1, j)`**
2. **The left neighbor `(i, j - 1)`**
3. **The top-left diagonal neighbor `(i - 1, j - 1)`**

A square of size `k` can only be formed at `(i, j)` if all three surrounding neighbor cells can independently anchor squares of size at least `k - 1`. If any of these neighbors is restricted to a smaller size, it bottlenecks the square expansion at `(i, j)`.

Therefore, the maximum side length of a square ending at `(i, j)` is:

```text
Max Side Length(i, j)
=
If matrix[i][j] == '0' -> 0
Else -> 1 + min(side from top-left, side from top, side from left)
```

By keeping a global tracker of the maximum side length found during the sweep, we can return its square area at the end.

---

# State Definition

Let:

```text
f(i, j)
=
Maximum side length of a square composed entirely of '1's
whose bottom-right corner is at cell coordinate (i, j)
```

---

# Recurrence

```text
If matrix[i][j] == '0' -> f(i, j) = 0
Else -> f(i, j) = 1 + min( f(i - 1, j - 1), f(i - 1, j), f(i, j - 1) )
```

whenever all coordinate lookups stay within valid matrix boundaries.

---

# Recursive Solution

## Base Cases

### Zeros

```text
matrix[i][j] == '0'

return 0
```

---

### Edge Cells (Row 0 or Col 0)

```text
i == 0 || j == 0

return 1 (since matrix[i][j] is '1')
```

---

## Java Code

```java
class Solution {

    private int solve(int i, int j, char[][] matrix) {
        // Base case: If we drift outside boundaries or hit a '0'
        if (i < 0 || j < 0 || matrix[i][j] == '0') {
            return 0;
        }

        // Base case: First row or first column can at most form a square of side 1
        if (i == 0 || j == 0) {
            return 1;
        }

        // Query the three required neighbor anchors recursively
        int topLeft = solve(i - 1, j - 1, matrix);
        int top = solve(i - 1, j, matrix);
        int left = solve(i, j - 1, matrix);

        return 1 + Math.min(topLeft, Math.min(top, left));
    }

    public int maximalSquare(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return 0;
        }

        int m = matrix.length;
        int n = matrix[0].length;
        int maxSide = 0;

        // Evaluate every cell as a potential bottom-right corner anchor
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == '1') {
                    maxSide = Math.max(maxSide, solve(i, j, matrix));
                }
            }
        }

        return maxSide * maxSide;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(3^(M + N))
Space Complexity: O(M + N)
```

---

# Memoization (Top-Down DP)

## Idea

Store maximum square side values inside a 2D cache matrix array `dp[m][n]`.  
Avoid expanding duplicate directional subproblem intersections.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int i, int j, char[][] matrix, int[][] dp) {
        if (i < 0 || j < 0 || matrix[i][j] == '0') {
            return 0;
        }

        if (i == 0 || j == 0) {
            return 1;
        }

        if (dp[i][j] != -1) {
            return dp[i][j];
        }

        int topLeft = solve(i - 1, j - 1, matrix, dp);
        int top = solve(i - 1, j, matrix, dp);
        int left = solve(i, j - 1, matrix, dp);

        return dp[i][j] = 1 + Math.min(topLeft, Math.min(top, left));
    }

    public int maximalSquare(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return 0;
        }

        int m = matrix.length;
        int n = matrix[0].length;
        int[][] dp = new int[m][n];
        for (int[] row : dp) {
            Arrays.fill(row, -1);
        }

        int maxSide = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == '1') {
                    maxSide = Math.max(maxSide, solve(i, j, matrix, dp));
                }
            }
        }

        return maxSide * maxSide;
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
dp[i][j] = maximum side length of a square ending at cell (i, j)
```

---

## Base Case

For any cell `(i, j)` where `matrix[i][j] == '1'` and `i == 0` or `j == 0`:

```text
dp[i][j] = 1
```

---

## Transition

For any cell `(i, j)` where `i > 0` and `j > 0`:

```text
If matrix[i][j] == '0' -> dp[i][j] = 0
Else -> dp[i][j] = 1 + min( dp[i-1][j-1], dp[i-1][j], dp[i][j-1] )
```

---

## Java Code

```java
class Solution {

    public int maximalSquare(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return 0;
        }

        int m = matrix.length;
        int n = matrix[0].length;
        int[][] dp = new int[m][n];
        int maxSide = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == '1') {
                    if (i == 0 || j == 0) {
                        dp[i][j] = 1;
                    } else {
                        dp[i][j] = 1 + Math.min(dp[i - 1][j - 1], Math.min(dp[i - 1][j], dp[i][j - 1]));
                    }
                    maxSide = Math.max(maxSide, dp[i][j]);
                }
            }
        }

        return maxSide * maxSide;
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

To compute cell values inside the current row `i`:

```text
dp[i][j] = 1 + min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1])
```

We look at the cell directly above (`dp[i-1][j]`), the cell to the left (`dp[i][j-1]`), and the top-left diagonal cell (`dp[i-1][j-1]`). 

We can drop the full matrix structure and compress space into a single 1D rolling array `dp` of size `n`. As we overwrite the array from left to right, we store the previous top-left diagonal state inside a simple primitive `prev` register tracker variable.

---

## Java Code

```java
class Solution {

    public int maximalSquare(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return 0;
        }

        int m = matrix.length;
        int n = matrix[0].length;
        
        // 1D rolling array representing states for the current row
        int[] dp = new int[n + 1];
        int maxSide = 0;
        int topLeftDiagonal = 0; // Tracks dp[i-1][j-1] context on overwrites

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                int temp = dp[j]; // Store this cell before overwriting it; it serves as top-left for cell j+1
                
                if (matrix[i - 1][j - 1] == '1') {
                    // current = 1 + min(top-left diagonal, top neighbor, left neighbor)
                    dp[j] = 1 + Math.min(topLeftDiagonal, Math.min(dp[j], dp[j - 1]));
                    maxSide = Math.max(maxSide, dp[j]);
                } else {
                    dp[j] = 0;
                }
                
                topLeftDiagonal = temp; // Set up diagonal context for the next column coordinate step
            }
        }

        return maxSide * maxSide;
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
matrix = [
  ["1","1"],
  ["1","1"]
]
```

---

### i = 1 (Row 1 Sweep)

```text
j = 1: temp = dp[1] (0). matrix[0][0]=='1' -> dp[1] = 1 + min(0, 0, 0) = 1. maxSide = 1. topLeftDiagonal = 0.
j = 2: temp = dp[2] (0). matrix[0][1]=='1' -> dp[2] = 1 + min(0, 0, 1) = 1. maxSide = 1. topLeftDiagonal = 0.
dp array: [0, 1, 1]
```

i = 2 (Row 2 Sweep)

```text
j = 1: topLeftDiagonal reset to 0 at row start.
temp = dp[1] (1). matrix[1][0]=='1' -> dp[1] = 1 + min(0, 1, 0) = 1. maxSide = 1. topLeftDiagonal = 1.
j = 2: temp = dp[2] (1). matrix[1][1]=='1' -> dp[2] = 1 + min(1, 1, 1) = 2. maxSide = 2. topLeftDiagonal = 1.
dp array: [0, 1, 2]
```

Answer

```text 
maxSide = 2 -> Area = 2 × 2 = 4
```

Visualization


Square Construction Grid Track

```text
[1] ───► [1] │        │ ▼        ▼ [1] ───► [2]  <-- formed by evaluating mins from top, left, and diagonal frames 
```

DP Pattern


Identify State


```text
f(i, j)

Max side length of a square ending at (i, j) anchored as bottom-right corner
```

Choices

```text
If grid[i][j] == '1', combine matching sub-squares from:
1. Top-Left neighbor (i - 1, j - 1)
2. Top neighbor (i - 1, j)
3. Left neighbor (i, j - 1)
```

Recurrence

```text 
If matrix[i][j] == '0' -> f(i, j) = 0 Else -> f(i, j) = 1 + min( f(i-1, j-1), f(i-1, j), f(i, j-1) )
```

Base Cases

```text 
f(out of bounds indices) = 0 f(i, j) = 0 if matrix[i][j] == '0'
```

Key Takeaways

✅ Classic 2D grid matrix neighborhood constraint problem.
✅ Unlike area calculation sweeps that use stacks (like Maximal Rectangle), this problem uses a fast local 3-neighbor minimum evaluation thanks to the uniform geometric constraint of a square.
✅ Space optimization compresses memory layouts from $O(M \times N)$ down to a single line buffer $O(N)$ using a single variable to capture the overwritten diagonal data.
✅ Common Interview Pattern:
text Maximize Square Matrix Configurations with Multi-Neighbor Dependencies 

Similar Problems

• Maximal Rectangle (LeetCode 85)
• Unique Paths (LeetCode 62)
• Minimum Path Sum (LeetCode 64)
• Count Square Submatrices with All Ones (LeetCode 1277)
