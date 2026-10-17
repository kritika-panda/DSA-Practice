# Rotate Image / Matrix by 90 Degrees ([LeetCode 48](https://leetcode.com))

## Problem Statement

You are given an `n x n` 2D `matrix` representing an image, rotate the image by **90 degrees (clockwise)**.

You have to rotate the image **in-place**, which means you have to modify the input 2D matrix directly. **DO NOT** allocate another 2D matrix and do the rotation.

---

## Examples

### Example 1

```text
Input: matrix = [[1,2,3],
,
 ]
Output: [[7,4,1],
,
 ,
 ]
```

### Explanation

```text
Rotating the 3x3 matrix 90 degrees clockwise transforms the rows into columns:
- Top row [1, 2, 3] becomes the right column [1, 2, 3]
- Bottom row [7, 8, 9] becomes the left column [7, 8, 9] (read bottom-up)
```

---

### Example 2

```text
Input: matrix = [[ 5, 1, 9,11],
                 [ 2, 4, 8,10],
,
 ]
Output: [[15,13, 2, 5],
,
 ,
 ]
```

---

# Intuition

A naive approach would allocate a secondary `n x n` matrix and map elements using the coordinate transform rule `rotated[j][n - 1 - i] = matrix[i][j]`. However, this violates the constraint to execute the rotation **in-place** with \(O(1)\) auxiliary space.

To rotate the matrix completely in-place, we can break the 90-degree clockwise rotation down into two sequential geometric properties:
1. **Transpose the Matrix:** Reflect the matrix across its main diagonal (top-left to bottom-right). This swaps rows with columns, transforming `matrix[i][j]` into `matrix[j][i]`.
2. **Reverse Each Row:** Flip each individual row horizontally from left to right. This mirrors the elements across the central vertical axis, changing `matrix[i][j]` to `matrix[i][n - 1 - j]`.

Combining a **Diagonal Transposition** followed by a **Horizontal Reflection** produces a mathematically flawless 90-degree clockwise rotation inside a single clean unified transformation phase.

---

# State Definition

The matrix manipulation pipeline tracks matrix cell swaps entirely in-place across two distinct loop limits:

```java
int n = matrix.length; // Dimension of the square matrix
```

---

# Recurrence / Structural State Condition

```text
1. Transpose Phase:
For i from 0 to n - 1:
    For j from i + 1 to n - 1:
        Swap(matrix[i][j], matrix[j][i])

2. Reverse Phase:
For i from 0 to n - 1:
    ReverseRow(matrix[i])
```

---

# Optimal In-Place Transpose-and-Reverse Solution

## Idea

We iterate across the upper triangular half of the matrix to swap coordinates symmetrically across the diagonal, completing the transposition. We then run a simple two-pointer reversal swap pass on each row vector to finalize the clockwise rotation using \(O(1)\) auxiliary storage space.

---

## Java Code

```java
class Solution {
    public void rotate(int[][] matrix) {
        // Guard Condition: A 1x1 matrix or empty matrix is implicitly rotated
        if (matrix == null || matrix.length <= 1) {
            return;
        }

        int n = matrix.length;

        // Phase 1: Transpose the matrix (reflect elements across the main diagonal)
        // We start the inner loop at 'i + 1' to only process the upper triangular matrix half,
        // avoiding double-swapping elements back to their original positions.
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }

        // Phase 2: Reverse each row individually (horizontal reflection mirror step)
        for (int i = 0; i < n; i++) {
            int left = 0;
            int right = n - 1;
            // Two-pointer linear row swapper
            while (left < right) {
                int temp = matrix[i][left];
                matrix[i][left] = matrix[i][right];
                matrix[i][right] = temp;
                left++;
                right--;
            }
        }
    }
}
```

---

## Complexity Analysis

Let N × N be the dimensional scale grid size of the input square `matrix`.

### Time Complexity

```text
Time Complexity: O(N²)
```
*Explanation:* The transposition phase processes roughly half the matrix cells, executing exactly \(\frac{N \times (N - 1)}{2}\) constant-time operations. The row reversal phase visits every cell once across \(N\) rows, taking \(\frac{N \times N}{2}\) operations. Total execution operations scale quadratically relative to the row length \(N\), matching linear time relative to the total node population.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. By mutatively exchanging element block addresses directly on the heap using local temporary variables, the algorithm completely drops extra proxy arrays or matrix grids, maintaining a flat constant auxiliary space footprint.

---

# Key Takeaways

✅ Core baseline design template for matrix transformations, 2D geometric transpositions, and spatial image filters.

✅ **The Symmetrical Loop Invariant:** When transposing a matrix, your inner loop **must start from `j = i + 1`** rather than `0`. If you allow `j` to loop from `0` to `n - 1` across every row, your code will visit every cell coordinate twice. This double-swaps elements (`A -> B` then `B -> A`), returning the matrix back to its un-transposed layout.

✅ Directly drives real-world microarchitectures like **graphics processing unit (GPU) texture map rotators** (rotating pixel frames by 90/180/270 degrees clockwise or counter-clockwise), spatial convolution operations, computer vision pre-processing layers, and video stream orientation encoders.

### Similar Problems

- Spiral Matrix ([LeetCode 54](https://leetcode.com))
- Rotate Image Counter-Clockwise (Transpose followed by a column reverse)
- Diagonal Traverse ([LeetCode 498](https://leetcode.com))
- Set Matrix Zeroes ([LeetCode 73](https://leetcode.com))
