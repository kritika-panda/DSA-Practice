# Number of Matching Subsequences

## Problem Statement

Given a string `s` and an array of strings `words`, return the number of `words[i]` that is a subsequence of `s`.

A **subsequence** of a string is a new string generated from the original string with some characters (can be none) deleted without changing the relative order of the remaining characters. (i.e., `"ace"` is a subsequence of `"abcde"` while `"aec"` is not).

The overall run time complexity should be:

```text
O(s.length() + ∑ words[i].length())
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

There are three words that are subsequences of `s`: `"a"`, `"acd"`, and `"ace"`. `"bb"` is not because `'b'` only appears once in `s`.

---

### Example 2

**Input**

```java
s = "dsahjpjauf"
words = ["ahjpjau", "ja", "ahbwgqk", "tnmlca"]
```

**Output**

```java
2
```

---

### Example 3

**Input**

```java
s = "a"
words = ["a", "a", "a"]
```

**Output**

```java
3
```

---

## Brute Force Approach

Check each word independently using a two-pointer approach to determine if it is a subsequence of `s`.

### Steps

1. Initialize a `count` variable to 0.
2. Loop through each word in the `words` array.
3. For each word, use two pointers (`p1` for `s` and `p2` for `word`) to check if it's a subsequence.
4. If a word is verified as a subsequence, increment `count`.
5. Return the final `count`.

### Complexity

```text
Time Complexity: O(words.length * s.length())
Space Complexity: O(1)
```

If `words` contains many long strings or repetitive entries, checking `s` from scratch for every single word causes a severe Time Limit Exceeded (TLE) bottleneck.

---

# Optimal Approach: Parallel State Tracking via Bucket Lists

## Key Idea

Instead of checking each word against `s` sequentially, we can process `s` character by character **just once** and advance all words in parallel using an array of buckets.

We create 26 buckets (one for each lowercase letter). Each bucket holds words that are currently waiting for that specific character to appear in `s`. We represent each word dynamically as a state pair or a simple string iterator wrapper: `(word, next_char_index)`.

1. Group all words into the 26 buckets based on their **very first character**.
2. Iterate through each character `c` of string `s` sequentially.
3. Grab the entire bucket list associated with character `c`. Clear that bucket.
4. For each word entry in that list, advance its index pointer by 1:
   - If the index pointer reaches the end of the word, it means the entire word has been successfully matched as a subsequence! Increment our total count.
   - If it hasn't reached the end, look up its *next* required character and move the word entry into that corresponding character's bucket list.

---

## Visual Understanding

Suppose:

```java
s = "abcde"
words = ["a", "acd", "ace"]
```

1. **Initialize Buckets based on first characters:**
   - Bucket `'a'`: `[("a", 0), ("acd", 0), ("ace", 0)]`
   - All other buckets are empty.

2. **Process `s` sequentially:**
   - **Step 1 (`c = 'a'`):** Grab Bucket `'a'`.
     - Entry `"a"`: Advance index to 1 (End of word!). `count = 1`.
     - Entry `"acd"`: Next character is `'c'`. Move to Bucket `'c'`.
     - Entry `"ace"`: Next character is `'c'`. Move to Bucket `'c'`.
   - **Step 2 (`c = 'b'`):** Grab Bucket `'b'` (Empty). Nothing changes.
   - **Step 3 (`c = 'c'`):** Grab Bucket `'c'` `[("acd", 1), ("ace", 1)]`.
     - Entry `"acd"`: Next character is `'d'`. Move to Bucket `'d'`.
     - Entry `"ace"`: Next character is `'e'`. Move to Bucket `'e'`.
   - **Step 4 (`c = 'd'`):** Grab Bucket `'d'` `[("acd", 2)]`.
     - Entry `"acd"`: Advance index to 3 (End of word!). `count = 2`.
   - **Step 5 (`c = 'e'`):** Grab Bucket `'e'` `[("ace", 2)]`.
     - Entry `"ace"`: Advance index to 3 (End of word!). `count = 3`.

Final `count` = `3`.

---

## Partition Variables

Let:

```java
List<StringWithIndex>[] buckets = new ArrayList[26];
int count = 0;
```

---

### Border Elements

A simple structural wrapper or a customized `StringIterator` keeps track of structural alignment:

```java
class WordState {
    String word;
    int index;
    WordState(String word, int index) {
        this.word = word;
        this.index = index;
    }
}
```

---

## Correct Partition Condition

When a pointer successfully reaches the boundary length of its associated word tracking string:

```java
if (state.index == state.word.length()) {
    count++;
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces traditional binary search range tracking with a multi-list reactive bucket simulation framework to process streaming text states in a single linear pass).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    private class WordState {
        String word;
        int index;

        WordState(String word, int index) {
            this.word = word;
            this.index = index;
        }
    }

    public int numMatchingSubseq(String s, String[] words) {

        // Step 1: Initialize 26 tracking buckets
        List<WordState>[] buckets = new ArrayList[26];
        for (int i = 0; i < 26; i++) {
            buckets[i] = new ArrayList<>();
        }

        // Step 2: Distribute words into buckets based on their first character
        for (String word : words) {
            if (word.length() > 0) {
                int bucketIdx = word.charAt(0) - 'a';
                buckets[bucketIdx].add(new WordState(word, 1));
            }
        }

        int count = 0;

        // Step 3: Process the string s sequentially in a single pass
        for (char c : s.toCharArray()) {
            int bucketIdx = c - 'a';
            
            // Grab the current active bucket list and clear it for reuse
            List<WordState> activeList = buckets[bucketIdx];
            buckets[bucketIdx] = new ArrayList<>();

            for (WordState state : activeList) {
                // If the word state pointer reached its end, a subsequence is matched
                if (state.index == state.word.length()) {
                    count++;
                } 
                // Otherwise, move the word state into its next required character bucket
                else {
                    int nextBucketIdx = state.word.charAt(state.index) - 'a';
                    state.index++; // Advance pointer index forward
                    buckets[nextBucketIdx].add(state);
                }
            }
        }

        return count;
    }
}
```

---

## Dry Run

### Input

```java
s = "abcde"
words = ["acd", "ace"]
```

---

### Initial State

```java
buckets['a'-'a'] = [("acd", 1), ("ace", 1)]
count = 0
```

---

### Step Execution Traversal

- **c = 'a':** `activeList = [("acd", 1), ("ace", 1)]`.
  - `"acd"` has next char `'c'`. Moves to `buckets['c'-'a']` with state pointer index set to `2`.
  - `"ace"` has next char `'c'`. Moves to `buckets['c'-'a']` with state pointer index set to `2`.
- **c = 'b':** `activeList` is empty.
- **c = 'c':** `activeList = [("acd", 2), ("ace", 2)]`.
  - `"acd"` has next char `'d'`. Moves to `buckets['d'-'a']` with state pointer index set to `3`.
  - `"ace"` has next char `'e'`. Moves to `buckets['e'-'a']` with state pointer index set to `3`.
- **c = 'd':** `activeList = [("acd", 3)]`.
  - Index `3 == "acd".length()`. Target fully matched -> `count = 1`.
- **c = 'e':** `activeList = [("ace", 3)]`.
  - Index `3 == "ace".length()`. Target fully matched -> `count = 2`.

---

### Answer

```java
2
```

---

## Why Do We Use Parallel Bucket Tracking?

Instead of rewinding the main string `s` thousands of times for separate string search validations, streaming all matching candidates forward concurrently ensures that each element inside the massive sequence string `s` is visited exactly once. This optimization avoids wasting CPU cycles on redundant string re-scans.

This parallel execution layout yields:

```text
O(s.length() + ∑ words[i].length())
```

which satisfies the optimal constraint cleanly.

---

## Complexity Analysis

### Time Complexity

```text
O(s.length() + ∑ words[i].length())
```

Initializing and shifting states through the structural array is linear relative to the total number of characters across all words, combined with a single linear pass `O(s.length())` to exhaust the sequence base characters.

---

### Space Complexity

```text
O(words.length)
```

The bucket list references store exactly one active state node per unique input word element array block.

---

## Key Insight

When multiple target patterns need to be validated against a single large sequence, grouping search records dynamically by their next expected character matches the streaming timeline perfectly without causing search pointer regression.

```text
Time  : O(s.length() + ∑ words[i].length())
Space : O(words.length)
```

---

## Similar Problems

