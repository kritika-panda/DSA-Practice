# Top K Frequent Words

## Problem Statement

Given an array of strings `words` and an integer `k`, return the `k` most frequent strings.

Return the answer **sorted by the frequency** from highest to lowest. Sort the words with the **same frequency** by their **lexicographical order**.

The overall run time complexity should be:

```text
O(n * log(k))
```

---

## Examples

### Example 1

**Input**

```java
words = ["i", "love", "leetcode", "i", "love", "coding"]
k = 2
```

**Output**

```java
["i", "love"]
```

**Explanation**

"i" and "love" are the two most frequent words.  
Note that "i" comes before "love" due to a lower alphabetical order.

---

### Example 2

**Input**

```java
words = ["the", "day", "is", "sunny", "the", "the", "the", "sunny", "is", "is"]
k = 4
```

**Output**

```java
["the", "is", "sunny", "day"]
```

**Explanation**

"the", "is", "sunny" and "day" are the four most frequent words, with the number of occurrences being 4, 3, 2 and 1 respectively.

---

### Example 3

**Input**

```java
words = ["apple", "apple", "banana"]
k = 1
```

**Output**

```java
["apple"]
```

---

## Brute Force Approach

Count word frequencies using a hash map, transfer the unique entries to a list, and sort the list globally using a custom comparator.

### Steps

1. Traverse the words array and store frequencies in a Hash Map `(word -> count)`.
2. Copy the unique word keys into a list.
3. Sort the list using a custom comparator:
   - Primary: Higher frequency comes first.
   - Secondary: If frequencies match, smaller alphabetical word comes first (lexicographical sorting).
4. Extract the first `k` elements from the sorted list and return them.

### Complexity

```text
Time Complexity: O(n * log(n))
Space Complexity: O(n)
```

Sorting all unique elements globally takes \(O(n \log n)\) time in the worst case, which becomes inefficient if k is much smaller than n. We can optimize this by maintaining only k elements at a time.

---

# Optimal Approach: Min-Heap / Priority Queue

## Key Idea

Instead of sorting all elements globally, we can track the top `k` elements using a customized **Min-Heap** (Priority Queue).

We want the Min-Heap to evict the worst candidates when its size exceeds `k`. The "worst" candidate inside our top-k view means:
1. Words with a **lower frequency**.
2. Words with a **higher alphabetical order** (lexicographically larger) if frequencies tie.

Therefore, our Min-Heap custom comparator works as follows:
- If frequencies differ, the word with the **lower frequency** stays on top (e.g., frequency 2 is worse than frequency 5).
- If frequencies tie, the word that is **lexicographically larger** stays on top (e.g., `"love"` is worse than `"i"` because `"love"` comes later alphabetically).

When we process all elements through this Min-Heap, it retains exactly the `k` best candidates. We pop them off and reverse the collection to arrange them from highest frequency to lowest.

---

## Visual Understanding

Suppose:

```java
words = ["i", "love", "leetcode", "i", "love", "coding"]
k = 2
```

1. **Build Frequency Map:**
   - `"i" -> 2`
   - `"love" -> 2`
   - `"leetcode" -> 1`
   - `"coding" -> 1`

2. **Stream to Min-Heap (Size Limit = 2):**
   - Add `"i"` (2). Heap: `["i"]`
   - Add `"love"` (2). Frequencies tie. `"love"` is lexicographically greater than `"i"`, so `"love"` sits at the head. Heap: `["love", "i"]`
   - Add `"leetcode"` (1). Size becomes 3. Evict the top element. Frequency 1 is smaller than 2, so `"leetcode"` goes to the top and is immediately evicted. Heap: `["love", "i"]`
   - Add `"coding"` (1). Similarly, `"coding"` has a lower frequency and is evicted.

3. **Extract Result:**
   - Pop `"love"`, then pop `"i"`.
   - Reverse the collected order to get: `["i", "love"]`.

---

## Partition Variables

Let:

```java
Map<String, Integer> freqMap = new HashMap<>();
PriorityQueue<String> minHeap = new PriorityQueue<>(...)
```

---

### Border Elements

The custom sorting conditions for the Min-Heap definition are configured cleanly using comparison logic:

```java
PriorityQueue<String> minHeap = new PriorityQueue<>(
    (w1, w2) -> freqMap.get(w1).equals(freqMap.get(w2)) ? 
                w2.compareTo(w1) : freqMap.get(w1) - freqMap.get(w2)
);
```

