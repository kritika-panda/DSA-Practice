# All Substrings of a Given String ([LeetCode 647 Variant / GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given a string `s`, generate and return **all possible substrings** of the string. 

A substring is a **contiguous** sequence of characters within a string. The relative chronological order of characters must be preserved, and no characters within the segment can be skipped. 

The output collection can return the substrings in **any order**.

---

## Examples

### Example 1

```text
Input: s = "abc"
Output: ["a", "ab", "abc", "b", "bc", "c"]
```

### Explanation

```text
The total number of contiguous segments for a string of length 3 is (3 * 4) / 2 = 6:
- Starting at index 0: "a", "ab", "abc"
- Starting at index 1: "b", "bc"
- Starting at index 2: "c"
```

---

### Example 2

```text
Input: s = "cd"
Output: ["c", "cd", "d"]
```

---

# Intuition

Unlike a *subsequence* where elements can be skipped dynamically (yielding \(2^N\) choices), a **substring must be a continuous block**. 

Because a substring is locked to a contiguous track, it is uniquely defined by two boundary landmarks: a **starting index (`i`)** and an **ending index (`j`)**. 
- The starting pointer `i` can slide from `0` to `N - 1`.
- For a fixed start position `i`, the ending pointer `j` can extend from `i + 1` up to `N`.

This geometric property simplifies the state-space search:
- The total number of valid pointer pairs (and thus unique substrings) for a string of length N is given by the arithmetic progression formula:
  \[\frac{N \times (N + 1)}{2} = O(N^2)\]
- Since the total combinations scale quadratically rather than exponentially, we do not need complex depth-first backtracking call trees. We can extract every substring cleanly using **Nested Linear Traversal Cursors**.

---

# Approach: Nested Sliding Window Pointers

## Idea

We configure a nested loop architecture. The outer loop positions the `start` anchor pointer at each valid index slot. The inner loop acts as an expanding window boundary, sliding the `end` pointer forward step-by-step to capture every contiguous block. Inside the inner block, we slice out the segment using built-in high-speed utility string methods like `substring(start, end)`.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> getAllSubstrings(String s) {
        List<String> result = new ArrayList<>();
        if (s == null || s.length() == 0) {
            return result;
        }

        int n = s.length();

        // Outer Loop: Shift the starting landmark cursor across the string timeline
        for (int i = 0; i < n; i++) {
            
            // Inner Loop: Expand the lookahead boundary pointer step-by-step
            // 'j' goes up to 'n' because the substring upper boundary is exclusive
            for (int j = i + 1; j <= n; j++) {
                
                // Extract the contiguous slice from index i to j-1
                String sub = s.substring(i, j);
                result.add(sub);
            }
        }

        return result;
    }
}
```

---

## Complexity Analysis

Let N be the total length of the input string `s`.

### Time Complexity

```text
Time Complexity: O(N³)
```
*Explanation:* The nested loop structure pairs indices exactly \(\frac{N(N+1)}{2}\) times, executing in quadratic O(N²) iterations. However, inside the inner loop, Java's `substring()` method allocates a new string object and copies characters sequentially, which takes linear O(N) time. Combining the O(N²) iterations with the inner O(N) string slicing operation yields an overall cubic time complexity of O(N³).

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. Excluding the structural heap allocation blocks required to collect and return the final substring collection lists, the algorithm tracks window boundaries in-place using only primitive integer local variables, keeping auxiliary storage completely flat.

---

# Key Takeaways

✅ Core baseline sliding window template for processing contiguous string stream slices.

✅ **Subsequence vs. Substring Boundaries:** Always identify the structural constraints of the problem right away. If skipping items is permitted, the state space explodes exponentially to **\(2^N\) (Subsequences)**. If the stream blocks must remain strictly contiguous, the combinations shrink dramatically to polynomial **O(N²) (Substrings)**.

✅ Directly drives real-world microarchitectures like **suffix tree builders**, text autocomplete search indexes, DNA genomic sequencing matchers, tokenizers inside compiler lexical analyzers, and rolling hash calculators (like Rabin-Karp).

✅ Common Architectural Paradigm:

```text
Nested Reference Window Expansions for Contiguous Sequence Slicing
```

### Similar Problems

- Palindromic Substrings ([LeetCode 647](https://leetcode.com))
- Longest Palindromic Substring ([LeetCode 5](https://leetcode.com))
- All Possible Subsequences of a String ([LeetCode 78 Variant](https://leetcode.com))
- Longest Substring Without Repeating Characters ([LeetCode 3](https://leetcode.com))
