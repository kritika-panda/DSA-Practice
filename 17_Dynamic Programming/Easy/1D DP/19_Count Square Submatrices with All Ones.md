# Count Square Submatrices with All Ones (LeetCode 1277)

## Problem Statement

Given a `m x n` matrix of ones and zeros, return *how many **square** submatrices have all ones*.

---

## Examples

### Example 1

```java
Input: matrix = [
  [0,1,1,1],
  [1,1,1,1],
  [0,1,1,1]
]
Output: 15
```

### Explanation

```text
- There are 10 squares of size 1x1.
- There are 4 squares of size 2x2.
- There is 1 square of size 3x3.

Total number of square submatrices = 10 + 4 + 1 = 15.
```

---

### Example 2

```java
Input: matrix = [
  [1,0,1],
  [1,1,0],
  [1,1,0]
]
Output: 7
```

### Explanation

```text
- There are 6 squares of size 1x1.
- There is 1 square of size 2x2.

Total number of square submatrices = 6 + 1 = 7.
```

---

# Intuition

This problem can be elegantly solved using **2D Dynamic Programming (Tabulation)**. 

Instead of searching for squares of various sizes independently, we can change our perspective: **What is the largest square submatrix whose bottom-right corner ends exactly at cell `(i, j)`?**

The beauty of this perspective shift is that if the largest square ending at `(i, j)` has a side length of `K`, it means it simultaneously forms exactly `K` distinct squares (of sizes \(1\times1, 2\times2, \dots, K\timesK\)) all anchored at that same bottom-right corner. Therefore, if we find this max side length for every cell, the sum of these values across the entire matrix will give us the total count of all valid square submatrices.

To form a square of side length `K` ending at `(i, j)`, the cell itself must be a `1`, and it relies on three adjacent historical neighbors to support it:
1. The cell directly above: `(i - 1, j)`
2. The cell directly to the left: `(i, j - 1)`
3. The cell diagonally up-left: `(i - 1, j - 1)`

The cell `(i, j)` can only expand the square as large as the **minimum** size supported by these three neighbors.

---

# State Definition

Let `dp[i][j]` be the side length of the largest square submatrix full of `1`s whose bottom-right corner is at cell `(i, j)`.
*   `i`: Row index in the matrix (from `0` to `m - 1`).
*   `j`: Column index in the matrix (from `0` to `n - 1`).

---

# Recurrence Relation

For any cell `(i, j)` where `matrix[i][j] == 1`:

```text
dp[i][j] = 1 + min( dp[i - 1][j], dp[i][j - 1], dp[i - 1][j - 1] )
```

If `matrix[i][j] == 0`, it cannot be part of any valid square, so `dp[i][j] = 0`.

---

# Base Cases

Any cell residing on the **first row (`i == 0`)** or the **first column (`j == 0`)** cannot look back to expand diagonally. Their max square size is strictly bounded by their own value:
```text
dp[0][j] = matrix[0][j]
dp[i][0] = matrix[i][0]
```

---

# Approach: Bottom-Up 2D Dynamic Programming

## Algorithm

1. Initialize a 2D matrix `dp[m][n]` of the same size as `matrix`.
2. Maintain a running variable `totalSquares = 0`.
3. Loop through every cell `(i, j)` in the matrix from `0` to `m - 1` and `0` to `n - 1`:
   - If `matrix[i][j] == 1`:
     - If it is on the boundary (`i == 0` or `j == 0`), `dp[i][j] = 1`.
     - Otherwise, compute: `dp[i][j] = 1 + Math.min(dp[i - 1][j], Math.min(dp[i][j - 1], dp[i - 1][j - 1]))`.
     - Add `dp[i][j]` to `totalSquares`.
4. Return `totalSquares`.

---

# Dry Run

```java
matrix = [
  [0, 1],
  [1, 1]
]
```

### Initial State

```text
totalSquares = 0
```

---

### Step 1 (Row 0, Boundary Pass)
- `(0, 0)` is 0 → `dp[0][0] = 0`. `totalSquares` = 0.
- `(0, 1)` is 1 → `dp[0][1] = 1`. `totalSquares` = 0 + 1 = 1.

---

