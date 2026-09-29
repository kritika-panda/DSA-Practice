# 221. Maximal Square

🔗 **Problem Link:**  
https://leetcode.com/problems/maximal-square/

---

# Problem Statement

Given an `m x n` binary matrix filled with:

```text
'0' and '1'
```

find the largest square containing only:

```text
'1'
```

and return its **area**.

---

## Example

### Input

```text
[
  ["1","0","1","0","0"],
  ["1","0","1","1","1"],
  ["1","1","1","1","1"],
  ["1","0","0","1","0"]
]
```

### Largest Square

```text
1 1
1 1
```

Side Length:

```text
2
```

Area:

```text
2 × 2 = 4
```

Output:

```text
4
```

---

# Key Observation

Suppose we are currently at:

```text
matrix[i][j] = '1'
```

Can we extend a square ending at this cell?

To form a larger square, we need:

```text
Top cell
Left cell
Top-left diagonal cell
```

all to already be part of valid squares.

---

# DP State

## Definition

```text
dp[i][j]
```

represents:

```text
Side length of the largest square
whose bottom-right corner is at (i,j)
```

---

## Example

```text
1 1
1 1
```

For bottom-right cell:

```text
dp[i][j] = 2
```

because a square of side length `2` ends there.

---

# Base Cases

For the first row:

```text
i = 0
```

or first column:

```text
j = 0
```

if the cell contains:

```text
'1'
```

then:

```text
dp[i][j] = 1
```

because no larger square can be formed.

---

# Transition

If:

```text
matrix[i][j] == '1'
```

then we can form a square using:

```text
Top      -> dp[i-1][j]

Left     -> dp[i][j-1]

Diagonal -> dp[i-1][j-1]
```

The largest square possible is limited by the smallest among these three.

---

## Formula

```text
dp[i][j] =
min(
    dp[i-1][j],
    dp[i][j-1],
    dp[i-1][j-1]
)
+ 1
```

---

## Why Take Minimum?

Consider:

```text
1 1 1
1 1 1
1 1 0
```

At the bottom-right:

```text
Top = 2
Left = 2
Diagonal = 2
```

A square of side 3 would require all surrounding regions to support it.

The limiting factor is always:

```text
min(top, left, diagonal)
```

So:

```text
new side = min(...) + 1
```

---

# Visual Understanding

Suppose:

```text
Top      = 2
Left     = 3
Diagonal = 2
```

```text
2 2
2 X
```

The largest square ending at `X` is:

```text
min(2,3,2) + 1
=
3
```

---

# Bottom-Up DP

We process every cell and compute:

```text
dp[i][j]
```

while keeping track of the largest side length found so far.

---

# Code

```java
class Solution {

    public int maximalSquare(char[][] matrix) {

        if (matrix == null ||
            matrix.length == 0 ||
            matrix[0].length == 0) {
            return 0;
        }

        int rows = matrix.length;
        int cols = matrix[0].length;

        int[][] dp = new int[rows][cols];

        int maxSide = 0;

        for (int i = 0; i < rows; i++) {

            for (int j = 0; j < cols; j++) {

                if (matrix[i][j] == '1') {

                    if (i == 0 || j == 0) {

                        dp[i][j] = 1;

                    } else {

                        dp[i][j] =
                                Math.min(
                                        Math.min(
                                                dp[i - 1][j],
                                                dp[i][j - 1]
                                        ),
                                        dp[i - 1][j - 1]
                                ) + 1;
                    }

                    maxSide =
                            Math.max(
                                    maxSide,
                                    dp[i][j]
                            );
                }
            }
        }

        return maxSide * maxSide;
    }
}
```

---

# Dry Run

## Input

```text
[
 [1,0,1,0,0],
 [1,0,1,1,1],
 [1,1,1,1,1],
 [1,0,0,1,0]
]
```

---

## DP Table

```text
1 0 1 0 0
1 0 1 1 1
1 1 1 2 2
1 0 0 1 0
```

---

### Explanation

At:

```text
dp[2][3]
```

Top:

```text
1
```

Left:

```text
1
```

Diagonal:

```text
1
```

Therefore:

```text
min(1,1,1) + 1 = 2
```

A square of side length:

```text
2
```

ends here.

---

## Maximum Side

```text
2
```

Area:

```text
2 × 2 = 4
```

Output:

```text
4
```

---

# Why This Works

For every cell:

```text
dp[i][j]
```

stores the largest square ending at that position.

When we reach a new cell:

```text
matrix[i][j] = '1'
```

we only need information from:

```text
Top
Left
Top-left diagonal
```

which have already been computed.

This makes Dynamic Programming a perfect fit.

---

# DP Visualization

For a cell:

```text
↖ ↑
← X
```

where:

```text
↖ = dp[i-1][j-1]
↑  = dp[i-1][j]
←  = dp[i][j-1]
```

Transition:

```text
X = min(↖, ↑, ←) + 1
```

---

# Space Optimization

Notice:

```text
dp[i][j]
```

depends only on:

```text
Current row
Previous row
```

Therefore the DP can be optimized to:

```text
O(n)
```

space using a 1D DP array.

---

# Complexity Analysis

## Time Complexity

We process every matrix cell exactly once.

```text
O(rows × cols)
```

---

## Space Complexity

DP table:

```text
O(rows × cols)
```

---

# Space Optimized Approach

Using:

```text
1D DP
```

Space can be reduced to:

```text
O(cols)
```

while maintaining:

```text
O(rows × cols)
```

time complexity.

---

# Pattern Recognition

This problem belongs to:

```text
Grid DP
```

Common clues:

✅ Matrix input

✅ Largest valid structure

✅ Depends on neighboring cells

✅ Build answer incrementally

✅ Use top, left, diagonal state transition

---

# Similar Problems

| Problem | Pattern |
|----------|----------|
| 221. Maximal Square | Grid DP |
| 1277. Count Square Submatrices With All Ones | Grid DP |
| 64. Minimum Path Sum | Grid DP |
| 62. Unique Paths | Grid DP |
| 931. Minimum Falling Path Sum | Grid DP |

---

# Key Takeaways

| Observation | Benefit |
|------------|----------|
| `dp[i][j]` = largest square ending at `(i,j)` | Natural state definition |
| Square expansion depends on 3 neighbors | Simple transition |
| Use minimum of top, left, diagonal | Ensures valid square formation |
| Track largest side length | Compute final answer |
| Area = side² | Final result |

---

# Complexity Summary

| Approach | Time | Space |
|-----------|--------|--------|
| 2D DP | O(rows × cols) | O(rows × cols) |
| Space Optimized DP | O(rows × cols) | O(cols) |

✅ **Optimal Approach:** Dynamic Programming where `dp[i][j]` stores the side length of the largest square ending at `(i,j)`, using the recurrence:

```text
dp[i][j] =
min(
    dp[i-1][j],
    dp[i][j-1],
    dp[i-1][j-1]
) + 1
```

with final answer:

```text
maxSide × maxSide
```
