# Kth Smallest Element in a Sorted Matrix

## Problem Statement

Given an `n x n` matrix `matrix` where each of the rows and columns is sorted in ascending order, return the `k`-th smallest element in the matrix.

Note that it is the `k`-th smallest element in the sorted order, not the `k`-th distinct element.

The overall run time complexity should be:

```text
O(32 * n * log(n))
```

---

## Examples

### Example 1

**Input**

```java
matrix = [,
 ,
 
];
k = 8;
```

**Output**

```java
13
```

**Explanation**

The elements in the matrix, sorted in ascending order, are:

```java
```

The 8th smallest number is `13`.

---

### Example 2

**Input**

```java
matrix = [[]];
k = 1;
```

**Output**

```java
-5
```

---

### Example 3

**Input**

```java
matrix = [,
 
];
k = 3;
```

**Output**

```java
12
```

---

## Brute Force Approach

Flatten the 2D matrix into a 1D array, sort it, and return the element at index `k - 1`.

### Steps

1. Initialize a 1D array or list of size `n * n`.
2. Copy all elements from the 2D matrix into the list.
3. Sort the list in non-decreasing order.
4. Return the element at index `k - 1`.

### Complexity

```text
Time Complexity: O(n^2 * log(n^2))
Space Complexity: O(n^2)
```

This approach uses an unnecessary amount of auxiliary memory and ignores the fact that both rows and columns are already sorted. The problem requires a more optimized approach.

---

# Optimal Approach: Binary Search on Value Range

## Key Idea

Instead of locating the element by counting positions directly, we can perform a Binary Search on the *value range* of the matrix.

The absolute minimum element sits at the top-left corner `(0, 0)`, and the absolute maximum element sits at the bottom-right corner `(n - 1, n - 1)`:

```text
low = matrix[0][0]
high = matrix[n - 1][n - 1]
```

For a chosen midpoint value `mid`, we count how many elements in the matrix are less than or equal to `mid`. 

Because both rows and columns are sorted, we can count the elements in `O(n)` time using a stair-step approach starting from the bottom-left corner `(row = n - 1, col = 0)`. If the total count of elements less than or equal to `mid` is at least `k`, then the `k`-th smallest element must be less than or equal to `mid`.

---

## Visual Understanding

Suppose:

```java
matrix = [,
 ,
 
];
k = 8;
```

Matrix dimensions: `n = 3`.  
Search range boundaries:

```text
low = matrix[0][0] = 1
high = matrix[2][2] = 15
```

Let's test `mid = 8` (midpoint of 1 and 15):

Count elements less than or equal to 8 using a stair-step walk starting at `(2, 0)`:
- Row 2, Col 0: `12 > 8` -> Move up (row = 1)
- Row 1, Col 0: `10 > 8` -> Move up (row = 0)
- Row 0, Col 0: `1 <= 8` -> Elements `[1]` are `<= 8`. Add `(col + 1) = 1` to count. Move right (col = 1)
- Row 0, Col 1: `5 <= 8` -> Elements `[1, 5]` are `<= 8`. Add `(col + 1) = 2` to count. Move right (col = 2)
- Row 0, Col 2: `9 > 8` -> Move up (row = -1). Out of bounds.

Total count = `1 + 2 = 3` elements.

Since `3 < 8`, the value 8 is too small to be the 8th smallest element. We move our search window right.

---

## Partition Variables

Let:

```java
low = matrix[0][0]
high = matrix[n - 1][n - 1]
```

During each binary search step:

```java
int mid = low + (high - low) / 2;
```

---

### Border Elements

A helper function counts elements `<= mid` using an optimized path traversal:

```java
int row = n - 1;
int col = 0;
int count = 0;
```

---

## Correct Partition Condition

```java
if (count < k) {
    low = mid + 1;
} else {
    high = mid - 1;
}
```

When `low > high`, the variable `low` will hold the exact value of the `k`-th smallest element.

---

## How to Move Binary Search

### Case 1

```text
count < k
```

There are fewer than `k` elements smaller than or equal to `mid`. The target element must be larger.

Move right:

```java
low = mid + 1;
```

---

### Case 2

```text
count >= k
```

There are at least `k` elements smaller than or equal to `mid`. The target element could be `mid` or smaller.

