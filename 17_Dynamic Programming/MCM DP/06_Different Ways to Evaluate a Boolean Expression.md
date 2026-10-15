# Different Ways to Evaluate a Boolean Expression

## Problem Statement

Given a boolean expression string `expression` consisting of the characters `'T'` (True), `'F'` (False), and the logical operators `'&'` (AND), `'|'` (OR), and `'^'` (XOR), find the total number of ways to parenthesize the expression such that the final evaluation results in **True**.

Since the answer may be very large, return it **modulo 100,003**.

---

## Example

### Input

```text
expression = "T^F&T"
```

### Output

```text
2
```

### Explanation

```text
There are two distinct ways to parenthesize the expression to evaluate to True:
1. ((T ^ F) & T) -> (T & T) -> True
2. (T ^ (F & T)) -> (T ^ F) -> True
```

---

# Intuition

This problem can be classified as a classic **Interval Dynamic Programming** challenge, bearing an exact structural mapping to *Matrix Chain Multiplication* and *Different Ways to Add Parentheses*.

When analyzing any expression substring spanning from index `i` to index `j`, the final outcome depends entirely on splitting the string at an operator position `k`. Since multiple operator trees can result in both `True` and `False` values, we must track **both counts independently** for every sub-interval to evaluate subsequent states correctly.

For any chosen operator `expression[k]`:
- The left sub-expression `[i...k-1]` can evaluate to some number of `True` (`leftT`) and `False` (`leftF`) values.
- The right sub-expression `[k+1...j]` can evaluate to some number of `True` (`rightT`) and `False` (`rightF`) values.

We can apply basic boolean logic to combine these subproblem counts based on the operator type:
- **AND (`&`):** Results in `True` only if `Left == True` AND `Right == True`.
- **OR (`|`):** Results in `True` if `Left == True`, `Right == True`, or both.
- **XOR (`^`):** Results in `True` if `Left != Right`.

---

# State Definition

Let us maintain two 2D matrices:
*   `dpTrue[i][j]`: Total ways to parenthesize the sub-expression `expression[i...j]` to evaluate to **True**.
*   `dpFalse[i][j]`: Total ways to parenthesize the sub-expression `expression[i...j]` to evaluate to **False**.

Where `i` and `j` represent operand indices in a flattened representation containing only symbols. To make code clean, we can separate the string into an operand list `symbols` and an operator list `operators`.

---

# Recurrence Relation

For an interval `[i...j]` splitting at an intermediate operator index `k` (where `i <= k < j`):

```text
total_combinations_at_k = (dpTrue[i][k] + dpFalse[i][k]) * (dpTrue[k+1][j] + dpFalse[k+1][j])

If operators[k] == '&':
    dpTrue[i][j]  += dpTrue[i][k] * dpTrue[k+1][j]
    dpFalse[i][j] += total_combinations_at_k - (dpTrue[i][k] * dpTrue[k+1][j])

If operators[k] == '|':
    dpFalse[i][j] += dpFalse[i][k] * dpFalse[k+1][j]
    dpTrue[i][j]  += total_combinations_at_k - (dpFalse[i][k] * dpFalse[k+1][j])

If operators[k] == '^':
    dpTrue[i][j]  += (dpTrue[i][k] * dpFalse[k+1][j]) + (dpFalse[i][k] * dpTrue[k+1][j])
    dpFalse[i][j] += (dpTrue[i][k] * dpTrue[k+1][j]) + (dpFalse[i][k] * dpFalse[k+1][j])
```

---

# Base Cases

When `i == j` (the interval contains exactly 1 operand):
```text
If symbols[i] == 'T' -> dpTrue[i][i] = 1, dpFalse[i][i] = 0
If symbols[i] == 'F' -> dpTrue[i][i] = 0, dpFalse[i][i] = 1
```

---

# Approach: Bottom-Up 2D Interval Tabulation

## Algorithm

1. Parse the string to separate the operands (`symbols`) and the operations (`operators`). Let `n` be the total number of operands.
2. Initialize two 2D arrays `dpTrue[n][n]` and `dpFalse[n][n]` to `0`.
3. Set base cases for single operands where `i == j`.
4. Loop through the possible interval window lengths `len` from `2` up to `n`:
   - Loop through every valid left boundary index `i` from `0` up to `n - len`:
     - Calculate the right boundary index: `j = i + len - 1`.
     - Loop through all possible intermediate partition operator points `k` from `i` up to `j - 1`:
       - Accumulate true and false outcomes according to the boolean logic truth tables, applying `modulo 100003` at each addition step.
