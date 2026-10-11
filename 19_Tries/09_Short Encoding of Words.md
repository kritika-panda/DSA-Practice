# Short Encoding of Words

## Problem Statement

A **valid encoding** of an array of `words` is a reference string `s` and an array of indices `indices` such that:

1. `indices.length == words.length`
2. The reference string `s` ends with the `'#'` character.
3. For each index `indices[i]`, the substring of `s` starting from `indices[i]` and up to the next `'#'` character is equal to `words[i]`.

Given an array of `words`, return the **length of the shortest reference string `s`** possible of any valid encoding of `words`.

The overall run time complexity should be:

```text
O(∑ words[i].length())
```

---

## Examples

### Example 1

**Input**

```java
words = ["time", "me", "bell"]
```

**Output**

```java
10
```

**Explanation**

A valid encoding would be `s = "time#bell#"` and `indices = [0, 2, 5]`.
- `words[0] = "time"` is a substring of `s` starting at index 0 up to the next `'#'`.
- `words[1] = "me"` is a substring of `s` starting at index 2 up to the next `'#'`.
- `words[2] = "bell"` is a substring of `s` starting at index 5 up to the next `'#'`.

The length of `s` is 10.

---

### Example 2

**Input**

```java
words = ["t"]
```

**Output**

```java
2
```

**Explanation**

A valid encoding would be `s = "t#"` and `indices = [0]`. The length of `s` is 2.

---

### Example 3

**Input**

```java
words = ["time", "time", "time"]
```

**Output**

```java
5
```

**Explanation**

Duplicate terms compress completely into the same reference slice layout `"time#"`.

---

## Brute Force Approach

Store all words inside a set and prune smaller words if they are found to be a suffix of any larger word in the array.

### Steps

1. Place all distinct items from the `words` array into a Hash Set.
2. For each word in the set, generate all possible proper suffixes (e.g., for `"time"`, suffixes are `"ime"`, `"me"`, `"e"`).
3. If any generated suffix matches another word present inside the set, remove that smaller suffix word from the set.
4. Calculate the sum of the lengths of all remaining words in the set, and add `1` extra character per remaining word for the tracking hash mark `'#'`.

### Complexity

```text
Time Complexity: O(∑ L_i^2) // where L_i is the length of words[i] due to substring slicing
Space Complexity: O(∑ L_i)
```

Generating substrings continuously creates extra garbage memory profiles. We can optimize suffix overlapping checks down to a clean linear pathway check using a Trie.

---

# Optimal Approach: Reversed Trie (Suffix Tree Strategy)

## Key Idea

A suffix match means one word matches the absolute trailing end of another word (e.g., `"me"` is a suffix of `"time"`). A standard **Trie** is optimized to track common prefixes, not suffixes. 

However, if we **reverse every string** before putting it into the Trie, matching suffixes maps perfectly to prefix lookups:
- `"time"` reversed becomes `"emit"`
- `"me"` reversed becomes `"em"`

By inserting the reversed words into a Trie, `"em"` becomes a strict prefix of `"emit"`. Therefore, any word that becomes a prefix of another word inside our reversed tree does not need to be written to our reference string separately—it will naturally be absorbed inside the larger word's encoding.

The final shortest reference string length will be the sum of the lengths of all **leaf nodes** in the Trie, plus `1` character for each leaf node to represent the tracking separator character `'#'`.

---

## Visual Understanding

Suppose:

```java
words = ["time", "me", "bell"]
```

Reversed words array: `["emit", "em", "lleb"]`.

Trie structure:

```text
      (root)
     /      \
   'e'      'l'

    |        |
   'm'       'l'

    |        |
   'i'       'e'

    |        |
   't'*      'b'*
```

- `"em"` stops processing at character node `'m'`. Because `'m'` has a child branch node `'i'`, it is a prefix of a longer string and not a leaf node.
- The leaf nodes are `'t'` (word length 4) and `'b'` (word length 4).
- Total length = `(4 + 1) + (4 + 1) = 10`.

---

## Partition Variables

Let:

```java
class TrieNode {
    TrieNode[] children = new TrieNode;
}
TrieNode root = new TrieNode();
Map<TrieNode, Integer> leafNodesMap = new HashMap<>();
```

---

### Border Elements

We track newly created nodes to isolate unique leaf nodes accurately:

