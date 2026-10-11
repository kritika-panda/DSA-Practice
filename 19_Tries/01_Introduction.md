# Introduction to Tries (Prefix Trees)

## Problem Statement

A **Trie** (derived from the word "retrieval" and pronounced as "try") is an advanced, tree-like data structure used for efficiently storing and retrieving keys in a dataset of strings. It is also known as a **Prefix Tree**.

Unlike a standard binary search tree, no node in a Trie stores the complete string key associated with that node. Instead, its position in the tree defines the key it is associated with. All the descendants of a node share a common prefix of the string associated with that node.

A standard Trie implementation should support the following core operations efficiently:

```text
1. insert(String word)
2. search(String word)
3. startsWith(String prefix)
```

---

## Structure and Representation

Each node in a Trie consists of a collection of pointers/references to its child nodes (typically mapped to an alphabet size) and a boolean flag indicating whether the node marks the completion of a full string.

### Conceptual Mapping

```java
class TrieNode {
    TrieNode[] children = new TrieNode[26]; // For lowercase English letters
    boolean isEndOfWord = false;
}
```

---

## Visual Understanding

Suppose we insert the following words into an empty Trie:
```text
"cat", "car", "cap", "do"
```

The resulting structural hierarchy looks like this:

```text
        (root)
       /      \
     'c'      'd'

      |        |
     'a'      'o'* (do)
   /  |  \
 't'*'r'*'p'* (cat, car, cap)
```

*(Note: The `*` indicates that `isEndOfWord` is set to `true` for that node).*

- The words `"cat"`, `"car"`, and `"cap"` all share the common prefix `"ca"`.
- Searching for `"can"` will fail quickly because the child reference for `'n'` is `null` under the `'a'` node.

---

## Core Operations

### 1. Insert Operation
We start at the root node and iterate through each character of the word. If the current character does not exist as a child node, we create a new `TrieNode`. We then move our pointer to this child node and repeat the process. Finally, we mark the `isEndOfWord` flag as `true` on the last node.

### 2. Search Operation
We traverse down the tree character by character following the input word. If at any point the child pointer for the next character is `null`, the word does not exist, and we return `false`. If we successfully match all characters, we return the value of `isEndOfWord` (ensuring it is a complete word and not just a prefix).

### 3. StartsWith (Prefix Search) Operation
This follows the exact same traversal logic as the `search` operation. The only difference is that if we successfully match all characters of the prefix, we immediately return `true`, regardless of whether `isEndOfWord` is true or false.

---

## Java Solution

```java
class Trie {

    private class TrieNode {
        private TrieNode[] children;
        private boolean isEndOfWord;

        public TrieNode() {
            this.children = new TrieNode[26]; // Supporting lowercase English letters 'a' through 'z'
            this.isEndOfWord = false;
        }
    }

    private final TrieNode root;

    public Trie() {
        root = new TrieNode();
    }

    // Inserts a word into the trie
    public void insert(String word) {
        if (word == null || word.isEmpty()) return;

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

    // Returns true if the word is in the trie
    public boolean search(String word) {
        if (word == null || word.isEmpty()) return false;

        TrieNode current = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (current.children[index] == null) {
                return false;
            }
            current = current.children[index];
        }
        return current.isEndOfWord;
    }

    // Returns true if there is any word in the trie that starts with the given prefix
    public boolean startsWith(String prefix) {
        if (prefix == null || prefix.isEmpty()) return false;

        TrieNode current = root;
        for (char c : prefix.toCharArray()) {
            int index = c - 'a';
            if (current.children[index] == null) {
                return false;
            }
            current = current.children[index];
        }
        return true;
    }
}
```

---

## Dry Run

### Operations
```java
Trie trie = new Trie();
trie.insert("cat");
trie.search("cat");   // Returns true
trie.search("ca");    // Returns false
trie.startsWith("ca"); // Returns true
```

---

### Step Execution Traversal

1. **`insert("cat")`**:
   - `'c'` is missing from `root.children` -> Create new node at index 2. Move to it.
   - `'a'` is missing from current children -> Create new node at index 0. Move to it.
   - `'t'` is missing from current children -> Create new node at index 19. Move to it.
   - End of string reached -> Set `current.isEndOfWord = true`.

2. **`search("ca")`**:
   - Traverses `'c'`, then moves to `'a'`.
   - String matches completed, but the current node's `isEndOfWord` flag is `false`. Returns `false`.

3. **`startsWith("ca")`**:
   - Traverses `'c'`, then moves to `'a'`.
   - Prefix matches completed successfully without encountering a `null` link. Returns `true`.

---

## Why Do We Use a Trie over a Hash Map?

While a Hash Map provides O(1) average time complexity for lookup, it has downfalls for specific string operations:
- **Prefix Matching:** A Hash Map cannot efficiently find all words starting with a specific prefix; it requires scanning all keys \(O(N \cdot L)\). A Trie resolves this in O(L) prefix length time.
- **Hash Collisions:** A Hash Map can slow down to \(O(L \cdot N)\) under extreme hash collisions. A Trie guarantees worst-case runtime bounds.
- **Space Efficiency:** Tries save memory when storing millions of words with overlapping prefixes (e.g., dictionaries), as common prefixes are stored exactly once.

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Reason |
| :--- | :--- | :--- |
| **`insert`** | **O(L)** | Where L is the length of the word being inserted. We loop exactly L times. |
| **`search`** | **O(L)** | Where L is the length of the word being searched. |
| **`startsWith`** | **O(L)** | Where L is the length of the prefix string. |

---

### Space Complexity

```text
O(N * L * Alphabet_Size)
```

In the absolute worst-case scenario (where no words share any common characters), each character of every word requires a separate node. However, in practice, memory scales efficiently due to heavy prefix sharing.

---

## Key Insight

A Trie exchanges a small memory overhead (storing explicit array pointers for alphabets) to gain deterministic, fast prefix-matching lookups that are completely insulated from hash collision penalties.

```text
Time  : O(L) per operation
Space : Dependent on character overlap
```

---

## Similar Problems

1. Implement Trie (Prefix Tree) (208)
2. Design Add and Search Words Data Structure (211)
3. Word Search II (212)
4. Replace Words (648)
5. Longest Word in Dictionary (720)
