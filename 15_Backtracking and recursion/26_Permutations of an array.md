# Permutations of an Array ([LeetCode 46](https://leetcode.com))

## Problem Statement

Given an array `nums` of **distinct** integers, return *all the possible **permutations***. You can return the answer in **any order**.

A permutation is an arrangement of all elements of the array into different positional configurations. For an array of size N with unique elements, there are exactly N! distinct permutations.

---

## Examples

### Example 1

```text
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

---

### Example 2

```text
Input: nums = [0,1]
Output: [[0,1],[1,0]]
```

---

### Example 3

```text
Input: nums = [1]
Output: [[1]]
```

---

# Intuition

Generating all permutations is a classic **Combinatorial Search** problem that can be modeled as exploring a state-space choice tree. Since all the elements in the input array are guaranteed to be distinct, we can solve this problem optimally using **Backtracking with In-Place Swapping**:

1.  **Positional Anchoring:** We use a pointer `index` to track the current position we are filling in the array. 
2.  **Multi-Way Choice Loop:** For the current `index`, we try swapping the element at `nums[index]` with every single element located from `index` up to `N - 1`. This places a new candidate element into the active slot position. We then recursively advance to process the subsequent position (`index + 1`).
3.  **The Backtrack Swap-Back:** Once the downstream recursive path finishes exploring, we perform the **exact same swap a second time**. This undoes the temporary array mutation, resetting the layout so the loop can cleanly try the next sibling option.
4.  **Why Swapping beats a Lookup Set:** While a standard backtracking approach can use a tracking `boolean[] used` array or a `HashSet` to skip elements already chosen, it consumes extra auxiliary memory. In-place swapping needs **no extra tracking structures**, reducing auxiliary storage to a bare minimum.

---

# State Definition (Optimal Backtracking Approach)

To trace our combinatorial options systematically using inline reference swaps directly on the heap:

```text
backtrack(index, nums):
- 'index' tracks our active position pointer slot being filled in the sequence.
- 'nums' acts as our mutable int[] workspace modified in-place across choices.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Case:
If index == nums.length -> result.add(convertToList(nums)); return

Recursive Controlled Branching:
For i from index to nums.length - 1:
    swap(nums, index, i)       // Place element i into position 'index'
    backtrack(index + 1, nums) // Recurse deeper to fill the rest of the slots
    swap(nums, index, i)       // Backtrack step (Undo the swap mutation)
```

---

# Optimal Backtracking Solution

## Idea

We launch a depth-first search directly on the input `nums` array workspace starting from index position 0. When our `index` pointer reaches the end of the array, a unique positional permutation is finalized, and we convert the primitive state values into a new integer list to save into our global results collection.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        if (nums == null || nums.length == 0) {
            return result;
        }

        // Launch our in-place recursive backtracking pass from index position 0
        backtrack(0, nums, result);
        return result;
    }

    private void backtrack(int index, int[] nums, List<List<Integer>> result) {
        // Base Case: If the position cursor reaches the end, a unique permutation is finalized
        if (index == nums.length) {
            result.add(convertToList(nums)); // Convert primitive array state to a heap list
            return;
        }

        // Iterate through all candidate elements available from 'index' to the end of the array
        for (int i = index; i < nums.length; i++) {
            // Choice Step: Swap element at index 'i' into our active target position 'index'
            swap(nums, index, i);

            // Recurse deeper by advancing our tracking pointer to the subsequent slot position
            backtrack(index + 1, nums, result);

            // Backtrack Step: Swap elements back to restore the original state layout context
            swap(nums, index, i);
        }
    }

    // High-speed array reference element swapper
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }

    // Utility helper to copy primitive int[] elements into a fresh List wrapper object
    private List<Integer> convertToList(int[] nums) {
        List<Integer> list = new ArrayList<>(nums.length);
        for (int num : nums) {
            list.add(num);
        }
        return list;
    }
}
```

---

## Complexity Analysis

Let N be the total length of the input array `nums`.

### Time Complexity

```text
Time Complexity: O(N × N!)
```
*Explanation:* For an array of length N with unique elements, there are exactly N! unique permutations, meaning the base case leaf node is reached exactly N! times. Converting the primitive array into a heap-allocated `List<Integer>` object takes O(N) linear time at each leaf, locking the overall execution runtime complexity to an asymptotic bound of \(O(N \times N!)\).

---

### Space Complexity

```text
Space Complexity: O(N) auxiliary space
```
*Explanation:* Highly optimal auxiliary memory footprint. The array shifts are executed completely in-place directly inside the input `nums` array workspace pool. Auxiliary memory usage scales linearly at O(N) to accommodate the maximum depth height of the recursive call stack (which reaches a maximum height of N).

---

# Key Takeaways

✅ Core fundamental paradigm for processing sequence rearrangements and combinatorial permutations.

✅ **The Double Swap Optimization:** Swapping values before a recursive call (`swap(nums, index, i)`) and running the exact same swap line right after it returns is an elegant design pattern. It lets the algorithm explore permutation permutations without allocating duplicate array buffers or using lookup tracking metrics.

✅ Directly drives real-world microarchitectures like **TSP (Traveling Salesperson) routing engine simulators** evaluating path combinations, brute-force security cryptographic decoders, string anagram generators, and layout optimizer planners.

✅ Common Architectural Paradigm:

```text
Positional Element Swapping with In-Place Reference Context Restorations
```

### Similar Problems

- Permutations II (Handling duplicate input arrays - [LeetCode 47](https://leetcode.com))
- Next Permutation ([LeetCode 31](https://leetcode.com))
- Permutation Sequence ([LeetCode 60](https://leetcode.com))
- Subsets / Power Set ([LeetCode 78](https://leetcode.com))
