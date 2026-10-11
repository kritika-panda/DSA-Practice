# Longest Word in Dictionary

## Problem Statement

Given an array of strings `words`, return the longest word in `words` that can be built one character at a time by other words in `words`.

If there is more than one possible answer, return the longest word with the smallest lexicographical order. If there is no answer, return an empty string `""`.

The overall run time complexity should be:

```text
O(n * l)
```

*(where n is the number of words and l is the average length of a word)*

---

## Examples

### Example 1

**Input**

```java
words = ["w", "wo", "wor", "worl", "world"]
```

**Output**

```java
"world"
```

**Explanation**

The word "world" can be built one character at a time by "w", "wo", "wor", and "worl".

---

### Example 2

**Input**

```java
words = ["a", "banana", "app", "appl", "ap", "apply", "apple"]
```

**Output**

```java
"apple"
```

**Explanation**

Both "apple" and "apply" can be built one character at a time by their prefixes. However, "apple" is lexicographically smaller than "apply", so it is returned.

---

### Example 3

**Input**

```java
words = ["banana"]
```

**Output**

```java
""
```

---

## Brute Force Approach

Sort the words by length and use a Hash Set to check if every prefix of a word exists in the array.

### Steps

1. Place all words into a Hash Set for O(1) lookups.
2. For each word, check all of its prefixes starting from length 1 up to length `len - 1`.
3. If all prefixes are present in the set, compare the word with the current best answer.
4. Update the answer if the current word is longer, or if it is equal in length but lexicographically smaller.

### Complexity

```text
Time Complexity: O(n * l^2)
Space Complexity: O(n * l)
```

Generating substrings for every prefix creates extra string overhead, scaling the lookup time quadratically by word length. We can optimize this down to linear time using a Trie.

---

# Optimal Approach: Trie with Prefix Validation

## Key Idea

A **Trie** is the ideal data structure for this problem because prefixes are natively embedded along its branches.

1. Insert all words into a Trie.
2. For each word, traverse down the Trie node path character by character.
3. At each node along the path, verify if the `isEndOfWord` flag is set to `true`. This guarantees that every prefix of the word exists independently in the dictionary.
4. If a word passes this full prefix path verification, compare it against our current best answer to determine if it is longer or lexicographically smaller.

---

## Visual Understanding

Suppose:

```java
words = ["w", "wo", "wor", "worl", "world"]
```

When building the Trie, the nodes are flagged as complete words at every character increment:

```text
  (root)
    |
   'w'*
    |
   'o'*
    |
   'r'*
    |
   'l'*
    |
   'd'*
```

When validating `"world"`, we step through `'w'`, `'o'`, `'r'`, `'l'`, and `'d'`. Since every node along this path has `isEndOfWord == true`, the word is valid.

---

## Partition Variables

Let:

```java
TrieNode root = new TrieNode();
String longestWord = "";
```

---

### Border Elements

The TrieNode tracks child pointers and word status:

```java
class TrieNode {
    TrieNode[] children = new TrieNode;
    boolean isEndOfWord = false;
}
```

---

## Correct Partition Condition

During path validation for a given word:

```java
for (char c : word.toCharArray()) {
    current = current.children[c - 'a'];
    if (current == null || !current.isEndOfWord) {
        return false;
    }
}
return true;
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with a character path scan inside a Trie to ensure linear-time validation).*

---

## Java Solution

```java
class Solution {

    private class TrieNode {
        TrieNode[] children = new TrieNode;
        boolean isEndOfWord = false;
    }

    private TrieNode root = new TrieNode();

    private void insert(String word) {
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

    private boolean hasAllPrefixes(String word) {
        TrieNode current = root;
        for (char c : word.toCharArray()) {
            current = current.children[c - 'a'];
            // If any prefix is missing in the dictionary, return false
            if (current == null || !current.isEndOfWord) {
                return false;
            }
        }
        return true;
    }

    public String longestWord(String[] words) {
        
        // Step 1: Insert all words into the Trie
        for (String word : words) {
            insert(word);
        }

        String result = "";

        // Step 2: Validate each word and find the optimal candidate
        for (String word : words) {
            if (hasAllPrefixes(word)) {
                // Check if current word is longer, or lexicographically smaller if lengths tie
                if (word.length() > result.length() || 
                   (word.length() == result.length() && word.compareTo(result) < 0)) {
                    result = word;
                }
            }
        }

        return result;
    }
}
```

---

## Dry Run

### Input

```java
words = ["a", "ap", "app", "banana"]
```

---

### Step Execution Traversal

1. **Insertion Phase:** All words are mapped into the Trie.
2. **Validation Phase:**
   - **word = "a":** Path node `'a'` has `isEndOfWord = true`. Valid. `result = "a"`.
   - **word = "ap":** Path nodes `'a'` and `'p'` both have `isEndOfWord = true`. Valid. Length 2 > 1, update `result = "ap"`.
   - **word = "app":** Path nodes `'a'`, `'p'`, `'p'` all have `isEndOfWord = true`. Valid. Length 3 > 2, update `result = "app"`.
   - **word = "banana":** Path nodes `'b'`, `'a'`, `'n'`, etc. are checked. Node `'b'` has `isEndOfWord = false`. Path validation fails immediately.

---

### Answer

```java
"app"
```

---

## Why Do We Use a Trie for Prefix Validation?

A Trie structures character sequences along contiguous physical pathways. Instead of performing expensive string substring slicing and isolated lookup calls, we scan word paths in a single pass to confirm prefix dependencies in linear time.

This path tracing yields:

```text
O(n * l)
```

which satisfies the optimal complexity constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n * l)
```

Where `n` is the number of words and `l` is the average length of a word. Inserting all words takes `O(n * l)` time, and validating them requires an additional `O(n * l)` pass.

---

### Space Complexity

```text
O(n * l * 26)
```

In the worst-case scenario, the Trie retains separate node paths for every unique character cluster present in the dictionary.

---

## Key Insight

By embedding dictionary entries into a shared prefix tree, verifying long character chains transforms from a quadratic substring search into a simple tracking walk across node links.

```text
Time  : O(n * l)
Space : O(n * l * 26)
```

---

## Similar Problems

1. Implement Trie (Prefix Tree) (208)
2. Design Add and Search Words Data Structure (211)
3. Word Search II (212)
4. Replace Words (648)
5. Longest Common Prefix (14)
