# Design Search Autocomplete System

**LeetCode 642 - Design Search Autocomplete System**

## Problem Statement

Design a search autocomplete system for a search engine.

Users may input a sentence character by character.

For each character typed:

- Return the top `3` historical sentences that have the same prefix as the current input.
- Suggestions should be sorted by:
  1. Higher frequency first.
  2. If frequencies are equal, lexicographically smaller sentence first.

Special character:

```java
'#'
```

indicates the current sentence is completed.

When `'#'` is entered:

- Store the sentence in the system.
- Increase its frequency by `1`.
- Return an empty list.

Implement the `AutocompleteSystem` class:

```java
AutocompleteSystem(String[] sentences, int[] times)

List<String> input(char c)
```

---

## Example

### Input

```java
sentences = ["i love you",
             "island",
             "ironman",
             "i love leetcode"]

times = [5, 3, 2, 2]
```

---

### Query

```java
input('i')
```

Available sentences:

```java
"i love you"      -> 5
"island"          -> 3
"ironman"         -> 2
"i love leetcode" -> 2
```

Top 3:

```java
["i love you", "island", "i love leetcode"]
```

---

### Query

```java
input(' ')
```

Current prefix:

```java
"i "
```

Matching sentences:

```java
"i love you"      -> 5
"i love leetcode" -> 2
```

Result:

```java
["i love you", "i love leetcode"]
```

---

### Query

```java
input('a')
```

Prefix:

```java
"i a"
```

No matches.

Result:

```java
[]
```

---

### Query

```java
input('#')
```

Store:

```java
"i a"
```

into history.

Return:

```java
[]
```

---

# Key Observations

We need to support:

```text
1. Prefix Search
2. Frequency Tracking
3. Dynamic Insertions
4. Top 3 Results
```

These requirements strongly suggest using a:

```text
Trie (Prefix Tree)
```

---

# Data Structure Design

Each Trie node stores:

```java
children
```

for next characters and

```java
countMap
```

containing all sentences passing through that node.

---

## Trie Node

```java
class TrieNode {
    Map<Character, TrieNode> children;
    Map<String, Integer> countMap;
}
```

---

# Why Store Sentences at Every Node?

Consider:

```text
i
├── l
│   └── o
```

Every node represents a prefix.

When searching:

```java
"i"
```

we instantly know all sentences beginning with `"i"`.

This avoids traversing the entire Trie during every query.

---

# Approach

## Initialization

Insert every sentence into the Trie.

For each node visited:

```java
countMap.put(sentence, frequency)
```

---

## Typing a Character

When a character is entered:

1. Append it to the current prefix.
2. Traverse the Trie to that prefix.
3. Collect all candidate sentences.
4. Sort by:
   - Frequency descending
   - Lexicographical ascending
5. Return top 3.

---

## End Character '#'

When:

```java
'#'
```

is received:

1. Insert current sentence into Trie.
2. Increase frequency by 1.
3. Reset input buffer.
4. Return empty list.

---

# Java Solution

```java
class AutocompleteSystem {

    class TrieNode {

        Map<Character, TrieNode> children;
        Map<String, Integer> countMap;

        TrieNode() {
            children = new HashMap<>();
            countMap = new HashMap<>();
        }
    }

    TrieNode root;
    String currentInput;

    public AutocompleteSystem(String[] sentences,
                              int[] times) {

        root = new TrieNode();
        currentInput = "";

        for (int i = 0; i < sentences.length; i++) {
            insert(sentences[i], times[i]);
        }
    }

    private void insert(String sentence, int count) {

        TrieNode node = root;

        for (char ch : sentence.toCharArray()) {

            node.children.putIfAbsent(
                ch,
                new TrieNode()
            );

            node = node.children.get(ch);

            node.countMap.put(
                sentence,
                node.countMap.getOrDefault(sentence, 0)
                + count
            );
        }
    }

    public List<String> input(char c) {

        if (c == '#') {

            insert(currentInput, 1);

            currentInput = "";

            return new ArrayList<>();
        }

        currentInput += c;

        TrieNode node = root;

        for (char ch : currentInput.toCharArray()) {

            if (!node.children.containsKey(ch)) {
                return new ArrayList<>();
            }

            node = node.children.get(ch);
        }

        List<String> candidates =
                new ArrayList<>(node.countMap.keySet());

        Collections.sort(
            candidates,
            (a, b) -> {

                int countA =
                        node.countMap.get(a);

                int countB =
                        node.countMap.get(b);

                if (countA == countB) {
                    return a.compareTo(b);
                }

                return countB - countA;
            }
        );

        if (candidates.size() > 3)
            return candidates.subList(0, 3);

        return candidates;
    }
}
```

---

# Dry Run

## Initialization

```java
sentences =
[
   "i love you",
   "island",
   "ironman",
   "i love leetcode"
]

times =
[
   5,
   3,
   2,
   2
]
```

