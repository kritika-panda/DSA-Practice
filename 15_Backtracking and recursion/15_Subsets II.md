# Subsets II ([LeetCode 90](https://leetcode.com))

## Problem Statement

Given an integer array `nums` that may contain **duplicates**, return *all possible subsets (the power set)*.

The solution set **must not** contain duplicate subsets. Return the solution in **any order**.

---

## Examples

### Example 1

```text
Input: nums = [1,2,2]
Output: [[],[1],[1,2],[1,2,2],[2],[2,2]]
```

### Explanation

```text
The duplicates inside the input block create potential overlap vectors. 
By managing identical items carefully, we extract exactly 6 distinct subset combinations.
```

---

### Example 2

```text
Input: nums = [0]
Output: [[],[0]]
```

---

# Intuition

This problem expands on the baseline **Subsets I** pattern. When an array contains duplicate elements, a standard unconstrained binary selection process will explore duplicate paths and generate identical subsets (e.g., picking the first `2` looks identical to picking the second `2`).

To avoid generating these redundant duplicates and filtering them out via an expensive post-processing collection like a `HashSet`, we apply a **Contiguous Duplicate Pruning Rule**:
1. **Sort the array `nums` up front.** This groups all identical elements together into a single contiguous block (e.g., `[1, 2, 2]`).
2. **Control the Decision Tree Branches:** Instead of doing a strict binary include/exclude choice at each individual step, we loop through all possible candidates available for the *next* slot in the subset.
3. Inside this selection loop, if we see that a candidate matches its immediate predecessor (`nums[i] == nums[i - 1]`), and it is *not* the first option we are looking at for this position level (`i > index`), we **skip it (pruning)**. This ensures that a duplicate value is selected at most once per position slot in the subset.

---

# State Definition (Optimal Backtracking Approach)

To trace our combinatorial decision choices using a clean recursive backtracking state tree:

```text
backtrack(index, currentPath):
- 'index' tracks our active position pointer inside the sorted nums array.
- 'currentPath' acts as a shared mutable array list container accumulating active choices.
```

---

# Recurrence / Structural Backtracking Invariant

```text
At each recursive frame level:
result.add(new ArrayList<>(currentPath)) // Every state reached is a valid subset

Loop i from index to nums.length - 1:
    If i > index AND nums[i] == nums[i - 1] -> Continue (Duplicate Guard Invariant)
    
    currentPath.add(nums[i])
    backtrack(i + 1, currentPath) // Advance to i + 1 to prevent reuse of same element position
    currentPath.remove(currentPath.size() - 1) // Backtrack step
```

---

# Optimal Backtracking Solution with Duplicate Pruning

## Idea

We sort the array up front to align duplicate elements. We launch a depth-first search starting from index 0. Because every single state node we arrive at in our decision tree represents a unique, valid subset, we add a deep copy of `currentPath` to our global result list at the absolute beginning of each function call.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        if (nums == null || nums.length == 0) {
            return result;
        }

        // Step 1: Sort the array up front to group duplicates and enable clean pruning
        Arrays.sort(nums);

        // Shared mutable path buffer workspace to prevent memory thrashing
        List<Integer> currentPath = new ArrayList<>();

        // Step 2: Launch recursive backtracking pass from index 0
        backtrack(0, nums, currentPath, result);
        return result;
    }

    private void backtrack(int index, int[] nums, List<Integer> currentPath, List<List<Integer>> result) {
        // Core Invariant: Every single node reached in the decision tree represents a unique combination
        result.add(new ArrayList<>(currentPath)); // Deep copy current buffer contents into results

        // Sweep forward through candidates starting from the current level 'index'
        for (int i = index; i < nums.length; i++) {
            // Pruning Invariant: Skip duplicate elements at the same recursion depth level 
            // to completely protect against generating duplicate combination arrays
            if (i > index && nums[i] == nums[i - 1]) {
                continue;
            }

            // Choice Step: Include the element in our active choice profile
            currentPath.add(nums[i]);

            // Recurse deeper. We pass 'i + 1' as the next index parameter
            // to ensure each independent item is selected at most once.
            backtrack(i + 1, nums, currentPath, result);

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
Time Complexity: O(N × 2^N) worst case
```
*Explanation:* In the worst case where all elements are entirely unique, there are exactly \(2^N\) subsets, causing our function to be called \(2^N\) times. Copying the linear `currentPath` into our global result collector takes O(N) operations, bounding the overall execution timeline strictly to \(O(N \times 2^N)\).

---

### Space Complexity

```text
Space Complexity: O(N) auxiliary space
```
*Explanation:* Excluding the structural memory blocks required to store and return the final combination list of lists, auxiliary storage space scales linearly at O(N) to accommodate the maximum depth of the recursive call stack and the size of the shared `currentPath` buffer container.

---

# Key Takeaways

✅ Core fundamental paradigm for processing bounded combinatorial power sets with duplicate element constraints.

✅ **The Level-Skip Principle:** The check `if (i > index && nums[i] == nums[i - 1]) continue;` allows duplicates to be chosen sequentially across *different* depth levels (e.g., generating `[2, 2]`), but forbids choosing identical duplicate peers horizontally at the *same* depth level. This isolates identical elements from stepping onto each other's choice tracks.

✅ Directly drives real-world microarchitectures like **file directory compression filters**, billing combination selectors, relational database unique constraint index validators, and inventory assembly controllers.

✅ Common Architectural Paradigm:

```text
Contiguous Sibling Choice Tree Pruning via Prefix Sorting and Horizontal Skip Invariants
```

### Similar Problems

- Subsets / Power Set ([LeetCode 78](https://leetcode.com))
- Combination Sum II ([LeetCode 40](https://leetcode.com))
- Permutations II ([LeetCode 47](https://leetcode.com))
- Palette Swapping Algorithms
