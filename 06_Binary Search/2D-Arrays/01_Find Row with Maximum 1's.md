# Find Row with Maximum 1's

## Problem Statement

Given a boolean 2D array of dimensions `n x m` where each row is sorted, find the 0-based index of the first row that has the maximum number of 1's. If no such row exists, return `-1`.

The overall run time complexity should be:

```text
O(n + m)
```

---

## Examples

### Example 1

**Input**

```java
matrix = [,
 ,
 ,
  [0, 0, 0, 0]
]
```

**Output**

```java
2
```

**Explanation**

Row 0 has 3 ones.  
Row 1 has 2 ones.  
Row 2 has 4 ones.  
Row 3 has 0 ones.  

The row with the maximum number of 1's is row 2.

---

### Example 2

**Input**

```java
matrix = [,
  [0, 0, 0]
]
```

**Output**

```java
-1
```

**Explanation**

There are no 1's in the entire matrix. Return -1.

---

### Example 3

**Input**

```java
matrix = [,
 ,
  [0, 1, 1]
]
```

**Output**

```java
1
```

**Explanation**

Both row 1 and row 2 have the maximum number of 1's (2 ones). We return the first one, which is row 1.

---

## Brute Force Approach

Count the number of 1's in each row linearly, tracking the row index with the highest count.

### Steps

1. Loop through each row from `0` to `n - 1`.
2. For each row, count the total number of 1's.
3. If the current row's count is strictly greater than the previous maximum count, update the maximum count and store the current row index.
4. Return the recorded row index.

### Complexity

```text
Time Complexity: O(n * m)
Space Complexity: O(1)
```

Since each row is already sorted, we can avoid scanning every element completely.

---

# Optimal Approach: Top-Right Pointer Elimination

## Key Idea

Instead of checking rows from scratch, we can leverage the sorted nature of the rows by starting from the **top-right corner** of the matrix `(row = 0, col = m - 1)`.

We look to push left as far as possible:

```text
If the current cell is 1:
Move left to see if there are more 1's in this row.
Update our max row index.

If the current cell is 0:
Move down to the next row.
```

By moving left on `1` and down on `0`, we only travel at most `n` steps down and `m` steps left, resulting in a highly optimized path.

---

## Visual Understanding

Suppose:

```java
matrix = [,
 ,
  [0, 0, 0, 0]
]
```

Matrix Dimensions:

```text
n = 3, m = 4
```

Start position at top-right corner `(0, 3)`:

```text
Row 0, Col 3 -> Value is 1. Move left (col = 2). Update max_row = 0.
Row 0, Col 2 -> Value is 1. Move left (col = 1). Update max_row = 0.
Row 0, Col 1 -> Value is 0. Move down (row = 1).
Row 1, Col 1 -> Value is 1. Move left (col = 0). Update max_row = 1.
Row 1, Col 0 -> Value is 0. Move down (row = 2).
Row 2, Col 0 -> Value is 0. Move down (row = 3). Out of bounds!
```

Final answer row index is `1`.

---

## Partition Variables

Let:

```java
row = 0 (Starts at the top row)
col = m - 1 (Starts at the rightmost column)
maxRowIndex = -1 (Tracks the best row found so far)
```

---

### Border Elements

As long as `row < n` and `col >= 0`:

```java
matrix[row][col] == 1 -> Better candidate found, decrements col
matrix[row][col] == 0 -> No better option in this row, increments row
```

---

## Correct Partition Condition

A row change updates the best track record only if a new `1` is successfully discovered to the left of our boundary baseline.

```java
if (matrix[row][col] == 1) {
    maxRowIndex = row;
}
```

---

## How to Move Binary Search

### Case 1

```java
matrix[row][col] == 1
```

The current row has a path of 1's that extends deeper than or matches our known best.

Move left:

```java
col--;
```

---

### Case 2

```java
matrix[row][col] == 0
```

This row does not contain more 1's than our current record column threshold.

Move down to inspect the next sorted row:

```java
row++;
```

---

## Java Solution

```java
class Solution {

    public int rowWithMax1s(int[][] matrix) {

        int n = matrix.length;
        if (n == 0) return -1;
        int m = matrix[0].length;

        int row = 0;
        int col = m - 1;
        int maxRowIndex = -1;

        while (row < n && col >= 0) {

            // If the element is 1, move left to check for more 1's
            if (matrix[row][col] == 1) {
                maxRowIndex = row;
                col--;
            } 
            // If the element is 0, move down to the next row
            else {
                row++;
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
matrix = [,
 ,
  [0, 0, 0]
]
```

---

### Initial Values

```java
n = 3
m = 3

row = 0
col = 2
maxRowIndex = -1
```

---

### Iteration 1

```java
matrix[0][2] = 1
```

Condition `matrix[0][2] == 1` is met.

```java
maxRowIndex = 0
col = 1
```

---

### Iteration 2

```java
matrix[0][1] = 1
```

Condition `matrix[0][1] == 1` is met.

```java
maxRowIndex = 0
col = 0
```

---

### Iteration 3

```java
matrix[0][0] = 0
```

Condition is `0`. Move to next row.

```java
row = 1
```

---

### Iteration 4

```java
matrix[1][0] = 1
```

Condition `matrix[1][0] == 1` is met.

```java
maxRowIndex = 1
col = -1
```

Loop terminates because `col < 0`.

---

### Answer

```java
1
```

---

## Why Do We Search from the Top-Right Corner?

Because the rows are sorted, a row can only beat our current maximum count of 1's if it contains a `1` at or to the left of our current column boundary indicator (`col`). This feature allows us to safely bypass columns and rows without viewing them entirely.

This stair-step scan yields:

```text
O(n + m)
```

which satisfies the constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n + m)
```

In the worst-case scenario, the pointer walks along the perimeter boundary lines, traversing at most `n` rows downward and `m` columns leftward.

---

### Space Complexity

```text
O(1)
```

No additional structural objects or heap space tracking memory profiles are used.

---

## Key Insight

Instead of running a binary search independently on all `n` rows—which would cost `O(n log m)`—we use the combined row-column sorted property to filter rows step-by-step with a moving boundary limit.

```text
Time  : O(n + m)
Space : O(1)
```

---

## Similar Problems

1. Search a 2D Matrix II
2. Count Negative Numbers in a Sorted Matrix
3. Leftmost Column with at Least a One
4. Kth Smallest Element in a Sorted Matrix
5. Find Peak Element II
