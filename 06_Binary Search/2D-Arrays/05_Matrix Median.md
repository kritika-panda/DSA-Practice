# Matrix Median

## Problem Statement

Given a row-wise sorted matrix `matrix` of dimensions `m x n` where `m * n` is always an odd number, find and return the median of the matrix.

The overall run time complexity should be:

```text
O(32 * m * log(n))
```

---

## Examples

### Example 1

**Input**

```java
matrix = [,
 ,
    [3, 6, 9]
];
```

**Output**

```java
5
```

**Explanation**

If we flatten the matrix into a sorted 1D array, it becomes:

```java
[1, 2, 3, 3, 5, 6, 6, 9, 9]
```

The median element (middle element) is `5`.

---

### Example 2

**Input**

```java
matrix = [,
 ,
    [3]
];
```

**Output**

```java
3
```

**Explanation**

Flattened sorted array:

```java
[1, 3, 3]
```

The median is `3`.

---

### Example 3

**Input**

```java
matrix = [
    [2, 5, 8]
];
```

**Output**

```java
5
```

---

## Brute Force Approach

Flatten the 2D matrix into a 1D array, sort it, and return the middle element.

### Steps

1. Create a 1D array or list of size `m * n`.
2. Traverse through all elements of the matrix and add them to the list.
3. Sort the list in ascending order.
4. Return the element at index `(m * n) / 2`.

### Complexity

```text
Time Complexity: O(m * n * log(m * n))
Space Complexity: O(m * n)
```

This approach uses significant extra memory and ignores the fact that each row is already sorted. The problem requires a more optimized solution.

---

# Optimal Approach: Binary Search on Value Range

## Key Idea

Instead of locating the median by sorting positions, we binary search for the *value* of the median itself. 

The range of possible values for the median is bounded by the smallest and largest elements in the matrix:

```text
low = minimum element in the matrix (found in the first column)
high = maximum element in the matrix (found in the last column)
```

For a middle value `mid`, we count how many elements in the matrix are less than or equal to `mid`. Since the total number of elements `total = m * n` is odd, an element is the median if there are at least `(total + 1) / 2` elements smaller than or equal to it.

We can count elements efficiently using binary search (`upper_bound` strategy) on each row because every row is individually sorted.

---

## Visual Understanding

Suppose:

```java
matrix = [,
 ,
    [3, 6, 9]
];
```

Total elements = `3 * 3 = 9`.  
We need at least `(9 + 1) / 2 = 5` elements less than or equal to our choice value.

Search range boundaries:

```text
low = min(first column) = 1
high = max(last column) = 9
```

Let's test `mid = 5`:
- Row 0: `[1, 3, 5]` -> 3 elements `<= 5`
- Row 1: `[2, 6, 9]` -> 1 element `<= 5`
- Row 2: `[3, 6, 9]` -> 1 element `<= 5`

Total count = `3 + 1 + 1 = 5` elements.

Since the count `5 >= 5`, `5` is a potential median candidate. We narrow our search window down to see if a smaller valid number exists.

---

## Partition Variables

Let:

```java
low = absolute minimum value in the matrix
high = absolute maximum value in the matrix
requiredCount = (m * n + 1) / 2
```

During each binary search step:

```java
int mid = low + (high - low) / 2;
```

---

### Border Elements

A helper function counts elements `<= mid` in a single row using a binary search setup:

```java
int count = countLessThanOrEqualTo(matrix[i], mid);
```

---

## Correct Partition Condition

Accumulate the counts from all rows:

```java
if (totalCount < requiredCount) {
    low = mid + 1;
} else {
    high = mid - 1;
}
```

When `low > high`, the variable `low` will hold the exact median value.

---

## How to Move Binary Search

### Case 1

```text
totalCount < requiredCount
```

There are too few elements smaller than or equal to `mid`. The target median value must be larger.

Move right:

```java
low = mid + 1;
```

---

### Case 2

```text
totalCount >= requiredCount
```

There are enough elements smaller than or equal to `mid`. This value could be the median or larger than the median.

Move left to look for a smaller tight boundary:

```java
high = mid - 1;
```

---

## Java Solution

