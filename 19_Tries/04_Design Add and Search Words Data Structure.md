# Design Add and Search Words Data Structure

## Problem Statement

Design a data structure that supports adding new words and finding if a string matches any previously added string.

The class should implement the `WordDictionary` class:
1. `void addWord(word)`: Adds `word` to the data structure.
2. `boolean search(word)`: Returns `true` if there is any string in the data structure that matches `word` or `false` otherwise. `word` may contain dots `'.'` where dots can be matched with any letter.

The overall run time complexity for standard search should be:

```text
O(l)
```

*(where l is the length of the word being searched, up to O(26^m) under wildcard search where m is the count of dots)*

---

## Examples

### Example 1

**Input**

```java
WordDictionary wordDictionary = new WordDictionary();
wordDictionary.addWord("bad");
wordDictionary.addWord("dad");
wordDictionary.addWord("mad");
wordDictionary.search("pad"); // return False
wordDictionary.search("bad"); // return True
wordDictionary.search(".ad"); // return True
wordDictionary.search("b.."); // return True
```

---

## Brute Force Approach

Store all words inside a Hash Set collection.

### Steps

1. `addWord(word)`: Insert the string directly into the Hash Set.
2. `search(word)`: If the search string contains no wildcards, verify existence using an \(O(1)\) set lookup. If wildcards `'.'` are present, loop through every single string in the collection and verify if it matches the character layout.

### Complexity

```text
Time Complexity: O(1) for addition, O(n * l) for wildcard search
Space Complexity: O(n * l)
```

Looping over the entire collection for wildcard search causes a performance bottleneck if the structure contains many words. We can optimize lookup paths using a Trie combined with backtracking.

---

# Optimal Approach: Trie with Backtracking

## Key Idea

A **Trie** is the ideal foundational structural grid to isolate word lookup trees. However, to handle wildcard characters `'.'`, we introduce a **backtracking DFS search** across child references.

- When we encounter a regular character like `'a'`, we navigate to its deterministic child pointer slot matching index `c - 'a'`.
- When we encounter a wildcard character `'.'`, it means any existing character path is acceptable. We must branch out and recursively inspect **all 26 potential child nodes**. If any of these parallel child paths successfully complete the word match, we return `true`.

---

## Visual Understanding

Suppose we run the following sequence:

```java
wordDictionary.addWord("bad");
wordDictionary.search(".ad");
```

Trie structural map:

```text
  (root)
    |
   'b'
    |
   'a'
    |
   'd'*  (bad)
```

When evaluating `".ad"`, the search sees `'.'` at index 0 and inspects all child slots of the root. It discovers a non-null child branch pointing to `'b'`. It moves into `'b'` and attempts to match the remaining pattern `"ad"`. The remaining string matches successfully, so it returns `true`.

---

## Partition Variables

Let:

```java
TrieNode root = new TrieNode();
```

---

### Border Elements

The TrieNode tracks character link branches along with completion properties:

```java
class TrieNode {
    TrieNode[] children = new TrieNode;
    boolean isEndOfWord = false;
}
```

---

## Correct Partition Condition

The base criteria to resolve deep recursive tracking checks looks as follows:

```java
if (index == word.length()) {
    return node.isEndOfWord;
}
```

---

## How to Move Binary Search

*(Note: This design technique trades standard binary range subdivision for a recursive Depth First Search backtracker over Trie character branch nodes to accommodate wildcard evaluations).*

---

## Java Solution

```java
class WordDictionary {

    private class TrieNode {
        TrieNode[] children = new TrieNode;
        boolean isEndOfWord = false;
    }

    private final TrieNode root;

    public WordDictionary() {
        root = new TrieNode();
    }
    
    // Adds a word into the data structure
    public void addWord(String word) {
        if (word == null) return;
        
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
    
    // Returns true if the word matches any stored string layout
    public boolean search(String word) {
        if (word == null) return false;
        return dfsSearch(word, 0, root);
    }

    private boolean dfsSearch(String word, int index, TrieNode node) {
        // Base case: if we reached the end of the search word
        if (index == word.length()) {
            return node.isEndOfWord;
        }

        char c = word.charAt(index);

        // Wildcard choice path: check all active branches recursively
        if (c == '.') {
            for (int i = 0; i < 26; i++) {
                if (node.children[i] != null && dfsSearch(word, index + 1, node.children[i])) {
                    return true;
                }
            }
            return false;
        } 
        // Deterministic character path
        else {
            int childIdx = c - 'a';
            if (node.children[childIdx] == null) {
                return false;
            }
            return dfsSearch(word, index + 1, node.children[childIdx]);
        }
    }
}
```

---

## Dry Run

### Input Operations

```java
WordDictionary dict = new WordDictionary();
dict.addWord("bad");
dict.search(".ad");
```

---

### Step Execution Traversal

1. **`addWord("bad")`**:
   - Maps path sequence: `root -> 'b' -> 'a' -> 'd' (isEndOfWord = true)`.

2. **`search(".ad")`**:
   - `dfsSearch(".ad", 0, root)` called. `index = 0`, character is `'.'`.
   - Loops through child indices `0 to 25`. At `i = 1` (`'b'`), `node.children[1]` is not null.
   - Triggers `dfsSearch(".ad", 1, node_b)`.
   - `index = 1`, character is `'a'`. `node_b.children['a'-'a']` exists (`node_a`).
   - Triggers `dfsSearch(".ad", 2, node_a)`.
   - `index = 2`, character is `'d'`. `node_a.children['d'-'a']` exists (`node_d`).
   - Triggers `dfsSearch(".ad", 3, node_d)`.
   - `index == 3` (word length matched). Returns `node_d.isEndOfWord` which is `true`.

---

### Answer

```java
true
```

---

## Why Do We Use a Backtracking Trie?

A standard Trie handles exact key matching efficiently. By incorporating local tracking branching on wildcard `'.'` occurrences, we isolate the evaluation strictly to paths that share valid matching properties. This avoids wasting time checking completely unrelated records.

This structured lookup yields:

```text
O(l) for exact text matches, up to O(26^l) for pure dots strings
```

which fulfills the requirement efficiently.

---

## Complexity Analysis

### Time Complexity

| Operation | Best Case | Worst Case (Wildcard heavy) |
| :--- | :--- | :--- |
| **`addWord`** | **O(l)** | **O(l)** |
| **`search`** | **O(l)** | **O(26^l)** |

*(where l is the length of the string input word)*

---

### Space Complexity

```text
O(n * l * 26)
```

The data layout allocates space matching the maximum total unique character nodes instantiated inside the prefix tree grid.

---

## Key Insight

Adding a recursive loop branch option directly over character array index trees handles variable character inputs elegantly without requiring structural modifications to the underlying storage model.

```text
Time  : O(l) average
Space : O(n * l * 26)
```

---

## Similar Problems

1. Implement Trie (Prefix Tree) (208)
2. Word Search II (212)
3. Longest Word in Dictionary (720)
4. Replace Words (648)
5. Prefix and Suffix Search (745)
