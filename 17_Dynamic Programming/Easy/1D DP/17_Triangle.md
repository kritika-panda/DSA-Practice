# Triangle (LeetCode 120)

## Problem Statement

Given a triangle array, return the minimum path sum from top to bottom.

For each step, you may move to:

- The same index `j` in the next row
- The adjacent index `j + 1` in the next row

### Example

```text
Input:
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]

Output:
11

Explanation:
2 → 3 → 5 → 1 = 11
```

---

# Intuition

At each cell `(i, j)`:

```text
Current Value +
Minimum(
    Down      -> (i+1, j),
    Down Right-> (i+1, j+1)
)
```

Brute force explores all possible paths.

Since many states repeat, use Dynamic Programming.

---

# State Definition

Let:

```java
dp[i][j]
```

represent the minimum path sum starting from position `(i, j)` to the bottom.

---

# Recurrence Relation

```text
dp[i][j] =
triangle[i][j] +
min(dp[i+1][j], dp[i+1][j+1])
```

---

# Approach 1: Recursion

## Idea

From every cell choose:

- Down
- Down Right

Return the minimum cost.

### Code

```java
class Solution {

    public int minimumTotal(List<List<Integer>> triangle) {
        return solve(0, 0, triangle);
    }

    private int solve(int row, int col, List<List<Integer>> triangle) {

        if (row == triangle.size() - 1)
            return triangle.get(row).get(col);

        int down = solve(row + 1, col, triangle);
        int diagonal = solve(row + 1, col + 1, triangle);

        return triangle.get(row).get(col) + Math.min(down, diagonal);
    }
}
```

### Complexity

```text
Time  : O(2^n)
Space : O(n) recursion stack
```

---

# Approach 2: Memoization (Top-Down DP)

## Idea

Store already computed states.

### Code

```java
class Solution {

    public int minimumTotal(List<List<Integer>> triangle) {

        int n = triangle.size();

        Integer[][] dp = new Integer[n][n];

        return solve(0, 0, triangle, dp);
    }

    private int solve(int row, int col,
                      List<List<Integer>> triangle,
                      Integer[][] dp) {

        if (row == triangle.size() - 1)
            return triangle.get(row).get(col);

        if (dp[row][col] != null)
            return dp[row][col];

        int down = solve(row + 1, col, triangle, dp);
        int diagonal = solve(row + 1, col + 1, triangle, dp);

        return dp[row][col] =
                triangle.get(row).get(col)
                + Math.min(down, diagonal);
    }
}
```

### Complexity

```text
Time  : O(n²)
Space : O(n²) + O(n)
```

---

# Approach 3: Tabulation (Bottom-Up DP)

## Idea

Start filling from the last row.

The last row already contains the answers.

Move upward.

### Code

```java
class Solution {

    public int minimumTotal(List<List<Integer>> triangle) {

        int n = triangle.size();

        int[][] dp = new int[n][n];

        for (int j = 0; j < n; j++) {
            dp[n - 1][j] = triangle.get(n - 1).get(j);
        }

        for (int i = n - 2; i >= 0; i--) {

            for (int j = 0; j <= i; j++) {

                int down = dp[i + 1][j];
                int diagonal = dp[i + 1][j + 1];

                dp[i][j] =
                        triangle.get(i).get(j)
                        + Math.min(down, diagonal);
            }
        }

        return dp[0][0];
    }
}
```

### Complexity

```text
Time  : O(n²)
Space : O(n²)
```

---

# Approach 4: Space Optimized DP

## Observation

Each row only depends on:

```text
Next Row
```

So we can keep only one array.

---

### Code

```java
class Solution {

    public int minimumTotal(List<List<Integer>> triangle) {

        int n = triangle.size();

        int[] front = new int[n];

        for (int j = 0; j < n; j++) {
            front[j] = triangle.get(n - 1).get(j);
        }

        for (int i = n - 2; i >= 0; i--) {

            int[] curr = new int[n];

            for (int j = 0; j <= i; j++) {

                curr[j] =
                        triangle.get(i).get(j)
                        + Math.min(front[j], front[j + 1]);
            }

            front = curr;
        }

        return front[0];
    }
}
```

### Complexity

```text
Time  : O(n²)
Space : O(n)
```

---

# Dry Run

```text
Triangle:

      2
     3 4
    6 5 7
   4 1 8 3
```

Initialize:

```text
front = [4,1,8,3]
```

Row = 2

```text
6 + min(4,1) = 7
5 + min(1,8) = 6
7 + min(8,3) = 10

front = [7,6,10]
```

Row = 1

```text
3 + min(7,6) = 9
4 + min(6,10) = 10

front = [9,10]
```

Row = 0

```text
2 + min(9,10) = 11
```

Answer:

```text
11
```

---

# Pattern Recognition

Use Triangle DP when:

- Input is triangular in shape.
- Need minimum/maximum path.
- Can move only to adjacent positions in next row.
- Current state depends on next row values.

Common Problems:

1. Triangle (LC 120)
2. Maximum Path Sum in Triangle
3. Variable Starting/Ending Point Grid DP
4. Ninja Training Variants
5. Falling Path Sum Problems

---

# DP Formula

```text
dp[i][j] =
triangle[i][j]
+
min(
    dp[i+1][j],
    dp[i+1][j+1]
)
```

### Space Optimized Transition

```java
curr[j] = triangle.get(i).get(j)
          + Math.min(front[j], front[j + 1]);
```

**Final Answer = `dp[0][0]` or `front[0]`**