### Step 2 (Row 1)
- `(1, 0)` is 1 (Boundary) → `dp[1][0] = 1`. `totalSquares` = 1 + 1 = 2.
- `(1, 1)` is 1 → Look at neighbors:
  - Up: `dp[0][1] = 1`
  - Left: `dp[1][0] = 1`
  - Diagonal: `dp[0][0] = 0`
  - `dp[1][1] = 1 + min(1, 1, 0) = 1 + 0 = 1`.
  - `totalSquares` = 2 + 1 = 3.

Final `dp` matrix matches individual element sizes, summing cleanly to 3.

---

# Java Solution

```java
class Solution {
    public int countSquares(int[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return 0;
        }

        int m = matrix.length;
        int n = matrix[0].length;
        int[][] dp = new int[m][n];
        int totalSquares = 0;

        // Traverse through each cell in the 2D grid matrix
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == 1) {
                    // Base Case Check: First row or first column cells are bounded by size 1
                    if (i == 0 || j == 0) {
                        dp[i][j] = 1;
                    } else {
                        // Core DP Step: Take the minimum of top, left, and top-left diagonal neighbors
                        dp[i][j] = 1 + Math.min(dp[i - 1][j], 
                                       Math.min(dp[i][j - 1], dp[i - 1][j - 1]));
                    }
                    
                    // Accumulate the count of all square submatrices ending at (i, j)
                    totalSquares += dp[i][j];
                }
            }
        }

        return totalSquares;
    }
}
```

---

# Space Optimization to O(N)

Since `dp[i][j]` depends only on the current row and the previous row, we can compress our storage matrix into a **1D array** of size `n`. We maintain a `prev` variable to store the old diagonal value `dp[i-1][j-1]` before it gets overwritten, optimizing our space footprint down to an absolute linear minimum.

```java
class Solution {
    public int countSquares(int[][] matrix) {
        if (matrix == null || matrix.length == 0) return 0;
        
        int m = matrix.length;
        int n = matrix[0].length;
        int[] dp = new int[n];
        int totalSquares = 0;

        for (int i = 0; i < m; i++) {
            int prev = 0; // Tracks the top-left diagonal value dp[i-1][j-1]
            for (int j = 0; j < n; j++) {
                int temp = dp[j]; // Store current value before overwriting it
                if (matrix[i][j] == 1) {
                    if (i == 0 || j == 0) {
                        dp[j] = 1;
                    } else {
                        dp[j] = 1 + Math.min(dp[j], Math.min(dp[j - 1], prev));
                    }
                    totalSquares += dp[j];
                } else {
                    dp[j] = 0;
                }
                prev = temp; // Update the diagonal tracker for the next column cycle
            }
        }
        return totalSquares;
    }
}
```

---

## Complexity Analysis

Let:
*   M: Number of rows in the matrix field.
*   N: Number of columns in the matrix field.

### Time Complexity
The algorithm loops through every cell in the grid exactly once using a nested two-tier loop. Inside, operations resolve instantly using basic primitive arithmetic minimizations, bounding runtime linearly relative to the area:
```text
Time Complexity: O(M × N)
```

### Space Complexity
- Standard approach: Matrix layout space of \(O(M \times N)\).
- Optimized approach: Compressed single-row buffer space of:
```text
Space Complexity: O(N)
```

---

# Key Pattern
This problem belongs to the **Grid Layer / Square Expansion Tabulation** dynamic programming template.

Common indicators:
- Processing a 2D matrix structure to track or count square properties.
- State evaluation requires checking three adjacent historical neighbors simultaneously (Top, Left, Diagonal Up-Left).
- Accumulating a running total sum of state values rather than just extracting a single terminal coordinate cell answer.

---

# Similar Problems
1. Maximal Square (Find largest single area - LeetCode 221)
2. Maximal Rectangle (LeetCode 85)
3. Unique Paths (LeetCode 62)
4. Minimum Path Sum (LeetCode 64)

---

# Takeaway
```text
Shift the perspective to evaluate squares anchoring at their bottom-right corner.

The maximum size length of a square at a cell directly matches the count of submatrices it creates.

Take the minimum of the three adjacent historical neighbors to dynamically scale square constraints.
```

**Pattern:** Grid Layer / Square Expansion Tabulation DP
