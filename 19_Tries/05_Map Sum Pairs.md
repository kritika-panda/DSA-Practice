# Map Sum Pairs

## Problem Statement

Design a map that allows you to associate integer values with string keys. The map must support two main operations: inserting a key-value pair and summing the values of all keys that share a specific prefix.

Implement the `MapSum` class:
1. `void insert(String key, int val)`: Inserts the `key-value` pair into the map. If the `key` already existed, the original value will be overwritten to the new one.
2. `int sum(String prefix)`: Returns the sum of all the pairs' values whose `key` starts with the `prefix`.

The overall run time complexity for prefix summation should be:

```text
O(l)
```

*(where l is the length of the prefix string)*

---

## Examples

### Example 1

**Input**

```java
MapSum mapSum = new MapSum();
mapSum.insert("apple", 3);  
mapSum.sum("ap");           // return 3 (apple = 3)
mapSum.insert("app", 2);    
mapSum.sum("ap");           // return 5 (apple + app = 3 + 2 = 5)
```

---

## Brute Force Approach

Store all incoming elements inside a standard Hash Map collection and manually loop over keys for matching prefixes.

### Steps

1. `insert(key, val)`: Add or overwrite the key and value straight into a standard `HashMap<String, Integer>`.
2. `sum(prefix)`: Initialize a total sum tracking counter to 0. Iterate through every single key string in the map. If `key.startsWith(prefix)` evaluates to true, add its associated value to our counter.

### Complexity

```text
Time Complexity: O(1) for insertion, O(n * m) for summation
Space Complexity: O(n * m)
```

*(where n is the total number of keys and m is the average length of a key)*

Scanning every key string sequentially becomes highly inefficient when the data pool scales. We can optimize the prefix search path using a modified Trie structure.

---

# Optimal Approach: Trie with Precalculated Prefix Sums

## Key Idea

A **Trie** naturally optimizes prefix filtering. We can achieve a strict O(l) summation runtime by **precalculating and caching prefix totals during insertion**.

Instead of only storing the end flag of a completed word, each node in our Trie maintains a `prefixSum` tracking score:
- When a completely new `key` is inserted, we traverse its character path from the root down to the leaf, adding `val` directly to the `prefixSum` score of every node we step through.
- If a `key` already exists, inserting it overwrites its previous value. To handle this properly, we first determine the value difference: `delta = newVal - oldVal`. We then traverse down the Trie path and update each node's precalculated total by adding `delta`.

To quickly check if a key already exists and retrieve its old value, we maintain a secondary standard Hash Map alongside the Trie.

---

## Visual Understanding

Suppose we run the following sequence:

```java
mapSum.insert("apple", 3);
mapSum.insert("app", 2);
```

Trie structural nodes tracking cached sums:

```text
  (root) [sum=5]
    |
   'a'   [sum=5]
    |
   'p'   [sum=5]
    |
   'p'   [sum=5]
    |
   'l'   [sum=3]
    |
   'e'   [sum=3]
```

When `mapSum.sum("ap")` is called, the function simply walks down to the node representing `'p'` and immediately reads its precalculated value, returning `5` in O(prefix length) time.

---

## Partition Variables

Let:

```java
class TrieNode {
    TrieNode[] children = new TrieNode;
    int prefixSum = 0;
}
Map<String, Integer> keyToVal = new HashMap<>();
TrieNode root = new TrieNode();
```

---

### Border Elements

The traversal checks prevent null-pointer exceptions if a prefix does not match any known branches:

```java
if (current.children[index] == null) {
    return 0; // Prefix path does not exist
}
```

---

## Correct Partition Condition

When evaluating the total sum at the target prefix boundary position:

```java
for (char c : prefix.toCharArray()) {
    int index = c - 'a';
    if (current.children[index] == null) return 0;
    current = current.children[index];
}
return current.prefixSum;
```

---

## How to Move Binary Search

