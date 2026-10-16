# Combination Sum III ([LeetCode 216](https://leetcode.com))

## Problem Statement

Find all valid combinations of `k` numbers that sum up to `n` such that the following conditions are true:
- Only numbers from `1` to `9` are used.
- Each number is used **at most once**.

Return *a list of all unique combinations*. The solution set **must not** contain duplicate combinations, and you may return the combinations in **any order**.

---

## Examples

### Example 1

```text
Input: k = 3, n = 7
Output: [[1,2,4]]
```

### Explanation

```text
The only valid combination of 3 numbers from 1 to 9 that sums to 7 is 1 + 2 + 4 = 7.
```

---

### Example 2

```text
Input: k = 3, n = 9
Output: [[1,2,6],[1,3,5],[2,3,4]]
```

### Explanation

```text
There are 3 unique combinations of 3 numbers that sum to 9:
1 + 2 + 6 = 9
1 + 3 + 5 = 9
2 + 3 + 4 = 9
```

---

### Example 3

```text
Input: k = 4, n = 1
Output: []
```

### Explanation

```text
There are no valid combinations. The smallest sum using 4 unique numbers from 1 to 9 is 1 + 2 + 3 + 4 = 10, which is strictly greater than 1.
```

---

# Intuition

This problem represents a highly constrained **Combinatorial Selection Search** puzzle. Since our candidate pool is strictly fixed to a small, static set of single digits (`1` to `9`), and the total number of items per subset is bound to exactly `k`, we can systematically solve it using a clean **Backtracking** approach.

We traverse through the digit range sequentially:
- At each digit `i` from our current index up to `9`, we face a choice to include it or skip it.
- **Pruning Invariants (Early Exit Checks):** Because the candidate numbers strictly increase monotonically, we can aggressively prune out unneeded search sub-trees to optimize runtime latency:
  1.  **Sum Breach:** If digit `i` exceeds our `remainingTarget`, we can **break out of the loop early** because all subsequent digits are guaranteed to overshoot the balance as well.
  2.  **Size Breach:** If the size of our tracking buffer `currentPath.size()` matches `k` but our `remainingTarget` is not zero, the branch is invalid. Conversely, if `currentPath.size() > k`, we stop.

---

# State Definition (Optimal Backtracking Approach)

To trace our combinatorics states systematically within a recursive state-space decision tree, we manage states using three parameters:

```text
backtrack(startDigit, remainingTarget, currentPath):
- 'startDigit' tracks the next available number in the range [1, 9] to prevent reuse.
- 'remainingTarget' tracks the remaining numeric balance required to hit the value n.
- 'currentPath' acts as a shared mutable array list container accumulating active choices.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Cases:
- If currentPath.size() == k:
      If remainingTarget == 0 -> result.add(new ArrayList<>(currentPath))
      Return

Recursive Controlled Branching:
For i from startDigit to 9:
    If i > remainingTarget -> Break (Sum Pruning)
    
    currentPath.add(i)
    backtrack(i + 1, remainingTarget - i, currentPath) // i + 1 enforces "at most once" constraint
    currentPath.remove(currentPath.size() - 1)         // Backtrack step
```

---

# Optimal Backtracking Solution with Pruning

## Idea

We launch a depth-first search starting with a `startDigit` of `1` and our target set to `n`. By combining size-limit base checks with lookahead sum-breaking invariants, we ensure the algorithm skips invalid path permissions entirely, operating within tight performance constraints.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> result = new ArrayList<>();
        
        // Optimization Guard: Check mathematical boundary underflows/overflows
        // Minimum possible sum for k numbers: sum of 1 to k
        // Maximum possible sum for k numbers: sum of (10-k) to 9
        if (k <= 0 || k > 9 || n <= 0) {
            return result;
        }

        // Shared mutable path buffer workspace to prevent memory thrashing
        List<Integer> currentPath = new ArrayList<>();

        // Launch recursive backtracking pass from digit 1
        backtrack(1, k, n, currentPath, result);
        return result;
    }

    private void backtrack(int startDigit, int k, int remainingTarget, List<Integer> currentPath, List<List<Integer>> result) {
        // Base Case: Once our path contains exactly k elements, check if target is met
        if (currentPath.size() == k) {
            if (remainingTarget == 0) {
                result.add(new ArrayList<>(currentPath)); // Deep copy current buffer contents into results
            }
            return; // Terminate further deeper searching for this branch
        }

        // Loop forward through available numbers strictly between startDigit and 9
        for (int i = startDigit; i <= 9; i++) {
            // Pruning Invariant: If the current digit exceeds our remaining target balance,
            // all subsequent numbers will as well since numbers are sorted. Break early!
            if (i > remainingTarget) {
                break;
            }

            // Choice Step: Include digit 'i' in our active choice profile
            currentPath.add(i);

            // Recurse deeper. We pass 'i + 1' as the next startDigit boundary parameter
            // to ensure each specific number is selected a maximum of exactly once.
            backtrack(i + 1, k, remainingTarget - i, currentPath, result);

            // Backtrack Step: Remove the trailing element to restore state context for the next branch
            currentPath.remove(currentPath.size() - 1);
        }
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(C(9, k) × k) -> Constant Upper Bound O(1)
```
*Explanation:* The candidate space is completely bounded since we choose combinations of size `k` from a total of exactly 9 unique digits. The absolute maximum number of combinations evaluated is bounded by the binomial coefficient $\binom{9}{k}$. The largest possible number of evaluations happens when $k = 4$ or $k = 5$, yielding at most $\binom{9}{5} = 126$ states. Copying each valid subset takes $O(k)$ time, bounding our operations to true constant limits.

---

### Space Complexity

```text
Space Complexity: O(k) -> Constant Upper Bound O(1)
```
*Explanation:* Excluding the structural memory blocks required to collect and return the final results list of lists, the auxiliary space used by the execution call stack depth and the shared `currentPath` buffer scales linearly with $k$. Since $k \le 9$, the memory footprint is bounded to true constant dimensions.

---

# Key Takeaways

✅ Core fundamental paradigm for processing combination searches within fixed limits.

✅ **The Double Constraint Architecture:** Combining size limits (`currentPath.size() == k`) with target sums (`remainingTarget == 0`) acts as a highly effective filter. By structuring the size check as our primary base case statement and the sum check as an inline loop breaker, the code avoids traversing down dead-end branches.

✅ Directly drives real-world microarchitectures like **PIN/code combination generators**, lotto ticket analyzer passes, sub-matrix subset dimension verifiers, and asset selection ledger systems.

✅ Common Architectural Paradigm:

```text
Double-Constrained Branching over Static Ranges with High-Speed Short-Circuit Breaks
```

### Similar Problems

- Combination Sum ([LeetCode 39](https://leetcode.com))
- Combination Sum II ([LeetCode 40](https://leetcode.com))
- Combinations ([LeetCode 77](https://leetcode.com))
- Subsets / Power Set ([LeetCode 78](https://leetcode.com))

<FollowUp>
Let me know which advanced backtracking puzzle or combination grouping pattern you want to structure next:
* Should we configure the raw markdown layout for **Combinations ([LeetCode 77](https://leetcode.com))** to see the baseline size-constrained subset template?
* Do you want to examine **Combination Sum II ([LeetCode 40](https://leetcode.com))** to trace variable duplicate pruning models?
* Or switch focus back to a linked list pointer puzzle track?

Tell me which **key terms** or problem targets you want to capture next!
</FollowUp>
