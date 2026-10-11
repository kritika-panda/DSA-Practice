# Word Search II

## Problem Statement

Given an `m x n` board of characters `board` and a list of strings `words`, return all words on the board.

Each word must be constructed from letters of sequentially adjacent cells, where **adjacent cells** are horizontally or vertically neighboring. The same letter cell may not be used more than once in a word.

The overall run time complexity should be bounded by:

```text
O(m * n * 4^l)
```

*(where m x n are the board dimensions and l is the maximum length of a word)*

---

## Examples

### Example 1

**Input**

```java
board = [
  ['o','a','a','n'],
  ['e','t','a','e'],
  ['i','h','k','r'],
  ['i','f','l','v']
];
words = ["oath","pea","eat","rain"]
```

**Output**

```java
["eat","oath"]
```

**Explanation**

- `"oath"` can be found starting at (0,0) -> (0,1) -> (1,1) -> (1,2).
- `"eat"` can be found starting at (1,3) -> (0,3) -> (0,2).

---

### Example 2

**Input**

```java
board = [
  ['a','b'],
  ['c','d']
];
words = ["abcb"]
```

**Output**

```java
[]
```

**Explanation**

The word `"abcb"` requires re-visiting the cell containing `'b'`, which is invalid.

---

### Example 3

**Input**

```java
board = [['a']];
words = ["a"]
```

**Output**

```java
["a"]
```

---

## Brute Force Approach

Perform a standard backtracking Depth-First Search (DFS) for each word independently starting from every single cell on the board.

### Steps

1. Iterate through each word in the `words` array.
2. For each word, scan the board to find cells matching the first letter.
3. Launch a recursive DFS from that cell to search for the remaining characters in the four adjacent directions.
4. Mark visited cells to prevent re-use within the same path.
5. If a word is successfully matched, add it to our results collection.

### Complexity

```text
Time Complexity: O(w * m * n * 4^l) // where w is the number of words
Space Complexity: O(l) for the recursion stack
```

When the dictionary contains many words, searching the grid completely from scratch for every single word causes severe Time Limit Exceeded (TLE) overhead.

---

# Optimal Approach: Backtracking Trie (Prefix Tree Graph)

## Key Idea

Instead of running a separate search for every word, we can search for **all words simultaneously** by storing our dictionary in a **Trie** and traversing the board just once.

1. Insert all terms from the `words` list into a Trie. Instead of a boolean flag, we store the complete string `word` directly at the terminal node.
2. Iterate through each cell `(r, c)` of the board and launch a backtracking DFS that steps through the board and the Trie branches in parallel.
3. If the current board character exists as a child node in the Trie, we move to that child node and explore its four neighboring directions recursively.
4. If a node contains a non-null `word` string, we have successfully located a word from our dictionary! We add it to our results and set `node.word = null` to prevent duplicate discoveries.

### Pruning Optimization

To maximize efficiency, when a leaf node matches a word, we can prune it from the Trie. If a node has no remaining children left to explore, we can safely disconnect it from its parent, preventing subsequent search paths from wasting time on dead ends.

---

## Visual Understanding

Suppose the board contains `'o' -> 'a' -> 't' -> 'h'` starting at the top left, and our Trie contains the path `root -> 'o' -> 'a' -> 't' -> 'h'`.

Trie character exploration logic:

```text
  (root)
    |
   'o'
    |
   'a'
    |
   't'
    |
   'h' [word = "oath"] -> Match found!
```

As the grid backtracking path advances to matching cells, the algorithm shifts its current pointer down the corresponding Trie branch. The moment it intercepts `node.word != null`, a word is recorded, bypassing isolated string verification matches entirely.

---

## Partition Variables

Let:

```java
class TrieNode {
    TrieNode[] children = new TrieNode;
    String word = null;
}
TrieNode root = new TrieNode();
List<String> result = new ArrayList<>();
```

---

### Border Elements

Grid boundaries and cell visit status are verified safely prior to executing recursive branch jumps:

```java
if (r < 0 || r >= board.length || c < 0 || c >= board[0].length || board[r][c] == '#') {
    return;
}
```

---

## Correct Partition Condition

The criterion to confirm that a valid word has been successfully intercepted along the board path is:

