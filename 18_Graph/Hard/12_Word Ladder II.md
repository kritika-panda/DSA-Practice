# Word Ladder II

## Problem Statement

Given two words, `beginWord` and `endWord`, and a dictionary `wordList`, return **all shortest transformation sequences** from `beginWord` to `endWord`.

Rules:

1. Only one letter can be changed at a time.
2. Every transformed word must exist in `wordList`.
3. Each sequence must begin with `beginWord`.
4. Each sequence must end with `endWord`.

Return all shortest paths.

---

## Example

### Example 1

```text
Input:
beginWord = "hit"
endWord = "cog"

wordList =
["hot","dot","dog","lot","log","cog"]

Output:
[
 [hit,hot,dot,dog,cog],
 [hit,hot,lot,log,cog]
]
```

---

## Difference Between Word Ladder I and II

### Word Ladder I

```text
Return shortest distance only
```

Output:

```text
5
```

---

### Word Ladder II

```text
Return all shortest paths
```

Output:

```text
[
 [hit,hot,dot,dog,cog],
 [hit,hot,lot,log,cog]
]
```

---

# Key Observation

If we simply perform BFS and stop at the first occurrence of `endWord`:

```text
We get only one answer.
```

But the problem asks:

```text
ALL shortest paths
```

Therefore we need:

```text
BFS + Path Reconstruction
```

---

# Intuition

Think of every word as a graph node.

```text
hit
 |
hot
/  \
dot lot
|    |
dog log
 \  /
  cog
```

Shortest paths are:

```text
hit → hot → dot → dog → cog

hit → hot → lot → log → cog
```

We need to return both.

---

# Efficient Strategy

The standard optimal solution uses:

## Phase 1

BFS

Purpose:

```text
Find shortest distance of every word
```

and

```text
Build parent relationships
```

---

## Phase 2

DFS / Backtracking

Purpose:

```text
Generate all shortest paths
```

using the parent map.

---

# Why Parent Map?

Instead of storing complete paths during BFS:

```text
Huge memory usage
```

Store only:

```text
child → parent
```

Example:

```text
hot → hit

dot → hot

lot → hot

dog → dot

log → lot

cog → dog
cog → log
```

Notice:

```text
cog has multiple parents
```

which means:

```text
multiple shortest paths exist
```

---

# BFS Construction

Suppose:

```text
hit
```

produces:

```text
hot
```

Store:

```text
parents[hot] = [hit]
```

---

Later:

```text
hot → dot
```

Store:

```text
parents[dot] = [hot]
```

---

Later:

```text
dog → cog
log → cog
```

Store:

```text
parents[cog] = [dog, log]
```

which allows reconstruction of all shortest paths.

---

# Dry Run

## Level 1

```text
hit
```

Distance:

```text
hit = 0
```

---

## Level 2

```text
hot
```

Distance:

```text
hot = 1
```

Parent:

```text
hot ← hit
```

---

## Level 3

```text
dot
lot
```

Parent relations:

```text
dot ← hot

lot ← hot
```

---

## Level 4

```text
dog
log
```

Parent relations:

```text
dog ← dot

log ← lot
```

---

## Level 5

```text
cog
```

Parent relations:

```text
cog ← dog

cog ← log
```

---

# Parent Graph

```text
          hit
           |
          hot
        /     \
      dot     lot
       |       |
      dog     log
        \     /
          cog
```

---

# DFS Backtracking

Start from:

```text
cog
```

Path 1:

```text
cog ← dog ← dot ← hot ← hit
```

Reverse:

```text
hit → hot → dot → dog → cog
```

---

Path 2:

```text
cog ← log ← lot ← hot ← hit
```

Reverse:

```text
hit → hot → lot → log → cog
```

---

# Java Solution (BFS + DFS)

