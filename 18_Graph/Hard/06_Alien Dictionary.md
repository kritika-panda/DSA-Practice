# Alien Dictionary

## Problem Statement

There is a new alien language that uses the English alphabet. However, the order of the letters is unknown to you. You are given a list of strings `words` from the alien language's dictionary, where the strings in `words` are **sorted lexicographically** according to the rules of this new language.

Return *a string of the unique letters in the new alien language sorted in **increasing lexicographical order** by the new language's rules.* If the given arrangement of words is impossible or contains a contradiction, return **an empty string `""`**.

If there are multiple valid solutions, return **any** of them.

The overall runtime complexity must be strictly bounded by:

```text
O(C)
```

*(where C is the total number of characters across all words in the input dictionary list)*

---

## Example 1

### Input

```text
words = ["wrt","wrf","er","ett","rftt"]
```

### Output

```text
"wertf"
```

### Explanation

```text
From the sorted dictionary we can infer the following rules:
- "wrt" comes before "wrf" -> 't' comes before 'f'
- "wrt" comes before "er"  -> 'w' comes before 'e'
- "er" comes before "ett"   -> 'r' comes before 't'
- "ett" comes before "rftt" -> 'e' comes before 'r'

Combining these rules gives us a unique order sequence: "wertf".
```

---

## Example 2

### Input

```text
words = ["z","x"]
```

### Output

```text
"zx"
```

---

## Example 3

### Input

```text
words = ["z","x","z"]
```

### Output

```text
""
```

### Explanation

```text
The order shows 'z' comes before 'x', but later says 'x' comes before 'z'. 
This is a direct contradiction cycle, making a valid ordering impossible.
```

---

# Intuition

This problem can be structurally rephrased as a graph-theory dependency task: **Can we find a valid Topological Sort of a Directed Graph?**
* **Nodes:** Each unique character present in the dictionary represents a vertex.
* **Directed Edges:** By comparing adjacent words in the list, we can identify which character has priority over another. For example, if we compare `"wrt"` and `"er"`, the first mismatch is at index 0 (`'w'` vs `'e'`). This proves that `'w'` must come before `'e'`, adding a directed edge `'w' → 'e'`.

We can implement this using **Kahn's Algorithm (BFS-based In-Degree Pruning)**:
1. **Unique Character Universe:** Collect every unique character present in all input words. Initialize their in-degrees to `0`.
2. **Edge Construction & In-Degree Capture:** Compare every pair of adjacent words `words[i]` and `words[i+1]`.
   * Find the first position where characters mismatch: `c1 = words[i].charAt(k)` and `c2 = words[i+1].charAt(k)`.
   * Add a directed edge `c1 → c2` to our adjacency list and increment the dependency count: `inDegree[c2]++`.
   * **The Prefix Failure Guard:** If `words[i]` starts with `words[i+1]` but is *longer* (e.g., `words[i] = "abc"`, `words[i+1] = "ab"`), the dictionary order is invalid by definition. Fail fast and return `""`.
3. **Seed Dependency-Free Elements:** Push all unique characters that have an `inDegree == 0` into a FIFO queue.
4. **Iterative Linear Sweep:** Pop characters from the queue, appending them to a `StringBuilder`. For each neighbor node, decrement its in-degree. If a neighbor's in-degree drops to `0`, push it onto the queue.
5. **Cycle Detection Check:** If the length of our output string matches the count of unique characters in the alphabet, the ordering is valid. If it falls short, a cyclic dependency deadlock occurred—return `""`.

---

# State Definition

Let:

```text
Map<Character, Set<Character>> adj = new HashMap<>(); // Adjacency list representation
Map<Character, Integer> inDegree = new HashMap<>();   // In-degree tracking map
StringBuilder order = new StringBuilder();             // Running container for the final sequence
```

---

# Recurrence / Pointer Transition Condition

```text
For each character 'neighbor' depending on 'curr' popped from the queue:
    inDegree.put(neighbor, inDegree.get(neighbor) - 1)
    If inDegree.get(neighbor) == 0:
        queue.offer(neighbor)
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By pre-populating unique characters into a hashing lookup engine and iterating through adjacent word arrays, graph building scales in linear time. Executing a standard Kahn's dependency sweep processes characters and priority edges exactly once, keeping execution securely bounded inside **O(C)** time limits.

---

## Java Code (Kahn's Algorithm)

```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.Map;
import java.util.Queue;
import java.util.Set;

class Solution {

