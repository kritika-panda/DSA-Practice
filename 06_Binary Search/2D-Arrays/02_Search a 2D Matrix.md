# Search a 2D Matrix

## Problem Statement

Given an `m x n` integer matrix `matrix` with the following two properties:
1. Each row is sorted in non-decreasing order.
2. The first integer of each row is greater than the last integer of the previous row.

Given an integer `target`, return `true` if `target` is in `matrix` or `false` otherwise.

The overall run time complexity should be:

```text
O(log(m * n))
```

---

## Examples

### Example 1

**Input**

```java
matrix = [,
 ,
  [23, 30, 34, 60]
];
target = 3;
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
  [23, 30, 34, 60]
];
target = 13;
```

**Output**

```java
false
```

---

### Example 3

**Input**

```java
matrix = [];
target = 0;
```

**Output**

```java
false
```

---

## Brute Force Approach

Scan through every element in the 2D matrix linearly to check if it matches the target value.

### Steps

1. Iterate through each row from `0` to `m - 1`.
2. Inside each row, iterate through each column from `0` to `n - 1`.
3. If any element matches `target`, return `true`.
4. If the end of the loops is reached without a match, return `false`.

### Complexity

```text
Time Complexity: O(m * n)
Space Complexity: O(1)
```

However, this linear approach ignores the sorted structural rules of the matrix. The problem requires a logarithmic solution.

---

# Optimal Approach: Flattened Binary Search

## Key Idea

Because the first element of any row is greater than the last element of the prior row, the entire 2D matrix can be visualized as a single **flattened 1D sorted array** of size `m * n`.

We can run standard binary search directly on this virtual array:

```text
Virtual Index 1D Array Range:
low = 0
high = (m * n) - 1
```

For any virtual index `mid` in our search, we convert it back to its 2D coordinates `(row, col)` using division and modulo operators with the total number of columns `n`.

---

## Visual Understanding

Suppose:

```java
matrix = [,
 ,
  [23, 30, 34, 60]
];
target = 3;
```

Matrix dimensions: `m = 3`, `n = 4`.  
Total elements = `3 * 4 = 12`.  
Virtual array ranges from index `0` to `11`.

Let's test `mid = 5`:

Convert virtual index `5` to 2D coordinates:

```text
row = mid / n = 5 / 4 = 1
col = mid % n = 5 % 4 = 1
```

Value at `matrix[1][1]` is `11`.

Since `11 > 3`, the target must reside in the lower half of the virtual array. We move our binary search window left.

---

## Partition Variables

Let:

```java
m = matrix.length
n = matrix[0].length
low = 0
high = (m * n) - 1
```

During each binary search step:

```java
int mid = low + (high - low) / 2;
```

---

### Border Elements

The conversion mapping logic to discover actual index nodes looks as follows:

```java
int row = mid / n;
int col = mid % n;
int midValue = matrix[row][col];
```

---

## Correct Partition Condition

```java
midValue == target
```

If true, we have successfully located the target element in the matrix structure.

---

## How to Move Binary Search

### Case 1

```java
midValue < target
```

The current value is smaller than our target value.

Move right to look for larger elements:

```java
low = mid + 1;
```

---

### Case 2

```java
midValue > target
```

The current value is larger than our target value.

Move left to look for smaller elements:

```java
high = mid - 1;
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

        int low = 0;
        int high = (m * n) - 1;

        while (low <= high) {

            int mid = low + (high - low) / 2;
            
            // Map 1D virtual index back to 2D coordinates
            int row = mid / n;
            int col = mid % n;
            int midValue = matrix[row][col];

            if (midValue == target) {
                return true;
            } 
            else if (midValue < target) {
                low = mid + 1;
            } 
            else {
                high = mid - 1;
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
  [23, 30, 34, 60]
];
target = 3;
```

---

### Initial Values

```java
m = 3
n = 4
low = 0
high = 11
```

---

### Iteration 1

```java
mid = 5
row = 5 / 4 = 1
col = 5 % 4 = 1
midValue = matrix[1][1] = 11
```

Since `11 > 3`:

```java
high = 5 - 1 = 4
```

---

### Iteration 2

```java
low = 0
high = 4
mid = 2
row = 2 / 4 = 0
col = 2 % 4 = 2
midValue = matrix[0][2] = 5
```

Since `5 > 3`:

```java
high = 2 - 1 = 1
```

---

### Iteration 3

```java
low = 0
high = 1
mid = 0
row = 0 / 4 = 0
col = 0 % 4 = 0
midValue = matrix[0][0] = 1
```

Since `1 < 3`:

```java
low = 0 + 1 = 1
```

---

### Iteration 4

```java
low = 1
high = 1
mid = 1
row = 1 / 4 = 0
col = 1 % 4 = 1
midValue = matrix[0][1] = 3
```

Since `3 == 3`, target found! Return `true`.

---

## Why Do We Treat it as a 1D Array?

The unique sorted properties guarantee that a sequence continuity extends perfectly from row to row. Converting coordinates with division (`/ n`) and modulus (`% n`) transforms the 2D workspace into a 1D search spectrum seamlessly.

This global boundary logic yields:

```text
O(log(m * n))
```

which satisfies the optimization constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(log(m * n))
```

Standard binary search cuts a total virtual elements space of size `m * n` exactly in half at every decision branch step.

---

### Space Complexity

```text
O(1)
```

No external structural data configurations or replicated elements are saved to storage arrays.

---

## Key Insight

Instead of wasting runtime navigating indices through nested dimensions or multiple localized row checks, we map the structural space into a single continuous sequence utilizing coordinate division transformations.

```text
Time  : O(log(m * n))
Space : O(1)
```

---

## Similar Problems

1. Search a 2D Matrix II
2. Find Row with Maximum 1's
3. Count Negative Numbers in a Sorted Matrix
4. First Bad Version
5. Find Peak Element II