```java
int index = c - 'a';
if (current.children[index] == null) {
    current.children[index] = new TrieNode();
}
```

---

## Correct Partition Condition

A TrieNode represents a unique leaf node if it has zero child references initialized at the end of the insertion phase:

```java
int totalLength = 0;
for (TrieNode node : leafNodesMap.keySet()) {
    if (isLeaf(node)) {
        totalLength += leafNodesMap.get(node) + 1;
    }
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with a path traversal inside a reversed Trie to aggregate character suffix matches in linear time).*

---

## Java Solution

```java
import java.util.HashMap;
import java.util.Map;

class Solution {

    private class TrieNode {
        TrieNode[] children = new TrieNode;
    }

    public int minimumLengthEncoding(String[] words) {

        TrieNode root = new TrieNode();
        
        // Maps a potential leaf node to the original length of its word
        Map<TrieNode, Integer> candidateLeaves = new HashMap<>();

        // Step 1: Insert reversed words into the Trie
        for (String word : words) {
            TrieNode current = root;
            
            // Traverse backward from the end of the word string
            for (int i = word.length() - 1; i >= 0; i--) {
                int index = word.charAt(i) - 'a';
                if (current.children[index] == null) {
                    current.children[index] = new TrieNode();
                }
                current = current.children[index];
            }
            
            // Mark this terminal node step as a candidate leaf node
            candidateLeaves.put(current, word.length());
        }

        // Step 2: Accumulate lengths of actual leaf nodes (nodes with 0 children)
        int totalLength = 0;
        for (Map.Entry<TrieNode, Integer> entry : candidateLeaves.entrySet()) {
            if (isLeaf(entry.getKey())) {
                totalLength += entry.getValue() + 1; // Word length + '#' character
            }
        }

        return totalLength;
    }

    private boolean isLeaf(TrieNode node) {
        for (int i = 0; i < 26; i++) {
            if (node.children[i] != null) {
                return false; // Has at least one child, so it's not a leaf
            }
        }
        return true;
    }
}
```

---

## Dry Run

### Input

```java
words = ["time", "me"]
```

---

### Step Execution Traversal

1. **Process word = "time":**
   - Characters are processed in reverse order: `'e' -> 'm' -> 'i' -> 't'`.
   - Node links are created sequentially downstream.
   - Node `'t'` is cached into `candidateLeaves` -> `{node_t -> 4}`.

2. **Process word = "me":**
   - Characters are processed in reverse order: `'e' -> 'm'`.
   - Reuses the existing path links for `'e'` and `'m'`.
   - Node `'m'` is cached into `candidateLeaves` -> `{node_t -> 4, node_m -> 2}`.

3. **Leaf Validation Phase:**
   - Inspect `node_t`: All 26 child references are null. It is an active leaf node. `totalLength += 4 + 1 = 5`.
   - Inspect `node_m`: The child reference at index `'i' - 'a'` is not null. It is a prefix node, not a leaf node. Skipped.

---

### Answer

```java
5
```

---

## Why Do We Reverse Strings inside the Trie?

Reversing strings changes the problem from finding shared suffixes into finding shared prefixes. This allows us to use a standard Trie data structure to identify overlapping elements in linear time, avoiding the expensive substring slicing operations required by brute-force methods.

This pattern aggregation yields:

```text
O(∑ words[i].length())
```

which satisfies the optimal time complexity constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(∑ words[i].length())
```

Inserting each word into the Trie requires a number of steps equal to the word's length. The final step walks through at most `n` candidate leaf nodes and performs a constant-time check (`26` iterations) for each node, ensuring a linear execution timeline.

---

### Space Complexity

```text
O(∑ words[i].length() * 26)
```

The data structure allocates tree tracking array storage slots proportional to the count of unique character nodes instantiated inside the prefix tree map.

---

## Key Insight

Reversing strings allows us to leverage a standard prefix tree to resolve suffix containment queries efficiently in a single top-down pass.

```text
Time  : O(∑ words[i].length())
Space : O(∑ words[i].length() * 26)
```

---

## Similar Problems

1. Implement Trie (Prefix Tree) (208)
2. Longest Word in Dictionary (720)
3. Replace Words (648)
4. Design Add and Search Words Data Structure (211)
5. Prefix and Suffix Search (745)
