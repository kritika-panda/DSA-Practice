# Word Break II

## Problem Statement

Given a string `s` and a dictionary of strings `wordDict`, add spaces in `s` to construct a sentence where each word is a valid dictionary word. Return **all such possible sentences** in any order.

The same word in the dictionary may be reused multiple times in the segmentation.

---

## Examples

### Example 1

**Input**

```java
s = "catsanddog"
wordDict = ["cat", "cats", "and", "sand", "dog"]
```

**Output**

```java
["cats and dog", "cat sand dog"]
```

---

### Example 2

**Input**

```java
s = "pineapplepenapple"
wordDict = ["apple", "pen", "applepen", "pine", "pineapple"]
```

**Output**

```java
["pine apple pen apple", "pineapple pen apple", "pine applepen apple"]
```

**Explanation**

Note that you are allowed to reuse a dictionary word.

---

### Example 3

**Input**

```java
s = "catsandog"
wordDict = ["cats", "dog", "sand", "and", "cat"]
```

**Output**

```java
[]
```

---

## Brute Force Approach

Use basic recursive backtracking to try matching every prefix of the string against the dictionary words, branching downwards for the remainder of the string.

### Steps

1. Start at index `0` of the string `s`.
2. Iterate through all possible end indices to extract a prefix substring.
3. If the prefix exists in `wordDict`, recursively invoke the backtracking routine on the remaining suffix string.
4. If the suffix reaches the end of the string, join the path with spaces and log it in a results container.

### Complexity

```text
Time Complexity: O(2^n) // Exponential branching depth
Space Complexity: O(n) for the recursion call stack depth
```

Without caching, this approach repeatedly recalculates identical subproblems for overlapping word boundaries, resulting in catastrophic Time Limit Exceeded (TLE) timeouts. We can optimize this using **Memoized Backtracking**.

---

# Optimal Approach: Memoized Backtracking (DFS + Caching)

## Key Idea

This problem can be modeled as a **Depth-First Search (DFS)** tree. To prevent exponential re-computations of overlapping partitions, we use a `HashMap` to cache results for every unique suffix string.

1. **Dictionary Optimization:** Convert `wordDict` into a `HashSet<String>` to reduce lookup checks from \(O(m)\) to an instant constant \(O(1)\) time.
2. **Memoization Map:** Maintain a `Map<String, List<String>> memo` where the key is a suffix substring of `s`, and the value is a list of all valid sentences that can be formed from that suffix.
3. **DFS Execution Rule:**
   - If the current string `s` is already present in `memo`, return its cached sentences list instantly.
   - If the string `s` is empty `""`, return a list containing an empty string `[""]` as a baseline assembly anchor.
   - Iterate a split pointer from index `1` up to `s.length()`. Slice out the prefix `left = s.substring(0, i)`.
   - If `left` is a valid dictionary word, recursively solve for the right segment `s.substring(i)`.
   - For every sentence returned by the subproblems of the right segment, prepend `left` followed by a space, and add it to our active local sentences tracker.
4. Log the complete local list into `memo` before returning.

---

## Visual Understanding

Suppose `s = "catsanddog"` and `wordDict = ["cat", "cats", "and", "sand", "dog"]`:

- **Root Step:** We process `"catsanddog"`.
  - Slice prefix `"cat"` \(\implies\) valid word! Recurse on remaining suffix `"sanddog"`.
- **Subproblem 1 (`"sanddog"`):**
  - Slice prefix `"sand"` \(\implies\) valid word! Recurse on remaining suffix `"dog"`.
- **Subproblem 2 (`"dog"`):**
  - Slice prefix `"dog"` \(\implies\) valid word! Suffix becomes `""` (Base anchor).
  - Returns `["dog"]`.
  - Cache stores: `"dog" -> ["dog"]`.
- **Return to Subproblem 1:** Appends `"sand"` with `"dog"` \(\implies\) returns `["sand dog"]`.
  - Cache stores: `"sanddog" -> ["sand dog"]`.
- **Return to Root Step:** Appends `"cat"` with `"sand dog"` \(\implies\) adds `"cat sand dog"` to results.

The tracking loops continue horizontally to extract the alternative choice path `"cats and dog"`.

---

## Partition Variables

Let:

```java
Set<String> wordSet = new HashSet<>(wordDict);
Map<String, List<String>> memo = new HashMap<>();
```

---

### Border Elements

The recursion terminates seamlessly when the sliced input breaks down to an empty string anchor baseline:

```java
if (s.isEmpty()) {
    List<String> base = new ArrayList<>();
    base.add("");
    return base;
}
```

---

## Correct Partition Condition

The localized combination loop stitches sentences together dynamically with spaces:

```java
List<String> subResults = dfs(suffix, wordSet, memo);
for (String sub : subResults) {
    String space = sub.isEmpty() ? "" : " ";
    res.add(prefix + space + sub);
}
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;

class Solution {

    public List<String> wordBreak(String s, List<String> wordDict) {
        if (s == null || s.length() == 0 || wordDict == null) {
            return new ArrayList<>();
        }

        // Step 1: Optimize dictionary lookups by using a HashSet
        Set<String> wordSet = new HashSet<>(wordDict);
        // Memoization map to cache structural results of processed suffixes
        Map<String, List<String>> memo = new HashMap<>();

        return dfs(s, wordSet, memo);
    }

    private List<String> dfs(String s, Set<String> wordSet, Map<String, List<String>> memo) {
        // Step 2: Return cached sentences list if suffix was previously encountered
        if (memo.containsKey(s)) {
            return memo.get(s);
        }

        List<String> res = new ArrayList<>();

        // Base case: Anchors space stitching when string is fully consumed
        if (s.isEmpty()) {
            res.add("");
            return res;
        }

        // Step 3: Iterate through prefix slices to partition the string
        for (int i = 1; i <= s.length(); i++) {
            String prefix = s.substring(0, i);

            // If the prefix segment is a valid dictionary word, process the remainder
            if (wordSet.contains(prefix)) {
                String suffix = s.substring(i);
                List<String> subResults = dfs(suffix, wordSet, memo);

                // Step 4: Stitch the current prefix together with subproblem suffix results
                for (String sub : subResults) {
                    String space = sub.isEmpty() ? "" : " ";
                    res.add(prefix + space + sub);
                }
            }
        }

        // Save results to cache memory before returning
        memo.put(s, res);
        return res;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Memoized Backtracking** | **O(2^n)** | **O(2^n)** | In worst-case conditions where the input string contains infinite combinations (e.g., `s = "aaaa"`, dictionary = `["a", "aa", "aaa"]`), the size of the final generated sentences array is inherently exponential. Caching optimizes real-world redundant tracks. |

---

## Key Insight

Caching suffix solutions inside a memoization map caps duplicate execution paths, shifting structural string partition trees into an efficient look-up dynamic programming design.

```text
Time  : O(2^n) worst-case combinatorial bound
Space : O(2^n) sentence output allocation footprint
```

---

## Similar Problems

1. Word Break (139)
2. Palindrome Partitioning (131)
3. Concatenated Words (472)
4. Restore IP Addresses (93)
5. Decode Ways (91)