*(Note: This design approach swaps traditional binary search range tracking for direct precalculated retrieval paths inside an advanced Trie structure).*

---

## Java Solution

```java
import java.util.HashMap;
import java.util.Map;

class MapSum {

    private class TrieNode {
        private TrieNode[] children;
        private int prefixSum;

        public TrieNode() {
            this.children = new TrieNode; // For lowercase English letters
            this.prefixSum = 0;
        }
    }

    private final TrieNode root;
    private final Map<String, Integer> keyToVal;

    public MapSum() {
        root = new TrieNode();
        keyToVal = new HashMap<>();
    }
    
    // Inserts or updates a key-value pair, modifying precalculated sums
    public void insert(String key, int val) {
        int oldVal = keyToVal.getOrDefault(key, 0);
        int delta = val - oldVal; // Calculate value change profile
        keyToVal.put(key, val);

        TrieNode current = root;
        current.prefixSum += delta; // Update root precalc score

        for (char c : key.toCharArray()) {
            int index = c - 'a';
            if (current.children[index] == null) {
                current.children[index] = new TrieNode();
            }
            current = current.children[index];
            current.prefixSum += delta; // Accumulate delta across paths
        }
    }
    
    // Returns the precalculated prefix summation match instantly
    public int sum(String prefix) {
        TrieNode current = root;
        
        for (char c : prefix.toCharArray()) {
            int index = c - 'a';
            if (current.children[index] == null) {
                return 0;
            }
            current = current.children[index];
        }
        
        return current.prefixSum;
    }
}
```

---

## Dry Run

### Input Operations

```java
MapSum mapSum = new MapSum();
mapSum.insert("apple", 3);
mapSum.insert("app", 2);
mapSum.sum("ap");
```

---

### Step Execution Traversal

1. **`insert("apple", 3)`**:
   - `key` does not exist in `keyToVal`. `delta = 3 - 0 = 3`.
   - Traverses nodes: `root -> 'a' -> 'p' -> 'p' -> 'l' -> 'e'`.
   - Each node along the path increments its `prefixSum` by `+3`.

2. **`insert("app", 2)`**:
   - `key` does not exist in `keyToVal`. `delta = 2 - 0 = 2`.
   - Traverses nodes: `root -> 'a' -> 'p' -> 'p'`.
   - Paths for `root`, `'a'`, `'p'`, and `'p'` increment their cached `prefixSum` values by `+2`.

3. **`sum("ap")`**:
   - Traverses character states down to the second node: `root -> 'a' -> 'p'`.
   - Reads the final matched node's `prefixSum` value directly, returning `5`.

---

### Answer

```java
5
```

---

## Why Do We Precalculate Sums inside the Trie?

By shifting computation weight to the insertion phase, we avoid running deep subtree traversals or matching every map key whenever a sum is requested. The node path stores the evaluation outcome directly, transforming a prefix search query into a direct retrieval step.

This design framework yields:

```text
O(k) for insertion, O(l) for sum lookups
```

*(where k is the key word length and l is the prefix search string length)*

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Reason |
| :--- | :--- | :--- |
| **`insert`** | **O(k)** | Where k is the length of the string key. We loop exactly k times. |
| **`sum`** | **O(l)** | Where l is the length of the prefix string being evaluated. |

---

### Space Complexity

```text
O(n * k * 26)
```

The underlying system allocates storage nodes matching the distinct character paths instantiated to maintain word map trees.

---

## Key Insight

Adding local aggregate tracking fields onto individual structural nodes transforms a heavy lookup aggregation pattern into an efficient constant-time reading step at the prefix boundary.

```text
Time  : O(l) for query evaluation
Space : O(n * k * 26)
```

---

## Similar Problems

1. Implement Trie (Prefix Tree) (208)
2. Design Add and Search Words Data Structure (211)
3. Replace Words (648)
4. Longest Word in Dictionary (720)
5. Prefix and Suffix Search (745)
