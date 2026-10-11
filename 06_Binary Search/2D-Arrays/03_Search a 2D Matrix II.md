# Search a 2D Matrix II

## Problem Statement

Write an efficient algorithm that searches for a value `target` in an `m x n` integer matrix `matrix`. This matrix has the following properties:
1. Integers in each row are sorted in ascending from left to right.
2. Integers in each column are sorted in ascending from top to bottom.

Given an integer `target`, return `true` if `target` is in `matrix` or `false` otherwise.

The overall run time complexity should be:

```text
O(m + n)
```

---

## Examples

### Example 1

**Input**

```java
matrix = [,
 ,
 ,
 ,
  [18, 21, 23, 26, 30]
];
target = 5;
```

**Output**

```java
true
```

---

### Example 2

**Input**

```java
matrix = [,
 ,
 ,
 ,
  [18, 21, 23, 26, 30]
];
target = 20;
```

**Output**

```java
false
```

---

### Example 3

**Input**

```java
matrix = [[]];
target = 1;
```

**Output**

```java
false
```

---

## Brute Force Approach

Scan through every element in the matrix linearly row by row and column by column to search for the target value.

### Steps

1. Iterate through each row from `0` to `m - 1`.
2. Inside each row, iterate through each column from `0` to `n - 1`.
3. If any element matches `target`, return `true`.
4. If the traversal completes without finding a match, return `false`.

### Complexity

```text
Time Complexity: O(m * n)
Space Complexity: O(1)
```

This brute-force approach ignores the fact that both rows and columns are sorted. The problem requires a linear-time boundary search strategy.

---

# Optimal Approach: Top-Right Pointer Elimination

## Key Idea

Because the rows are sorted left-to-right and columns are sorted top-to-bottom, we can pick a starting point that acts like a decision tree choice node. Starting from the **top-right corner** `(row = 0, col = n - 1)` provides a clear pathway:

```text
If current cell == target:
Return true.

If current cell > target:
Everything below this cell in the column is also larger than target.
Safely eliminate this column by moving left (col--).

If current cell < target:
Everything to the left of this cell in the row is also smaller than target.
Safely eliminate this row by moving down (row++).
```

By initializing at the top-right (or bottom-left) corner, every step strictly narrows down our searchable grid matrix.

---

## Visual Understanding

Suppose:

```java
matrix = [,
 ,
  [3,   6,  9]
];
target = 5;
```

Matrix dimensions: `m = 3`, `n = 3`.  
Start position at top-right corner `(0, 2)`:

```text
Row 0, Col 2 -> Value is 7. Since 7 > 5, move left (col = 1).
Row 0, Col 1 -> Value is 4. Since 4 < 5, move down (row = 1).
Row 1, Col 1 -> Value is 5. Since 5 == 5, target located!
```

We completely bypassed inspecting the values `1`, `2`, `3`, `6`, `8`, and `9`.

---

## Partition Variables

Let:

```java
m = matrix.length
n = matrix[0].length
row = 0
col = n - 1
```

During each lookup check loop step:

```java
int current = matrix[row][col];
```

---

### Border Elements

The traversal boundaries are secured by guarding our structural grid limits:

```java
while (row < m && col >= 0)
```

---

## Correct Partition Condition

```java
current == target
```

If true, the matching structural node inside the sorted matrix grid space has been discovered.

---

## How to Move Binary Search

### Case 1

```java
current > target
```

The current value is too large, meaning all elements downstream in this current column are also out of range.

Move left to isolate lower values:

```java
col--;
```

---

### Case 2

```java
current < target
```

The current value is too small, implying all previous horizontal row elements can be safely discarded.

Move down to check larger elements:

```java
row++;
```

---

## Java Solution

```java
class Solution {

    public boolean searchMatrix(int[][] matrix, int target) {

        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }

        int m = matrix.length;
        int n = matrix[0].length;

        // Initialize pointer to the top-right corner
        int row = 0;
        int col = n - 1;

        while (row < m && col >= 0) {

            int current = matrix[row][col];

            if (current == target) {
                return true;
            } 
            else if (current > target) {
                col--; // Eliminate current column
            } 
            else {
                row++; // Eliminate current row
            }
        }

        return false;
    }
}
```

---

## Dry Run

### Input

```java
matrix = [,
 ,
  [3,   6,  9]
];
target = 5;
```

---

### Initial Values

```java
m = 3
n = 3
row = 0
col = 2
```

---

### Iteration 1

```java
current = matrix[0][2] = 7
```

Since `7 > 5`:

```java
col = 2 - 1 = 1
```

---

### Iteration 2

```java
current = matrix[0][1] = 4
```

Since `4 < 5`:

```java
row = 0 + 1 = 1
```

---

### Iteration 3

```java
current = matrix[1][1] = 5
```

Since `5 == 5`, match confirmed! Return `true`.

---

## Why Do We Search from the Top-Right Corner?

The top-right corner acts as a perfect sorting axis where one structural axis path moves downward towards progressively larger numbers, while the other moves leftward towards progressively smaller values. This structural property provides a clear directional choice at each step.

This path walk guarantees a maximum traversal scale of:

```text
O(m + n)
```

which satisfies the constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(m + n)
```

At every iteration, we either move one row down or one column left. Thus, the loop can execute at most `m + n` times before reaching the edges of the grid.

---

### Space Complexity

```text
O(1)
```

No supplemental arrays, recursive frames, or hash memory blocks are created.

---

## Key Insight

Instead of applying a binary search setup repeatedly across independent dimensions—which scales to an `O(m log n)` rate—leveraging the shared top-right cell property reduces search paths into a single perimeter walk across rows and columns.

```text
Time  : O(m + n)
Space : O(1)
```

---

## Similar Problems

1. Search a 2D Matrix
2. Find Row with Maximum 1's
3. Count Negative Numbers in a Sorted Matrix
4. Find Peak Element II
5. Kth Smallest Element in a Sorted Matrix