    public String alienOrder(String[] words) {
        if (words == null || words.length == 0) {
            return "";
        }

        Map<Character, Set<Character>> adj = new HashMap<>();
        Map<Character, Integer> inDegree = new HashMap<>();

        // Step 1: Initialize the graph tracking maps for all unique characters present
        for (String word : words) {
            for (char c : word.toCharArray()) {
                inDegree.put(c, 0);
                adj.put(c, new HashSet<>());
            }
        }

        // Step 2: Build the priority edges by comparing adjacent words
        for (int i = 0; i < words.length - 1; i++) {
            String w1 = words[i];
            String w2 = words[i + 1];
            int minLen = Math.min(w1.length(), w2.length());

            // Edge Case Guard: Check if a longer word invalidly precedes its own prefix
            if (w1.length() > w2.length() && w1.startsWith(w2)) {
                return ""; // Lexicographical contradiction caught
            }

            for (int k = 0; k < minLen; k++) {
                char c1 = w1.charAt(k);
                char c2 = w2.charAt(k);

                if (c1 != c2) {
                    // If this priority relationship edge hasn't been logged yet, record it
                    if (!adj.get(c1).contains(c2)) {
                        adj.get(c1).add(c2);
                        inDegree.put(c2, inDegree.get(c2) + 1);
                    }
                    break; // Once the first mismatch is processed, trailing characters tell us nothing
                }
            }
        }

        // Step 3: Seed our FIFO queue with all nodes containing zero incoming dependencies
        Queue<Character> queue = new LinkedList<>();
        for (char c : inDegree.keySet()) {
            if (inDegree.get(c) == 0) {
                queue.offer(c);
            }
        }

        StringBuilder order = new StringBuilder();

        // Step 4: Iteratively process the queue and compile the character priorities
        while (!queue.isEmpty()) {
            char curr = queue.poll();
            order.append(curr);

            for (char neighbor : adj.get(curr)) {
                inDegree.put(neighbor, inDegree.get(neighbor) - 1);
                
                if (inDegree.get(neighbor) == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        // Step 5: If our sequence length matches the unique character universe size, it's valid
        if (order.length() < inDegree.size()) {
            return ""; // Cyclic dependency deadlock occurred; return empty string
        }

        return order.toString();
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(C) -> Where C is the total number of characters across all words in the dictionary. Comparing words takes time proportional to the length of the words, and Kahn's algorithm runs linearly relative to the unique vertices and edges formed.
Space Complexity: O(U + E) -> Where U is the count of unique characters (capped at 26) and E is the unique dependency edges formed (capped at U²).
```

---

# Dry Run

## Input

```text
words = ["wrt", "wrf", "er"]
```

---

### Graph Compilation & Execution Trace

```text
1. Initialize Character Universe: Universe = {w, r, t, f, e}, inDegree = all 0
2. Compare "wrt" and "wrf": 
   - First mismatch at index 2 ('t' vs 'f') -> Edge: 't' → 'f'. inDegree['f'] = 1
3. Compare "wrf" and "er":
   - First mismatch at index 0 ('w' vs 'e') -> Edge: 'w' → 'e'. inDegree['e'] = 1
4. Queue Seed: Nodes with inDegree 0 -> queue = ['w', 'r', 't']
5. Pull 'w' -> order = "w". Neighbor 'e' inDegree drops to 0 -> queue = ['r', 't', 'e']
6. Pull 'r' -> order = "wr". No neighbors.
7. Pull 't' -> order = "wrt". Neighbor 'f' inDegree drops to 0 -> queue = ['e', 'f']
8. Pull 'e' -> order = "wrte". No neighbors.
9. Pull 'f' -> order = "wrtef".

Output matches valid sequence constraints.
```

---

# Visualization

## Priority Edge Ordering Track

```text
  [In: 0]             [In: 1]
    (w) ──────────────► (e)
    
  [In: 0]             [In: 0]             [In: 1]
    (r)                 (t) ──────────────► (f)
```

---

# Graph Paradigm Pattern

## Identify State

```text
Topological string sequence compilation utilizing string-comparison edge matching driven by Kahn's algorithm.
```

---

## Choices

```text
Identify priority limits from word mismatch offsets. Prune incoming edge nodes dynamically via a linear BFS structure.
```

Key Takeaways

✅ Advanced, high-frequency tier Graph sorting and multi-property validation question.
✅ The algorithm integrates an implicit prefix matching constraint test: a longer string preceding its own shortened base configuration violates lexicographical indexing rules.
✅ Models real-world system engines like building Spam Translation sequence models, compile optimization scripts, or parsing historical priority trees from raw textual logs.
