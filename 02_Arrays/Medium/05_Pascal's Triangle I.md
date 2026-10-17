# Pascal's Triangle ([LeetCode 118](https://leetcode.com))

## Problem Statement

Given an integer `numRows`, return the first `numRows` of **Pascal's triangle**.

In **Pascal's triangle**, each number is the sum of the two numbers directly above it as shown:

```text
      1
     1 1
    1 2 1
   1 3 3 1
  1 4 6 4 1
```

---

## Examples

### Example 1

```text
Input: numRows = 5
Output: [[1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1]]
```

---

### Example 2

```text
Input: numRows = 1
Output: [[1]]
```

---

# Intuition

Pascal's Triangle can be structurally modeled as a dynamic **Combinatorial Grid Generation** task. Each row `i` contains exactly `i + 1` elements (0-indexed).

We can exploit the core mathematical structural definition of the triangle:
1. The absolute first and last positions of any given row are **always** equal to `1` (`row.get(0) = 1` and `row.get(row.size() - 1) = 1`).
2. For any internal cell located at row index `i` and column index `j`, its value payload is computed directly by adding the two adjacent upper elements from the immediate preceding row (`i - 1`):
   \[\text{row}[j] = \text{prevRow}[j - 1] + \text{prevRow}[j]\]

By referencing the previously compiled row array list inside our global results cache, we can systematically construct subsequent tiers in a clean, iterative bottom-up manner without expensive redundant re-calculations.

---

# State Definition

The multi-level triangle pipeline tracks active grid constructions using localized list pointer objects:

```java
List<List<Integer>> triangle = new ArrayList<>(); // Global cached results list matrix container
List<Integer> prevRow = triangle.get(i - 1);       // Reference pointer to the immediate upper prerequisite layer
```

---

# Recurrence / Structural State Condition

```text
triangle[0] = [1]
For i from 1 to numRows - 1:
    row = Empty List
    row.add(1) // Front boundary anchor
    For j from 1 to i - 1:
        row.add(triangle[i - 1][j - 1] + triangle[i - 1][j])
    row.add(1) // Tail boundary anchor
    triangle.add(row)
```

---

# Optimal Bottom-Up Dynamic Programming Solution

## Idea

We seed our global collection matrix with the initial `[1]` base case tier. We then initialize an outer loop to track row increments up to `numRows`. By pairing boundary digit assignments with a nested linear lookup loop that reads raw values from the previous cached row frame, we populate the triangle structure efficiently in single-pass linear rows.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> triangle = new ArrayList<>();
        
        // Guard Condition: Request ranges equal to or below zero return empty matrix wrappers
        if (numRows <= 0) {
            return triangle;
        }

        // Base Case Step: The absolute first row tier is always initialized to [1]
        List<Integer> firstRow = new ArrayList<>();
        firstRow.add(1);
        triangle.add(firstRow);

        // Core Step: Iterative bottom-up row tier construction pass
        for (int i = 1; i < numRows; i++) {
            List<Integer> row = new ArrayList<>();
            List<Integer> prevRow = triangle.get(i - 1); // Extract upper row prerequisite frame

            // 1. Anchor the absolute front boundary cell digit to 1
            row.add(1);

            // 2. Populate internal cells by combining adjacent upper parent nodes
            for (int j = 1; j < i; j++) {
                int sum = prevRow.get(j - 1) + prevRow.get(j);
                row.add(sum);
            }

            // 3. Anchor the absolute terminal tail boundary cell digit to 1
            row.add(1);

            // Append the fully constructed structural layer row list to our matrix cache
            triangle.add(row);
        }

        return triangle; // Return the completely generated Pascal's Triangle grid profile
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(numRows²)
```
*Explanation:* The total number of element cells populated inside a Pascal's Triangle of depth R is given by the summation arithmetic progression formula: \(\frac{R \times (R + 1)}{2}\). Since each cell reads historical parent values and writes its own results inside O(1) constant time, the overall execution timeline scales quadratically relative to the total rows requested.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal memory footprint. Excluding the allocation blocks required to compile and return the final output nested lists structure (which natively consumes $O(\text{numRows}^2)$ memory resources), the algorithm manages calculations using only local variable pointer references, preserving a completely flat constant auxiliary space footprint.

---

# Key Takeaways

✅ Core fundamental paradigm for processing dynamic programming matrices, binomial expansion grids, and combinations coefficients.

✅ **The Value of Prerequisite Caching:** Instead of using expensive recursive tree routines to compute individual binomial combinations coefficients like $\binom{n}{k} = \frac{n!}{k!(n-k)!}$ from scratch (which risks massive integer overflows and performance degradation), using a bottom-up array grid builder treats previously solved rows as immediate memory lookup registers.

✅ Directly drives real-world microarchitectures like **binomial probability distribution engines**, polynomial algebraic expansion calculations compilers, cellular signal combination filters, and computer graphics blending models.

### Similar Problems

- Pascal's Triangle II (Extracting a single specific target row level - [LeetCode 119](https://leetcode.com))
- Unique Paths ([LeetCode 62](https://leetcode.com))
- Triangle ([LeetCode 120](https://leetcode.com))
- Combination Sum ([LeetCode 39](https://leetcode.com))
