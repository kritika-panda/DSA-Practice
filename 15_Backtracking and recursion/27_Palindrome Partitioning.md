# Palindrome Partitioning ([LeetCode 131](https://leetcode.com))

## Problem Statement

Given a string `s`, partition `s` such that every substring of the partition is a **palindrome**. Return *all possible palindrome partitioning combinations of `s`*.

A palindrome is a string that reads the same backward as forward.

---

## Examples

### Example 1

```text
Input: s = "aab"
Output: [["a","a","b"],["aa","b"]]
```

### Explanation

```text
The two valid ways to segment "aab" such that every substring is palindromic are:
1. ["a", "a", "b"] -> "a", "a", and "b" are all individual palindromes.
2. ["aa", "b"]     -> "aa" and "b" are both valid palindromes.
```

---

### Example 2

```text
Input: s = "a"
Output: [["a"]]
```

---

# Intuition

This problem represents a classic **Combinatorial Splitting Search** that maps cleanly onto a state-space decision tree. We need to find every single valid way to chop up a string into pieces under a strict palindromic constraint.

We can systematically find all valid partition paths using **Backtracking (Depth-First Search)**:
1. **Slicing Boundaries:** We use a `start` pointer to track the beginning of the active string segment we are evaluating. We then slide an ending pointer `i` from `start` to `s.length() - 1` to carve out a candidate substring: `s.substring(start, i + 1)`.
2. **Palindromic Verification (Pruning):** Before descending down a recursive track, we run a high-speed two-pointer lookup check on the sliced candidate substring. If it is **not** a palindrome, this choice is completely invalid, so we **prune that branch instantly** and skip to the next index layout.
3. **Recursive Exploration:** If the candidate chunk is a valid palindrome, it is safe to proceed. We append this substring to our dynamic path buffer container and recursively advance our `start` cursor forward to the next index position (`i + 1`) to process the remaining characters.
4. **The Backtrack Pop:** Once the downstream recursive sub-trees are fully mapped out, we pop the trailing substring off our path buffer workspace. This resets our state variables, allowing sibling branches to evaluate alternative partition cuts smoothly.
5. **Base Case Victory:** If our `start` cursor reaches the end of the string (`start == s.length()`), it means the entire string has been successfully and cleanly sliced into palindrome segments, so we save a deep copy of our path buffer into the global results collection.

---

# State Definition (Optimal Backtracking Approach)

To trace our combinatorics states systematically within a recursive state-space decision tree using a shared mutable buffer:

```text
backtrack(start, currentPath):
- 'start' tracks the current active pointer index where our next substring slice begins.
- 'currentPath' acts as a shared mutable array list container accumulating active palindrome slices.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Case:
If start == s.length() -> result.add(new ArrayList<>(currentPath)); return

Recursive Controlled Branching:
For i from start to s.length() - 1:
    If isPalindrome(s, start, i):
        currentPath.add(s.substring(start, i + 1))
        backtrack(i + 1, currentPath) // Recurse onto remaining suffix string
        currentPath.remove(currentPath.size() - 1) // Backtrack step (Undo slice choice)
```

---

# Optimal Backtracking Solution

## Idea

The implementation pairs a linear expansion lookahead loop with an in-place two-pointer palindrome checker. By leveraging a single mutable `ArrayList` workspace for string segments, it avoids generating memory garbage during the recursive phase, copying data only when a final valid leaf node is achieved.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> result = new ArrayList<>();
        // Guard Condition: An empty string query returns an empty collection wrapper
        if (s == null || s.length() == 0) {
            return result;
        }

        // Shared mutable path buffer workspace to prevent heap memory thrashing
        List<String> currentPath = new ArrayList<>();

        // Launch recursive backtracking pass starting from index position 0
        backtrack(0, s, currentPath, result);
        return result;
    }

    private void backtrack(int start, String s, List<String> currentPath, List<List<String>> result) {
        // Base Case: If the start cursor reaches the end, a unique valid partition layout is finalized
        if (start == s.length()) {
            result.add(new ArrayList<>(currentPath)); // Deep copy current buffer contents into results
            return;
        }

        // Slide the lookahead ending pointer 'i' forward step-by-step
        for (int i = start; i < s.length(); i++) {
            // Pruning Invariant: Check if the segment from 'start' to 'i' forms a valid palindrome
            if (isPalindrome(s, start, i)) {
                // Choice Step: Squeeze out the palindromic slice and append it to our buffer
                currentPath.add(s.substring(start, i + 1));

                // Recurse deeper by moving our starting boundary cursor right past the active cut
                backtrack(i + 1, s, currentPath, result);

                // Backtrack Step: Remove trailing slice to clean state variables for the next branch
                currentPath.remove(currentPath.size() - 1);
            }
        }
    }

    // High-speed two-pointer inline palindrome verifier
    private boolean isPalindrome(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                return false; // Mismatch located, not a palindrome
            }
            left++;
            right--;
        }
        return true;
    }
}
```

---

## Complexity Analysis

Let N be the total character length of the input string `s`.

### Time Complexity

```text
Time Complexity: O(N × 2^N) worst case
```
*Explanation:* In the absolute worst-case scenario where every single character in the string is identical (e.g., `"aaaa"`), there exist \(2^{N-1}\) possible ways to slice the string into partition segments. At each of these states, checking for a palindrome takes up to O(N) operations, bounding the overall execution timeline strictly inside \(O(N \times 2^N)\) parameters.

---

### Space Complexity

```text
Space Complexity: O(N) auxiliary space
```
*Explanation:* Highly optimal. The string slices are handled completely in-place on the fly. Auxiliary memory usage scales linearly at O(N) to accommodate the maximum depth height of the recursive call stack and the size of the shared `currentPath` buffer container.

---

# Key Takeaways

✅ Core fundamental paradigm for processing combinatorial string segmentation and chunk constraints.

✅ **The Value of Controlled Pruning:** Instead of generating all possible multi-slice permutations of substrings and validating them at the leaf nodes, running the check `if (isPalindrome(...))` directly inside the loop cuts off invalid branch trees immediately, optimizing search speeds.

✅ Directly drives real-world microarchitectures like **natural language processing tokenizers** (splitting text streams into valid syllable or base vocabulary blocks), genomic DNA sequencing sequence parsers, data transmission block filters, and cryptographic text processors.

✅ Common Architectural Paradigm:

```text
Linear Segment Choice Trees with Controlled Constraint-Guided Structural Prunings
```

### Similar Problems

- Palindrome Partitioning II (Finding minimum partition cuts using DP - [LeetCode 132](https://leetcode.com))
- Restore IP Addresses ([LeetCode 93](https://leetcode.com))
- Word Break II ([LeetCode 140](https://leetcode.com))
- Subsets / Power Set ([LeetCode 78](https://leetcode.com))
