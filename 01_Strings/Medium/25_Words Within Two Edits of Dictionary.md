# Words Within Two Edits of Dictionary

## Problem Statement

You are given two string arrays, `queries` and `dictionary`. All words in both arrays have the **same length**.

In one edit operation, you can change any character of a query word to any other character. Find all words from `queries` that can be transformed into any word from `dictionary` using **at most two edit operations**.

Return *a list of all matching words from `queries` in their **original relative order**.*

The overall runtime complexity must be:

```text
O(n * m * L)
```

*(where n is the length of queries, m is the length of dictionary, and L is the length of each word)*

---

## Examples

### Example 1

**Input**

```java
queries = ["word","note","ants","wood"]
dictionary = ["wood","joke","moat"]
```

**Output**

```java
["word","note","wood"]
```

**Explanation**

- `"word"` can be changed to `"wood"` by changing `'r' -> 'o'` (**1 edit**). Keep it.
- `"note"` can be changed to `"joke"` by changing `'n' -> 'j'` and `'t' -> 'k'` (**2 edits**). Keep it.
- `"ants"` requires 3 edits to match any dictionary word. Discard it.
- `"wood"` matches `"wood"` exactly (**0 edits**). Keep it.

---

### Example 2

**Input**

```java
queries = ["yes"]
dictionary = ["not"]
```

**Output**

```java
[]
```

---

## Brute Force Approach

Use nested loops to check every query against every dictionary word, using a string mismatch count mechanism to track difference profiles.

### Steps

1. Initialize an empty results list.
2. For each word in `queries`, run an inner loop through each word in `dictionary`.
3. Compute the edit distance by counting positions where `query.charAt(k) != dictWord.charAt(k)`.
4. If the count of mismatches strictly exceeds 2, continue checking.
5. If a dictionary word is found with a mismatch count ≤ 2, add the query word to the results and break the inner loop immediately.

### Complexity

```text
Time Complexity: O(n * m * L)
Space Complexity: O(1) auxiliary space
```

Since the bounds for the problem parameters are typically small (n, m, L ≤ 100), this approach runs extremely fast. We can optimize the matching loop by incorporating an **early exit pruning optimization** inside the character matching step.

---

# Optimal Approach: Optimized Character Scan with Early Exit Pruning

## Key Idea

Since all words share the **exact same length**, the "edit distance" between two words simplifies to their **Hamming Distance**—the number of positions at which the corresponding characters are different.

We can solve this problem optimally in-place by verifying each query across the dictionary fields while pruning dead matching paths instantly:
1. Initialize a result list `result`.
2. Loop through each string `q` inside `queries`.
3. For each query word, scan through the words `d` inside `dictionary`:
   - Maintain a running counter `mismatches = 0`.
   - Loop through characters from index `0` up to `L - 1`. If `q.charAt(i) != d.charAt(i)`, increment `mismatches`.
   - **Early Exit Pruning:** The moment `mismatches` crosses strictly **above 2**, this dictionary word path is invalid. Break the character loop immediately to save CPU clock cycles.
   - If the character loop finishes and `mismatches <= 2`, it means `q` is within 2 edits of a valid dictionary entry. Add `q` straight into `result`, and break the dictionary loop to move onto the next query element.

---

## Visual Understanding

Suppose `q = "word"` and `d = "joke"` with length 4:

- Track `mismatches = 0`. Scan characters left to right:
  - **i = 0:** `'w' != 'j'` \(\implies\) `mismatches = 1`.
  - **i = 1:** `'o' == 'o'` \(\implies\) holds at 1.
  - **i = 2:** `'r' != 'k'` \(\implies\) `mismatches = 2`.
  - **i = 3:** `'d' != 'e'` \(\implies\) `mismatches = 3`.
- At **i = 3**, `mismatches` reaches 3, which is >2. The inner check can be terminated early if this threshold is crossed sooner on longer strings.

If we check `q = "word"` against `d = "wood"`:
- `mismatches` ends at exactly `1`, which is ≤ 2. The query `"word"` is saved instantly.

---

## Partition Variables

Let:

```java
List<String> validQueries = new ArrayList<>();
int L = queries[0].length();
```

---

### Border Elements

The early exit condition blocks redundant character scans the moment a word pair accumulates more than 2 differences:

```java
if (q.charAt(i) != d.charAt(i)) {
    mismatches++;
    if (mismatches > 2) break; // Prune dead match paths instantly
}
```

---

## Correct Partition Condition

The outer loop handles results preservation in their original sequence tracks automatically:

```java
for (String q : queries) {
    if (canTransform(q, dictionary)) {
        validQueries.add(q);
    }
}
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    public List<String> twoEditWords(String[] queries, String[] dictionary) {
        List<String> result = new ArrayList<>();
        if (queries == null || dictionary == null) {
            return result;
        }

        int wordLen = queries[0].length();

        // Step 1: Process each query word sequentially
        for (String q : queries) {
            
            // Step 2: Compare against the dictionary collection
            for (String d : dictionary) {
                int mismatches = 0;

                // Step 3: Compute character difference footprints
                for (int i = 0; i < wordLen; i++) {
                    if (q.charAt(i) != d.charAt(i)) {
                        mismatches++;
                        
                        // Early Exit Pruning: Stop comparing if edits exceed 2
                        if (mismatches > 2) {
                            break;
                        }
                    }
                }

                // If a valid match within 2 edits is confirmed, save the query word
                if (mismatches <= 2) {
                    result.add(q);
                    break; // Move to the next query word immediately
                }
            }
        }

        return result;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Pruned Linear Scan** | **O(n * m * L)** | **O(1)** | Evaluates all query blocks against dictionary records up to string length boundaries. Runs completely in-place with zero auxiliary heap track allocations. |

---

## Key Insight

Since all words share identical lengths, the edit criteria maps to a standard character mismatch count, enabling path pruning filters to eliminate redundant trailing array passes.

```text
Time  : O(n * m * L) worst-case bounded complexity
Space : O(1) absolute constant auxiliary memory footprint
```

---

## Similar Problems

1. Edit Distance (72)
2. Valid Anagram (242)
3. First Unique Character in a String (387)
4. Implement Magic Dictionary (676)
5. Find All Anagrams in a String (438)
