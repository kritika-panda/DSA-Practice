# Pascal's Triangle II ([LeetCode 119](https://leetcode.com))

## Problem Statement

Given an integer `rowIndex`, return the `rowIndex`-th (**0-indexed**) row of the **Pascal's triangle**.

In **Pascal's triangle**, each number is the sum of the two numbers directly above it.

**Performance Target:**
- Optimize your algorithm to use only **O(k)** auxiliary space, where `k = rowIndex`.

---

## Examples

### Example 1

```text
Input: rowIndex = 3
Output: [1,3,3,1]
```

---

### Example 2

```text
Input: rowIndex = 0
Output: [1]
```

---

### Example 3

```text
Input: rowIndex = 1
Output: [1,1]
```

---

# Intuition

A straightforward approach would be to fully generate all rows up to `rowIndex` matching the *Pascal's Triangle I* approach. However, storing the entire 2D matrix structure uses an \(O(k^2)\) space footprint, which violates the strict \(O(k)\) optimization constraint.

To achieve a true **O(k) linear space footprint**, we can compress our workspace into a single **1D Rolling Array Cache**:
- We allocate a single list or array of size `rowIndex + 1`, pre-filled with `0`s, and seed index `0` with `1`.
- To generate row `i` from row `i - 1` inside the same shared memory buffer, we can loop **backward from right to left** (from index `i` down to `1`).
- The transition rule states: `row[j] = row[j] + row[j - 1]`.
- **Why Backward?** If we scanned forward from left to right, updating `row[j]` would overwrite its historical value prematurely. When we subsequently step to evaluate `row[j + 1]`, the prerequisite data `row[j]` would already hold the *new* value instead of the *old* row value, corrupting the subsequent math. Stepping backward keeps historical prerequisite values safe behind the cursor path.

---

# State Definition

The space-optimized 1D generation pipeline tracks state transitions inside a single array register:

```java
List<Integer> row = new ArrayList<>(rowIndex + 1); // Shared 1D rolling array buffer workspace
```

---

# Recurrence / Structural 1D Rolling State Condition

```text
Initialize row with 1 at index 0, others 0
For i from 1 to rowIndex:
    For j from i down to 1:
        row[j] = row[j] + row[j - 1]
Return row
```

---

# Optimal 1D Rolling Space Dynamic Programming Solution

## Idea

We initialize an `ArrayList` seeded with `1`. We configure an outer loop tracking rows up to `rowIndex`. By nesting an inner math loop that sweeps **backward from index `i` down to `1`**, we update cell values in-place inside a single shared array buffer using strictly \(O(k)\) memory.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<Integer> getRow(int rowIndex) {
        // Initialize an ArrayList with an explicit optimal capacity allocation
        List<Integer> row = new ArrayList<>(rowIndex + 1);
        
        // Base Case: Seed index 0 with 1 (the peak apex node value of Pascal's Triangle)
        row.add(1);

        // Core Step: Iterative bottom-up row tier construction pass
        for (int i = 1; i <= rowIndex; i++) {
            // Append a placeholder 0 or 1 at the end to expand the row size to i+1 slots
            row.add(1);

            // Sweep BACKWARD from right to left to perform inline, in-place summation
            // Scanning backward protects historical prerequisite elements from premature overwrites
            for (int j = i - 1; j >= 1; j--) {
                int sum = row.get(j) + row.get(j - 1);
                row.set(j, sum); // Overwrite cell values directly inside the same buffer
            }
        }

        return row; // Return strictly the target row elements slice requested
    }
}
```

---

## Complexity Analysis

Let K be the target `rowIndex` value parameter.

### Time Complexity

```text
Time Complexity: O(K²)
```
*Explanation:* The outer loop iterates exactly K times. For each row `i`, the inner backward pass steps from `i - 1` down to `1`. The total basic addition operations executed match the triangular number summation sequence \(\frac{K \times (K + 1)}{2}\), bounding the runtime complexity quadratically to \(O(K^2)\).

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. Excluding the allocation of the output list `row` required to return the final target layer (which consumes \(O(K)\) space as an inherent problem condition constraint), the calculations execute completely in-place inside a single array buffer using only simple local integer cursors, keeping auxiliary storage flat at \(O(1)\).

---

# Key Takeaways

✅ Peak-tier fundamental array space-compression template representing 1D row array cache reductions.

✅ **The Memory Overwrite Protection Invariant:** Squashing a multi-dimensional state grid down to a rolling 1D array cache is a highly strategic system design pattern. Whenever row cell calculations depend on values from the same index and previous columns of the prior row, **always traverse your loops backward**. Reversing the track safeguards your prerequisites, allowing in-place mutations without helper buffers.

✅ Directly drives real-world microarchitectures like **network combinatorial sliding window filters**, computing individual statistical binomial coefficients blocks on the fly, streaming memory compaction buffers, and digital signal audio filters.

### Similar Problems

- Pascal's Triangle ([LeetCode 118](https://leetcode.com))
- Unique Paths ([LeetCode 62](https://leetcode.com))
- Triangle ([LeetCode 120](https://leetcode.com))
- Bounded Knapsack Space Optimizations