---

## Correct Partition Condition

When pushing unique map entries into the priority queue, we strictly maintain a maximum element horizon of `k`:

```java
for (String word : freqMap.keySet()) {
    minHeap.offer(word);
    if (minHeap.size() > k) {
        minHeap.poll(); // Evicts the lowest frequency or largest lexicographical element
    }
}
```

---

## How to Move Binary Search

*(Note: This optimal solution leverages a Heap-based filtering constraint instead of standard Binary Range Splitting, as bounding memory dynamically to size k fulfills the logarithmic target constraint efficiently).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.PriorityQueue;

class Solution {

    public List<String> topKFrequent(String[] words, int k) {

        // Step 1: Count element frequencies
        Map<String, Integer> freqMap = new HashMap<>();
        for (String word : words) {
            freqMap.put(word, freqMap.getOrDefault(word, 0) + 1);
        }

        // Step 2: Initialize custom Min-Heap
        PriorityQueue<String> minHeap = new PriorityQueue<>(
            (w1, w2) -> freqMap.get(w1).equals(freqMap.get(w2)) ? 
                        w2.compareTo(w1) : freqMap.get(w1) - freqMap.get(w2)
        );

        // Step 3: Maintain top k frequent words in the heap
        for (String word : freqMap.keySet()) {
            minHeap.offer(word);
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }

        // Step 4: Extract and sort result correctly
        List<String> result = new ArrayList<>();
        while (!minHeap.isEmpty()) {
            result.add(minHeap.poll());
        }
        
        // Reverse because the min candidate pops out first
        Collections.reverse(result);
        return result;
    }
}
```

---

## Dry Run

### Input

```java
words = ["i", "love", "leetcode", "i", "love", "coding"]
k = 2
```

---

### Map Setup & Heap Operations

```java
freqMap = {"i"=2, "love"=2, "leetcode"=1, "coding"=1}
```

- **Insert "leetcode" (1):** Heap = `["leetcode"]`
- **Insert "coding" (1):** `"coding".compareTo("leetcode") < 0`. Heap head = `"leetcode"`. Heap = `["leetcode", "coding"]`
- **Insert "i" (2):** Heap size = 3. `"leetcode"` (freq 1) is smaller than `"i"` (freq 2), so it sits at the head and gets evicted via `poll()`. Heap = `["coding", "i"]`
- **Insert "love" (2):** Heap size = 3. `"coding"` (freq 1) has the lowest frequency, sits at the head, and gets evicted. Heap = `["love", "i"]` (where `"love"` sits at head because `freqMap` matches but `"love".compareTo("i") > 0`).

---

### Unrolling Phase

```java
result = []
```

- **Poll 1:** `"love"` popped. `result = ["love"]`.
- **Poll 2:** `"i"` popped. `result = ["love", "i"]`.
- **Reverse:** `Collections.reverse(result)` -> `["i", "love"]`.

---

### Answer

```java
["i", "love"]
```

---

## Why Do We Use a Min-Heap of Size K?

By bounding the capacity of the tracking structure strictly to `k`, insertion overhead remains restricted to \(O(\log k)\) instead of growing with the total count of distinct words. This optimization avoids expensive full array comparison sorts.

This bounded heap model yields:

```text
O(n * log(k))
```

which satisfies the constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n * log(k))
```

Building the frequency map takes O(n) time. Iterating through the unique keys and inserting them into the heap takes \(O(u \log k)\) time, where u is the number of unique words (u ≤ n). Reversing the final output of size k takes O(k) time.

---

### Space Complexity

```text
O(n)
```

The frequency Hash Map consumes O(n) space to store text counts, while the active priority queue requires O(k) space to hold elements.

---

## Key Insight

When an answer requires secondary lexicographical constraints alongside primary value ranks, configuring a heap comparator to evict alphabetical tail-enders on frequency ties isolates candidates cleanly in a single pass.

```text
Time  : O(n * log(k))
Space : O(n)
```

---

## Similar Problems

1. Top K Frequent Elements (347)
2. Sort Characters By Frequency (451)
3. K Closest Points to Origin (973)
4. Find K Pairs with Smallest Sums (373)
5. Kth Largest Element in a Stream (703)
