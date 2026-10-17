# Set Matrix Zeroes

## Problem Statement

Given an `m x n` integer matrix `matrix`, if an element is `0`, set its entire row and column to `0`.

You must do it **in-place**.

### Example 1

**Input:**

```java
matrix =
[
  [1,1,1],
  [1,0,1],
  [1,1,1]
]
```

**Output:**

```java
[
  [1,0,1],
  [0,0,0],
  [1,0,1]
]
```

### Example 2

**Input:**

```java
matrix =
[
  [0,1,2,0],
  [3,4,5,2],
  [1,3,1,5]
]
```

**Output:**

```java
[
  [0,0,0,0],
  [0,4,5,0],
  [0,3,1,0]
]
```

---

# Intuition

Whenever we encounter a `0`:

- Entire row should become `0`.
- Entire column should become `0`.

A brute-force approach would be to remember all rows and columns containing `0` and later update the matrix.

To optimize space further, we can use the **first row and first column as markers**.

---

# Approach 1: Better Solution Using Extra Arrays

## Algorithm

1. Create two arrays:
   - `rows[m]`
   - `cols[n]`
2. Traverse the matrix.
3. If a cell contains `0`:
   - Mark its row in `rows`.
   - Mark its column in `cols`.
4. Traverse the matrix again.
5. If either row or column is marked:
   - Set the cell to `0`.

---

## Dry Run

### Input

```java
[
 [1,1,1],
 [1,0,1],
 [1,1,1]
]
```

### Row Markers

```java
rows = [0,1,0]
```

### Column Markers

```java
cols = [0,1,0]
```

### Final Matrix

```java
[
 [1,0,1],
 [0,0,0],
 [1,0,1]
]
```

---

## Java Solution

```java
class Solution {
    public void setZeroes(int[][] matrix) {

        int m = matrix.length;
        int n = matrix[0].length;

        int[] rows = new int[m];
        int[] cols = new int[n];

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {

                if (matrix[i][j] == 0) {
                    rows[i] = 1;
                    cols[j] = 1;
                }
            }
        }

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {

                if (rows[i] == 1 || cols[j] == 1) {
                    matrix[i][j] = 0;
                }
            }
        }
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** `O(m × n)`
- **Space Complexity:** `O(m + n)`

---

# Approach 2: Optimal Solution (Constant Space)

## Key Idea

Use:

- First row as column markers
- First column as row markers

Since `matrix[0][0]` cannot represent both independently, use a separate variable:

```java
col0
```

to track whether the first column should become zero.

---

## Algorithm

### Step 1: Mark Rows and Columns

Whenever:

```java
matrix[i][j] == 0
```

mark:

```java
matrix[i][0] = 0
matrix[0][j] = 0
```

---

### Step 2: Fill Inner Matrix

For every cell excluding first row and first column:

```java
if(matrix[i][0] == 0 || matrix[0][j] == 0)
    matrix[i][j] = 0;
```

---

### Step 3: Handle First Row

If:

```java
matrix[0][0] == 0
```

make entire first row zero.

---

### Step 4: Handle First Column

If:

```java
col0 == 0
```

make entire first column zero.

---

## Visual Example

### Initial Matrix

```java
[
 [1,1,1],
 [1,0,1],
 [1,1,1]
]
```

After marking:

```java
[
 [1,0,1],
 [0,0,1],
 [1,1,1]
]
```

First row and column now store marker information.

After processing:

```java
[
 [1,0,1],
 [0,0,0],
 [1,0,1]
]
```

---

## Java Solution (Optimal)

```java
class Solution {
    public void setZeroes(int[][] matrix) {

        int m = matrix.length;
        int n = matrix[0].length;

        int col0 = 1;

        for (int i = 0; i < m; i++) {

            if (matrix[i][0] == 0) {
                col0 = 0;
            }

            for (int j = 1; j < n; j++) {

                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }

        for (int i = m - 1; i >= 0; i--) {

            for (int j = n - 1; j >= 1; j--) {

                if (matrix[i][0] == 0 ||
                    matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }

            if (col0 == 0) {
                matrix[i][0] = 0;
            }
        }
    }
}
```

---

## Why Traverse Backwards?

Consider:

```java
for (int i = 0; i < m; i++)
```

If we start modifying cells immediately, we may overwrite marker information before using it.

Traversing from:

```java
m - 1 → 0
n - 1 → 1
```

preserves all markers until they are fully utilized.

---

## Complexity Analysis

### Optimal Approach

- **Time Complexity:** `O(m × n)`
- **Space Complexity:** `O(1)`

---

# Comparison of Approaches

| Approach | Time | Space |
|-----------|---------|---------|
| Extra Row & Column Arrays | O(m × n) | O(m + n) |
| First Row & First Column Markers | O(m × n) | O(1) |

---

# Interview Follow-up

### Why not set rows and columns to zero immediately when we find a zero?

Because newly inserted zeros would affect future decisions and create incorrect results.

Example:

```java
[
 [1,0],
 [1,1]
]
```

If we instantly modify the matrix, newly created zeros may incorrectly trigger additional row/column updates.

Hence, we must first **record** which rows and columns should become zero and then perform the updates.

---

# Key Takeaways

- Use row and column markers to identify affected cells.
- Extra arrays solution is easy to understand.
- Optimal solution uses the first row and first column as storage.
- Use `col0` separately to handle the first column.
- Traverse backwards during the update phase.
- Optimal complexity is **O(m × n)** time and **O(1)** space.
- A classic interview problem frequently asked by Amazon, Microsoft, Google, and Meta.
