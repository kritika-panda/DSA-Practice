# Replace Elements by Their Rank in an Array

## Problem Statement

Given an array of integers `arr`, replace each element with its **rank**.

The rank of an element is calculated based on the following rules:
* Rank is an integer starting from 1.
* The larger the element value, the larger its rank. If two elements are equal, they must share the exact same rank.
* The ranks must be as small as possible and consecutive (i.e., there should be no gaps between ranks even if elements share a rank).

Return *the array after replacing its elements with their corresponding ranks*.

The overall runtime complexity must be:

```text
O(n * log(n))
```

*(where n is the total number of elements inside the arr array)*

---

## Examples

### Example 1

**Input**

```java
arr = [40, 10, 20, 30]
```

**Output**

```java
[4, 1, 2, 3]
```

**Explanation**

- `10` is the smallest element \(\implies\) Rank 1.
- `20` is the second smallest element \(\implies\) Rank 2.
- `30` is the third smallest element \(\implies\) Rank 3.
- `40` is the largest element \(\implies\) Rank 4.

---

### Example 2

**Input**

```java
arr = [100, 100, 100]
```

**Output**

```java
[1, 1, 1]
```

**Explanation**

All elements are equal, so they share the exact same starting rank of 1.

---

### Example 3

**Input**

```java
arr = [37, 12, 28, 9, 100, 56, 80, 56, 12]
```

**Output**

```java
[5, 2, 4, 1, 8, 6, 7, 6, 2]
```

---

## Brute Force Approach

For every element in the array, run a full scan loop across the entire array to count how many distinct elements are strictly smaller than it, adding 1 to the count to determine its absolute rank.

### Steps

1. Create a brand-new result array `ranks` of size `n`.
2. Run an outer loop tracking index `i` from `0` to `n - 1`.
3. Use a nested inner loop or a unique tracking set to identify and count how many unique elements are smaller than `arr[i]`.
4. Store `smallerCount + 1` into `ranks[i]`.
5. Repeat for all elements and return the array.

### Complexity

```text
Time Complexity: O(n^2) // due to nested scanning operations across elements
Space Complexity: O(n) to track unique matching subsets
```

Scanning the entire array repeatedly for every single element creates a severe quadratic time bottleneck. We can optimize this process down to **log-linear time** by using a sorted clone and a mapping table.

---

# Optimal Approach: Array Cloning with Hash Mapping

## Key Idea

We can resolve the rank assignments efficiently by sorting a copy of the dataset. Sorting automatically clusters matching values together and arranges elements in an ascending timeline, revealing ranks natively.

To map these ranks back to the original unsorted array indexes, we can pair up elements using a **HashMap**:
1. **Clone the Original Layout:** Create a copy of the input array: `int[] cloned = arr.clone()`.
2. **Sort the Clone:** Sort the `cloned` array in ascending order using `Arrays.sort(cloned)`.
3. **Map Ranks Unique Entries:** Initialize a `HashMap<Integer, Integer> rankMap` and an integer rank counter `rank = 1`.
4. Iterate through the sorted `cloned` array:
   - Check if the current number is already a key inside our map using `rankMap.containsKey(num)`.
   - **The Tie-Breaker Filter:** If the key is *not* present, log it along with the current rank value: `rankMap.put(num, rank++)`. If it *is* already present, skip it so equal elements share the exact same rank seamlessly without incrementing our counter.
5. **In-Place Overwrite:** Iterate through the original `arr` array from left to right, looking up each element's rank from `rankMap` and overwriting it in-place.

---

## Visual Understanding

Suppose `arr = [37, 12, 28, 12]`:

- **Step 1 (Clone & Sort):** `cloned = [12, 12, 28, 37]`
- **Step 2 (Build Rank Map):** Initialize `rank = 1`.
  - `num = 12`: Missing from map. `rankMap.put(12, 1)`. Increment `rank = 2`.
  - `num = 12`: Already exists in map! Skip it. `rank` remains 2.
  - `num = 28`: Missing from map. `rankMap.put(28, 2)`. Increment `rank = 3`.
  - `num = 37`: Missing from map. `rankMap.put(37, 3)`. Increment `rank = 4`.
  - Final Map: `{12 -> 1, 28 -> 2, 37 -> 3}`.
- **Step 3 (Rewrite Array):**
  - `arr[0] = 37 \implies rankMap.get(37) = 3`.
  - `arr[1] = 12 \implies rankMap.get(12) = 1`.
  - `arr[2] = 28 \implies rankMap.get(28) = 2`.
  - `arr[3] = 12 \implies rankMap.get(12) = 1`.

The original array is updated cleanly to `[3, 1, 2, 1]`.

---

## Partition Variables

Let:

```java
int[] sortedCloned = arr.clone();
Arrays.sort(sortedCloned);
Map<Integer, Integer> rankMap = new HashMap<>();
int rank = 1;
```

---

### Border Elements

The algorithm handles arrays containing all duplicates or heavily polarized extremes safely, capping consecutive ranks without skipping values:

```java
if (!rankMap.containsKey(num)) {
    rankMap.put(num, rank++);
}
```

---

## Correct Partition Condition

The map dictionary values are pulled sequentially to reassign array nodes inside a single linear tracking pass:

```java
for (int i = 0; i < arr.length; i++) {
    arr[i] = rankMap.get(arr[i]);
}
return arr;
```

---

## Java Solution

```java
import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;

class Solution {

    public int[] arrayRankTransform(int[] arr) {
        if (arr == null || arr.length == 0) {
            return new int[0];
        }

        // Step 1: Clone the original array to maintain its unsorted baseline tracking indices
        int[] sortedCloned = arr.clone();
        
        // Step 2: Sort the cloned copy to group duplicate fields and reveal ranks natively
        Arrays.sort(sortedCloned);

        // HashMap to bind each unique integer element to its unique rank scale
        Map<Integer, Integer> rankMap = new HashMap<>();
        int rank = 1;

        // Step 3: Populate the map with consecutive ranks, skipping duplicate entries
        for (int num : sortedCloned) {
            if (!rankMap.containsKey(num)) {
                rankMap.put(num, rank);
                rank++; // Increment rank only when a completely new distinct value is found
            }
        }

        // Step 4: Overwrite the original array in-place using lookup values from the map
        for (int i = 0; i < arr.length; i++) {
            arr[i] = rankMap.get(arr[i]);
        }

        return arr;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Array Rank Transform** | **O(n * log(n))** | **O(n)** | Cloning and sorting the array requires log-linear time. The tracking dictionary and array clones allocate auxiliary memory blocks proportional to the size of the input elements. |

---

## Key Insight

Mapping sorted distinct element thresholds onto a custom hash table decouples chronological value placement from original indexing frames, applying integer ranks in optimal log-linear time.

```text
Time  : O(n * log(n)) log-linear sorting performance timeline
Space : O(n) dynamic lookup map and array copy allocations
```

---

## Similar Problems

1. Rank Teams by Votes (1366)
2. Sort an Array (912)
3. Kth Largest Element in an Array (215)
4. Merge Sorted Array (88)
5. Find Relative Ranks (506)
