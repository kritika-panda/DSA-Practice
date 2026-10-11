# Find Peak Element II

## Problem Statement

A peak element in a 2D grid is an element that is strictly greater than all of its adjacent neighbors to the left, right, top, and bottom.

Given a 0-indexed `m x n` matrix `mat` where no two adjacent cells are equal, find any peak element `mat[i][j]` and return its length 2 coordinate array `[i, j]`.

The overall run time complexity should be:

```text
O(n * log(m))
```

---

## Examples

### Example 1

**Input**

```java
mat = [,
 
];
```

**Output**

```java
[0, 1]
```

**Explanation**

Both `4` and `2` are peak elements. For `4`, the coordinates are `[0, 1]`. `4` is greater than its neighbors `1` and `3`.

---

### Example 2

**Input**

```java
mat = [,
 ,
 
];
```

**Output**

```java
[1, 1]
```

**Explanation**

`5` is strictly greater than all its adjacent neighbors (`3`, `2`, `4`, `1`). Its coordinates are `[1, 1]`.

---

### Example 3

**Input**

```java
mat = [[]];
```

**Output**

```java
[0, 0]
```

---

## Brute Force Approach

Scan through every element in the 2D grid matrix linearly, and check if it is strictly greater than all its active horizontal and vertical neighbors.

### Steps

1. Iterate through each row from `0` to `m - 1`.
2. Inside each row, iterate through each column from `0` to `n - 1`.
3. For each cell, check its top, bottom, left, and right neighbors (handling boundaries safely).
4. If a cell satisfies the peak condition, return its coordinates immediately.

### Complexity

```text
Time Complexity: O(m * n)
Space Complexity: O(1)
```

However, this linear approach ignores binary search scaling possibilities. The problem requires a logarithmic solution over the row or column dimension.

---

# Optimal Approach: Binary Search on Columns/Rows

## Key Idea

We can run a Binary Search on the columns (from `col = 0` to `col = n - 1`). For any middle column `midCol`, we find the row index containing the maximum element in that specific column. 

Because it is the maximum element in `midCol`, it is already guaranteed to be strictly greater than its top and bottom neighbors. Thus, we only need to compare it horizontally:

```text
If maxElement > leftNeighbor and maxElement > rightNeighbor:
We found a 2D peak! Return its coordinates.

If leftNeighbor > maxElement:
A peak is guaranteed to exist on the left side. Move search window left.

If rightNeighbor > maxElement:
A peak is guaranteed to exist on the right side. Move search window right.
```

---

## Visual Understanding

Suppose:

```java
mat = [,
 ,
 
];
```

Matrix Dimensions: `m = 3`, `n = 3`.  
Binary Search on columns: `low = 0`, `high = 2`.  
Let's find `midCol = 1`.

Find the maximum element in column 1:
- `mat[0][1] = 4`
- `mat[1][1] = 5`
- `mat[2][1] = 2`

The max element in column 1 is `5` at row 1.  
Now, compare `5` with its left and right neighbors:
- Left neighbor: `mat[1][0] = 3`
- Right neighbor: `mat[1][2] = 2`

Since `5 > 3` and `5 > 2`, `5` is a 2D peak element. We return `[1, 1]`.

---

## Partition Variables

Let:

```java
lowCol = 0
highCol = n - 1
```

During each binary search step:

```java
int midCol = lowCol + (highCol - lowCol) / 2;
```

---

### Border Elements

We track the maximum element row in the current column using a helper variable:

```java
int maxRow = findMaxRowInColumn(mat, midCol, m);
```

---

## Correct Partition Condition

```java
boolean isLeftValid = (midCol == 0 || mat[maxRow][midCol] > mat[maxRow][midCol - 1]);
boolean isRightValid = (midCol == n - 1 || mat[maxRow][midCol] > mat[maxRow][midCol + 1]);

if (isLeftValid && isRightValid) {
    return new int[]{maxRow, midCol};
}
```