Move left to narrow down the upper range boundary:

```java
high = mid - 1;
```

---

## Java Solution

```java
class Solution {

    public int kthSmallest(int[][] matrix, int k) {

        int n = matrix.length;
        int low = matrix[0][0];
        int high = matrix[n - 1][n - 1];

        while (low <= high) {

            int mid = low + (high - low) / 2;
            
            // Count elements less than or equal to mid
            int count = countLessThanOrEqualTo(matrix, mid, n);

            if (count < k) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }

        return low;
    }

    private int countLessThanOrEqualTo(int[][] matrix, int target, int n) {
        
        int count = 0;
        int row = n - 1;
        int col = 0;

        // Stair-step search from bottom-left to top-right
        while (row >= 0 && col < n) {
            if (matrix[row][col] <= target) {
                count += (row + 1);
                col++;
            } else {
                row--;
            }
        }

        return count;
    }
}
```

---

## Dry Run

### Input

```java
matrix = [,
 ,
 
];
k = 8;
```

---

### Initial Values

```java
low = 1
high = 15
```

---

### Iteration 1

```java
mid = 8
```

Evaluate `countLessThanOrEqualTo(matrix, 8, 3)`:
- `matrix[2][0] = 12 > 8` -> `row = 1`
- `matrix[1][0] = 10 > 8` -> `row = 0`
- `matrix[0][0] = 1 <= 8` -> `count += 1`, `col = 1`
- `matrix[0][1] = 5 <= 8` -> `count += 1`, `col = 2`
- `matrix[0][2] = 9 > 8`  -> `row = -1`
- `count = 3`

Since `3 < 8`:

```java
low = 8 + 1 = 9
```

---

### Iteration 2

```java
low = 9
high = 15
mid = 12
```

Evaluate `countLessThanOrEqualTo(matrix, 12, 3)`:
- `matrix[2][0] = 12 <= 12` -> `count += 3`, `col = 1`
- `matrix[2][1] = 13 > 12`  -> `row = 1`
- `matrix[1][1] = 11 <= 12` -> `count += 2`, `col = 2`
- `matrix[1][2] = 14 > 12`  -> `row = 0`
- `matrix[0][2] = 9 <= 12`  -> `count += 1`, `col = 3`
- `count = 3 + 2 + 1 = 6`

Since `6 < 8`:

```java
low = 12 + 1 = 13
```

---

### Iteration 3

```java
low = 13
high = 15
mid = 14
```

Evaluate `countLessThanOrEqualTo(matrix, 14, 3)`:
- Yields `count = 8`.

Since `8 >= 8`:

```java
high = 14 - 1 = 13
```

---

### Iteration 4

```java
low = 13
high = 13
mid = 13
```

Evaluate `countLessThanOrEqualTo(matrix, 13, 3)`:
- Yields `count = 8`.

Since `8 >= 8`:

```java
high = 13 - 1 = 12
```

Loop terminates because `low > high` (`13 > 12`).

---

### Answer

```java
13
```

---

## Why Do We Use Binary Search on the Value Range?

The values within the matrix map smoothly across a bounded monotonic numerical scale between `matrix[0][0]` and `matrix[n-1][n-1]`. By validating candidate values using a linear stair-step path through the rows and columns, we bypass the need to explicitly sort the structural matrix space.

This range optimization yields:

```text
O(32 * n)  -> roughly bounded as O(n * log(max_val - min_val))
```

which satisfies the optimization constraints.

---

## Complexity Analysis

### Time Complexity

```text
O(n * log(high - low))
```

The binary search runs over the integer range space (`log(max_val - min_val)` steps, which maxes out at approximately 32 iterations). In each iteration, the stair-step function walks at most `2n` elements (`O(n)`) to calculate the matching value counts.

---

### Space Complexity

```text
O(1)
```

No supplemental matrices, heaps, or tracking lists are created in storage memory.

---

## Key Insight

Treating a positional selection problem from an inverse value perspective turns the matrix properties into an efficient step-by-step search boundary filter.

```text
Time  : O(n * log(high - low))
Space : O(1)
```

---

## Similar Problems

1. Matrix Median
2. Find Peak Element II
3. Search a 2D Matrix II
4. Split Array Largest Sum
5. K-th Element of Two Sorted Arrays
