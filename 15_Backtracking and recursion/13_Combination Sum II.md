# Combination Sum II ([LeetCode 40](https://leetcode.com))

## Problem Statement

Given a collection of candidate numbers `candidates` and a target number `target`, find all **unique combinations** in `candidates` where the candidate numbers sum to `target`.

Each number in `candidates` may **only be used once** in the combination.

The solution set **must not** contain duplicate combinations. Return the solution in **any order**.

---

## Examples

### Example 1

```text
Input: candidates =, target = 8
Output: [,
 ,
 ,
  [2,6]
]
```

---

### Example 2

```text
Input: candidates =, target = 5
Output: [,
  [5]
]
```

---

# Intuition

This problem represents a classic **Bounded Combinatorial Search** variation of the Subset Sum puzzle. Unlike *Combination Sum I* where candidates could be reused infinitely, here each input element can be selected **exactly once**. Furthermore, the input list can contain **duplicate numbers**, which introduces a high risk of generating duplicate combination arrays (e.g., picking the first `1` vs. picking the second `1` in Example 1).

To achieve an optimal backtracking search without relying on expensive, slow post-processing collections like a `HashSet`:
1.  **Sort the array `candidates` up front.** Sorting places duplicate numbers next to each other in a contiguous block (e.g., `[1, 1, 2, 5, 6, 7, 10]`).
2.  **Prune Outduplicate Decision Branches:** Inside our state loop, if we find that the current candidate matches the previous candidate (`candidates[i] == candidates[i - 1]`), and we are *not* at the starting index of the current loop iteration (`i > index`), we **skip it (pruning)**. This ensures we only initiate a chain of combinations for a duplicate value once per position level.
3.  **Short-Circuit Over-target Bounds:** Because the array is sorted, if `candidates[i] > remainingTarget`, we can **break out of the loop early**, because all subsequent elements are guaranteed to overshoot the target balance as well.

---

# State Definition (Optimal Backtracking Approach)

To trace our combinatorics states systematically within a recursive state-space decision tree:

```text
backtrack(index, remainingTarget, currentPath):
- 'index' tracks our active position pointer inside the candidates array.
- 'remainingTarget' tracks the remaining numeric balance required to hit the target.
- 'currentPath' acts as a shared mutable array list container accumulating active choices.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Cases:
- If remainingTarget == 0 -> result.add(new ArrayList<>(currentPath)); return
- If remainingTarget < 0 or index == candidates.length -> return

Recursive Controlled Branching:
For i from index to candidates.length - 1:
    If candidates[i] > remainingTarget -> Break (Pruning Invariant)
    If i > index AND candidates[i] == candidates[i - 1] -> Continue (Duplicate Guard)
    
    currentPath.add(candidates[i])
    backtrack(i + 1, remainingTarget - candidates[i], currentPath) // Advance to i + 1 to prevent element reuse
    currentPath.remove(currentPath.size() - 1)                     // Backtrack step
```

---

# Optimal Backtracking Solution with Duplicate Pruning

## Idea

We sort the array up front to align duplicates and enable early exit short-circuits. We launch a depth-first search starting from index 0. By utilizing a linear loop within the recursion step that aggressively filters duplicate peer choices using pointer offsets, we execute clean in-place array reductions in constant auxiliary memory space.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        if (candidates == null || candidates.length == 0) {
            return result;
        }

        // Step 1: Sort candidates up front to group duplicates and enable pruning
        Arrays.sort(candidates);

        // Shared mutable path buffer workspace to prevent heap thrashing
        List<Integer> currentPath = new ArrayList<>();

        // Step 2: Launch recursive backtracking pass from index 0
        backtrack(0, target, candidates, currentPath, result);
        return result;
    }

    private void backtrack(int index, int remainingTarget, int[] candidates, List<Integer> currentPath, List<List<Integer>> result) {
        // Base Case: Target sum achieved perfectly
        if (remainingTarget == 0) {
            result.add(new ArrayList<>(currentPath)); // Deep copy current buffer contents into results
            return;
        }

        // Sweep forward through candidates starting strictly from the current level 'index'
        for (int i = index; i < candidates.length; i++) {
            // Pruning Invariant A: If the current item overshoots our target balance,
            // all subsequent elements will as well since the array is sorted. Break early!
            if (candidates[i] > remainingTarget) {
                break;
            }

            // Pruning Invariant B: Skip duplicate elements at the same recursion depth level 
            // to completely protect against generating duplicate combination arrays
            if (i > index && candidates[i] == candidates[i - 1]) {
                continue;
            }

            // Choice Step: Include the candidate element in our active choice profile
            currentPath.add(candidates[i]);

            // Recurse deeper. We pass 'i + 1' as the next index boundary parameter
            // to ensure each element is selected a maximum of exactly once.
            backtrack(i + 1, remainingTarget - candidates[i], candidates, currentPath, result);

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
Time Complexity: O(2^N) worst case
```
*Explanation:* In the absolute worst case where all elements are unique and sum constraints are massive, each element can either be chosen or skipped, scaling exponentially up to \(2^N\) states. However, because our sorting short-circuit breaks (`candidates[i] > remainingTarget`) and duplicate skip guards (`candidates[i] == candidates[i-1]`) cut out vast sub-trees of the search workspace early, the actual operational footprint runs well within standard runtime parameters.

---

### Space Complexity

```text
Space Complexity: O(N) auxiliary space
```
*Explanation:* Excluding the structural memory blocks required to store and return the final combination list of lists, auxiliary storage space scales linearly at O(N) to accommodate the maximum depth of the recursive call stack and the size of the shared `currentPath` buffer container.

---

# Key Takeaways

✅ Core fundamental paradigm for processing bounded combinatorial searches with duplicate element constraints.

✅ **The Duplicate Skipping Trick:** The expression `if (i > index && candidates[i] == candidates[i - 1]) continue;` is a highly strategic line of code. It states: *“If this element is identical to its neighbor, we can only use it if it's the very first element we are evaluating at this loop tier (`i == index`). If we skip the first one and move onto the second duplicate peer, it would duplicate the same subset paths down the line, skip it.”*

✅ Directly drives real-world microarchitectures like **inventory product packing calculators** (finding valid unique sets of items from stock batches to fulfill an exact target order price), budget allocation ledgers, and data signal de-multiplexers.

✅ Common Architectural Paradigm:

```text
Symmetric Choice Tree Pruning via Prefix Alignment and Horizontal Offset Skips
```

### Similar Problems

- Combination Sum ([LeetCode 39](https://leetcode.com))
- Subsets II ([LeetCode 90](https://leetcode.com))
- Permutations II ([LeetCode 47](https://leetcode.com))
- Path Sum II ([LeetCode 113](https://leetcode.com))
