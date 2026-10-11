# Replace Words

## Problem Statement

In English, we have a concept called **root**, which can be followed by some other word to form another longer word—let's call this word **derivative**. For example, when the root `"an"` is followed by the derivative word `"other"`, we can form a new word `"another"`.

Given a dictionary consisting of many **roots** and a `sentence` consisting of words separated by spaces, replace all the derivatives in the sentence with the root forming it. If a derivative can be replaced by more than one root, replace it with the **root that has the shortest length**.

Return the sentence after the replacement.

The overall run time complexity should be:

```text
O(d * l + s)
```

*(where d is the number of roots, l is the average length of a root, and s is the length of the sentence)*

---

## Examples

### Example 1

**Input**

```java
dictionary = ["cat", "bat", "rat"]
sentence = "the cattle was rattled by the battery"
```

**Output**

```java
"the cat was rat by the bat"
```

**Explanation**

- `"cattle"` matches root `"cat"` -> Replaced by `"cat"`.
- `"rattled"` matches root `"rat"` -> Replaced by `"rat"`.
- `"battery"` matches root `"bat"` -> Replaced by `"bat"`.

---

### Example 2

**Input**

```java
dictionary = ["a", "b", "c"]
sentence = "aadsfasf absbs bbact cadsfafs"
```

**Output**

```java
"a a b c"
```

---

### Example 3

**Input**

```java
dictionary = ["catt", "cat"]
sentence = "the cattle"
```

**Output**

```java
"the cat"
```

**Explanation**

Both `"cat"` and `"catt"` can form `"cattle"`. We choose the shortest root, which is `"cat"`.

---

## Brute Force Approach

Put all roots into a Hash Set, split the sentence into individual words, and check every prefix of each word against the set.

### Steps

1. Place all strings from `dictionary` into a `HashSet<String>`.
2. Split the `sentence` into individual words using spaces as delimiters.
3. For each word, iterate through its characters to construct prefixes of increasing length (from index `1` to `len`).
4. Look up each prefix in the Hash Set. The first prefix found is guaranteed to be the shortest root. Replace the word with this prefix.
5. Join all processed words back together with space separators and return the final string.

### Complexity

```text
Time Complexity: O(w * l^2 + s)
Space Complexity: O(d * l + s)
```

*(where w is the number of words in the sentence and l is the maximum length of a word)*

Generating substrings for every prefix creates massive string copying overhead. This causes quadratic runtime behavior per word, which can be optimized to linear time using a Trie.

---

# Optimal Approach: Trie for Shortest Prefix Matching

## Key Idea

A **Trie** is uniquely optimized for locating the shortest prefix match efficiently. By storing roots in a Trie, we can find replacements using a single top-down pass for each word, completely eliminating substring generation overhead.

1. Insert all root words from the `dictionary` into the Trie.
2. Split the input `sentence` into tokens using a string split operation.
3. For each word token, step down the Trie character by character:
   - If we hit a node where `isEndOfWord == true`, we have successfully located the **shortest matching root**. We stop searching and replace the word with the prefix matched up to this step.
   - If we hit a `null` child pointer branch before finding any valid root, it means the word cannot be formed by any root in our dictionary. We leave the original word unchanged.
4. Append the results into a string builder using space delimiters.

---

## Visual Understanding

Suppose the dictionary contains roots `"cat"` and `"catt"`. 

Trie structure:

```text
  (root)
    |
   'c'
    |
   'a'
    |
   't'*  (Shortest Root Found!)
    |
   't'*
```

When scanning the word `"cattle"`, the algorithm steps through `'c'`, `'a'`, and `'t'`. The moment it processes `'t'`, it encounters `isEndOfWord == true`. It stops traversing immediately and returns `"cat"`, bypassing the longer root `"catt"` and the rest of the derivative characters.

---

## Partition Variables

Let:

```java
class TrieNode {
    TrieNode[] children = new TrieNode; // Mapped to lowercase English letters
    boolean isEndOfWord = false;
}
TrieNode trieRoot = new TrieNode();
```

---

### Border Elements

If a word's character branch does not exist in the Trie, traversal terminates safely, returning the original word:

