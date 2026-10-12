# Reorganize String

## Problem Statement

Given a string `s`, rearrange the characters of `s` so that any two adjacent characters are not the same.

Return any possible rearrangement of `s` or return an empty string `""` if it is not possible.

The overall run time complexity should be:

```text
O(n)
```

*(where n is the length of the string s, optimizable to O(n) post frequency extraction using counting sort properties)*

---

## Examples

### Example 1

**Input**

```java
s = "aab"
```

**Output**

```java
"aba"
```

---

### Example 2

**Input**

```java
s = "aaab"
```

**Output**

```java
""
```

**Explanation**

Since there are three 'a's and only one 'b', it is impossible to arrange them such that no two 'a's sit next to each other.

---

### Example 3

**Input**

```java
s = "vvvlo"
```

**Output**

```java
"vlvov"
```

**Explanation**

"vovlv" is also a completely acceptable valid configuration.

---

## Brute Force Approach

Count all character frequencies using a hash map, push the unique characters into a Max-Heap, and dynamically pop the two most frequent characters on each loop cycle to construct the string sequentially.

### Steps

1. Traverse the string and store character counts inside an integer frequency array of size 26.
2. Push all character entries into a Max-Heap (Priority Queue) sorted strictly by their frequency count descending.
3. In each iteration, pop the top two characters out of the heap to avoid adjacent duplicate placement.
4. Append one instance of each character to a `StringBuilder`, decrement their remaining counts, and push them back into the Max-Heap if their counts are still greater than 0.
5. If one element is left in the heap with a count greater than 1, return `""` as it cannot be safely scheduled.

### Complexity

```text
Time Complexity: O(n * log(26)) -> O(n)
Space Complexity: O(26) -> O(1)
```

While a Priority Queue ensures correct alternative spacing, managing a tree structure creates unnecessary log-time balance overhead per step. We can optimize this to a pure linear single-pass arrangement using an interleaved index placement strategy.

---

# Optimal Approach: Interleaved Frequency Placement

## Key Idea

We can place the characters in strict linear time using an **Interleaved Index Counting Sort** approach.

If an array can be rearranged with no adjacent elements equal, it implies that the single most frequent element can appear at most \(\lfloor (n + 1) / 2 \rfloor\) times. If the count of the highest frequent character exceeds this threshold, we can immediately return `""`.

If it passes this condition, we take that most frequent character and place it at every **alternate slot** starting from index `0` (i.e., indices `0, 2, 4, 6...`). Because it is spaced out, it can never sit next to itself.

Once the most frequent character is completely placed, we fill the remaining alternate slots with the rest of the characters in any order. If our alternate pointer hits or exceeds the end of the string during placement, we simply wrap it around to start filling the remaining empty **odd indices** (i.e., indices `1, 3, 5, 7...`).

---

## Visual Understanding

Suppose `s = "aab"` where `n = 3`:

1. **Build Count Array:**
   - Frequency map: `a -> 2`, `b -> 1`. Most frequent is `'a'` (count 2).
   - Check threshold: `2 <= (3 + 1) / 2` \(\implies\) `2 <= 2` (Valid!).

2. **Interleave the Most Frequent Character (Index pointer starts at 0):**
   - Place `'a'` at index 0 -> `['a', _, _]`
   - Place `'a'` at index 2 -> `['a', _, 'a']`
   - Next index would be 4 (Out of bounds). Wrap pointer around to **index 1**.

3. **Interleave Remaining Characters Continuously:**
   - Place `'b'` at index 1 -> `['a', 'b', 'a']`

The final reconstructed layout forms `"aba"`.

---

## Partition Variables

Let:

```java
int[] counts = new int[26];
int maxCount = 0;
int maxCharIdx = 0;
int idx = 0;
```

---

### Border Elements

The boundary wrap-around rule transfers the active tracking index from even layers straight back to the initial odd index position:

```java
if (idx >= s.length()) {
    idx = 1; // Pivot back to fill the alternate unassigned slots
}
```

---

## Correct Partition Condition

The strict impossibility filter triggers early if the most frequent element exceeds half the string length ceiling:

