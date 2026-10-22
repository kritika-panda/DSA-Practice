# Concatenated Words

## Problem Statement

Given an array of strings `words` (without duplicates), return all the **concatenated words** in the given list of words.

A **concatenated word** is defined as a string that is comprised entirely of at least two shorter words (or same length if duplication was allowed, but here words are unique) in the given array.

The overall run time complexity should be bounded by:

```text
O(n * l^3)
```

*(where n is the number of words and l is the maximum length of a word)*

---

## Examples

### Example 1

**Input**

```java
words = ["cat","cats","catsdogcats","dog","dogs","dogcatsdog","hippopotamuses","rat","ratcatdogcat"]
```

**Output**

```java
["catsdogcats","dogcatsdog","ratcatdogcat"]
```

**Explanation**

- `"catsdogcats"` can be concatenated by `"cats"`, `"dog"`, and `"cats"`.
- `"dogcatsdog"` can be concatenated by `"dog"`, `"cats"`, and `"dog"`.
- `"ratcatdogcat"` can be concatenated by `"rat"`, `"cat"`, `"dog"`, and `"cat"`.

---

### Example 2

**Input**

```java
words = ["cat","dog","catdog"]
```

**Output**

```java
["catdog"]
```

---

### Example 3

**Input**

```java
words = ["a","b","ab","abc"]
```

**Output**

```java
["ab"]
```

**Explanation**

`"ab"` is formed by `"a"` + `"b"`. `"abc"` cannot be formed because `"c"` is not in the array.

---

## Brute Force Approach

Sort words by length and use a standard recursive backtracking method for each word to check if it can be partitioned into any combination of other words in the list.

### Steps

1. Put all words into a Hash Set for \(O(1)\) lookups.
2. For each word, try partitioning it at every possible index `i` from `1` to `len - 1`.
3. If the prefix exists in the set, recursively check if the remaining suffix can be formed by other words.
4. If a word can be successfully broken down into two or more words from the set, append it to the results list.

### Complexity

```text
Time Complexity: O(n * 2^l)
Space Complexity: O(n * l)
```

For long words, testing all exponential partition combinations repeatedly creates a massive processing bottleneck, resulting in Time Limit Exceeded (TLE) errors. We can optimize this using a Trie combined with Depth-First Search (DFS) and memoization.

---

# Optimal Approach: Trie with Word Partitioning DFS

## Key Idea

A **Trie** allows us to efficiently find all valid word prefixes in a single top-down pass instead of executing individual substring lookups.

1. Insert all terms from the `words` list into a Trie.
2. For each word, strip it out of the Trie temporarily (or skip its own terminal node) so it cannot match itself as a single piece.
3. Launch a backtracking DFS to see if the word can be completely formed by other words in the Trie:
   - Walk down the Trie following the characters of the word.
   - Every time we hit a node where `isEndOfWord == true`, we found a valid prefix component. We can branch out and recursively launch a new search from the Trie root to process the remaining suffix.
4. Use a memoization array or set to cache failed suffix paths to ensure we never re-evaluate the same sub-problem twice.

---

## Visual Understanding

Suppose our dictionary contains `"cat"`, `"dog"`, and `"catdog"`.

Trie structure layout:

```text
     (root)
     /    \
   'c'    'd'

    |      |
   'a'    'o'

    |      |
   't'*   'g'*  (cat, dog)
```

When validating `"catdog"`, the algorithm walks down to `'t'` (`isEndOfWord == true`). It marks `"cat"` as a valid split and jumps back to the `root` to evaluate the remaining segment `"dog"`. The remaining path matches `"dog"` completely and finishes at a terminal node, confirming `"catdog"` is a valid concatenated word.

---

## Partition Variables

Let:

```java
class TrieNode {
    TrieNode[] children = new TrieNode;
    boolean isEndOfWord = false;
}
TrieNode root = new TrieNode();
Boolean[] memo;
```

---

### Border Elements

The memoization array handles position states across the word string boundary to avoid redundant sub-path evaluation loops:

```java
if (index == word.length()) return count >= 2;
if (memo[index] != null) return memo[index];
```

---

## Correct Partition Condition

The criteria to confirm that a valid sequence partition path has successfully verified the target layout is:

```java
if (current.isEndOfWord) {
    if (dfs(word, i + 1, count + 1, root, memo)) {
        return memo[index] = true;
    }
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with a segmented character path DFS over a Trie to prune and cache suffix partition permutations in linear time).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    private class TrieNode {
        private TrieNode[] children = new TrieNode;
        private boolean isEndOfWord = false;
    }

    private void insert(String word, TrieNode root) {
        TrieNode current = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (current.children[index] == null) {
                current.children[index] = new TrieNode();
            }
            current = current.children[index];
        }
        current.isEndOfWord = true;
    }

    public List<String> findAllConcatenatedWordsInADict(String[] words) {
        
        TrieNode root = new TrieNode();
        List<String> result = new ArrayList<>();

        // Step 1: Insert all words into the Trie
        for (String word : words) {
            if (!word.isEmpty()) {
                insert(word, root);
            }
        }

        // Step 2: Validate each word using a memoized DFS partition search
        for (String word : words) {
            if (word.isEmpty()) continue;
            
            // Boolean array for caching results at each index position of the word
            Boolean[] memo = new Boolean[word.length()];
            
            if (canForm(word, 0, 0, root, memo)) {
                result.add(word);
            }
        }

        return result;
    }

    private boolean canForm(String word, int index, int count, TrieNode root, Boolean[] memo) {
        // Base case: if we reached the end of the word, check if it's composed of >= 2 words
        if (index == word.length()) {
            return count >= 2;
        }

        // Return cached result if this suffix path was already evaluated
        if (memo[index] != null) {
            return memo[index];
        }

        TrieNode current = root;

        // Traverse the Trie along the characters of the word starting from 'index'
        for (int i = index; i < word.length(); i++) {
            int childIdx = word.charAt(i) - 'a';
            
            if (current.children[childIdx] == null) {
                break; // Path broken, this split layout is invalid
            }
            
            current = current.children[childIdx];
            
            // If a valid word component ends here, try branching out to check the remaining suffix
            if (current.isEndOfWord) {
                if (canForm(word, i + 1, count + 1, root, memo)) {
                    return memo[index] = true;
                }
            }
        }

        return memo[index] = false;
    }
}
```

---

## Dry Run

### Input

```java
words = ["cat", "dog", "catdog"]
```

---

### Step Execution Traversal

1. **Trie Configuration:** `"cat"` and `"dog"` are stored inside the tree. `"catdog"` also registers its complete character path sequence.
2. **Evaluation of word = "catdog":**
   - `canForm("catdog", 0, 0, ...)` is called.
   - Loops from `i = 0` to `5`. Matches path nodes `'c' -> 'a' -> 't'`.
   - At `i = 2` (`'t'`), `current.isEndOfWord` is `true`.
   - Triggers recursive call `canForm("catdog", 3, 1, ...)`.
   - Inside the nested frame, tracking resets to the root to evaluate the suffix `"dog"` from index `3`.
   - Matches path nodes `'d' -> 'o' -> 'g'`.
   - At `i = 5` (`'g'`), `current.isEndOfWord` is `true`.
   - Triggers recursive call `canForm("catdog", 6, 2, ...)`.
   - Index `6 == word.length()`. Count `2 >= 2` holds `true`. Returns `true` up the call stack.

---

### Answer

```java
["catdog"]
```

---

## Why Do We Use a Trie with Memoization?

By validating prefixes dynamically using a Trie instead of running isolated string slicing lookups, we trace valid candidate boundary indices in a single top-down pass. Combining this with a memoization cache ensures that failed suffix paths are discovered exactly once, avoiding combinatorial processing explosions.

This optimized search graph yields:

```text
O(n * l^3) worst-case time complexity
```

which satisfies the efficiency constraints perfectly.

---

## Complexity Analysis

### Time Complexity

```text
O(n * l^3)
```

Where `n` is the number of words and `l` is the maximum length of a word. Building the Trie takes `O(n * l)` time. For each word, the memoized DFS checks at most `l` index states, and the inner loop slices down at most `l` characters, resulting in an `O(l^2)` validation pass per word.

---

### Space Complexity

```text
O(n * l * 26)
```
The underlying data layout allocates tree storage nodes proportional to the count of unique character components present across the entire word list.

## Key Insight

Using a Trie to identify valid component prefixes combined with memoization to remember failed suffix boundaries reduces an exponential string partitioning problem into a clean, polynomial-time dependency check.
```text
Time  : O(n * l^3) worst-case
Space : O(n * l * 26) memory nodes
```
