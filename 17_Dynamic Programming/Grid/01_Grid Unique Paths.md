# Unique Paths

## Problem Statement

There is a robot on an `m x n` grid. The robot is initially located at the **top-left corner** (i.e., `grid[0][0]`) and attempts to move to the **bottom-right corner** (i.e., `grid[m - 1][n - 1]`).

The robot can only move either **down** or **right** at any point in time.

Given the two integers `m` and `n`, return *the number of possible unique paths that the robot can take to reach the bottom-right corner*.

The test cases are generated so that the answer will be less than or equal to 2 × 10⁹.

---

## Example 1

### Input

```text
m = 3, n = 7
```

### Output

```text
28
```

---

## Example 2

### Input

```text
m = 3, n = 2
```

### Output

```text
3
```

### Explanation

```text
From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1. Right → Down → Down
2. Down → Down → Right
3. Down → Right → Down
```

---

# Intuition

At any given cell `(i, j)`, the robot can only enter it from two adjacent directions:
1. **From the top cell `(i - 1, j)`:** Moving downward.
2. **From the left cell `(i, j - 1)`:** Moving rightward.

Therefore, the total unique ways to reach cell `(i, j)` is the sum of the unique ways to reach those two predecessor coordinates:

```text
Unique Paths(i, j)
=
paths from (i - 1, j)
+
paths from (i, j - 1)
```

This structural dependency creates a classic grid-based Dynamic Programming puzzle.

---

# State Definition

Let:

```text
f(i, j)
=
Number of unique paths from the top-left 
origin corner (0, 0) to cell (i, j)
```

---

# Recurrence

```text
f(i, j) = f(i - 1, j) + f(i, j - 1)
```

whenever both source coordinates fall within valid grid boundaries.

---

# Recursive Solution

## Base Cases

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

    private int solve(int i, int j) {
        // Base case: If we drift outside grid boundary coordinates
        if (i < 0 || j < 0) {
            return 0;
        }

        // Base case: Start origin position holds exactly 1 initial path config
        if (i == 0 && j == 0) {
            return 1;
        }

        // Branch paths dynamically from top and left neighbors
        int up = solve(i - 1, j);
        int left = solve(i, j - 1);

        return up + left;
    }

    public int uniquePaths(int m, int n) {
        return solve(m - 1, n - 1);
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

    private int solve(int i, int j, int[][] dp) {
        if (i < 0 || j < 0) {
            return 0;
        }

        if (i == 0 && j == 0) {
            return 1;
        }

        // Return cached state instantly if previously evaluated
        if (dp[i][j] != -1) {
            return dp[i][j];
        }

        int up = solve(i - 1, j, dp);
        int left = solve(i, j - 1, dp);

        return dp[i][j] = up + left;
    }

    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n];
        for (int[] row : dp) {
            Arrays.fill(row, -1);
        }

        return solve(m - 1, n - 1, dp);
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
dp[i][j] = unique paths from the origin to cell (i, j)
```

---

## Base Case

```text
dp[0][0] = 1
```

---

## Transition

For any cell `(i, j)` where `i > 0` or `j > 0`:

```text
dp[i][j] = (i > 0 ? dp[i - 1][j] : 0) + (j > 0 ? dp[i][j - 1] : 0)
```

---

## Java Code

```java
class Solution {

    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n];

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // Base Case setup
                if (i == 0 && j == 0) {
                    dp[0][0] = 1;
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
dp[i][j] = dp[i-1][j] + dp[i][j-1]
```

We only ever read values from the **immediate preceding cell in the current row** (`dp[i][j-1]`) and the **matching column cell of the row right above it** (`dp[i-1][j]`). 

Therefore, storing a full 2D grid matrix is redundant. We can compress the entire environment allocation down to a single 1D rolling array `prev` of size `n` representing the row above, and a `temp` array representing the current row.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    public int uniquePaths(int m, int n) {
        // Array representing the row immediately above our active workspace row
        int[] prev = new int[n];
        Arrays.fill(prev, 1); // Row 0 fields can only ever be entered via 1 unique route from the left

        for (int i = 1; i < m; i++) {
            int[] temp = new int[n];
            temp[0] = 1; // Left boundary column cell always has exactly 1 entry route down from top

            for (int j = 1; j < n; j++) {
                // current = up (prev[j]) + left (temp[j-1])
                temp[j] = prev[j] + temp[j - 1];
            }
            prev = temp; // Roll our active row frame up to serve as the baseline for next row cycle
        }

        return prev[n - 1];
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
m = 3, n = 3
```

---

### Initialize (i = 0 Row Configuration)

```text
prev = [1, 1, 1]  (Base baseline defaults)
```

---

### i = 1 (Row 1 Calculation Sweep)

```text
temp[0] = 1

j = 1: temp[1] = prev[1] + temp[0] = 1 + 1 = 2
j = 2: temp[2] = prev[2] + temp[1] = 1 + 2 = 3

prev updates to: [1, 2, 3]
```

---

### i = 2 (Row 2 Calculation Sweep)

```text
temp[0] = 1

j = 1: temp[1] = prev[1] + temp[0] = 2 + 1 = 3
j = 2: temp[2] = prev[2] + temp[1] = 3 + 3 = 6

prev updates to: [1, 3, 6]
```

---

### Answer

```text
6
```

---

# Visualization

## Unique Path Routing Combinations Grid

```text
[ 1 ] ─── [ 1 ] ─── [ 1 ]
  │         │         │
  ▼         ▼         │
[ 1 ] ─── [ 2 ] ─── [ 3 ]
  │         │         │
  ▼         ▼         ▼
[ 1 ] ─── [ 3 ] ─── [ 6 ]
```

---

# DP Pattern

## Identify State

```text
f(i, j)
=
Total unique path variations from origin to cell coordinate (i, j)
```

---

## Choices

```text
To arrive at cell (i, j):
1. Travel downward from cell (i - 1, j)
2. Travel rightward from cell (i, j - 1)
```

---

## Recurrence

```text
f(i, j) = f(i - 1, j) + f(i, j - 1)
```

---

## Base Cases

```text
f(0, 0) = 1
f(out of bounds) = 0
```

---

# Key Takeaways

✅ Classic 2D Grid Coordinate matrix optimization architecture

✅ Straightforward Combinatorics problem; can also be evaluated mathematically using combinations formula \(\binom{(m-1) + (n-1)}{m-1}\)

✅ Space optimization squashes memory footprint from quadratic \(O(M \times N)\) down to a single rolling 1D line tracking lookup list O(N)

✅ Common Interview Pattern:

```text
Count Matrix Paths with Fixed Movement Constraints
```

### Similar Problems

- Unique Paths II (LeetCode 63)
- Minimum Path Sum (LeetCode 64)
- Dungeon Game (LeetCode 174)
- Cherry Pickup (LeetCode 741)
- Triangle (LeetCode 120)
