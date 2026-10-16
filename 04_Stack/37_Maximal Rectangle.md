# Maximal Rectangle ([LeetCode 85](https://leetcode.com))

## Problem Statement

Given a `rows x cols` binary `matrix` filled with `'0'`s and `'1'`s, find the largest rectangle containing only `'1'`s and return *its area*.

---

## Examples

### Example 1

```java
Input: matrix = [
  ["1","0","1","0","0"],
  ["1","0","1","1","1"],
  ["1","1","1","1","1"],
  ["1","0","0","1","0"]
]
Output: 6
```

### Explanation

```text
The maximal rectangle is formed by the subgrid from row 1 to 2, and col 2 to 4:
  ["1","1","1"],
  ["1","1","1"]
Area = 2 rows * 3 cols = 6.
```

---

### Example 2

```java
Input: matrix = [["0"]]
Output: 0
```

---

### Example 3

```java
Input: matrix = [["1"]]
Output: 1
```

---

# Intuition

This problem can be elegantly reduced to a series of **Largest Rectangle in Histogram** subproblems.

Imagine tracking the matrix row by row:
- For each row, we can treat the contiguous vertical sequence of `'1'`s reaching up from that row as a **histogram column**.
- As we move from row `r` to row `r + 1`, we update our histogram heights:
  - If `matrix[r][c] == '1'`, the height increases by `1` (`heights[c] += 1`).
  - If `matrix[r][c] == '0'`, the vertical line breaks completely, resetting the height back to `0` (`heights[c] = 0`).
- By running the linear **Monotonic Increasing Stack** histogram optimization engine on our tracking array after compiling each row layer, we find the global maximum area grid across the entire 2D matrix structure.

---

# Brute Force Solution

## Idea

A naive solution checks every pair of coordinates `(r1, c1)` and `(r2, c2)` as possible bounding boundaries for a subgrid rectangle, and then scans inside to verify if it contains exclusively `'1'`s. This requires checking O(N² × M²) areas accompanied by an inner validation verification pass, costing an inefficient O(N³ × M³) operations.

---

## Java Code

```java
class Solution {
    public int maximalRectangle(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return 0;
        }

        int rows = matrix.length;
        int cols = matrix[0].length;
        int maxArea = 0;

        // Try every possible top-left coordinate (r1, c1)
        for (int r1 = 0; r1 < rows; r1++) {
            for (int c1 = 0; c1 < cols; c1++) {
                // Try every possible bottom-right coordinate (r2, c2)
                for (int r2 = r1; r2 < rows; r2++) {
                    for (int c2 = c1; c2 < cols; c2++) {
                        
                        if (isValidRectangle(matrix, r1, c1, r2, c2)) {
                            int area = (r2 - r1 + 1) * (c2 - c1 + 1);
                            maxArea = Math.max(maxArea, area);
                        }
                    }
                }
            }
        }
        return maxArea;
    }

    private boolean isValidRectangle(char[][] matrix, int r1, int c1, int r2, int c2) {
        for (int r = r1; r <= r2; r++) {
            for (int c = c1; c <= c2; c++) {
                if (matrix[r][c] == '0') {
                    return false;
                }
            }
        }
        return true;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(R³ × C³) where R is rows and C is columns.
- **Space Complexity:** O(1) auxiliary memory footprint.

---

# State Definition (Optimal Approach)

To optimize the execution to \(O(R \times C)\) linear time, we maintain a rolling 1D integer histogram cache layout:

```java
int[] heights = new int[cols]; // 1D array accumulating contiguous vertical '1' spans
```

---

# Recurrence / Structural State Condition

```text
For each row r from 0 to rows - 1:
    For each col c from 0 to cols - 1:
        heights[c] = (matrix[r][c] == '1') ? heights[c] + 1 : 0
        
    maxArea = max(maxArea, largestRectangleInHistogram(heights))
```

---

# Optimal Monotonic Stack Solution

## Idea

We loop through the matrix row by row to compile vertical column heights. After each row's height transitions finish, we trigger our single-pass monotonic stack histogram algorithm. We handle index flushes cleanly by running the inner stack loop up to `cols` using a virtual terminal sentinel value of `0`.

---

## Java Code

```java
import java.util.Stack;

class Solution {
    public int maximalRectangle(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return 0;
        }

        int rows = matrix.length;
        int cols = matrix[0].length;
        int[] heights = new int[cols];
        int maxArea = 0;

        // Process the matrix row by row layer sequentially
        for (int r = 0; r < rows; r++) {
            // Step 1: Update the continuous histogram heights for the current row
            for (int c = 0; c < cols; c++) {
                if (matrix[r][c] == '1') {
                    heights[c] += 1;
                } else {
                    heights[c] = 0; // The vertical bar line breaks completely
                }
            }

            // Step 2: Calculate the maximal rectangle area inside the current histogram layer
            maxArea = Math.max(maxArea, calculateHistogramMax(heights));
        }

        return maxArea;
    }

    private int calculateHistogramMax(int[] heights) {
        int n = heights.length;
        Stack<Integer> stack = new Stack<>();
        int maxArea = 0;

        // Run loop up to n to process the virtual 0-height sentinel at the end
        for (int i = 0; i <= n; i++) {
            int currentHeight = (i == n) ? 0 : heights[i];

            while (!stack.isEmpty() && heights[stack.peek()] > currentHeight) {
                int height = heights[stack.pop()];
                int leftBound = stack.isEmpty() ? -1 : stack.peek();
                int rightBound = i;

                int width = rightBound - leftBound - 1;
                maxArea = Math.max(maxArea, height * width);
            }
            stack.push(i);
        }

        return maxArea;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(R × C)
```
*Explanation:* For each row R, we update a height array of size C and run a monotonic stack lookup processing C elements. Since each index item within the row layer undergoes at most one push and pop operation, the total time complexity scales strictly linearly relative to the area of the matrix (R × C).

---

### Space Complexity

```text
Space Complexity: O(C)
```
*Explanation:* Auxiliary storage space scales linearly to accommodate the 1D rolling `heights` column cache array along with the index values tracked inside the monotonic stack, which are bounded by the width of the matrix columns (C).

---

# Key Takeaways

✅ Advanced 2D matrix transformation utilizing dynamic problem reduction mechanics.

✅ **The Value of Row Flattening:** Many advanced 2D matrix grid problems can be deconstructed into a series of isolated 1D array operations. Compiling properties dynamically from top to bottom translates spatial grid distributions into predictable sequential ranges.

✅ Directly models execution systems like **maximal layout bounding grid boxes** inside optical text recognition processors, screen graphics masking algorithms, database sub-table cluster compilers, and maps rendering frameworks.

✅ Common Architectural Paradigm:

```text
Layered Row Accumulation for Monotonic Sub-Grid Dimensional Reductions
```

### Similar Problems

- Largest Rectangle in Histogram ([LeetCode 84](https://leetcode.com))
- Maximal Square ([LeetCode 221](https://leetcode.com))
- Count Square Submatrices with All Ones ([LeetCode 1277](https://leetcode.com))
- Unique Paths II ([LeetCode 63](https://leetcode.com))
