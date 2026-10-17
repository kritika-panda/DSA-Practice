# Max Sum of Rectangle No Larger Than K ([LeetCode 363](https://leetcode.com))

## Problem Statement

Given an `m x n` matrix `matrix` and an integer `k`, return *the max sum of a rectangle in the matrix such that its sum is no larger than `k`*.

It is guaranteed that there will be at least one rectangle with a sum no larger than `k`.

---

## Examples

### Example 1

```text
Input: matrix = [[1,0,1],[0,-2,3]], k = 2
Output: 2
```

### Explanation

```text
The rectangle [[0,-2],[3]] (or the full bottom-right sub-matrix) has a sum of 2, 
which is the maximum possible sum no larger than k (since k = 2).
```

---

### Example 2

```text
Input: matrix = [[2,2,-1]], k = 3
Output: 3
```

---

# Intuition

If this problem simply asked for the maximum sum of a rectangle with no constraints, we could solve it in O(cols² × rows) time using **Kadane's Algorithm** over compressed rows. However, adding the upper-bound constraint k changes the problem: a greedy maximum calculation might overshoot k, while the valid optimal sum could be a smaller or negative sub-matrix sum.

To solve this efficiently, we combine **2D Row Compression** with a **Prefix Sum Binary Search (TreeSet)** tracking strategy:
1. **Row Compression:** We fix a pair of columns `left` and `right`. We then compress all 2D numbers between these column boundaries into a 1D array of size `m` (rows), where each slot `rowSums[r]` stores the sum of elements from column `left` to `right` for that row.
2. **The 1D Bounded Subarray Problem:** The problem now reduces to finding a contiguous subarray within `rowSums` whose sum is maximized but strictly ≤ k.
3. **Prefix Sum Search Optimization:** For a continuous running prefix sum `currentSum`, we want to find a previous prefix sum `prevSum` such that:
   \[\text{currentSum} - \text{prevSum} \le k \implies \text{prevSum} \ge \text{currentSum} - k\]
   To maximize the subarray sum (currentSum - prevSum), we want to find the **smallest possible `prevSum`** that is ≥ currentSum - k.
4. **Logarithmic Lookup Framework:** By keeping our historical prefix sums inside a balanced binary search tree (`TreeSet` in Java), we can find this optimal `prevSum` using a high-speed ceiling lookup (`treeSet.ceiling(currentSum - k)`) in \(O(\log M)\) time.

---

# State Definition

The multi-dimensional array mapping tracks bounding columns and cumulative prefix trees:

```java
int[] compressed = new int[innerLimit]; // Compressed 1D representation of columns left to right
TreeSet<Integer> prefixSums = new TreeSet<>(); // Balanced BST storing historical prefix sums
```

---

# Recurrence / Structural Squeezing Condition

```text
For left from 0 to cols - 1:
    Initialize compressed[] = 0
    For right from left to cols - 1:
        // Update compressed with elements from column 'right'
        For r from 0 to rows - 1 -> compressed[r] += matrix[r][right]
        
        // Find max subarray sum <= k in compressed
        prefixSums = {0}, currentSum = 0
        For sum in compressed:
            currentSum += sum
            target = currentSum - k
            prevSum = prefixSums.ceiling(target)
            if prevSum != null:
                maxSum = max(maxSum, currentSum - prevSum)
            prefixSums.add(currentSum)
```

---

# Optimal Row-Compressed Prefix Tree Solution

## Idea

We iterate across all column combinations to map the 2D matrix down to a rolling 1D layout. For each combination, we run an in-place prefix summation pass paired with a `TreeSet` ceiling query to locate sub-matrices that sit closest to k without overshooting it.

---

## Java Code

