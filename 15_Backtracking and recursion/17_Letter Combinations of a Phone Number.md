# Letter Combinations of a Phone Number ([LeetCode 17](https://leetcode.com))

## Problem Statement

Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent. Return the answer in **any order**.

A mapping of digits to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

| Digit | Letters | Digit | Letters |
| :--- | :--- | :--- | :--- |
| **2** | a, b, c | **6** | m, n, o |
| **3** | d, e, f | **7** | p, q, r, s |
| **4** | g, h, i | **8** | t, u, v |
| **5** | j, k, l | **9** | w, x, y, z |

---

## Examples

### Example 1

```text
Input: digits = "23"
Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

---

### Example 2

```text
Input: digits = ""
Output: []
```

---

### Example 3

```text
Input: digits = "2"
Output: ["a","b","c"]
```

---

# Intuition

This problem represents a classical **Combinatorial String Generation** problem that maps cleanly onto a state-space decision tree. At each index slot of the input string `digits`, the current number maps to a specific pool of characters. 

We can systematically explore all permutation combinations using **Backtracking (Depth-First Search)**:
1. **Map Retrieval:** We track our position pointer using an `index` state variable. For the current digit `digits.charAt(index)`, we look up its mapped string of letters (e.g., `'2'` maps to `"abc"`).
2. **Multi-Way Branching:** We loop through every character in that mapped letter cluster. For each character, we append it to our mutable tracking buffer, increment our index pointer by 1 (`index + 1`), and recurse deeper to evaluate the subsequent digit slot.
3. **The Backtrack Cleanup:** Once the downstream recursive path finishes, we pop the trailing character off our buffer workspace to restore structural context, freeing up the slot for the next letter in the current button cluster.

---

# State Definition (Optimal Backtracking Approach)

To trace our combinatorics states systematically within a recursive state-space decision tree, we manage states using two parameters:

```text
backtrack(index, currentPath):
- 'index' tracks our active position pointer inside the input digits string.
- 'currentPath' acts as a shared mutable StringBuilder container accumulating active character paths.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Case:
If index == digits.length() -> result.add(currentPath.toString()); return

Recursive Controlled Branching:
String letters = KEYPAD_MAPPING[digits.charAt(index) - '0']
For char letter : letters.toCharArray():
    currentPath.append(letter)
    backtrack(index + 1, currentPath)
    currentPath.deleteCharAt(currentPath.length() - 1) // Backtrack step
```

---

# Optimal Backtracking Solution

## Idea

We initialize a static array directory to cache the telephone button keypad mappings for O(1) constant-time character cluster lookups. We launch our depth-first search starting from index 0. By utilizing a shared `StringBuilder` buffer workspace, the algorithm explores all unique letter combinations without generating wasteful intermediate objects on the heap.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    // Static lookup map caching telephone button keypad clusters for O(1) retrieval
    private static final String[] KEYPAD_MAPPING = {
        "",     // 0
        "",     // 1
        "abc",  // 2
        "def",  // 3
        "ghi",  // 4
        "jkl",  // 5
        "mno",  // 6
        "pqrs", // 7
        "tuv",  // 8
        "wxyz"  // 9
    };

    public List<String> letterCombinations(String digits) {
        List<String> result = new ArrayList<>();
        
        // Guard Condition: An empty string query returns an empty collection wrapper
        if (digits == null || digits.length() == 0) {
            return result;
        }

        // Shared mutable string builder workspace to eliminate heap memory thrashing
        StringBuilder currentPath = new StringBuilder();

        // Launch recursive backtracking pass from digit index 0
        backtrack(0, digits, currentPath, result);
        return result;
    }

    private void backtrack(int index, String digits, StringBuilder currentPath, List<String> result) {
        // Base Case: If the position index matches digits length, a unique combination is finalized
        if (index == digits.length()) {
            result.add(currentPath.toString()); // Convert buffer workspace to immutable string
            return;
        }

        // Fetch the letter options mapped to the current digit key
        int digitNode = digits.charAt(index) - '0';
        String letters = KEYPAD_MAPPING[digitNode];

        // Multi-Way Branching: Iterate through each letter option in the cluster
        for (int i = 0; i < letters.length(); i++) {
            char letter = letters.charAt(i);

            // Choice Step: Append the active letter option to our tracking path buffer
            currentPath.append(letter);

            // Recurse deeper by advancing our tracking pointer to the next subsequent digit slot
            backtrack(index + 1, digits, currentPath, result);

            // Backtrack Step: Delete the trailing character to clean state variables for the next branch
            currentPath.deleteCharAt(currentPath.length() - 1);
        }
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(4^N) worst case
```
*Explanation:* Let N be the length of the input digits string. In the worst-case configuration, digits like `'7'` and `'9'` contain exactly 4 mapped letters each. The maximum possible leaf combinations in the state-space decision tree is bounded by \(4^N\). Converting the linear `StringBuilder` to an immutable string takes O(N) operations at each leaf, keeping the overall worst-case execution time tightly bounded by \(O(N \cdot 4^N)\) boundaries.

---

### Space Complexity

```text
Space Complexity: O(N) auxiliary space
```
*Explanation:* Excluding the structural memory blocks required to store and return the final combination list, auxiliary storage space scales linearly at O(N) to accommodate the maximum height depth of the recursive call stack and the size of the shared `currentPath` buffer container.

---

# Key Takeaways

✅ Core fundamental paradigm for processing multi-way branch maps and permutation strings.

✅ **The Value of In-Place Buffer Sharing:** Utilizing a single `StringBuilder` buffer workspace coupled with trailing tail deletions (`currentPath.deleteCharAt(...)`) is a critical backtracking optimization. This keeps memory allocation costs tightly clamped, preventing the program from generating garbage copies at every branch level.

✅ Directly drives real-world microarchitectures like **T9 predictive text entry algorithms** on cellular keypads, automated phone tree directory routing menus, mnemonic passphrase generators, and combinatorial token parsers.

✅ Common Architectural Paradigm:

```text
Directory-Mapped Multi-Way Branching Trees with Mutable String Buffer Inversions
```

### Similar Problems

- Generate Parentheses ([LeetCode 22](https://leetcode.com))
- Combination Sum ([LeetCode 39](https://leetcode.com))
- Subsets / Power Set ([LeetCode 78](https://leetcode.com))
- Word Search ([LeetCode 79](https://leetcode.com))
