# Pascal's Triangle III (Exact Cell Value Finder)

## Problem Statement

Given a row index `r` and a column index `c` (**0-indexed**), return the exact value of the cell located at position `(r, c)` inside **Pascal's Triangle**. 

The solution must complete with an optimal **O(c)** time complexity and an **O(1)** constant auxiliary space footprint, without generating any full rows or 1D/2D tracking matrices on the heap.

---

## Examples

### Example 1

```text
Input: r = 4, c = 2
Output: 6
```

### Explanation

```text
Row 4 of Pascal's Triangle is.
The element at index 2 is 6.
```

---

### Example 2

```text
Input: r = 0, c = 0
Output: 1
```

---

### Example 3

```text
Input: r = 5, c = 4
Output: 5
```

---

# Intuition

Generating previous rows or maintaining rolling array lists just to find a single cell value uses unnecessary memory and time. 

Mathematically, any cell at row `r` and column `c` inside Pascal's Triangle corresponds exactly to the **Binomial Coefficient** \(\binom{r}{c}\) (read as "\(r\) choose \(c\)"). The formula is:
\[\binom{r}{c} = \frac{r!}{c! \cdot (r - c)!}\]

To compute this value in **O(c) time and O(1) space** without risking massive integer overflows from calculating raw factorials, we can rewrite the formula as a product of fractions:
\[\binom{r}{c} = \frac{r \cdot (r - 1) \cdot (r - 2) \cdots (r - c + 1)}{1 \cdot 2 \cdot 3 \cdots c}\]

We can accumulate this product incrementally inside a single linear loop from `0` to `c - 1`:
- **The Optimization Symmetry Invariant:** Since \(\binom{r}{c} == \binom{r}{r - c}\), we can optimize our loop length by setting `c = Math.min(c, r - c)`. This guarantees the loop runs at most \(r / 2\) times.
- **The Fractional Step Invariant:** At each step `i` (from `0` to `c - 1`), we multiply our running result by \((r - i)\) and divide it by \((i + 1)\). Because the product of any \(k\) consecutive integers is always divisible by \(k!\), this intermediate division is guaranteed to divide perfectly with zero remainders, allowing us to maintain standard integer precision throughout the loop.

---

# State Definition

The analytical generation pipeline tracks the rolling multiplicative product values inside a single long accumulator register:

```java
long res = 1; // 64-bit integer product register tracking cumulative combination subsets
```

---

# Recurrence / Structural Combination Condition

```text
c = min(c, r - c)
res = 1
For i from 0 to c - 1:
    res = res * (r - i)
    res = res / (i + 1)
Return res
```

---

# Optimal Single-Pass Mathematical Combination Solution

## Idea

We apply the symmetry rule to minimize our iteration count. We then loop exactly `c` times, multiplying by the descending row factors and dividing by the ascending column indexes on the fly. This calculates the value inside a clean linear sweep using zero extra heap memory arrays.

---

## Java Code

```java
class Solution {
    public int getPascalCellValue(int r, int c) {
        // Guard Condition: Out of bounds columns or invalid indices return 0 fallback bounds
        if (c < 0 || c > r || r < 0) {
            return 0;
        }

        // Symmetry Optimization: nCr is equivalent to nC(n-r)
        // (e.g., choosing 4 items out of 5 is the same as picking 1 item to leave behind)
        c = Math.min(c, r - c);

        long res = 1;

        // Core Step: Incremental calculation of the Binomial Coefficient
        for (int i = 0; i < c; i++) {
            res *= (r - i); // Multiply by the falling row numerator factor
            res /= (i + 1); // Divide by the rising column denominator factor
            
            // Note: The intermediate mathematical division is guaranteed to result in a 
            // whole integer with zero remainder because the product of 'i + 1' consecutive 
            // numbers is always divisible by '(i + 1)!'.
        }

        return (int) res; // Cast our stable 64-bit register safely back to 32-bit int
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(c)
```
*Explanation:* The loop runs exactly `Math.min(c, r - c)` times. At each step, it performs basic constant-time \(O(1)\) primitive multiplication and division operations, bounding the overall execution timeline strictly linearly relative to the column position parameter `c`.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. The algorithm calculates the cell value using a single local 64-bit primitive variable register. It bypasses recursive call stacks, array builders, and hash tables completely, maintaining a completely flat constant space footprint.

---

# Key Takeaways

✅ Peak-tier fundamental computing paradigm for combinatorial optimizations, combinatorial algorithms, and formulaic stream reductions.

✅ **The Real-Time Factorial Reduction Rule:** Calculating raw factorials directly (like calling `factorial(n)`) is a major performance bottleneck that triggers severe integer overflow crashes even for small values (e.g., \(21!\) overflows a 64-bit `long`). Interleaving multiplications and divisions step-by-step keeps intermediate numbers compact, allowing you to compute precise combination counts safely.

✅ Directly drives real-world microarchitectures like **asymmetric cryptography key generation modules** (computing massive combinatorial probability fields inline directly inside hardware registers), statistical machine learning algorithms, game theory engine permutations calculators, and network multiplexers.

### Similar Problems

- Pascal's Triangle ([LeetCode 118](https://leetcode.com))
- Pascal's Triangle II ([LeetCode 119](https://leetcode.com))
- Unique Paths ([LeetCode 62](https://leetcode.com))
- Combinations ([LeetCode 77](https://leetcode.com))
