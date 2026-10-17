# Count Negative Numbers in a Sorted Matrix

## Problem Statement

Given an `m x n` matrix `grid` where:

- Each row is sorted in non-increasing order.
- Each column is sorted in non-increasing order.

Return the number of negative numbers in the matrix.

### Example 1

**Input:**

```java
grid = [
 [4,3,2,-1],
 [3,2,1,-1],
 [1,1,-1,-2],
 [-1,-1,-2,-3]
]
```

**Output:**

```java
8
```

### Example 2

**Input:**

```java
grid = [
 [3,2],
 [1,0]
]
```

**Output:**

```java
0
```

---

# Intuition

Since every row is sorted in decreasing order:

```java
[5, 2, 0, -1, -3]
```

Once we encounter the first negative number in a row, all elements to its right will also be negative.

This allows us to count multiple negatives at once instead of checking each element individually.

---

# Approach 1: Brute Force

## Algorithm

1. Traverse every cell.
2. Count cells having value less than zero.

---

## Java Solution

```java
class Solution {
    public int countNegatives(int[][] grid) {

        int count = 0;

        for (int[] row : grid) {
            for (int num : row) {

                if (num < 0) {
                    count++;
                }
            }
        }

        return count;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** `O(m × n)`
- **Space Complexity:** `O(1)`

---

# Approach 2: Binary Search on Every Row

## Intuition

Because every row is sorted:

```java
[5, 2, 0, -1, -3]
```

we can use binary search to find the first negative element.

Then:

```java
negativeCount = n - firstNegativeIndex
```

---

## Algorithm

For each row:

1. Find the first negative number using binary search.
2. Add:

```java
n - index
```

to the answer.

---

## Dry Run

### Row

```java
[4,3,2,-1]
```

Binary Search finds:

```java
index = 3
```

Negatives:

```java
4 - 3 = 1
```

---

## Java Solution

```java
class Solution {

    public int countNegatives(int[][] grid) {

        int m = grid.length;
        int n = grid[0].length;

        int count = 0;

        for (int[] row : grid) {

            int left = 0;
            int right = n - 1;

            while (left <= right) {

                int mid = left + (right - left) / 2;

                if (row[mid] < 0) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            }

            count += (n - left);
        }

        return count;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** `O(m log n)`
- **Space Complexity:** `O(1)`

---

# Approach 3: Optimal Two-Pointer Traversal

## Intuition

Since:

- Rows are sorted.
- Columns are sorted.

Start from the **bottom-left corner**.

### Why Bottom-Left?

At position `(row, col)`:

- If value is negative:
  - Everything to the right is also negative.
  - Count them together.
  - Move upward.
- If value is non-negative:
  - Move right.

This allows us to visit each row and column at most once.

---

## Visual Example

### Input

```java
[
 [4,3,2,-1],
 [3,2,1,-1],
 [1,1,-1,-2],
 [-1,-1,-2,-3]
]
```

Start:

```java
row = 3
col = 0
```

Current:

```java
grid[3][0] = -1
```

All elements from column `0` to `3` are negative.

Count:

```java
4
```

Move up.

---

## Dry Run

| Position | Value | Count Added | Total |
|-----------|--------|------------|--------|
| (3,0) | -1 | 4 | 4 |
| (2,0) | 1 | 0 | 4 |
| (2,1) | 1 | 0 | 4 |
| (2,2) | -1 | 2 | 6 |
| (1,2) | 1 | 0 | 6 |
| (1,3) | -1 | 1 | 7 |
| (0,3) | -1 | 1 | 8 |

Final Answer:

```java
8
```

---

# Java Solution (Optimal)

```java
class Solution {
    public int countNegatives(int[][] grid) {

        int m = grid.length;
        int n = grid[0].length;

        int row = m - 1;
        int col = 0;

        int count = 0;

        while (row >= 0 && col < n) {

            if (grid[row][col] < 0) {

                count += (n - col);
                row--;

            } else {

                col++;
            }
        }

        return count;
    }
}
```

---

# Why Does This Work?

When:

```java
grid[row][col] < 0
```

because the row is sorted in decreasing order:

```java
col ... n-1
```

are all negative.

So we can count:

```java
n - col
```

negatives in one step.

When:

```java
grid[row][col] >= 0
```

everything above may still contain negatives, but this column position cannot help us, so move right.

---

# Complexity Analysis

### Optimal Approach

- **Time Complexity:** `O(m + n)`
- **Space Complexity:** `O(1)`

---

# Comparison of Approaches

| Approach | Time | Space |
|-----------|---------|---------|
| Brute Force | O(m × n) | O(1) |
| Binary Search Per Row | O(m log n) | O(1) |
| Two Pointers (Optimal) | O(m + n) | O(1) |

---

# Key Takeaways

- Matrix is sorted both row-wise and column-wise.
- Brute force checks every element.
- Binary search leverages row sorting.
- The optimal solution starts from the **bottom-left corner**.
- When a negative number is found, count the entire remaining portion of the row.
- Optimal complexity is **O(m + n)** time and **O(1)** space.
- A classic matrix + two-pointer interview problem.