```java
if (maxCount > (s.length() + 1) / 2) {
    return ""; // Inherent collision constraint met
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitions with an interleaved non-comparison frequency writing pipeline to secure direct linear executions).*

---

## Java Solution

```java
class Solution {

    public String reorganizeString(String s) {
        if (s == null || s.length() == 0) {
            return "";
        }

        // Step 1: Track frequencies of all lowercase characters
        int[] counts = new int[26];
        int maxCount = 0;
        int maxCharIdx = 0;

        for (int i = 0; i < s.length(); i++) {
            int charIdx = s.charAt(i) - 'a';
            counts[charIdx]++;
            if (counts[charIdx] > maxCount) {
                maxCount = counts[charIdx];
                maxCharIdx = charIdx;
            }
        }

        // Step 2: Validate if a viable layout arrangement is mathematically possible
        if (maxCount > (s.length() + 1) / 2) {
            return "";
        }

        char[] result = new char[s.length()];
        int idx = 0;

        // Step 3: Fill alternate even slots with the absolute most frequent character first
        while (counts[maxCharIdx] > 0) {
            result[idx] = (char) (maxCharIdx + 'a');
            idx += 2;
            counts[maxCharIdx]--;
        }

        // Step 4: Populate the remaining even and odd slots with all other characters
        for (int i = 0; i < 26; i++) {
            while (counts[i] > 0) {
                // If the pointer slips out of bounds, reset it to start parsing odd indices
                if (idx >= s.length()) {
                    idx = 1;
                }
                result[idx] = (char) (i + 'a');
                idx += 2;
                counts[i]--;
            }
        }

        return String.valueOf(result);
    }
}
```

---

## Dry Run

### Input

```java
s = "vvvlo"
```

---

### Step Execution Traversal

- **Frequency Mapping:** `counts['v'-'a'] = 3`, `counts['l'-'a'] = 1`, `counts['o'-'a'] = 1`. `maxCount = 3`, `maxCharIdx = 21` ('v').
- **Threshold Check:** `3 > (5 + 1) / 2` \(\implies\) `3 > 3` is False. Processing continues.
- **Top Element Interleaving:**
  - Writes `'v'` at `idx = 0` \(\implies\) `result[0] = 'v'`. `idx = 2`.
  - Writes `'v'` at `idx = 2` \(\implies\) `result[2] = 'v'`. `idx = 4`.
  - Writes `'v'` at `idx = 4` \(\implies\) `result[4] = 'v'`. `idx = 6`.
  - `counts['v'-'a']` is now 0. Loop ends.
- **Remaining Elements Loop:**
  - Reaches `i = 11` ('l'). `idx = 6 >= 5`, so `idx` resets to `1`.
  - Writes `'l'` at `idx = 1` \(\implies\) `result[1] = 'l'`. `idx = 3`.
  - Reaches `i = 14` ('o').
  - Writes `'o'` at `idx = 3` \(\implies\) `result[3] = 'o'`. `idx = 5`.

Final character array conversion converts directly to `"vlvov"`.

---

### Answer

```java
"vlvov"
```

---

## Why Is the Time Complexity Linear?

The algorithm scans the input collection exactly once to build frequency tallies. The placement phase writes each element exactly once into its precalculated destination index. By skipping complex comparison-based sorting or heap balancing steps entirely, the string layout is restructured in a single, predictable pass.

This direct interleaving framework yields:

```text
O(n)
```

which satisfies the optimal time complexity requirement.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Where `n` is the total number of characters in string `s`. Extracting frequencies takes linear time, and the placement loops run exactly `n` times to populate the result array layout.

---

### Space Complexity

```text
O(n)
```

The algorithm utilizes an auxiliary output character array of length `n` to assemble the rearranged configuration, along with a constant-size frequency array of length 26.

---

## Key Insight

Placing the absolute most frequent items on alternate indices first ensures that identical values are safely separated, converting a complex constraint arrangement problem into a simple index-skipping write pipeline.

```text
Time  : O(n) execution runtime
Space : O(n) array memory allocation
```

---

## Similar Problems

1. Task Scheduler (621)
2. Rearrange String k Distance Apart (358)
3. Distant Barcodes (1054)
4. Longest Happy String (1405)
5. Sort Characters By Frequency (451)
