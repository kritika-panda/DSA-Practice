# Number of Matching Subsequences

## Problem Statement

Given a string `s` and an array of strings `words`, return *the number of `words[i]` that is a **subsequence** of `s`*.

A **subsequence** of a string is a new string generated from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters (i.e., `"ace"` is a subsequence of `"abcde"` while `"aec"` is not).

The overall runtime complexity must be strictly bounded by:

```text
O(length(s) + total_chars(words))
```

---

## Examples

### Example 1

**Input**

```java
s = "abcde"
words = ["a", "bb", "acd", "ace"]
```

**Output**

```java
3
```

**Explanation**

There are three words that are subsequences of `s`: `"a"`, `"acd"`, and `"ace"`.

---

### Example 2

**Input**

```java
s = "dsahjpjauf"
words = ["ahjpjau", "ja", "ahbwzgqnuk", "tnmlcaqoku"]
```

**Output**

```java
2
```

---

## Brute Force Approach

For each word in the `words` array, run a separate two-pointer subsequence check linearly against the target string `s`.

### Steps

1. Initialize a `count = 0`.
2. For each word in `words`:
   - Initialize two pointers: `i = 0` for `word` and `j = 0` for `s`.
   - Advance `j` through `s`, incrementing `i` whenever `word.charAt(i) == s.charAt(j)`.
   - If `i == word.length()`, increment `count`.
3. Return `count`.

### Complexity

```text
Time Complexity: O(words.length * length(s))
Space Complexity: O(1)
```

If the `words` array is massive and `s` is very long, checking each word independently from scratch repeatedly scans `s`, leading to a severe **Time Limit Exceeded (TLE)** timeout. We need to process all words **simultaneously** in a single pass over `s`.

---

# Optimal Approach: Multi-Pointer Bucket Slicing

## Key Idea

Instead of matching each word against `s` individually, we can scan `s` **exactly once** and advance the matching state of all words together. 

We can bucket the words based on the **character they are currently waiting for**:
1. Create an array of 26 buckets (lists), one for each lowercase letter.
2. Each item in a bucket will store a custom pointer node containing:
   - The original word string token.
   - The current index position within that word we need to match next.
3. **Initialization:** For each word in `words`, wrap it in a node at index `0` and place it into the bucket corresponding to its very first character (`word.charAt(0)`).
4. **Single Pass Sweep:** Iterate through the characters of `s` from left to right:
   - Grab the bucket matching the current character `c`. Clear this bucket immediately so we can re-distribute its nodes.
   - For each node in that bucket, increment its word index pointer because we just found a match!
   - If the incremented index reaches the end of the word, it is a fully verified subsequence—increment our global `count`.
   - Otherwise, place the node into the new bucket corresponding to the *next* character the word is waiting for.

This handles all words simultaneously in a unified parallel timeline.

---

## Visual Understanding

Suppose `s = "abcde"` and `words = ["acd", "ace"]`:

- **Initialization:** 
  - Both words start with `'a'`. 
  - Bucket `['a']` holds: `Node("acd", index:0)` and `Node("ace", index:0)`. All other buckets are empty.

- **Scan `s` character by character:**
  - **`c = 'a'`:** 
    - Fetch and clear Bucket `['a']`.
    - `Node("acd")` advances to `index:1` (waiting for `'c'`). Move to Bucket `['c']`.
    - `Node("ace")` advances to `index:1` (waiting for `'c'`). Move to Bucket `['c']`.
  - **`c = 'b'`:** Bucket `['b']` is empty. No action.
  - **`c = 'c'`:** 
    - Fetch and clear Bucket `['c']`.
    - `Node("acd")` advances to `index:2` (waiting for `'d'`). Move to Bucket `['d']`.
    - `Node("ace")` advances to `index:2` (waiting for `'e'`). Move to Bucket `['e']`.
  - **`c = 'd'`:** 
    - Fetch and clear Bucket `['d']`.
    - `Node("acd")` advances to `index:3`. End of word reached! **`count = 1`**.
  - **`c = 'e'`:** 
    - Fetch and clear Bucket `['e']`.
    - `Node("ace")` advances to `index:3`. End of word reached! **`count = 2`**.

The algorithm terminates with `count = 2` in a single pass over `s`.

---

## Partition Variables

Let:

```java
// Array of lists to act as our 26 character tracking buckets
List<Node>[] heads = new ArrayList[26];
for (int i = 0; i < 26; i++) heads[i] = new ArrayList<>();
```

---

### Node Structural Definition

We use a simple object layer to hold our state transitions efficiently:

```java
class Node {
    String word;
    int index;
    Node(String word, int index) {
        this.word = word;
        this.index = index;
    }
}
```

---

## Correct Partition Condition

The nodes advance and shift buckets dynamically based on whether they have more pending characters to resolve:

```java
if (node.index == node.word.length()) {
    count++; // Subsequence fully validated
} else {
    char nextChar = node.word.charAt(node.index);
    heads[nextChar - 'a'].add(node); // Move to the next waiting bucket
}
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    // Lightweight state node to track progress across words
    private static class Node {
        String word;
        int index;

        Node(String word, int index) {
            this.word = word;
            this.index = index;
        }
    }

    public int numMatchingSubseq(String s, String[] words) {
        // Step 1: Initialize 26 character waiting buckets
        List<Node>[] buckets = new ArrayList[26];
        for (int i = 0; i < 26; i++) {
            buckets[i] = new ArrayList<>();
        }

        // Step 2: Categorize all words based on their initial starting character
        for (String word : words) {
            if (word.length() > 0) {
                buckets[word.charAt(0) - 'a'].add(new Node(word, 0));
            }
        }

        int count = 0;

        // Step 3: Scan the target string s exactly once from left to right
        for (int i = 0; i < s.length(); i++) {
            char currChar = s.charAt(i);
            int bucketIdx = currChar - 'a';

            // Isolate and clear the active waiting bucket to process its nodes
            List<Node> activeBucket = buckets[bucketIdx];
            buckets[bucketIdx] = new ArrayList<>();

            for (Node node : activeBucket) {
                node.index++; // The current character matched! Move to next index

                // Condition A: Word is fully parsed and verified as a subsequence
                if (node.index == node.word.length()) {
                    count++;
                } 
                // Condition B: Word is waiting for another character down its line
                else {
                    char nextChar = node.word.charAt(node.index);
                    buckets[nextChar - 'a'].add(node); // Re-queue into its new waiting bucket
                }
            }
        }

        return count;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Multi-Pointer Bucket Slicing** | **O(length(s) + total_chars(words))** | **O(words.length)** | Each character in `s` is visited once. Each character of every word is moved between buckets at most once when a match occurs. The space allocation holds a single node per input word. |

---

## Key Insight

Grouping the word streams into buckets based on the character they are currently waiting for converts a repetitive multi-scan problem into an efficient single-pass streaming evaluation.

```text
Time  : O(length(s) + total_chars(words)) optimal linear scale
Space : O(words.length) for structural tracking nodes
```

---

## Similar Problems

1. Is Subsequence (392)
2. Longest Word in Dictionary through Deleting (524)
3. Shortest Way to Form String (1055)
4. Distinct Subsequences (115)
5. Find All Anagrams in a String (438)