```java
import java.util.*;

class Solution {

    private Map<String, List<String>> parents = new HashMap<>();
    private List<List<String>> result = new ArrayList<>();

    public List<List<String>> findLadders(String beginWord,
                                          String endWord,
                                          List<String> wordList) {

        Set<String> dict = new HashSet<>(wordList);

        if (!dict.contains(endWord))
            return result;

        Map<String, Integer> distance = new HashMap<>();

        bfs(beginWord, endWord, dict, distance);

        if (!distance.containsKey(endWord))
            return result;

        List<String> path = new ArrayList<>();
        path.add(endWord);

        dfs(endWord, beginWord, path);

        return result;
    }

    private void bfs(String beginWord,
                     String endWord,
                     Set<String> dict,
                     Map<String, Integer> distance) {

        Queue<String> queue = new LinkedList<>();

        queue.offer(beginWord);

        distance.put(beginWord, 0);

        while (!queue.isEmpty()) {

            String word = queue.poll();

            int level = distance.get(word);

            char[] chars = word.toCharArray();

            for (int i = 0; i < chars.length; i++) {

                char original = chars[i];

                for (char ch = 'a'; ch <= 'z'; ch++) {

                    chars[i] = ch;

                    String nextWord = new String(chars);

                    if (!dict.contains(nextWord))
                        continue;

                    if (!distance.containsKey(nextWord)) {

                        distance.put(nextWord, level + 1);

                        queue.offer(nextWord);

                        parents.putIfAbsent(nextWord,
                                           new ArrayList<>());

                        parents.get(nextWord).add(word);
                    }
                    else if (distance.get(nextWord)
                             == level + 1) {

                        parents.get(nextWord).add(word);
                    }
                }

                chars[i] = original;
            }
        }
    }

    private void dfs(String word,
                     String beginWord,
                     List<String> path) {

        if (word.equals(beginWord)) {

            List<String> sequence =
                    new ArrayList<>(path);

            Collections.reverse(sequence);

            result.add(sequence);

            return;
        }

        if (!parents.containsKey(word))
            return;

        for (String parent : parents.get(word)) {

            path.add(parent);

            dfs(parent, beginWord, path);

            path.remove(path.size() - 1);
        }
    }
}
```

---

# Complexity Analysis

Let:

```text
N = Number of words
L = Length of each word
```

### BFS

For each word:

```text
L positions
26 replacements
```

Time:

```text
O(N × L × 26)
=
O(N × L)
```

---

### DFS

Depends on number of shortest paths.

Let:

```text
P = number of shortest paths
```

Then:

```text
O(P × PathLength)
```

---

### Overall

```text
Time Complexity:
O(N × L + P × L)

Space Complexity:
O(N + P × L)
```

---

# Alternative Solution

Another commonly used approach is:

```text
Queue<List<String>>
```

Store complete paths during BFS.

Example:

```text
[hit]

[hit, hot]

[hit, hot, dot]

[hit, hot, lot]
```

When reaching:

```text
cog
```

store the path.

---

## Why is it Less Efficient?

Because every queue entry stores:

```text
entire path
```

instead of a single word.

Memory grows significantly.

---

# Interview Discussion

### Why Not Use DFS Alone?

Because:

```text
DFS does not guarantee shortest path.
```

It may explore long paths first.

---

### Why BFS?

Because:

```text
BFS explores level by level.
```

The first time we reach a level:

```text
it is the shortest distance.
```

---

### Why Store Multiple Parents?

Example:

```text
dog → cog

log → cog
```

Both belong to shortest paths.

If only one parent is stored:

```text
One valid answer is lost.
```

---

# Interview Takeaways

### Pattern

```text
Unweighted Graph
+
All Shortest Paths
```

---

### Standard Technique

```text
BFS
→ Find shortest levels

DFS
→ Reconstruct all shortest paths
```

---

### Core Data Structures

```java
Map<String, Integer> distance

Map<String, List<String>> parents

Queue<String>

List<List<String>> result
```

---

### Important Observation

```text
Word Ladder I
=
Shortest Distance
=
BFS only

Word Ladder II
=
All Shortest Paths
=
BFS + DFS Backtracking
```

This BFS + Parent Mapping + DFS reconstruction approach is the most commonly expected solution in FAANG and top product-company interviews.
