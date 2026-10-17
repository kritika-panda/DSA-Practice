# Spiral Matrix ([LeetCode 54](https://leetcode.com))

## Problem Statement

Given an `m x n` `matrix`, return *all elements of the `matrix` in **spiral order***.

---

## Examples

### Example 1

```text
Input: matrix = [[1,2,3],
,
 ]
Output: [1,2,3,6,9,8,7,4,5]
```

### Explanation

```text
The spiral traversal paths inward:
1. Row 0 left-to-right: 1 -> 2 -> 3
2. Col 2 top-to-bottom: 6 -> 9
3. Row 2 right-to-left: 8 -> 7
4. Col 0 bottom-to-top: 4
5. Row 1 left-to-right: 5
```

---

### Example 2

```text
Input: matrix = [[1,2,3,4],
,
 ]
Output: [1,2,3,4,8,12,11,10,9,5,6,7]
```

---

# Intuition

Traversing a matrix in spiral order can be modeled as sequentially peeling away the layers of a **2D Box Boundary Window**. 

We establish four mutable coordinate landmarks to pin the active tracking boundaries:
- `top`: Initialized to row `0`.
- `bottom`: Initialized to the last row `m - 1`.
- `left`: Initialized to col `0`.
- `right`: Initialized to the last col `n - 1`.

A single complete spiral cycle is broken down into exactly four directional linear sweeps:
1. **Left to Right:** Traverse across row `top` from column `left` to `right`. Once done, increment `top++` to contract the upper ceiling.
2. **Top to Bottom:** Traverse down column `right` from row `top` to `bottom`. Once done, decrement `right--` to contract the right-hand wall.
3. **Right to Left:** Traverse across row `bottom` from column `right` to `left`. Once done, decrement `bottom--` to contract the floor.
4. **Bottom to Top:** Traverse up column `left` from row `bottom` to `top`. Once done, increment `left++` to contract the left-hand wall.

**The Over-Traversal Guard Flag:** Because the bounds contract dynamically after each directional sweep, we must insert explicit safety checks before running the *Right to Left* and *Bottom to Top* passes. This prevents the algorithm from re-traversing rows or columns that were already consumed inside an asymmetrical matrix layer.

---

# State Definition

The matrix perimeter slicing pipeline tracks active layer boundaries using four boundary registers:

```java
int top = 0;
int bottom = matrix.length - 1;
int left = 0;
int right = matrix[0].length - 1;
```

---

# Recurrence / Structural State Condition

```text
While left <= right AND top <= bottom:
    1. For i from left to right -> result.add(matrix[top][i]); top++
    2. For i from top to bottom -> result.add(matrix[i][right]); right--
    
    3. If top <= bottom:
           For i from right down to left -> result.add(matrix[bottom][i]); bottom--
    4. If left <= right:
           For i from bottom down to top -> result.add(matrix[i][left]); left++
```

---

# Optimal Single-Pass Layer Reduction Solution

## Idea

We initialize an iterative `while` loop that runs until our opposing boundary markers cross over each other. By incorporating inline safety checks directly before the final two inner loops, we prevent duplicate item allocations, mapping out the spiral path cleanly in-place.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<Integer> spiralOrder(char[][] matrix) { // Using standard placeholder type signature
        List<Integer> result = new ArrayList<>();
        
        // Guard Condition: Handle empty or degenerate matrix frameworks cleanly
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return result;
        }

        // Define tracking boundary landmarks
        int top = 0;
        int bottom = matrix.length - 1;
        int left = 0;
        int right = matrix[0].length - 1;

        // Core Step: Boundary window contraction loop
        while (left <= right && top <= bottom) {
            
            // Sweep 1: Left to Right across the active top ceiling row
            for (int j = left; j <= right; j++) {
                result.add((int) matrix[top][j]);
            }
            top++; // Contract the upper ceiling layer downward

            // Sweep 2: Top to Bottom down the active rightmost wall column
            for (int i = top; i <= bottom; i++) {
                result.add((int) matrix[i][right]);
            }
            right--; // Contract the right wall layer leftward

            // Sweep 3: Right to Left across the active bottom floor row
            // Guard Check protects against row double-processing inside single-row layers
            if (top <= bottom) {
                for (int j = right; j >= left; j--) {
                    result.add((int) matrix[bottom][j]);
                }
                bottom--; // Contract the floor layer upward
            }

            // Sweep 4: Bottom to Top up the active leftmost wall column
            // Guard Check protects against column double-processing inside single-column layers
            if (left <= right) {
                for (int i = bottom; i >= top; i--) {
                    result.add((int) matrix[i][left]);
                }
                left++; // Contract the left wall layer rightward
            }
        }

        return result; // Return the fully sequenced spiral collection profile
    }
}
```

---

## Complexity Analysis

Let M × N be the total dimensional matrix grid layout size.

### Time Complexity

```text
Time Complexity: O(M × N)
```
*Explanation:* The loops visit each coordinate cell in the grid exactly once. Inside the inner blocks, it performs direct index reads and appends them to our list in O(1) constant time, bounding the total execution runtime footprint strictly linearly relative to the total node population.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. Excluding the memory allocations required to store and return the final spiral results collection list (which scales as an inherent output requirement), the algorithm updates positional boundaries in-place using only four local scalar integer tracking registers, preserving a flat constant space footprint.

---

# Key Takeaways

✅ Core baseline template for 2D matrix perimeter indexing and geometric coordinate grid traversals.

✅ **The Inner Boundary Guard Rule:** The inclusion of `if (top <= bottom)` and `if (left <= right)` mid-loop checks is a vital defensive coding invariant. Without these conditional guards, when processing a matrix with a single row or column layer, the script will double-read elements during the backward passes, corrupting your output data log with duplicate entries.

✅ Directly drives real-world microarchitectures like **image texture rotation mapping filters** (processing pixels along a concentric spiral track for downscaling or transformations), hardware matrix printing layers, radar raster sweeps, and video block encoders.

### Similar Problems

- Spiral Matrix II (Generating a matrix from scratch along a spiral track - [LeetCode 59](https://leetcode.com))
- Spiral Matrix III ([LeetCode 885](https://leetcode.com))
- Rotate Image / Matrix ([LeetCode 48](https://leetcode.com))
- Diagonal Traverse ([LeetCode 498](https://leetcode.com))
