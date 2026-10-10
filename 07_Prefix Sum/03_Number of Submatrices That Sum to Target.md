# Number of Submatrices That Sum to Target

## Problem Statement

Given a 2D `matrix` and a `target`, return the number of non-empty submatrices that sum to `target`.

A submatrix `matrix[r1..r2][c1..c2]` is the set of all elements `matrix[x][y]` such that `r1 <= x <= r2` and `c1 <= y <= c2`.

Two submatrices `(r1, c1, r2, c2)` and `(r1', c1', r2', c2')` are different if they have some coordinate that is different: for example, if `r1 != r1'`.

---

## Examples

### Example 1

```text
Input: matrix = [[0,1,0],[1,1,1],[0,1,0]], target = 0
Output: 4
```

### Explanation

```text
The four 1x1 submatrices containing only 0s sum up to target 0:
1. matrix[0][0]
2. matrix[0][2]
3. matrix[2][0]
4. matrix[2][2]
```

---

### Example 2

```text
Input: matrix = [[1,-1],[-1,1]], target = 0
Output: 5
```

### Explanation

```text
The 5 submatrices that sum to 0 are:
- Four 1x1 submatrices:, [-1], [-1], [1]
- The entire 2x2 matrix: [[1,-1],[-1,1]]
```

---

### Example 3

```text
Input: matrix = [[904]], target = 0
Output: 0
```

---

# Key Concept: Dimensionality Reduction (2D to 1D)

Finding a submatrix sum in 2D can be optimized by compressing the problem into a 1D array problem. 

If we fix a pair of columns `baseCol` and `j`, we can treat the sum of elements within each row between these two columns as a single element in a virtual 1D array. Once compressed, the problem reduces to the classic **Subarray Sum Equals K** challenge, which can be solved efficiently in linear time using a **Prefix Sum HashMap**.

```text
  Column Boundary: [baseCol ... j]
  Row 0:   [ x x x x ]  ---> Sum = S0
  Row 1:   [ x x x x ]  ---> Sum = S1   ===> Apply 1D Subarray Sum Equals Target on [S0, S1, S2...]
  Row 2:   [ x x x x ]  ---> Sum = S2
```

---

# Intuition

1. **Phase 1 (Row-wise Prefix Sums):** Transform each row of the matrix into its own prefix sum array. This allows us to find the sum of any row segment from `baseCol` to `j` in O(1) time using subtraction: `matrix[i][j] - matrix[i][baseCol - 1]`.
2. **Phase 2 (Column Combinations Loop):** Use a nested double loop to fix all possible pairs of columns (`baseCol` and `j`).
3. **Phase 3 (1D Subarray Mapping):** For a fixed column pair, iterate through all rows from top to bottom. Maintain a running vertical sum and track the frequency of these cumulative sums in a HashMap. At each row, check if `sum - target` exists in the map to identify valid matching submatrices.

---

# Java Implementation

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int numSubmatrixSumTarget(int[][] matrix, int target) {
        int m = matrix.length, n = matrix[0].length;
        int count = 0;

        // Step 1: Compute the prefix sum for each row independently
        for (int[] row : matrix) {
            for (int i = 1; i < n; i++) {
                row[i] += row[i - 1];
            }
        }

        // Step 2: Fix the column boundaries (baseCol to j)
        for (int baseCol = 0; baseCol < n; baseCol++) {
            for (int j = baseCol; j < n; j++) {
                // HashMap to track frequencies of prefix sums for the virtual 1D array
                Map<Integer, Integer> prefixCount = new HashMap<>();
                prefixCount.put(0, 1); // Base case: an empty segment has a sum of 0
                int sum = 0;

                // Step 3: Run a 1D Subarray Sum check vertically across all rows
                for (int i = 0; i < m; i++) {
                    // Extract the compressed row sum between column boundaries in O(1) time
                    sum += matrix[i][j] - (baseCol > 0 ? matrix[i][baseCol - 1] : 0);
                    
                    // If (sum - target) exists, it marks the top boundary of a valid submatrix
                    count += prefixCount.getOrDefault(sum - target, 0);
                    
                    // Record the current prefix sum in the frequency map
                    prefixCount.put(sum, prefixCount.getOrDefault(sum, 0) + 1);
                }
            }
        }

        return count;
    }
}
```

### Complexity Analysis

* **Time Complexity:** \(O(N \times M)\) where M is the number of rows and N is the number of columns in the matrix. The outer two column loops run O(N²) times, and the inner row traversal runs O(M) times per column pair. Map operations take O(1) time on average.
* **Space Complexity:** O(M) auxiliary space. The `prefixCount` HashMap is re-initialized for each column pair and stores at most M unique running sum records matching the vertical row limits.