```java
if (current.children[index] == null) {
    return word; // No matching root prefix path exists
}
```

---

## Correct Partition Condition

The criterion to confirm that a valid shortest prefix root boundary has been successfully intercepted is:

```java
if (current.isEndOfWord) {
    return sb.toString();
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with a direct top-down prefix tree traversal to achieve linear-time string modifications).*

---

## Java Solution

```java
import java.util.List;

class Solution {

    private class TrieNode {
        private TrieNode[] children;
        private boolean isEndOfWord;

        public TrieNode() {
            this.children = new TrieNode; // Support 'a' through 'z'
            this.isEndOfWord = false;
        }
    }

    private final TrieNode root = new TrieNode();

    private void insert(String rootWord) {
        TrieNode current = root;
        for (char c : rootWord.toCharArray()) {
            int index = c - 'a';
            if (current.children[index] == null) {
                current.children[index] = new TrieNode();
            }
            current = current.children[index];
        }
        current.isEndOfWord = true;
    }

    private String findShortestRoot(String word) {
        TrieNode current = root;
        StringBuilder prefixBuilder = new StringBuilder();

        for (char c : word.toCharArray()) {
            int index = c - 'a';
            
            // If the character path doesn't exist, no root can match this word
            if (current.children[index] == null) {
                return word;
            }
            
            current = current.children[index];
            prefixBuilder.append(c);
            
            // The first completed root found is guaranteed to be the shortest one
            if (current.isEndOfWord) {
                return prefixBuilder.toString();
            }
        }
        
        return word;
    }

    public String replaceWords(List<String> dictionary, String sentence) {
        
        // Step 1: Insert all root terms into the Trie
        for (String rootWord : dictionary) {
            insert(rootWord);
        }

        // Step 2: Split the sentence into individual words
        String[] words = sentence.split(" ");
        StringBuilder resultBuilder = new StringBuilder();

        // Step 3: Find root replacements for each word token
        for (int i = 0; i < words.length; i++) {
            resultBuilder.append(findShortestRoot(words[i]));
            if (i < words.length - 1) {
                resultBuilder.append(" "); // Append space delimiter between words
            }
        }

        return resultBuilder.toString();
    }
}
```

---

## Dry Run

### Input

```java
dictionary = ["cat", "bat"]
sentence = "the cattle battery"
```

---

### Step Execution Traversal

1. **Trie Building:** Roots `"cat"` and `"bat"` are inserted into the Trie.
2. **Sentence Processing:** Split into tokens `["the", "cattle", "battery"]`.
   - **word = "the":** Path `'t' -> 'h' -> 'e'` checks index links. Node `'t'` is missing from the root children array. Returns original word `"the"`.
   - **word = "cattle":** Steps through `root -> 'c' -> 'a' -> 't'`. Node `'t'` has `isEndOfWord = true`. Stops immediately and returns `"cat"`.
   - **word = "battery":** Steps through `root -> 'b' -> 'a' -> 't'`. Node `'t'` has `isEndOfWord = true`. Stops immediately and returns `"bat"`.

Final String Construction = `"the" + " " + "cat" + " " + "bat"` = `"the cat bat"`.

---

### Answer

```java
"the cat bat"
```

---

## Why Do We Use a Trie for Root Replacement?

Instead of generating separate string slices repeatedly and performing isolated set lookups for every possible prefix length, a Trie maps matching characters along contiguous paths. The moment a valid root marker is hit, the search exits immediately, ensuring highly efficient processing for long text blocks.

This path evaluation yields:

```text
O(d * l + s)
```

which satisfies the optimal time constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(d * l + s)
```

Where `d` is the number of roots, `l` is the average length of a root word, and `s` is the length of the sentence. Building the Trie takes `O(d * l)` time, while splitting and scanning the sentence requires an additional `O(s)` pass.

---

### Space Complexity

```text
O(d * l * 26 + s)
```

The tree nodes allocate memory to store character branches for all dictionary entries, while the output string builder mirrors the total size of the final sentence.

---

## Key Insight

Terminating path validation loops early upon encountering the first valid word-end flag naturally isolates the shortest match in a single pass without extra slicing filters.
text Time  : O(d * l + s) Space : O(d * l * 26 + s)