```java
if (node.word != null) {
    result.add(node.word);
    node.word = null; // Prevent duplicate additions
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with a parallel DFS walk across both a grid and a Trie structure to achieve optimal search pruning).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    private class TrieNode {
        private TrieNode[] children = new TrieNode;
        private String word = null;
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
        current.word = word; // Store the full word string at the leaf node
    }

    public List<String> findWords(char[][] board, String[] words) {
        
        List<String> result = new ArrayList<>();
        TrieNode root = new TrieNode();
        
        // Step 1: Build the Trie from the dictionary words
        for (String word : words) {
            insert(word, root);
        }

        int rows = board.length;
        int cols = board[0].length;

        // Step 2: Launch parallel DFS from every grid cell position
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                dfsSearch(board, r, c, root, result);
            }
        }

        return result;
    }

    private void dfsSearch(char[][] board, int r, int c, TrieNode parent, List<String> result) {
        
        char ch = board[r][c];
        int childIdx = ch - 'a';
        
        // If the character path doesn't exist in the Trie, stop exploring this branch
        if (childIdx < 0 || parent.children[childIdx] == null) {
            return;
        }

        TrieNode current = parent.children[childIdx];

        // Match condition: a full word is discovered
        if (current.word != null) {
            result.add(current.word);
            current.word = null; // Clear to prevent recording duplicate items
        }

        // Mark the current cell as visited using a sentinel character '#'
        board[r][c] = '#';

        // Explore the 4 adjacent directions (up, down, left, right)
        int[] dr = {-1, 1, 0, 0};
        int[] dc = {0, 0, -1, 1};

        for (int i = 0; i < 4; i++) {
            int newR = r + dr[i];
            int newC = c + dc[i];

            if (newR >= 0 && newR < board.length && newC >= 0 && newC < board[0].length 
                && board[newR][newC] != '#') {
                dfsSearch(board, newR, newC, current, result);
            }
        }

        // Backtrack: restore the original character back to the grid cell
        board[r][c] = ch;

        // Optimization: Prune leaf nodes dynamically from the Trie to minimize search paths
        if (isEmpty(current)) {
            parent.children[childIdx] = null;
        }
    }

    private boolean isEmpty(TrieNode node) {
        for (int i = 0; i < 26; i++) {
            if (node.children[i] != null) {
                return false;
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
board = [['a', 'b'], ['c', 'd']]
words = ["ab", "ac"]
```

---

### Step Execution Traversal

1. **Trie Configuration:** Words `"ab"` and `"ac"` are mapped into the Trie: `root -> 'a' -> 'b' (word="ab")` and `root -> 'a' -> 'c' (word="ac")`.
2. **Grid Iteration at cell (0,0) containing 'a':**
   - `root.children['a'-'a']` exists. Move to node `'a'`.
   - Mark cell (0,0) as `#`.
   - Move to right cell (0,1) containing `'b'`. Node `'a'.children['b'-'a']` exists (`node_b`).
   - `node_b.word != null`. Add `"ab"` to results. Clear `node_b.word = null`.
   - Backtrack from (0,1). Node `b` is now empty, so it is pruned from node `a`.
   - Move to down cell (1,0) containing `'c'`. Node `'a'.children['c'-'a']` exists (`node_c`).
   - `node_c.word != null`. Add `"ac"` to results. Clear `node_c.word = null`.
   - Backtrack from (1,0). Node `c` is pruned from node `a`.
   - Backtrack from (0,0). Node `a` is pruned from `root`.

The remaining grid cell loops terminate instantly because `root.children` is completely pruned and empty.

---

### Answer

```java
["ab","ac"]
```

---

## Why Do We Use a Backtracking Trie?

By storing the entire dictionary in a Trie, we merge duplicate prefixes across words. When the search sweeps through the grid, it explores common prefix paths just once for all matching words simultaneously. Combining this with leaf pruning ensures that dead-end paths are permanently discarded, preventing redundant exploration loops.

This combined pruning graph yields:
text O(m * n * 4^l) worst-case time complexity 
which satisfies the optimization constraints efficiently.

## Complexity Analysis

Time Complexity:
```text
 O(m * n * 4^l)
```
Where m x n are the dimensions of the board and l is the length of the longest word. Building the Trie takes linear time proportional to the total characters in the dictionary list. The worst-case runtime occurs if the grid features broad path combinations that fit large word lengths. In practice, pruning minimizes runtime dramatically.

Space Complexity:
```text
 O(w * l * 26)
```
The data structure allocates tree tracking memory nodes to store character branches for all words in the dictionary. The call stack consumes O(l) memory to handle recursion frames.


## Key Insight

Traversing a grid concurrently with a prefix tree allows us to evaluate a massive dictionary of words in a single pass, while dynamic leaf pruning cuts off dead ends to maximize performance.
text Time  : O(m * n * 4^l) worst-case Space : O(w * l * 26) memory nodes 
