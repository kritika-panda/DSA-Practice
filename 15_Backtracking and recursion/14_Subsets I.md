# Subsets / Power Set ([LeetCode 78](https://leetcode.com))

## Problem Statement

Given an integer array `nums` of **unique** elements, return *all possible subsets (the power set)*.

The solution set **must not** contain duplicate subsets. Return the solution in **any order**.

A power set of a set with N elements contains exactly \(2^N\) subsets.

---

## Examples

### Example 1

```text
Input: nums = [1,2,3]
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

### Explanation

```text
All combinations of selecting 0, 1, 2, or all 3 elements are explored, yielding 2³ = 8 distinct subsets.
```

---

### Example 2

```text
Input: nums = [0]
Output: [[],[0]]
```

---

# Intuition

Generating a power set represents a core **Combinatorial Search** problem. For every element in the array, we face a fundamental binary decision choice: **either we include it in the current subset, or we exclude it**.

We can solve this problem elegantly using two main strategies:
1. **Backtracking (Decision Tree DFS):** We construct a recursive state-space tree. At each index level, we branch twice—once by adding `nums[index]` into our running tracking combination path, and once by bypassing it. When our search depth pointer reaches the end of the array, the current path is copied into our global results collection.
2. **Bit Manipulation (Binary Bitmask Counting):** Since there are \(2^N\) subsets, we can map each subset to a unique integer state from `0` to \(2^N - 1\). The binary bit representation of the integer acts as a selector mask. If the j-th bit of integer i is set to `1` (`(i >> j) & 1`), we include `nums[j]` in the i-th subset; if it is `0`, we omit it.

---

# Brute Force Solution (Cascading Iteration)

## Idea

We start with an initial empty list inside our results (`[[]]`). For each element in `nums`, we iterate through all existing subsets recorded in our collection so far, create a duplicate copy of each, append the new element to those copies, and merge them back into the main list. This iterative cascade naturally builds out the \(2^N\) combinations.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        // Start with the base empty subset
        result.add(new ArrayList<>());

        // Cascading iteration pass
        for (int num : nums) {
            int currentSize = result.size();
            for (int i = 0; i < currentSize; i++) {
                // Create a clone copy of the existing subset layout
                List<Integer> newSubset = new ArrayList<>(result.get(i));
                newSubset.add(num); // Invert state by injecting the new item
                result.add(newSubset);
            }
        }
        return result;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** \(O(N \times 2^N)\) because the outer loop runs N times, doubling the size of the results array list inside during each pass, and copying subsets takes linear time relative to element length.
- **Space Complexity:** \(O(N \times 2^N)\) memory allocation footprint to store the total power set elements on the heap.

---

# State Definition (Optimal Backtracking Approach)

To model our combinatorial decision tree explicitly using a clean recursive backtracking framework, we manage states via a shared tracking buffer path:

```text
backtrack(index, currentPath):
- 'index' tracks our current position pointer in the nums array loop.
- 'currentPath' acts as a dynamic state container accumulating active elements on-the-fly.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Case:
If index == nums.length -> result.add(new ArrayList<>(currentPath)); return

Recursive Branch Decisions:
1. Choose to INCLUDE:
   currentPath.add(nums[index])
   backtrack(index + 1, currentPath) // Explore downstream
   currentPath.remove(currentPath.size() - 1) // Undo state change (Backtrack)

2. Choose to EXCLUDE:
   backtrack(index + 1, currentPath) // Explore downstream bypassing index element
```

---

# Optimal Backtracking Solution

## Idea

We launch a depth-first recursive search starting from index 0. By utilizing a single mutable tracking path array list container and cleanly popping off the tail element right after a recursive track completes (the backtrack step), we explore all path permissions without generating wasteful copy garbage on the heap.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        // Launch tracking pass with an empty path and index set to 0
        generateSubsets(0, nums, new ArrayList<>(), result);
        return result;
    }

    private void generateSubsets(int index, int[] nums, List<Integer> currentPath, List<List<Integer>> result) {
        // Base Case: If the search index reaches the end, a unique subset layout is finalized
        if (index == nums.length) {
            result.add(new ArrayList<>(currentPath)); // Deep copy path contents into results
            return;
        }

        // Branch Choice 1: Include the current element inside our active track configuration
        currentPath.add(nums[index]);
        generateSubsets(index + 1, nums, currentPath, result); // Recurse deeper
        
        // Backtrack Step: Undo the state modification to restore contextual alignment
        currentPath.remove(currentPath.size() - 1);

        // Branch Choice 2: Exclude the current element and skip to the subsequent position pointer
        generateSubsets(index + 1, nums, currentPath, result);
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N × 2^N)
```
*Explanation:* There are exactly \(2^N\) distinct subsets in the power set, meaning the base case statement is reached exactly \(2^N\) times. Copying the valid linear `currentPath` into our global `result` collector takes O(N) operations, bounding the overall mathematical time complexity to \(O(N \times 2^N)\).

---

### Space Complexity

```text
Space Complexity: O(N) auxiliary space
```
*Explanation:* Excluding the structural memory blocks required to store and return the final power set output lists, auxiliary storage space scales linearly at O(N) to accommodate the deep recursive call stack and the size of the shared `currentPath` buffer list.

---

# Key Takeaways

✅ Core fundamental paradigm for generating combinatorial subsets and permutations using structural backtracking state trees.

✅ **The Value of Deep Copying:** When saving a path layout into a global collection inside a backtracking engine (`result.add(new ArrayList<>(currentPath))`), you must **ALWAYS** generate a new deep copy shell. Saving the `currentPath` reference directly will store a pointer to a single mutable list that changes continuously, leaving you with a list of empty references at the end of the runtime pipeline.

✅ Directly drives real-world microarchitectures like **hardware knapsack weight configuration selectors**, relational database query optimizer planners evaluating column join permutations, circuit switch simulators, and feature vector subset extraction tools.

✅ Common Architectural Paradigm:

```text
Stateful Binary Decision Branching with Complementary Tail De-allocations
```

### Similar Problems

- Subsets II (Contains duplicate array inputs - [LeetCode 90](https://leetcode.com))
- Permutations ([LeetCode 46](https://leetcode.com))
- Combination Sum ([LeetCode 39](https://leetcode.com))
- Letter Combinations of a Phone Number ([LeetCode 17](https://leetcode.com))
