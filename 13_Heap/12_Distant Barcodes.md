# Distant Barcodes

## Problem Statement

Given an array of integers `barcodes`, rearrange the barcodes so that no two adjacent barcodes are equal. You may return any answer that satisfies this condition.

The problem guarantees that an answer always exists for the given input.

The overall run time complexity should be:

```text
O(n)
```

*(where n is the total number of elements in the barcodes array)*

---

## Examples

### Example 1

**Input**

```java
barcodes = [1, 1, 1, 2, 2, 2]
```

**Output**

```java
[1, 2, 1, 2, 1, 2]
```

**Explanation**

In the rearranged array, no two identical elements sit next to each other. Another valid configuration is `[2, 1, 2, 1, 2, 1]`.

---

### Example 2

**Input**

```java
barcodes = [1, 1, 1, 1, 2, 2, 3, 3]
```

**Output**

```java
[1, 2, 1, 2, 1, 3, 1, 3]
```

**Explanation**

Other arrangements like `[1, 3, 1, 3, 1, 2, 1, 2]` or `[3, 1, 3, 1, 2, 1, 2, 1]` are also completely acceptable.

---

### Example 3

**Input**

```java
barcodes = [7]
```

**Output**

```java
[7]
```

---

## Brute Force Approach

Count all frequencies using a hash map, push the unique elements into a Max-Heap, and dynamically pop the two most frequent elements on each loop cycle to construct the array sequentially.

### Steps

1. Traverse the array and store element counts inside a `HashMap<Integer, Integer>`.
2. Push all mapping entries into a Max-Heap (Priority Queue) sorted strictly by their frequency count descending.
3. In each iteration, pop the top two entries out of the heap to avoid adjacent duplicate placement.
4. Place one item from each entry into our result array, decrement their counts, and push them back into the Max-Heap if their remaining frequencies are greater than 0.

### Complexity

```text
Time Complexity: O(n * log(k)) // where k is the number of unique elements
Space Complexity: O(k)
```

While using a priority queue ensures correct alternate item spacing, managing a heap structure creates a logarithmic sorting overhead. If an answer is guaranteed to exist, we can achieve true linear time using an optimized interleaved index placement strategy.

---

# Optimal Approach: Interleaved Frequency Placement

## Key Idea

We can place the elements in linear time using a **Counting Sort / Interleaving** approach.

If an array can be rearranged with no adjacent elements equal, it implies that the single most frequent element can appear at most \(\lfloor (n + 1) / 2 \rfloor\) times. If we take the most frequent element and place it at every **alternate slot** starting from index `0` (i.e., indices `0, 2, 4, 6...`), it can never sit next to itself.

Once the most frequent element is completely placed, we fill the remaining alternate slots with the rest of the elements in any order. If our alternate pointer hits or exceeds the end of the array during placement, we simply wrap it around to start filling the remaining empty **odd indices** (i.e., indices `1, 3, 5, 7...`).

---

## Visual Understanding

Suppose `barcodes = [1, 1, 1, 1, 2, 2, 3, 3]` where `n = 8`:

1. **Build Count Array:**
   - Elements range up to 10000 typically based on standard constraints.
   - Frequency map: `1 -> 4`, `2 -> 2`, `3 -> 2`. Most frequent is `1` (count 4).

2. **Interleave the Most Frequent Element (Index pointer starts at 0):**
   - Place `1` at index 0 -> `[1, _, _, _, _, _, _, _]`
   - Place `1` at index 2 -> `[1, _, 1, _, _, _, _, _]`
   - Place `1` at index 4 -> `[1, _, 1, _, 1, _, _, _]`
   - Place `1` at index 6 -> `[1, _, 1, _, 1, _, 1, _]`
   - Next index would be 8 (Out of bounds). Wrap pointer around to **index 1**.

3. **Interleave Remaining Elements Continuously:**
   - Place `2` at index 1 -> `[1, 2, 1, _, 1, _, 1, _]`
   - Place `2` at index 3 -> `[1, 2, 1, 2, 1, _, 1, _]`
   - Place `3` at index 5 -> `[1, 2, 1, 2, 1, 3, 1, _]`
   - Place `3` at index 7 -> `[1, 2, 1, 2, 1, 3, 1, 3]`

The final reconstructed array is valid.

---

## Partition Variables

Let:

```java
int[] freq = new int; // Standard constraint limit up to 10001
int maxFreq = 0;
int maxNum = 0;
int idx = 0;
```

---

### Border Elements

The boundary wrap-around rule transfers the active tracking index from even layers straight back to the initial odd index position:

```java
if (idx >= barcodes.length) {
    idx = 1; // Pivot back to fill the alternate unassigned slots
}
```

---

## Correct Partition Condition

The most frequent item must always be written into the array framework first to prevent any potential element concentration collisions:

```java
while (freq[maxNum] > 0) {
    result[idx] = maxNum;
    idx += 2;
    freq[maxNum]--;
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitions with an interleaved non-comparison frequency writing pipeline to secure linear execution times).*

---

## Java Solution

```java
class Solution {

    public int[] rearrangeBarcodes(int[] barcodes) {
        if (barcodes == null || barcodes.length <= 2) {
            return barcodes;
        }

        // Step 1: Track frequencies of all numbers (Max value is bounded by 10000)
        int[] freq = new int[10001];
        int maxFreq = 0;
        int maxNum = 0;

        for (int num : barcodes) {
            freq[num]++;
            if (freq[num] > maxFreq) {
                maxFreq = freq[num];
                maxNum = num;
            }
        }

        int[] result = new int[barcodes.length];
        int idx = 0;

        // Step 2: Fill alternate even slots with the absolute most frequent element first
        while (freq[maxNum] > 0) {
            result[idx] = maxNum;
            idx += 2;
            freq[maxNum]--;
        }

        // Step 3: Populate the remaining even and odd slots with all other numbers
        for (int i = 0; i < freq.length; i++) {
            while (freq[i] > 0) {
                // If the pointer slips out of bounds, reset it to start parsing odd indices
                if (idx >= barcodes.length) {
                    idx = 1;
                }
                result[idx] = i;
                idx += 2;
                freq[i]--;
            }
        }

        return result;
    }
}
```

---

## Dry Run

### Input

```java
barcodes = [1, 1, 1, 2, 2, 2]
```

---

### Step Execution Traversal

- **Frequency Mapping:** `freq[1] = 3`, `freq[2] = 3`. `maxNum` evaluates to `1`. `maxFreq = 3`.
- **Top Element Interleaving:**
  - Writes `1` at `idx = 0` \(\implies\) `result[0] = 1`. `idx = 2`.
  - Writes `1` at `idx = 2` \(\implies\) `result[2] = 1`. `idx = 4`.
  - Writes `1` at `idx = 4` \(\implies\) `result[4] = 1`. `idx = 6`.
  - `freq[1]` is now 0. Loop ends.
- **Remaining Elements Loop:**
  - Reaches `i = 2` (`freq[2] = 3`).
  - Next write position `idx = 6`. Out of bounds (`6 >= 6`). Pointer resets to `idx = 1`.
  - Writes `2` at `idx = 1` \(\implies\) `result[1] = 2`. `idx = 3`.
  - Writes `2` at `idx = 3` \(\implies\) `result[3] = 2`. `idx = 5`.
  - Writes `2` at `idx = 5` \(\implies\) `result[5] = 2`. `idx = 7`.

Final Array Array Layout = `[1, 2, 1, 2, 1, 2]`.

---

## Why Is the Time Complexity Linear?

The algorithm scans the input collection exactly once to build the frequency metrics. The placement phase writes each element exactly once into its pre-calculated destination index. By skipping complex comparison-based sorting or heap insertions entirely, the array is restructured in a single, predictable pass.

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

Where `n` is the total number of items in the barcodes array. Extracting frequencies takes linear time, and the placement loops run exactly `n` times to populate the result array.

---

### Space Complexity

```text
O(n)
```

The algorithm utilizes an auxiliary output array of length `n` to assemble the rearranged configuration, along with a constant-size frequency array of length 10001.

---

## Key Insight

Placing the most frequent items on alternate indices first ensures that identical values are safely separated, converting a complex constraint arrangement problem into a simple index-skipping write pipeline.

```text
Time  : O(n) execution runtime
Space : O(n) array memory allocation
```

---

## Similar Problems

1. Task Scheduler (621)
2. Reorganize String (767)
3. Rearrange String k Distance Apart (358)
4. Longest Happy String (1405)
5. Sort Characters By Frequency (451)
