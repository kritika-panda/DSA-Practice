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

## Example

```java
Trie trie = new Trie();

trie.insert("apple");

System.out.println(trie.search("apple"));    // true
System.out.println(trie.search("app"));      // false

System.out.println(trie.startsWith("app"));  // true

trie.insert("app");

System.out.println(trie.search("app"));      // true
```

### Step-by-Step

#### 1. Insert `"apple"`

Trie Structure:

```text
(root)
  └── a
       └── p
            └── p
                 └── l
                      └── e*
```

`*` = End of a valid word

---

#### 2. Search `"apple"`

```java
trie.search("apple");
```

Traversal:

```text
a → p → p → l → e*
```

Result:

```text
true
```

The complete word exists and ends at a word node.

---

#### 3. Search `"app"`

```java
trie.search("app");
```

Traversal:

```text
a → p → p
```

Result:

```text
false
```

Although the path exists, `"app"` was not inserted yet, so the last node is not marked as a complete word.

---

#### 4. Check Prefix `"app"`

```java
trie.startsWith("app");
```

Traversal:

```text
a → p → p
```

Result:

```text
true
```

The prefix exists, even if it is not yet a complete word.

---

#### 5. Insert `"app"`

Trie Structure:

```text
(root)
  └── a
       └── p
            └── p*
                 └── l
                      └── e*
```

Now both words exist:

```text
app
apple
```

---

#### 6. Search `"app"` Again

```java
trie.search("app");
```

Traversal:

```text
a → p → p*
```

Result:

```text
true
```

The node is now marked as a complete word.
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
       /      
     'c'      'd'

      |        |
     'a'      'o'* (do)
   /  |  
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

# Why Do We Use a Trie over a Hash Map?

While a Hash Map provides **O(1)** average-time lookup, it has limitations for certain string-based operations. A Trie is specifically designed for efficient string processing and prefix-based queries.

> A **HashMap is optimized for exact key lookup**, while a **Trie is optimized for character-by-character traversal of strings**.

Because of this design difference, operations such as:

- Prefix Search
- Autocomplete
- Dictionary Lookup
- Spell Checking
- Word Suggestions

are significantly more efficient in a Trie.

---

# 1. Exact Lookup: HashMap vs Trie

Suppose we store the following words:

```text
apple
app
application
banana
bat
ball
```

---

## HashMap

```java
Map<String, Boolean> map = new HashMap<>();

map.put("apple", true);
map.put("app", true);
map.put("application", true);
```

Checking whether a word exists:

```java
map.containsKey("apple");
```

### Complexity

```text
O(L)
```

Where:

- `L = length of the string`

### Why not O(1)?

Before looking up a key, Java computes its hash code:

```java
"apple".hashCode()
```

This requires reading all characters:

```text
a → p → p → l → e
```

Therefore:

```text
Hash Calculation = O(L)
Bucket Lookup    = O(1)

Total            = O(L)
```

---

## Trie

Trie Structure:

```text
root
 |
 a
 |
 p
 |
 p
 |
 l
 |
 e
```

Searching for `"apple"`:

```text
a → p → p → l → e
```

### Complexity

```text
O(L)
```

Where:

- `L = length of the word`

Thus, both HashMap and Trie provide similar performance for exact word lookup.

---

# Why Use Trie Then?

Because many real-world problems involve more than exact lookup.

Examples:

```text
Find all words starting with "app"
Find all contacts starting with "jo"
Autocomplete search
Dictionary suggestions
```

This is where Trie provides a major advantage.

---

# 2. Prefix Matching Problem

Suppose we have:

```text
apple
app
application
apply
banana
bat
ball
```

Query:

```text
Find all words starting with "app"
```

Expected Output:

```text
app
apple
application
apply
```

---

## HashMap Solution

HashMap has no knowledge that certain words share a prefix.

Internally it stores independent keys:

```text
hash("apple")
hash("app")
hash("application")
hash("apply")
hash("banana")
...
```

To find matching words:

```java
for (String word : map.keySet()) {
    if (word.startsWith("app")) {
        result.add(word);
    }
}
```

Every key must be checked.

---

### Complexity

Let:

```text
N = number of words
P = prefix length
L = average word length
```

Each check:

```java
word.startsWith("app")
```

takes:

```text
O(P)
```

and is executed for all words:

```text
N times
```

Total:

```text
O(N × P)
```

Often approximated as:

```text
O(N × L)
```

---

## Example

Dictionary contains:

```text
1,000,000 words
```

Searching:

```text
"app"
```

HashMap must inspect:

```text
1,000,000 words
```

even if only a few words match.

---

## Trie Solution

Trie:

```text
root
 |
 a
 |
 p
 |
 p
 /|\
l l i
```

Searching for prefix `"app"`:

```text
a → p → p
```

### Complexity

```text
O(P)
```

Where:

- `P = prefix length`

For `"app"`:

```text
O(3)
```

The search time depends only on the prefix length, not on the number of stored words.

Whether the dictionary contains:

```text
100 words
1,000 words
1,000,000 words
100,000,000 words
```

the lookup still takes:

```text
O(P)
```

---

## Visual Comparison

### HashMap

```text
app?

apple
app
application
apply
banana
bat
ball
 ^
 scan everything
```

---

### Trie

```text
root
 |
 a
 |
 p
 |
 p
```

Directly reaches the prefix node.

---

# 3. Autocomplete Example

Imagine a search engine.

User types:

```text
jav
```

Expected suggestions:

```text
java
javascript
java tutorial
java stream
```

---

## HashMap

To generate suggestions:

```text
Scan every stored query
Check whether it starts with "jav"
```

This must be repeated for:

```text
j
ja
jav
java
```

Very expensive at scale.

---

## Trie

User input is processed incrementally:

```text
j → a → v
```

Once the `"jav"` node is reached:

```text
All suggestions are already stored under that subtree
```

### Complexity

```text
O(P + Results)
```

Where:

- `P = prefix length`

This is why Tries are commonly used in:

- Search engines
- Contact lookup
- IDE code completion
- Dictionary applications

---

# 4. Hash Collision Issue

HashMap performance depends on the quality of the hash function.

---

## Normal Case

```text
apple  → bucket 10
banana → bucket 35
cat    → bucket 88
dog    → bucket 51
```

Search complexity:

```text
O(1)
```

on average.

---

## Worst Case: Collisions

Suppose all words hash to the same bucket:

```text
apple  → bucket 5
banana → bucket 5
cat    → bucket 5
dog    → bucket 5
```

Bucket becomes:

```text
bucket 5

apple
banana
cat
dog
...
```

Searching for:

```java
containsKey("dog")
```

may require checking multiple entries.

Worst-case:

```text
O(N)
```

---

## String Comparisons Matter

To compare strings:

```text
dog
```

against:

```text
apple
banana
cat
dog
```

multiple character comparisons may occur.

Assuming:

```text
L = average string length
```

Worst-case:

```text
O(N × L)
```

---

## Trie

Trie never relies on hashing.

Searching:

```text
d → o → g
```

always follows a fixed path.

### Complexity

```text
O(L)
```

Guaranteed.

---

# 5. Space Efficiency Through Shared Prefixes

This advantage is often misunderstood.

A Trie node can consume more memory individually, but Tries can save memory when many words share prefixes.

---

## Example

Words:

```text
interview
internet
internal
interval
into
```

Common prefix:

```text
int
```

---

## HashMap Storage

Each word stores the prefix separately:

```text
interview
internet
internal
interval
into
```

Repeated characters:

```text
i n t
i n t
i n t
i n t
i n t
```

stored multiple times.

---

## Trie Storage

```text
root
 |
 i
 |
 n
 |
 t
 / \
e   o
```

Prefix:

```text
i → n → t
```

stored only once.

Branches occur only when words differ.

---

## Visual Comparison

### HashMap

```text
interview
internet
internal
interval
into
```

Stores:

```text
i n t
i n t
i n t
i n t
i n t
```

five times.

---

### Trie

```text
i
|
n
|
t
```

stored once.

---

# When Trie Saves Significant Memory

Words:

```text
precompute
predict
prediction
prefix
prevent
preview
```

Shared prefix:

```text
pre
```

Trie stores:

```text
p → r → e
```

a single time.

For dictionaries containing millions of related words, this can greatly reduce duplication.

---

# But There Is a Trade-off

A basic Trie node often looks like:

```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
}
```

Even if a node contains only one actual child:

```text
a
```

space for:

```text
26 references
```

still exists.

Therefore:

### Small Datasets

```text
HashMap usually consumes less memory.
```

### Large Datasets With Heavy Prefix Sharing

```text
Trie may be more space efficient.
```

Many real systems use optimized versions:

- Compressed Trie
- Radix Tree
- Patricia Trie
- Ternary Search Tree

to reduce memory overhead.

---

# Complexity Summary

