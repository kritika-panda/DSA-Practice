# Top K Frequent Elements

## Problem Statement

Given an integer array `nums` and an integer `k`, return the `k` most frequent elements. You may return the answer in any order.

The overall run time complexity should be:

```text
O(n)
```

---

## Examples

### Example 1

**Input**

```java
nums = [1, 1, 1, 2, 2, 3]
k = 2
```

**Output**

```java
[1, 2]
```

**Explanation**

The frequency of `1` is 3, the frequency of `2` is 2, and the frequency of `3` is 1.  
The 2 most frequent elements are `1` and `2`.

---

### Example 2

**Input**

```java
nums = [1]
k = 1
```

**Output**

```java
[1]
```

---

### Example 3

**Input**

```java
nums = [4, 4, 4, 6, 6, 8, 8, 8, 8]
k = 2
```

**Output**

```java
[8, 4]
```

---

## Brute Force Approach

Count frequencies using a hash map, transfer the unique entries to a list, and sort the list in descending order based on their counts.

### Steps

1. Traverse the array and store frequencies in a Hash Map `(element -> count)`.
2. Copy the unique elements into a list.
3. Sort the list using a custom comparator that checks the stored frequencies in descending order.
4. Extract the first `k` elements from the sorted list and return them.

### Complexity

```text
Time Complexity: O(n * log(n))
Space Complexity: O(n)
```

Sorting all unique elements prevents this approach from achieving linear time execution. Alternatively, using a Min-Heap/Priority Queue scales to `O(n log k)`, which still falls short of strict linear time.

---

# Optimal Approach: Bucket Sort Strategy

## Key Idea

Instead of sorting the elements by their frequencies, we can use the **Bucket Sort** technique. 

Since the maximum frequency an element can achieve is bounded by the array length `n`, we can create an array of lists (buckets) where the index represents the frequency itself:

```text
Index of Bucket = Frequency of Elements
```

1. Count the frequency of each element using a frequency map.
2. Place each unique element into the bucket corresponding to its frequency count.
3. Iterate backward through the buckets (from frequency `n` down to `0`) and gather the elements until we have collected `k` elements.

Because no comparison-based sorting is required, this approach runs in true linear time.

---

## Visual Understanding

Suppose:

```java
nums = [1, 1, 1, 2, 2, 3]
k = 2
```

1. **Build Frequency Map:**
   - `1 -> 3`
   - `2 -> 2`
   - `3 -> 1`

2. **Populate Buckets Array (Indices 0 to 6):**
   - Bucket 0: `[]`
   - Bucket 1: `[3]` (frequency is 1)
   - Bucket 2: `[2]` (frequency is 2)
   - Bucket 3: `[1]` (frequency is 3)
   - Bucket 4, 5, 6: `[]`

3. **Traverse Buckets Backward:**
   - Scan Bucket 3 -> Add `1` to results. (Collected: 1)
   - Scan Bucket 2 -> Add `2` to results. (Collected: 2)
   - We have collected `k = 2` elements. Stop.

Final Result = `[1, 2]`.

---

## Partition Variables

Let:

```java
Map<Integer, Integer> frequencyMap = new HashMap<>()
List<Integer>[] bucket = new List[nums.length + 1]
```

---

### Border Elements

The bucket array is initialized to contain empty array lists:

```java
for (int i = 0; i <= nums.length; i++) {
    bucket[i] = new ArrayList<>();
}
```

Elements are bucketed via:

```java
int frequency = frequencyMap.get(key);
bucket[frequency].add(key);
```

---

## Correct Partition Condition

When traversing the bucket arrays from right to left to assemble the final result:

```java
for (int pos = bucket.length - 1; pos >= 0 && resultPtr < k; pos--) {
    if (!bucket[pos].isEmpty()) {
        for (int num : bucket[pos]) {
            res[resultPtr++] = num;
            if (resultPtr == k) break;
        }
    }
}
```

This collects elements greedily based on the highest frequency tiers.

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with an explicit frequency bucket distribution map to achieve linear runtime constraints).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

class Solution {

    public int[] topKFrequent(int[] nums, int k) {

        // Step 1: Count element frequencies
        Map<Integer, Integer> frequencyMap = new HashMap<>();
        for (int num : nums) {
            frequencyMap.put(num, frequencyMap.getOrDefault(num, 0) + 1);
        }

        // Step 2: Initialize frequency buckets
        List<Integer>[] bucket = new List[nums.length + 1];
        for (int i = 0; i <= nums.length; i++) {
            bucket[i] = new ArrayList<>();
        }

        // Step 3: Distribute unique elements into matching frequency indices
        for (int key : frequencyMap.keySet()) {
            int frequency = frequencyMap.get(key);
            bucket[frequency].add(key);
        }

        // Step 4: Gather top k elements from highest frequency downward
        int[] result = new int[k];
        int counter = 0;

        for (int pos = bucket.length - 1; pos >= 0 && counter < k; pos--) {
            if (!bucket[pos].isEmpty()) {
                for (int num : bucket[pos]) {
                    result[counter++] = num;
                    if (counter == k) {
                        break;
                    }
                }
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
nums = [1, 1, 1, 2, 2, 3]
k = 2
```

---

### Initial Maps and Buckets

```java
frequencyMap = {1=3, 2=2, 3=1}
bucket array size = 7
bucket[1] = [3]
bucket[2] = [2]
bucket[3] = [1]
```

---

### Gathering Phase

```java
result = [0, 0]
counter = 0
```

- **pos = 6:** Empty bucket.
- **pos = 5:** Empty bucket.
- **pos = 4:** Empty bucket.
- **pos = 3:** `bucket[3] = [1]`. `result[0] = 1`. `counter = 1`.
- **pos = 2:** `bucket[2] = [2]`. `result[1] = 2`. `counter = 2`.

Loop terminates because `counter == k` condition is met.

---

### Answer

```java
[1, 2]
```

---

## Why Do We Use the Bucket Sorting Principle?

The maximum frequency any value can achieve is naturally capped by the size of the array `n`. Mapping unique items directly to indices equal to their frequencies acts like a perfect non-comparison sort, avoiding the `O(n log n)` overhead of standard sorting algorithms.

This bucket distribution yields:

```text
O(n)
```

which satisfies the optimal time constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Building the frequency map takes `O(n)` time. Distributing elements into buckets takes `O(n)` time (since there are at most `n` unique elements). Traversing the buckets backward to extract `k` elements performs at most `n` checks.

---

### Space Complexity

```text
O(n)
```

The frequency Hash Map stores at most `n` elements, and the bucket list array structures use `O(n)` memory slots to track grouped values.

---

## Key Insight

When the maximum classification score (in this case, frequency) is bounded strictly by the length of the input data array, mapping scores directly onto matching indices avoids standard comparison sorting costs entirely.

```text
Time  : O(n)
Space : O(n)
```

---

## Similar Problems

1. Sort Characters By Frequency (451)
2. K Closest Points to Origin (973)
3. Top K Frequent Words (692)
4. Sort Array by Increasing Frequency (1636)
5. Group Anagrams (49)
