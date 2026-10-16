# Combination Sum ([LeetCode 39](https://leetcode.com))

## Problem Statement

Given an array of **distinct** integers `candidates` and a target integer `target`, return *a list of all **unique combinations** of `candidates` where the chosen numbers sum to `target`*. You may return the combinations in **any order**.

The **same** number may be chosen from `candidates` an **unlimited number of times**. Two combinations are unique if the frequency of at least one of the chosen numbers is different.

It is guaranteed that the number of unique combinations that sum up to `target` is less than `150` combinations for the given inputs.

---

## Examples

### Example 1

```text
Input: candidates = [2,3,6,7], target = 7
Output: [[2,2,3],[7]]
```

### Explanation

```text
- 2 and 3 are candidates, and 2 + 2 + 3 = 7. Note that 2 can be used multiple times.
- 7 is a candidate, and 7 = 7.
These are the only two unique combinations.
```

---

### Example 2

```text
Input: candidates = [2,3,5], target = 8
Output: [[2,2,2,2],[2,3,3],[3,5]]
```

---

### Example 3

```text
Input: candidates = [2], target = 1
Output: []
```

---

# Intuition

This problem is a classic **Combinatorial Optimization Search** problem that can be modeled as a tree traversal over choices. Because we can reuse each candidate an **unlimited number of times**, this maps conceptually onto an **Unbounded Knapsack** branching pattern.

For each candidate at a given `index`, we have two major logical branches:
1. **Include the element:** Add `candidates[index]` into our running tracking path, subtract its value from the remaining target, and **stay at the same `index`** to allow unlimited reuse of this element in subsequent steps.
2. **Exclude the element:** Leave the path as is, and move the position pointer to the next candidate (`index + 1`), permanently relinquishing the ability to use the current element again along this specific branch.

To optimize the search workspace:
- We can **sort the array `candidates` up front**.
- If we ever see that `candidates[index] > remainingTarget`, we can **short-circuit and break out of the loop early** (Pruning), because all subsequent elements in a sorted array are guaranteed to overshoot the target as well.

---

# State Definition (Optimal Backtracking Approach)

To formalise our paths inside a clean, controlled recursive backtracking framework, we track state parameters via three contextual values:

```text
backtrack(index, remainingTarget, currentPath):
- 'index' tracks our active position pointer inside the candidates array.
- 'remainingTarget' tracks the remaining numeric balance required to complete the sum.
- 'currentPath' acts as a shared mutable array list container accumulating active choices.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Cases:
- If remainingTarget == 0 -> result.add(new ArrayList<>(currentPath)); return
- If remainingTarget < 0 or index == candidates.length -> return

Recursive Branch Decisions:
For i from index to candidates.length - 1:
    If candidates[i] > remainingTarget -> Break (Pruning Invariant)
    
    currentPath.add(candidates[i])
    backtrack(i, remainingTarget - candidates[i], currentPath) // Stay at index 'i' for unlimited reuse
    currentPath.remove(currentPath.size() - 1)                 // Backtrack step
```

---

# Optimal Backtracking Solution with Pruning

## Idea

We sort the available choices up front to maximize our short-circuit efficiency. We launch a depth-first search starting from index 0. By utilizing a loop inside the recursive step that starts from the current `index` (to prevent generating duplicate permutations of the same combinations), we sweep cleanly across valid search bounds using $O(1)$ auxiliary buffer modifications.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        if (candidates == null || candidates.length == 0) {
            return result;
        }

        // Step 1: Sort candidates up front to enable high-speed short-circuit pruning
        Arrays.sort(candidates);

        // Shared mutable workspace list to avoid massive duplicate heap garbage allocations
        List<Integer> currentPath = new ArrayList<>();

        // Step 2: Launch our recursive backtracking pass from index 0
        backtrack(0, target, candidates, currentPath, result);
        return result;
    }

    private void backtrack(int index, int remainingTarget, int[] candidates, List<Integer> currentPath, List<List<Integer>> result) {
        // Base Case: Target achieved perfectly
        if (remainingTarget == 0) {
            result.add(new ArrayList<>(currentPath)); // Deep copy current buffer contents into results
            return;
        }

        // Iterate through candidates starting strictly from 'index' to eliminate duplicate combinations
        for (int i = index; i < candidates.length; i++) {
            // Pruning Invariant: If the current item overshoots our target balance, 
            // all subsequent elements will as well since the array is sorted. Break early!
            if (candidates[i] > remainingTarget) {
                break;
            }

            // Choice Step: Include the candidate element in our active choice profile
            currentPath.add(candidates[i]);

            // Recurse deeper. Note that we pass 'i' as the next index boundary (instead of i + 1)
            // to natively permit unlimited reuse of the same element at the next level layer.
            backtrack(i, remainingTarget - candidates[i], candidates, currentPath, result);

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
Time Complexity: Exponential bounded loosely by O(N^(T/M + 1))
```
*Explanation:* Let $N$ be the number of candidates, $T$ be the target value, and $M$ be the minimum value among candidates. The maximum depth of our search tree occurs when we continuously select the smallest candidate, reaching a height of $T/M$. The branching factor at each step is at most $N$. Because we prune out redundant structural tracks via sorting and bounded loops, the actual operational footprint is highly optimal and well within sub-millisecond ranges for the problem constraints.

---

### Space Complexity

```text
Space Complexity: O(T / M) auxiliary space
```
*Explanation:* Excluding the structural memory blocks required to store and return the final combination list of lists, auxiliary storage space scales linearly to accommodate the maximum height depth of the recursive call stack and the size of the shared `currentPath` buffer container, bounded by $T/M$.

---

# Key Takeaways

✅ Core fundamental paradigm for processing unbounded knapsack and loose coin-change target generation paths.

✅ **The Permutation Avoidance Rule:** To avoid generating duplicate variations of the identical combination pool (e.g., getting both `[2, 2, 3]` and `[3, 2, 2]`), ensure your recursive loop starts processing from the active parameter index (`for (int i = index; ...)`) and passes that index `i` deeper down. Never restart your loops from `0` unless order matters (permutations).

✅ Directly drives real-world microarchitectures like **automated cash register change dispatch systems** (finding optimal combinations of coins to match a cash amount), cargo packing scheduling managers, resource quota dividers, and ledger balancing systems.

✅ Common Architectural Paradigm:

```text
Unbounded Option Branching with Monotonic Sorting Pruning Guards
```

### Similar Problems

- Combination Sum II (Elements can only be used once - [LeetCode 40](https://leetcode.com))
- Combination Sum III ([LeetCode 216](https://leetcode.com))
- Coin Change ([LeetCode 322](https://leetcode.com))
- Subsets / Power Set ([LeetCode 78](https://leetcode.com))

<FollowUp>
Let me know which advanced backtracking sum challenge or array configuration optimization problem you want to explore next:
* Should we configure the raw markdown code for **Combination Sum II ([LeetCode 40](https://leetcode.com))** to see how subset choice changes when elements can only be used once?
* Do you want to examine **Coin Change ([LeetCode 322](https://leetcode.com))** to explore scalar minimum token optimizations using Dynamic Programming?
* Or transition back into an independent data structures track?

Tell me which **key terms** or problem targets you want to tackle next!
</FollowUp>