If both conditions are true, the peak has been successfully located.

---

## How to Move Binary Search

### Case 1

```java
mat[maxRow][midCol] < mat[maxRow][midCol - 1]
```

The neighbor to the left is larger, meaning a peak exists in the left half of the matrix.

Move left:

```java
highCol = midCol - 1;
```

---

### Case 2

```java
mat[maxRow][midCol] < mat[maxRow][midCol + 1]
```

The neighbor to the right is larger, meaning a peak exists in the right half of the matrix.

Move right:

```java
lowCol = midCol + 1;
```

---

## Java Solution

```java
class Solution {

    public int[] findPeakGrid(int[][] mat) {

        int m = mat.length;
        int n = mat[0].length;

        int lowCol = 0;
        int highCol = n - 1;

        while (lowCol <= highCol) {

            int midCol = lowCol + (highCol - lowCol) / 2;

            // Find the row index with the maximum element in the midCol column
            int maxRow = findMaxRowInColumn(mat, midCol, m);

            boolean isLeftValid = (midCol == 0 
                    || mat[maxRow][midCol] > mat[maxRow][midCol - 1]);
                    
            boolean isRightValid = (midCol == n - 1 
                    || mat[maxRow][midCol] > mat[maxRow][midCol + 1]);

            if (isLeftValid && isRightValid) {
                return new int[]{maxRow, midCol};
            } 
            else if (!isLeftValid) {
                // If left neighbor is greater, look in the left columns
                highCol = midCol - 1;
            } 
            else {
                // If right neighbor is greater, look in the right columns
                lowCol = midCol + 1;
            }
        }

        return new int[]{-1, -1};
    }

    private int findMaxRowInColumn(int[][] mat, int col, int m) {
        int maxRowIndex = 0;
        for (int i = 1; i < m; i++) {
            if (mat[i][col] > mat[maxRowIndex][col]) {
                maxRowIndex = i;
            }
        }
        return maxRowIndex;
    }
}
```

---

## Dry Run

### Input

```java
mat = [,
 ,
 
];
```

---

### Initial Values

```java
m = 3
n = 3
lowCol = 0
highCol = 2
```

---

### Iteration 1

```java
midCol = 1
```

Find the maximum row in column 1:
- Row 0: `4`
- Row 1: `5`
- Row 2: `2`
- Max value row index `maxRow = 1`.

Check horizontal neighbors for `mat[1][1] = 5`:
- Left neighbor: `mat[1][0] = 3` -> `5 > 3` (Left is valid)
- Right neighbor: `mat[1][2] = 2` -> `5 > 2` (Right is valid)

Since both are valid, target peak found at `[1, 1]`.

---

### Answer

```java
[1, 1]
```

---

## Why Do We Check the Column Maximum?

By locking onto the maximum element of a column, we instantly eliminate the need to verify top and bottom vertical paths. This reduction transforms a 2D peak finding problem into a standard 1D binary search problem across the remaining column space dimension.

This dimension isolation logic yields:

```text
O(m * log(n))  [or O(n * log(m)) if searching over rows]
```

which satisfies the optimization constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(m * log(n))
```

The binary search splits the `n` columns in half at each step (`log n` steps). In each step, we scan through `m` elements linearly to find the column maximum.

---

### Space Complexity

```text
O(1)
```

No external structural tables or heavy auxiliary memory blocks are used.

---

## Key Insight

Isolating the global maximum along a single line removes one entire dimension of constraints, allowing us to safely navigate the grid and isolate peak boundaries using a divide-and-conquer strategy.

```text
Time  : O(m * log(n))
Space : O(1)
```

---

## Similar Problems

1. Find Peak Element (1D)
2. Search a 2D Matrix II
3. Find Row with Maximum 1's
4. Kth Smallest Element in a Sorted Matrix
5. Row with Max 1s
