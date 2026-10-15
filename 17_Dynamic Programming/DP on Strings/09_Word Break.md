# Word Break (LeetCode 139)

## Problem Statement

Given a string `s` and a dictionary of strings `wordDict`, return `true` *if `s` can be segmented into a space-separated sequence of one or more dictionary words*.

**Note:** The same word in the dictionary may be reused multiple times in the segmentation.

---

## Examples

### Example 1

```text
Input: s = "leetcode", wordDict = ["leet","code"]
Output: true
```

### Explanation

```text
Return true because "leetcode" can be segmented as "leet code".
```

---

### Example 2

```text
Input: s = "applepenapple", wordDict = ["apple","pen"]
Output: true
```

### Explanation

```text
Return true because "applepenapple" can be segmented as "apple pen apple".
Note that you are allowed to reuse a dictionary word.
```

---

### Example 3

```text
Input: s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]
Output: false
```

---

# Intuition

This problem can be elegantly solved using **1D Grid Partitioning Dynamic Programming**. 

Instead of searching for random splits across the entire string at once, we solve it incrementally: **Can the prefix of the string up to index `i` be split cleanly into valid dictionary words?**

To determine if `dp[i]` is true:
- We look back at all possible split positions `j` (where `0 <= j < i`).
- For a split position `j` to be valid, two sub-conditions must be satisfied simultaneously:
  1. The prefix up to `j` must already be a valid word combination (`dp[j] == true`).
  2. The remaining substring trailing from `j` to `i` (`s.substring(j, i)`) must exist as a valid entry inside our dictionary.
- If we find *any* index `j` that satisfies both conditions, then the prefix up to `i` is valid (`dp[i] = true`), and we can break early to process the next index.

---

# State Definition

Let `dp[i]` be a boolean flag indicating whether the prefix string `s[0...i-1]` (of length `i`) can be successfully segmented into valid dictionary words.
*   `i`: The length of the active prefix string being evaluated (from `0` to `n`).

---

# Recurrence Relation

```text
dp[i] = true if there exists a j (0 <= j < i) such that:
    dp[j] == true AND wordDict.contains( s.substring(j, i) )
```

---

# Base Cases

An empty prefix string (length `0`) requires zero splits and is considered valid by default to anchor our lookback chain:
```text
dp[0] = true
```

---

# Approach: Bottom-Up 1D Dynamic Programming Tabulation

## Algorithm

1. Convert `wordDict` into a `HashSet` called `wordSet` to achieve $O(1)$ constant-time lookup checks.
2. Initialize a boolean array `dp[n + 1]` with `false`. Set `dp[0] = true`.
3. Run an outer loop `i` from `1` up to `n` tracking the length of the active prefix:
   - Run an inner loop `j` from `0` up to `i - 1` to find valid partition splits:
     - If `dp[j] == true` AND `wordSet.contains(s.substring(j, i))`:
       - Set `dp[i] = true`.
       - Break out of the inner loop early (one valid split configuration is sufficient).
4. Return `dp[n]`.

---

# Java Solution

```java
import java.util.HashSet;
import java.util.List;
import java.util.Set;

class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        if (s == null || s.length() == 0) {
            return true;
        }

        int n = s.length();
        
        // Push dictionary words into a HashSet for O(1) existence checks
        Set<String> wordSet = new HashSet<>(wordDict);
        
        // dp[i] tracks whether the prefix string of length i is segmentable
        boolean[] dp = new boolean[n + 1];
        
        // Base case: An empty string is valid
        dp[0] = true;

        // Build states incrementally using bottom-up tabulation
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                // If prefix s[0...j-1] is valid, check the remaining substring s[j...i-1]
                if (dp[j] && wordSet.contains(s.substring(j, i))) {
                    dp[i] = true;
                    break; // Optimization: Stop checking alternative splits once true
                }
            }
        }

        return dp[n];
    }
}
```

---

## Complexity Analysis

Let:
*   N: The total length of the string `s`.
*   M: The maximum length of a word inside the dictionary (used to optimize string slicing).

### Time Complexity
The nested loop architecture evaluates splits across the string. The outer loop runs $N$ times, and the inner loop checks up to $N$ partition boundaries. Inside, generating substrings costs time proportional to the substring length (bounded by $M$). This yields a standard worst-case runtime complexity of:
```text
Time Complexity: O(N³) or O(N² × M)
```

### Space Complexity
The auxiliary storage is dominated by the flat 1D DP tabulation array of size $N + 1$, along with the hash set container used to store the unique vocabulary strings:
```text
Space Complexity: O(N + total_dict_characters)
```

---

# Key Pattern
This problem belongs to the **1D Continuous Array Partitioning / Substring Splitting** dynamic programming template.

Common indicators:
- Segmenting or partitioning a sequence into chunks that must meet dictionary or block validation properties.
- State layers where solving for index `i` requires looping back to examine all previous boundary cuts `j`.
- Lookback mechanisms utilizing accelerated hash lookups for trailing chunks.

---

# Similar Problems
1. Word Break II (Reconstruct and print all valid sentence paths - LeetCode 140)
2. Palindrome Partitioning (LeetCode 131)
3. Partition Equal Subset Sum (LeetCode 416)
4. Decode Ways (LeetCode 91)

---

# Takeaway
```text
Map continuous string partition choices onto a linear 1D lookback array.

Deduplicate vocabulary lookups by caching dictionary arrays inside a HashSet upfront.

Verify that the preceding prefix slice is true before spending time evaluating the trailing substring.
```

**Pattern:** 1D Continuous Array Partitioning DP

<FollowUp>
We can scale this partition framework to handle more complex text layout rules. If you'd like, let me know if we should map out the raw markdown code for **Word Break II (LeetCode 140)** to see how to reconstruct the sentences using backtracking, or look at **Decode Ways (LeetCode 91)**!
</FollowUp>
