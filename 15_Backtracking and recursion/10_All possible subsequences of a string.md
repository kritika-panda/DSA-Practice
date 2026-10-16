# All Possible Subsequences of a String ([LeetCode 78 Variant / GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given a string `s`, your task is to generate and return **all possible subsequences** of the string. 

A subsequence of a string is a new string generated from the original string by deleting some (or zero) characters without changing the relative order of the remaining characters. 

The output array should contain the subsets. It can include the **empty string `""`** as a valid subsequence and can be returned in **any order**.

---

## Examples

### Example 1

```text
Input: s = "abc"
Output: ["", "c", "b", "bc", "a", "ac", "ab", "abc"]
```

### Explanation

```text
There are 2³ = 8 unique ways to pick or skip characters from "abc":
- Skip all: ""
- Pick only 'c': "c"
- Pick only 'b': "b"
- Pick 'b' and 'c': "bc"
... and so on, generating the complete power set.
```

---

### Example 2

```text
Input: s = "a"
Output: ["", "a"]
```

---

# Intuition

Generating all subsequences of a string is a fundamental **Combinatorial Search** problem. It can be modeled as a state-space binary tree where we step through each character from left to right. 

For every character index position, we face an invariant **binary decision**:
1. **Include** the character at the current index into our subset path.
2. **Exclude** (skip) the character at the current index.

```text
                  [Root: "abc"]
                   /         \
            Include 'a'      Exclude 'a'
              /     \          /     \
          Inc 'b' Exc 'b'   Inc 'b' Exc 'b'
```

Since we make 2 choices for each of the N characters, a string of length N will yield exactly **\(2^N\) possible subsequences** (including the empty string). Because we explicitly need to collect every path rather than just counting them, **Backtracking** via Depth-First Search (DFS) is the optimal strategy.

---

# Brute Force Solution (Iterative Cascading)

## Idea

We start our results collection with a single empty string `[""]`. We then iterate through each character of the string. For each character, we loop through all the subsequences generated so far, create a copy of each, append the current character to those copies, and merge them back into our main list. This cascading expansion doubles our results size during each step.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> powerSet(String s) {
        List<String> result = new ArrayList<>();
        // Seed with the base empty string
        result.add("");

        // Process each character sequentially
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            int currentSize = result.size();
            
            // Duplicate and append 'ch' to every existing substring layout
            for (int j = 0; j < currentSize; j++) {
                result.add(result.get(j) + ch);
            }
        }
        return result;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** \(O(N \times 2^N)\) because the outer loop runs N times, doubling the results array list size during each pass, and copying strings scales with the length of the string.
- **Space Complexity:** \(O(N \times 2^N)\) memory allocation footprint to preserve the total power set elements concurrently on the heap.

---

# State Definition (Optimal Backtracking Approach)

To formalise our paths inside a clean, controlled recursive backtracking framework, we track state parameters via two contextual values:

```text
backtrack(index, currentPath):
- 'index' tracks our current active pointer position in the string 's'.
- 'currentPath' acts as a shared mutable StringBuilder buffer tracking the active state configuration.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Case:
If index == s.length() -> result.add(currentPath.toString()); return

Recursive Controlled Branching:
1. Choose to INCLUDE:
   currentPath.append(s.charAt(index))
   backtrack(index + 1, currentPath)
   currentPath.deleteCharAt(currentPath.length() - 1) // Undo state change (Backtrack)

2. Choose to EXCLUDE:
   backtrack(index + 1, currentPath)
```

---

# Optimal Controlled Backtracking Solution

## Idea

We launch a depth-first recursive search starting from index 0. By utilizing a single mutable `StringBuilder` tracking container and cleanly popping off the tail character right after a recursive track completes (the backtrack step), we explore all path permissions without generating wasteful copy garbage on the heap.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> allSubsequences(String s) {
        List<String> result = new ArrayList<>();
        if (s == null) {
            return result;
        }

        // Shared mutable string builder workspace to eliminate garbage heap collections
        StringBuilder currentPath = new StringBuilder();

        // Launch backtracking pass from the 0-th index position
        generateSubsequences(0, s, currentPath, result);
        return result;
    }

    private void generateSubsequences(int index, String s, StringBuilder currentPath, List<String> result) {
        // Base Case: If the index matches string length, a unique subsequence is finalized
        if (index == s.length()) {
            result.add(currentPath.toString()); // Convert buffer to immutable string
            return;
        }

        // Choice 1: INCLUDE the current character in our active path track
        currentPath.append(s.charAt(index));
        generateSubsequences(index + 1, s, currentPath, result); // Recurse deeper
        
        // Backtrack Step: Remove the trailing character to clean the state for the next branch
        currentPath.deleteCharAt(currentPath.length() - 1);

        // Choice 2: EXCLUDE the current character and skip directly downstream
        generateSubsequences(index + 1, s, currentPath, result);
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N × 2^N)
```
*Explanation:* There are exactly \(2^N\) combinations in the power set, meaning the base case leaf node statement is reached exactly \(2^N\) times. Converting the linear mutable `StringBuilder` into an immutable `String` takes O(N) operations, bounding the overall execution timeline strictly to \(O(N \times 2^N)\).

---

### Space Complexity

```text
Space Complexity: O(N) auxiliary space
```
*Explanation:* Excluding the structural memory blocks required to store and return the final \(2^N\) string result collection, auxiliary storage space scales linearly at O(N) to accommodate the recursive call stack depth and the max size of the shared `StringBuilder` path buffer.

---

# Key Takeaways

✅ Core baseline template for generating combinations and power sets from streaming token sequences.

✅ **The String Reversal Order Rule:** In standard subsequences, the relative ordering of elements **must** be preserved. Backtracking from left to right naturally enforces this invariant. Sorting or shuffling the characters beforehand or switching lookup steps randomly will destroy the chronological context.

✅ Directly drives real-world microarchitectures like **wildcard text regex pattern matchers**, compiler token parsers, genomic sequence variant analysis tools, and file difference engines.

✅ Common Architectural Paradigm:

```text
Binary State-Space Branching Trees with Complementary Tail De-allocations
```

### Similar Problems

- Subsets / Power Set ([LeetCode 78](https://leetcode.com))
- Subsets II (Handling duplicate element constraints - [LeetCode 90](https://leetcode.com))
- Letter Combinations of a Phone Number ([LeetCode 17](https://leetcode.com))
- Generate Parentheses ([LeetCode 22](https://leetcode.com))
