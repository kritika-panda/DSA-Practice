# Prefix and Suffix Search

## Problem Statement

Design a special dictionary that searches for words matching both a specific prefix and a specific suffix.

Implement the `WordFilter` class:
1. `WordFilter(String[] words)`: Initializes the object with the `words` in the dictionary.
2. `int f(String pref, String suff)`: Returns the index of the word in the dictionary that has the prefix `pref` and the suffix `suff`. If there is more than one valid word, return **the largest of their indices**. If no such word exists, return `-1`.

The overall run time complexity for querying should be:

```text
O(p + s)
```

*(where p is the length of the prefix and s is the length of the suffix)*

---

## Examples

### Example 1

**Input**

```java
WordFilter wordFilter = new WordFilter(new String[]{"apple"});
wordFilter.f("a", "e"); // return 0
```

**Explanation**

The word at index 0 is "apple", which begins with the prefix "a" and ends with the suffix "e".

---

## Brute Force Approach

Iterate backward through the array of words on each query, manually verifying character boundaries using built-in string functions.

### Steps

1. `WordFilter(words)`: Save the array of strings directly to an internal class reference variable.
2. `f(pref, suff)`: Loop backward from index `n - 1` down to `0`. For each word, check if `word.startsWith(pref)` and `word.endsWith(suff)` evaluate to true simultaneously. Return the index of the first match found, or `-1` if the loop finishes with no match.

### Complexity

```text
Time Complexity: O(1) for initialization, O(n * l) per query
Space Complexity: O(n * l)
```

*(where n is the total number of words and l is the length of the word being inspected)*

Scanning the entire dictionary on every query causes severe performance penalties for dense lookup systems. We can optimize lookups using a single, unified Trie configuration.

---

# Optimal Approach: Unified Wrap-Around Trie

## Key Idea

A standard Trie handles prefix matches easily, but cannot look up suffixes efficiently. We can combine prefix and suffix constraints into a single query by **inserting modified wrap-around versions of each word** into the Trie.

For a word like `"apple"` at index `0`, we can append a separator character like `'{'` (which sits right after `'z'` in ASCII) along with the word itself:

```text
"apple" -> "apple{apple"
```

We generate all possible suffix start positions by sliding a window from right to left, and insert each suffix wrapper sequence into our Trie:

```text
"e{apple"
"le{apple"
"ple{apple"
"pple{apple"
"apple{apple"
"{apple" (handles empty suffix conditions)
```

Each node in this unified Trie updates and stores the current word's `index`. Since we process words from index `0` up to `n - 1`, a node's stored index is naturally overwritten with the largest index available.

When querying `f(pref, suff)`, we construct a search pattern: `suff + "{" + pref`. Querying this unified string inside our Trie satisfies both prefix and suffix constraints in a single top-down pass.

---

## Visual Understanding

Suppose we insert the word `"apple"` at index `0`. We generate the wrap-around combinations. One of those suffix permutations will look like this:

```text
Suffix = "e", Prefix = "a" -> Search target: "e{a"
```

Trie structural nodes tracking the largest matched indices:

```text
  (root) [idx=0]
    |
   'e'   [idx=0]
    |
   '{'   [idx=0]
    |
   'a'   [idx=0]
```

When querying `f("a", "e")`, the search string `"e{a"` walks down to the node representing `'a'` and immediately reads its stored index value, returning `0` in linear time relative to query length.

---

## Partition Variables

Let:

```java
class TrieNode {
    TrieNode[] children = new TrieNode; // Size 27 to accommodate lowercase letters plus '{'
    int weight = -1;
}
TrieNode root = new TrieNode();
```

---

### Border Elements

The alphabet array size is extended to 27 cells to map the separator character `'{'` safely:

```java
int index = c - 'a'; // '{' maps to index 26 since '{' - 'a' = 26
```

---

## Correct Partition Condition

When retrieving the weight metric value at the final pattern string index boundary:

```java
for (char c : query.toCharArray()) {
    int index = c - 'a';
    if (current.children[index] == null) return -1;
    current = current.children[index];
}
return current.weight;
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces traditional binary search range tracking with a multi-prefix wrap-around tree topology to optimize combined structural string queries).*

---

## Java Solution

```java
class WordFilter {

    private class TrieNode {
        private TrieNode[] children;
        private int weight;

        public TrieNode() {
            this.children = new TrieNode[27]; // 26 lowercase letters + '{' separator
            this.weight = -1;
        }
    }

    private final TrieNode root;

    public WordFilter(String[] words) {
        root = new TrieNode();
        
        // Step 1: Process every word from the dictionary
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            String basePattern = "{" + word;
            
            // Generate all possible suffix start positions
            for (int j = 0; j <= word.length(); j++) {
                String suffix = word.substring(j);
                String fullInsertString = suffix + basePattern;
                
                // Step 2: Insert the generated wrap-around string into the Trie
                insert(fullInsertString, i);
            }
        }
    }

    private void insert(String text, int wordWeight) {
        TrieNode current = root;
        current.weight = wordWeight; // Store index at root

        for (char c : text.toCharArray()) {
            int index = c - 'a'; // '{' naturally resolves to index 26
            if (current.children[index] == null) {
                current.children[index] = new TrieNode();
            }
            current = current.children[index];
            current.weight = wordWeight; // Overwrites weight with the largest index
        }
    }
    
    // Step 3: Query the unified wrap-around string pattern
    public int f(String pref, String suff) {
        TrieNode current = root;
        String queryPattern = suff + "{" + pref;

        for (char c : queryPattern.toCharArray()) {
            int index = c - 'a';
            if (current.children[index] == null) {
                return -1;
            }
            current = current.children[index];
        }

        return current.weight;
    }
}
```

---

## Dry Run

### Input Operations

```java
WordFilter wordFilter = new WordFilter(new String[]{"apple"});
wordFilter.f("a", "e");
```

---

### Step Execution Traversal

1. **Initialization (`WordFilter`)**:
   - Loops through `words[0]` (`"apple"`).
   - Generates suffix strings: `""`, `"e"`, `"le"`, `"ple"`, `"pple"`, `"apple"`.
   - Inserts strings into the Trie: `"{apple"`, `"e{apple"`, `"le{apple"`, etc.
   - All nodes built along these character branches assign their `weight = 0`.

2. **Querying (`f("a", "e")`)**:
   - Constructs the search target string: `suff + "{" + pref` -> `"e{a"`.
   - Steps top-down through the Trie: `root -> 'e' -> '{' -> 'a'`.
   - The path matches successfully. It reads the final matched node's `weight` directly, returning `0`.

---

### Answer

```java
0
```

---

## Why Do We Use a Wrap-Around Unified Trie?

By transforming a dual-ended string match into a single search pattern (`suff + "{" + pref`), we consolidate prefix and suffix constraints into a single data structure. This allows us to locate the largest matching word index in linear time relative to query length, avoiding expensive full-dictionary scans.

This unified tracking model yields:

```text
O(n * l^2) for initialization, O(p + s) per query
```

*(where n is word count, l is average word length, p is prefix length, and s is suffix length)*

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Reason |
| :--- | :--- | :--- |
| **`WordFilter` (Init)** | **O(n * l^2)** | For each of the `n` words, we generate `l` suffixes and insert strings of length up to `2l`. |
| **`f` (Query)** | **O(p + s)** | We traverse the character path of the query pattern exactly `p + s + 1` times. |

---

### Space Complexity

```text
O(n * l^2 * 27)
```

The tree structures create storage nodes proportional to the combined lengths of all suffixes generated across dictionary entries.

---

## Key Insight

Appending raw data components using unique separator markers transforms multi-dimensional string filtering problems into simple, single-pass pathway lookups.

```text
Time  : O(p + s) per query request
Space : O(n * l^2 * 27)
```

---

## Similar Problems

1. Implement Trie (Prefix Tree) (208)
2. Design Add and Search Words Data Structure (211)
3. Map Sum Pairs (677)
4. Stream of Characters (1032)
5. Longest Word in Dictionary (720)
