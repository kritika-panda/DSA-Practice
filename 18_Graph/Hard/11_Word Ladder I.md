# Word Ladder

## Problem Statement

A transformation sequence from word `beginWord` to word `endWord` using a dictionary `wordList` is a sequence of words such that:

1. The first word is `beginWord`.
2. The last word is `endWord`.
3. Only one character can be changed at a time.
4. Each transformed word must exist in `wordList`.

Return the length of the **shortest transformation sequence** from `beginWord` to `endWord`.

If no such sequence exists, return `0`.

---

## Example

### Example 1

```text
Input:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log","cog"]

Output:
5

Explanation:
hit → hot → dot → dog → cog

Length = 5
```

### Example 2

```text
Input:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]

Output:
0

Explanation:
"cog" does not exist in wordList.
```

---

# Intuition

Think of each word as a node in a graph.

Two words are connected if they differ by exactly one character.

Example:

```text
hot ─ dot
 │     │
lot ─ log
       │
      cog
```

We need the **shortest path** from `beginWord` to `endWord`.

Whenever we need the shortest path in an unweighted graph:

✅ Use **Breadth First Search (BFS)**

---

# Approach

## Step 1: Store Dictionary in HashSet

Fast lookup:

```java
Set<String> dict = new HashSet<>(wordList);
```

Checking existence becomes:

```text
O(1)
```

---

## Step 2: Start BFS

Queue stores:

```text
(Current Word, Current Level)
```

Example:

```java
Queue<Pair<String, Integer>> q
```

Initially:

```text
(hit,1)
```

---

## Step 3: Generate Neighbors

For every character position:

```text
hot
 ^
```

Replace it with:

```text
a,b,c,d,...z
```

Generated words:

```text
aot
bot
cot
...
zot
```

If generated word exists in dictionary:

```java
dict.contains(word)
```

enqueue it.

---

## Step 4: Mark Visited

Remove from dictionary immediately.

```java
dict.remove(word);
```

This prevents revisiting.

---

## Dry Run

```text
beginWord = hit
endWord = cog

dictionary:
hot dot dog lot log cog
```

### Level 1

```text
hit
```

Generate:

```text
hot
```

Queue:

```text
hot
```

---

### Level 2

```text
hot
```

Generate:

```text
dot
lot
```

Queue:

```text
dot, lot
```

---

### Level 3

```text
dot → dog
lot → log
```

Queue:

```text
dog, log
```

---

### Level 4

```text
dog → cog
```

Found end word.

Answer:

```text
5
```

---

# BFS Visualization

```text
                hit
                 |
                hot
             /       \
           dot       lot
            |         |
           dog       log
             \       /
               cog
```

Shortest path discovered level by level.

---

# Java Solution (BFS)

```java
import java.util.*;

class Solution {
    public int*ladderLength(String beginWord,
   *                        String endWord,
                            List<String> wordList) {

        Set<String> dict = new HashSet<>(wordList);

        if (!dict.contains(endWord))
            return 0;

        Queue<String> queue = new LinkedList<>();
        queue.offer(beginWord);

        int level = 1;

        while (!queue.isEmpty()) {

            int size = queue.size();

            for (int i = 0; i < size; i++) {

                String word = queue.poll();

                if (word.equals(endWord))
                    return level;

                char[] chars = word.toCharArray();

                for (int pos = 0; pos < chars.length; pos++) {

                    char original = chars[pos];

                    for (char ch = 'a'; ch <= 'z'; ch++) {

                        chars[pos] = ch;

                        String nextWord = new String(chars);

                        if (dict.contains(nextWord)) {
                            queue.offer(nextWord);
                            dict.remove(nextWord);
                        }
                    }

                    chars[pos] = original;
                }
            }

            level++;
        }

        return 0;
    }
}
```

---

# Complexity Analysis

Let:

```text
N = number of words
L = length of each word
```

For every word:

- Try all positions → `L`
- For each position try `26` letters

Work per word:

```text
O(26 * L)
```

Total:

```text
Time Complexity = O(N × L × 26)
                = O(N × L)

Space Complexity = O(N)
```

---

# Why BFS Works?

BFS explores:

```text
Level 1
Level 2
Level 3
...
```

The first time we reach:

```text
endWord
```

we are guaranteed to have found the shortest transformation sequence.

Hence BFS is optimal for this problem.

---

# Optimized Approach: Bidirectional BFS

Instead of searching only from:

```text
beginWord → endWord
```

Search from both ends:

```text
beginWord ←→ endWord
```

Example:

```text
hit → hot → dot

cog ← dog ← dot
```

Search spaces meet in the middle.

---

## Why Faster?

Normal BFS:

```text
Branching factor = b
Depth = d

Time ≈ O(b^d)
```

Bidirectional BFS:

```text
O(b^(d/2) + b^(d/2))
```

Huge improvement for large dictionaries.

---

# Bidirectional BFS Solution

```java
import java.util.*;

class Solution {

    public int ladderLength(String beginWord,
                            String endWord,
                            List<String> wordList) {

        Set<String> dict = new HashSet<>(wordList);

        if (!dict.contains(endWord))
            return 0;

        Set<String> beginSet = new HashSet<>();
        Set<String> endSet = new HashSet<>();

        beginSet.add(beginWord);
        endSet.add(endWord);

        int level = 1;

        while (!beginSet.isEmpty() && !endSet.isEmpty()) {

            if (beginSet.size() > endSet.size()) {
                Set<String> temp = beginSet;
                beginSet = endSet;
                endSet = temp;
            }

            Set<String> nextLevel = new HashSet<>();

            for (String word : beginSet) {

                char[] chars = word.toCharArray();

                for (int i = 0; i < chars.length; i++) {

                    char original = chars[i];

                    for (char ch = 'a'; ch <= 'z'; ch++) {

                        chars[i] = ch;

                        String nextWord = new String(chars);

                        if (endSet.contains(nextWord))
                            return level + 1;

                        if (dict.contains(nextWord)) {
                            nextLevel.add(nextWord);
                            dict.remove(nextWord);
                        }
                    }

                    chars[i] = original;
                }
            }

            beginSet = nextLevel;
            level++;
        }

        return 0;
    }
}
```

---

# Interview Takeaways

### Pattern Identification

```text
Word → Node
One-character change → Edge
Shortest sequence → Shortest path
```

### Key Observation

```text
Unweighted Graph
+
Shortest Path
=
BFS
```

### Follow-up

```text
Can we optimize further?
```

Answer:

```text
Use Bidirectional BFS
```

This reduces the search space dramatically and is the preferred interview solution for Word Ladder.