| Operation | HashMap | Trie |
|------------|----------|-------|
| Insert Word | O(L) | O(L) |
| Search Word | O(L) Average | O(L) |
| Prefix Search | O(N × P) | O(P) |
| Autocomplete | O(N × L) | O(P + Results) |
| Worst-Case Search | O(N × L) | O(L) |
| Shared Prefix Storage | No | Yes |

Where:

- `N` = number of words
- `L` = average word length
- `P` = prefix length

---

# Interview One-Liner

> We use a Trie when efficient prefix-based operations are required. While a HashMap is excellent for exact string lookup, prefix searches require scanning all keys. A Trie stores characters hierarchically, enabling prefix search, autocomplete, and dictionary operations in **O(prefix length)** time while also allowing common prefixes to be shared across words.

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Reason |
| :--- | :--- | :--- |
| **`insert`** | **O(L)** | Where L is the length of the word being inserted. We loop exactly L times. |
| **`search`** | **O(L)** | Where L is the length of the word being searched. |
| **`startsWith`** | **O(L)** | Where L is the length of the prefix string. |

---

# Space Complexity: O(N × L × Alphabet_Size)

This space complexity is commonly used for a **Trie implementation that stores a fixed-size children array at every node**.

Example:

```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    boolean isEndOfWord;
}
```

Where:

```text
Alphabet_Size = 26
```

for lowercase English letters.

---

# Breaking It Down

Let:

- `N` = Number of words
- `L` = Average length of each word
- `Alphabet_Size` = Number of possible characters per node

Example:

```text
N = 1000 words
L = 10 characters
Alphabet_Size = 26
```

---

# Step 1: Number of Nodes

In the worst case, none of the words share any prefix.

Words:

```text
apple
zebra
qwert
mouse
...
```

Each character creates a new Trie node.

Total nodes:

```text
N × L
```

For:

```text
1000 words
10 characters each
```

Nodes:

```text
1000 × 10

= 10,000 nodes
```

---

# Step 2: Space Per Node

Each node contains:

```java
TrieNode[] children = new TrieNode[26];
```

Every node reserves space for:

```text
26 child references
```

even if only one child exists.

Example:

```text
Node('a')
```

still stores:

```text
children[0]
children[1]
children[2]
...
children[25]
```

Total:

```text
26 references
```

---

# Step 3: Total Space

Total nodes:

```text
N × L
```

Space per node:

```text
Alphabet_Size
```

Therefore:

```text
(N × L) × Alphabet_Size
```

Result:

```text
O(N × L × Alphabet_Size)
```

---

# Numerical Example

Suppose:

```text
1000 words
10 characters each
26 letters
```

Worst-case nodes:

```text
1000 × 10
= 10,000 nodes
```

Each node:

```text
26 pointers
```

Total storage:

```text
10,000 × 26

= 260,000 references
```

Thus:

```text
O(N × L × 26)
```

which is written as:

```text
O(N × L × Alphabet_Size)
```

---

# Why We Often Simplify It to O(N × L)

In Big-O analysis, the alphabet size is usually considered constant.

For English lowercase letters:

```text
Alphabet_Size = 26
```

For ASCII:

```text
Alphabet_Size = 128
```

For Extended ASCII:

```text
Alphabet_Size = 256
```

Since these values do not grow with input size:

```text
O(N × L × 26)
```

becomes:

```text
O(N × L)
```

because constants are ignored.

---

# Interview Explanation

### Precise Answer

For an array-based Trie:

```text
Space Complexity = O(N × L × Alphabet_Size)
```

because:

- There can be up to `N × L` nodes.
- Each node maintains an array of size `Alphabet_Size`.

---

### Common Simplified Answer

```text
Space Complexity = O(N × L)
```

because the alphabet size is a constant (26, 128, 256, etc.) and is omitted in Big-O notation.

---

# Example of Worst Case

Words:

```text
abc
def
ghi
jkl
```

No shared prefixes.

Trie:

```text
root
 ├─ a → b → c
 ├─ d → e → f
 ├─ g → h → i
 └─ j → k → l
```

Number of nodes:

```text
4 × 3 = 12
```

Each node stores:

```text
26 references
```

Total space:

```text
12 × 26
```

Hence:

```text
O(N × L × Alphabet_Size)
```

---

# Example of Best Case

Words:

```text
app
apple
application
apply
```

Trie:

```text
root
 |
 a
 |
 p
 |
 p
```

Most characters are shared.

Actual nodes are far fewer than:

```text
N × L
```

So the real memory usage is much smaller.

This is why Trie space complexity is usually stated as:

```text
O(Total Characters Inserted)
```

or

```text
O(N × L)
```

with the understanding that shared prefixes reduce the actual number of nodes.

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