Trie stores frequency information at every prefix node.

---

## Input 'i'

Prefix:

```java
"i"
```

Candidates:

```java
i love you      -> 5
island          -> 3
ironman         -> 2
i love leetcode -> 2
```

Sort:

```java
5 > 3 > 2
```

Result:

```java
[
 "i love you",
 "island",
 "i love leetcode"
]
```

---

## Input ' '

Prefix:

```java
"i "
```

Candidates:

```java
i love you      -> 5
i love leetcode -> 2
```

Result:

```java
[
 "i love you",
 "i love leetcode"
]
```

---

## Input 'a'

Prefix:

```java
"i a"
```

No matching node.

Result:

```java
[]
```

---

## Input '#'

Insert:

```java
"i a"
```

Frequency:

```java
1
```

Reset current input.

Return:

```java
[]
```

---

# Optimization Discussion

The above solution stores:

```java
Map<String, Integer>
```

at every Trie node.

This makes querying fast but increases memory usage.

---

## Production-Level Optimization

Store only:

```java
Top 3 Sentences
```

at every Trie node.

Insertion updates:

```java
Top K heap/list
```

for every prefix.

This reduces query time from:

```text
O(N log N)
```

to almost:

```text
O(1)
```

per node traversal.

---

# Complexity Analysis

Assume:

```java
n = number of sentences
m = average sentence length
```

---

### Building Trie

```text
O(n × m)
```

---

### Query

Trie Traversal:

```text
O(prefix length)
```

Sorting candidates:

```text
O(k log k)
```

where:

```java
k = number of matching sentences
```

---

### Space Complexity

Trie Storage:

```text
O(total characters)
```

plus stored frequency maps.

---

# Follow-Up: Optimized Design

Many interviewers ask:

> Can we make queries faster?

The answer is **yes**.

Instead of storing all matching sentences at every Trie node, we store only the **Top 3 hottest sentences** for that prefix.

---

## Basic Approach

At every Trie node:

```java
Map<String, Integer> countMap
```

stores all sentences passing through that prefix.

For every query:

1. Retrieve all matching sentences.
2. Sort them by:
   - Frequency descending
   - Lexicographical ascending
3. Return top 3.

### Complexity

#### Insert

```text
O(length)
```

#### Search

```text
O(length + k log k)
```

where:

```text
k = number of matching sentences
```

---

## Optimized Approach

Store only the top 3 hottest sentences at every Trie node.

```java
class TrieNode {
    Map<Character, TrieNode> children;
    List<String> topSentences;
}
```

Now each node directly contains:

```text
Top 3 most frequently searched sentences
for that prefix
```

---

## Example

Suppose we have:

```java
"i love you"      -> 5
"island"          -> 3
"ironman"         -> 2
"i love leetcode" -> 2
```

At prefix:

```java
"i"
```

store:

```java
[
    "i love you",
    "island",
    "i love leetcode"
]
```

At prefix:

```java
"i "
```

store:

```java
[
    "i love you",
    "i love leetcode"
]
```

---

## Search Operation

Suppose user types:

```java
input('i')
```

### Without Optimization

```text
Traverse Trie
→ Get all matching sentences
→ Sort them
→ Return top 3
```

### With Optimization

```text
Traverse Trie
→ Return node.topSentences
```

No sorting required.

---

## Complexity

### Insert

For each character:

```text
Update node's Top 3 list
```

Complexity:

```text
O(length)
```

because the Top 3 list size is constant.

---

### Search

Simply traverse the Trie.

```text
O(length)
```

No additional sorting.

---

## Why Space Increases

The same sentence may appear in the top-3 list of many Trie nodes.

Example:

```java
"i love you"
```

can exist at:

```text
i
i_
i_l
i_lo
i_lov
i_love
...
```

This increases memory usage.

---

## Trade-Off

### Basic Design

```text
Insert  -> O(length)
Search  -> O(length + k log k)
Space   -> Lower
```

### Optimized Design

```text
Insert  -> O(length)
Search  -> O(length)
Space   -> Higher
```

---

## Which Design Do Interviewers Prefer?

### LeetCode Solution

Usually acceptable:

```text
Trie + Frequency Map
```

---

### System Design / Google Follow-Up

Expected answer:

```text
Store Top 3 hottest sentences at every Trie node.
```

because query speed is more important than memory usage in autocomplete systems.

Examples:

```text
Google Search
YouTube Search
Amazon Search
Netflix Search
```

prioritize:

```text
Fast Query Response
```

over memory optimization.

---

## Key Insight

A production-grade autocomplete system avoids sorting during every search.

Instead:

```text
Each Trie node maintains its Top 3 hottest sentences.
```

which allows:

```text
Insert  -> O(length)
Search  -> O(length)
```

This is the optimal design typically discussed in senior-level FAANG interviews.