```java
import java.util.TreeSet;

class Solution {
    public int maxSumSubmatrix(int[][] matrix, int k) {
        // Guard Condition: Handle degenerate empty input boundaries cleanly
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return 0;
        }

        int rows = matrix.length;
        int cols = matrix[0].length;
        int maxSum = Integer.MIN_VALUE;

        // Optimization: Standardise search space so that we always loop 
        // across the smaller dimension to optimize row compression passes
        boolean transpose = cols > rows;
        int outerLimit = transpose ? rows : cols;
        int innerLimit = transpose ? cols : rows;

        // Step 1: Fix the starting column boundary (or row if transposed)
        for (int left = 0; left < outerLimit; left++) {
            // Compressed array holding sums of elements across fixed boundaries
            int[] compressed = new int[innerLimit];

            // Step 2: Expand the right column boundary out step-by-step
            for (int right = left; right < outerLimit; right++) {
                
                // Compress 2D grid matrix rows into our 1D workspace
                for (int i = 0; i < innerLimit; i++) {
                    compressed[i] += transpose ? matrix[left][i] : matrix[i][right];
                }

                // Step 3: Find the maximum contiguous subarray sum <= k using a TreeSet
                TreeSet<Integer> prefixSums = new TreeSet<>();
                // Base Case: An empty prefix sum of 0 is always achievable at the start
                prefixSums.add(0);
                
                int currentSum = 0;

                for (int sum : compressed) {
                    currentSum += sum;

                    // Compute the minimal prerequisite prefix sum threshold required
                    int target = currentSum - k;
                    
                    // O(log M) ceiling lookup finds the smallest historical prefix sum >= target
                    Integer prevSum = prefixSums.ceiling(target);
                    
                    if (prevSum != null) {
                        int rectSum = currentSum - prevSum;
                        if (rectSum > maxSum) {
                            maxSum = rectSum;
                        }
                    }

                    // Short-Circuit Optimization: If we achieve exactly k, return it immediately
                    if (maxSum == k) {
                        return k;
                    }

                    // Log current prefix sum to the search tree registry
                    prefixSums.add(currentSum);
                }
            }
        }

        return maxSum;
    }
}
```

---

## Complexity Analysis

Let M be the number of rows and N be the number of columns inside the `matrix`.

### Time Complexity

```text
Time Complexity: O(min(M, N)² × max(M, N) × log(max(M, N)))
```
*Explanation:* Assuming N < M due to our transposition optimization check, the outer nested loops process column pairings exactly \(\frac{N \times (N+1)}{2}\) times, executing in O(N²) steps. For each combination, we scan across M rows linearly. Inside that loop, a balanced binary search tree insertion and lookup (`TreeSet.ceiling()`) takes logarithmic \(O(\log M)\) time. This yields an overall runtime bounded cleanly inside polynomial dimensions.

---

### Space Complexity

```text
Space Complexity: O(max(M, N))
```
*Explanation:* Highly optimal space footprint. The grid matrix compressions are written to a single 1D array workspace of size M (or N). The auxiliary storage used by our prefix `TreeSet` logs scales linearly to hold at most M integers concurrently, keeping the footprint highly efficient.

---

# Key Takeaways

✅ Advanced 2D matrix transformation archetype representing the peak of row compression and bounded subarray matching templates.

✅ **The Matrix Transposition Optimization Rule:** When designing row compression routines, always check dimensions upfront and transpose the search tree if columns outnumber rows (`cols > rows`). By ensuring that the outer nested loops always iterate over the smaller dimensional footprint (\(\min(M, N)\)), you keep array initialization and state checks minimal, preventing severe execution bottlenecks.

✅ Directly drives real-world microarchitectures like **geographic spatial density mapping filters** (finding bounded municipal zones containing matching target population densities no larger than an infrastructure resource quota), automated financial ledger sub-matrix auditing tools, computer vision bounding box processors, and real-time radar mapping matrix modules.

### Similar Problems

- Subarray Sum Equals K ([LeetCode 560](https://leetcode.com))
- Maximum Subarray / Kadane's Algorithm ([LeetCode 53](https://leetcode.com))
- Range Sum Query 2D - Immutable ([LeetCode 304](https://leetcode.com))