```java
class Solution {

    public int findMatrixMedian(int[][] matrix) {

        int m = matrix.length;
        int n = matrix[0].length;

        // Find initial low and high boundaries
        int low = Integer.MAX_VALUE;
        int high = Integer.MIN_VALUE;

        for (int i = 0; i < m; i++) {
            low = Math.min(low, matrix[i][0]);
            high = Math.max(high, matrix[i][n - 1]);
        }

        int requiredCount = (m * n + 1) / 2;

        while (low <= high) {

            int mid = low + (high - low) / 2;
            int totalCount = 0;

            // Count elements less than or equal to mid across all rows
            for (int i = 0; i < m; i++) {
                totalCount += countLessThanOrEqualTo(matrix[i], mid);
            }

            if (totalCount < requiredCount) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }

        return low;
    }

    private int countLessThanOrEqualTo(int[] row, int target) {
        int l = 0;
        int h = row.length - 1;
        
        while (l <= h) {
            int mid = l + (h - l) / 2;
            if (row[mid] <= target) {
                l = mid + 1;
            } else {
                h = mid - 1;
            }
        }
        return l;
    }
}
```

---

## Dry Run

### Input

```java
matrix = [,
 ,
    [3, 6, 9]
];
```

Total elements = `9`. `requiredCount = 5`.

---

### Initial Values

```java
low = 1
high = 9
```

---

### Iteration 1

```java
mid = 5
```

Evaluate total count `<= 5`:
- Row 0: `[1, 3, 5]` -> count = 3
- Row 1: `[2, 6, 9]` -> count = 1
- Row 2: `[3, 6, 9]` -> count = 1
- `totalCount = 5`

Since `5 >= 5`:

```java
high = 5 - 1 = 4
```

---

### Iteration 2

```java
low = 1
high = 4
mid = 2
```

Evaluate total count `<= 2`:
- Row 0: `[1, 3, 5]` -> count = 1
- Row 1: `[2, 6, 9]` -> count = 1
- Row 2: `[3, 6, 9]` -> count = 0
- `totalCount = 2`

Since `2 < 5`:

```java
low = 2 + 1 = 3
```

---

### Iteration 3

```java
low = 3
high = 4
mid = 3
```

Evaluate total count `<= 3`:
- Row 0: `[1, 3, 5]` -> count = 2
- Row 1: `[2, 6, 9]` -> count = 1
- Row 2: `[3, 6, 9]` -> count = 1
- `totalCount = 4`

Since `4 < 5`:

```java
low = 3 + 1 = 4
```

---

### Iteration 4

```java
low = 4
high = 4
mid = 4
```

Evaluate total count `<= 4`:
- Row 0: `[1, 3, 5]` -> count = 2
- Row 1: `[2, 6, 9]` -> count = 1
- Row 2: `[3, 6, 9]` -> count = 1
- `totalCount = 4`

Since `4 < 5`:

```java
low = 4 + 1 = 5
```

Loop terminates because `low > high` (`5 > 4`).

---

### Answer

```java
5
```

---

## Why Do We Use Binary Search on the Value Range?

The values in the matrix are bounded by a fixed integer range (typically 32-bit values). Checking whether a value is a valid candidate splits our active numeric range in half at each step. By leveraging the sorted rows, we can calculate counts globally in logarithmic time per step.

This value filtering approach yields:

```text
O(32 * m * log(n))
```

which satisfies the constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(32 * m * log(n))
```

The outer binary search runs for a maximum of 32 iterations (since integer values fit within 32 bits). In each iteration, we run a nested row check across `m` rows, where each row calculation takes `O(log n)` time.

---

### Space Complexity

```text
O(1)
```

No temporary flat arrays or supplemental storage maps are allocated.

---

## Key Insight

When an explicit coordinate framework cannot be targeted easily because rows are isolated, treating the problem from a value threshold perspective unlocks an efficient search space using mathematical boundaries.

```text
Time  : O(32 * m * log(n))
Space : O(1)
```

---

## Similar Problems

1. Kth Smallest Element in a Sorted Matrix
2. Find Peak Element II
3. Search a 2D Matrix II
4. Split Array Largest Sum
5. K-th Element of Two Sorted Arrays