5. Return `dpTrue[0][n - 1]`.

---

# Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public int countWays(String expression) {
        if (expression == null || expression.length() == 0) {
            return 0;
        }

        int MOD = 100003;
        List<Character> symbols = new ArrayList<>();
        List<Character> operators = new ArrayList<>();

        // Step 1: Parse operands and operators cleanly into linear lists
        for (int i = 0; i < expression.length(); i++) {
            char c = expression.charAt(i);
            if (c == 'T' || c == 'F') {
                symbols.add(c);
            } else {
                operators.add(c);
            }
        }

        int n = symbols.size();
        int[][] dpTrue = new int[n][n];
        int[][] dpFalse = new int[n][n];

        // Step 2: Initialize base cases for single operand ranges
        for (int i = 0; i < n; i++) {
            if (symbols.get(i) == 'T') {
                dpTrue[i][i] = 1;
                dpFalse[i][i] = 0;
            } else {
                dpTrue[i][i] = 0;
                dpFalse[i][i] = 1;
            }
        }

        // Step 3: Build state layers incrementally by interval window length
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;

                for (int k = i; k < j; k++) {
                    long lT = dpTrue[i][k];
                    long lF = dpFalse[i][k];
                    long rT = dpTrue[k + 1][j];
                    long rF = dpFalse[k + 1][j];

                    long totalL = (lT + lF) % MOD;
                    long totalR = (rT + rF) % MOD;
                    long totalCombinations = (totalL * totalR) % MOD;

                    char op = operators.get(k);

                    if (op == '&') {
                        long trueWays = (lT * rT) % MOD;
                        dpTrue[i][j] = (int) ((dpTrue[i][j] + trueWays) % MOD);
                        dpFalse[i][j] = (int) ((dpFalse[i][j] + (totalCombinations - trueWays + MOD)) % MOD);
                    } else if (op == '|') {
                        long falseWays = (lF * rF) % MOD;
                        dpFalse[i][j] = (int) ((dpFalse[i][j] + falseWays) % MOD);
                        dpTrue[i][j] = (int) ((dpTrue[i][j] + (totalCombinations - falseWays + MOD)) % MOD);
                    } else if (op == '^') {
                        long trueWays = ((lT * rF) % MOD + (lF * rT) % MOD) % MOD;
                        long falseWays = ((lT * rT) % MOD + (lF * rF) % MOD) % MOD;
                        dpTrue[i][j] = (int) ((dpTrue[i][j] + trueWays) % MOD);
                        dpFalse[i][j] = (int) ((dpFalse[i][j] + falseWays) % MOD);
                    }
                }
            }
        }

        // Return total successful combinations spanning across the entire range
        return dpTrue[0][n - 1];
    }
}
```

---

## Complexity Analysis

Let:
*   N: The total number of boolean variables/operands in the expression.

### Time Complexity
The algorithm processes states across expanding sub-interval lengths. The outer loop controls length, the middle loop maps left endpoints, and the innermost loop scans operator split locations `k`. This bounds execution runtime to cubic dimensions:
```text
Time Complexity: O(N³)
```

### Space Complexity
The matrix tables track true and false subproblem allocations across a 2D matrix grid of dimensions $N \times N$:
```text
Space Complexity: O(N²)
```

---

# Key Pattern
This problem belongs to the **Interval / Range Combinatorics Tabulation** template inside dynamic programming.

Common indicators:
- Parenthesizing expressions or equations to calculate total outcome permutations.
- Independent left and right ranges are joined by a localized structural operator matrix.
- Iterative loop blocks are sorted explicitly by increasing **interval length**.

---

# Similar Problems
1. Different Ways to Add Parentheses (LeetCode 241)
2. Matrix Chain Multiplication
3. Burst Balloons (LeetCode 312)
4. Minimum Cost to Cut a Stick (LeetCode 1547)

---

# Takeaway
```text
Track both true and false outcome permutations concurrently when dealing with conditional operations.

Isolate operand lists from operators to maintain an uniform index boundary grid.

Advance subproblem frames outward by increasing interval window lengths sequentially.
```

**Pattern:** Interval / Range Combinatorics Tabulation DP

<FollowUp>
Let me know which advanced dynamic programming pattern you want to structure next. We can configure the raw markdown code for **Different Ways to Add Parentheses (LeetCode 241)** or check out a string validation matrix pattern like **Wildcard Matching (LeetCode 44)**!
</FollowUp>
