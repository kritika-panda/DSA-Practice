# H-Index

## Problem Statement

Given an array of integers `citations` where `citations[i]` is the number of citations a researcher received for their `i`-th paper, return the researcher's h-index.

According to the definition of h-index on Wikipedia: The h-index is defined as the maximum value of `h` such that the given researcher has published at least `h` papers that have each been cited at least `h` times.

The overall run time complexity should be:

```text
O(n)
```

---

## Examples

### Example 1

**Input**

```java
citations = [3, 0, 6, 1, 5]
```

**Output**

```java
3
```

**Explanation**

The researcher has 5 papers in total with citation counts `[3, 0, 6, 1, 5]`.  
There are 3 papers with at least 3 citations each (`3, 6, 5`).  
There are no more than 3 papers with at least 4 citations each (only `6, 5`).  
Therefore, the h-index is 3.

---

### Example 2

**Input**

```java
citations = [1, 3, 1]
```

**Output**

```java
1
```

**Explanation**

There are 3 papers with citation counts `[1, 3, 1]`.  
There is at least 1 paper with at least 1 citation.  
There are not 2 papers with at least 2 citations (only `3`).  
Therefore, the h-index is 1.

---

### Example 3

**Input**

```java
citations = [100]
```

**Output**

```java
1
```

---

## Brute Force Approach

Sort the citations array in descending order, then iterate through it to find the last position where the citation count is greater than or equal to the paper count rank.

### Steps

1. Sort the `citations` array in ascending or descending order.
2. If sorted in ascending order, traverse from right to left (highest to lowest citations).
3. Track the number of papers processed so far.
4. Stop as soon as the citation count for the current paper drops below the running count of papers.
5. Return the maximum count achieved.

### Complexity

```text
Time Complexity: O(n * log(n))
Space Complexity: O(1) or O(n) depending on the sorting implementation
```

Comparison-based sorting prevents this approach from achieving a strict linear time complexity. The problem requires a non-comparison counting framework.

---

# Optimal Approach: Counting Sort / Bucket Strategy

## Key Idea

We can achieve linear time complexity using a **Counting Sort** strategy.

The maximum possible h-index for a researcher cannot exceed the total number of published papers `n`. Even if a paper has 1,000 citations, it can contribute at most a value of `n` to the h-index threshold calculation.

Therefore, we can create an array of buckets of size `n + 1`:
- The index of the bucket represents the **citation count**.
- The value in the bucket tracks **how many papers** received that exact number of citations.
- Any paper with a citation count greater than `n` is clamped and counted straight into the final bucket index `n`.

Once the buckets are populated, we iterate backward from index `n` down to `0`, accumulating paper counts. The first index where the running total of papers meets or exceeds the current citation index value is our maximum h-index.

---

## Visual Understanding

Suppose:

```java
citations = [3, 0, 6, 1, 5]
```

Total papers `n = 5`. We create a bucket array of size `5 + 1 = 6`.

1. **Populate Buckets Array (Indices 0 to 5):**
   - `citations[0] = 3` -> Increment bucket[3]
   - `citations[1] = 0` -> Increment bucket[0]
   - `citations[2] = 6` -> Clamped to `n = 5`. Increment bucket[5]
   - `citations[3] = 1` -> Increment bucket[1]
   - `citations[4] = 5` -> Increment bucket[5]

State of Buckets array:
```text
Index (Citations): 0  1  2  3  4  5
Value (Papers):    1  1  0  1  0  2
```

2. **Traverse Buckets Backward:**
   - **pos = 5:** Add papers from bucket[5] (2) to `totalPapers`. `totalPapers = 2`. Is `2 >= 5`? No.
   - **pos = 4:** Add papers from bucket[4] (0) to `totalPapers`. `totalPapers = 2`. Is `2 >= 4`? No.
   - **pos = 3:** Add papers from bucket[3] (1) to `totalPapers`. `totalPapers = 3`. Is `3 >= 3`? **Yes!**

The loop returns the first matching index: `3`.

---

## Partition Variables

Let:

```java
int n = citations.length;
int[] buckets = new int[n + 1];
```

---

### Border Elements

We distribute the paper citation frequencies into the buckets using the clamping rule:

```java
for (int c : citations) {
    if (c >= n) {
        buckets[n]++;
    } else {
        buckets[c]++;
    }
}
```

---

## Correct Partition Condition

When accumulating papers from right to left through the buckets:

```java
int totalPapers = 0;
for (int h = n; h >= 0; h--) {
    totalPapers += buckets[h];
    if (totalPapers >= h) {
        return h;
    }
}
```

The first `h` value that satisfies this condition represents the maximum possible h-index.

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with a direct non-comparison counting sort map to ensure strict linear time guarantees).*

---

## Java Solution

```java
class Solution {

    public int hIndex(int[] citations) {

        if (citations == null || citations.length == 0) {
            return 0;
        }

        int n = citations.length;
        int[] buckets = new int[n + 1];

        // Step 1: Populate the citation count buckets
        for (int c : citations) {
            if (c >= n) {
                buckets[n]++; // Clamp higher citations to maximum possible h-index value
            } else {
                buckets[c]++;
            }
        }

        // Step 2: Accumulate papers from highest citation bucket down to 0
        int totalPapers = 0;
        for (int h = n; h >= 0; h--) {
            totalPapers += buckets[h];
            
            // If the total number of papers found so far is >= current h threshold
            if (totalPapers >= h) {
                return h;
            }
        }

        return 0;
    }
}
```

---

## Dry Run

### Input

```java
citations = [3, 0, 6, 1, 5]
```

---

### Initial Buckets Configuration

```java
n = 5
buckets = [0, 0, 0, 0, 0, 0] // Size 6
```

- **c = 3:** `buckets[3] = 1`
- **c = 0:** `buckets[0] = 1`
- **c = 6:** `6 >= 5` -> `buckets[5] = 1`
- **c = 1:** `buckets[1] = 1`
- **c = 5:** `5 >= 5` -> `buckets[5] = 2`

Final `buckets` array = `[1, 1, 0, 1, 0, 2]`

---

### Accumulation Phase

```java
totalPapers = 0
```

- **h = 5:** `totalPapers += 2` -> `2`. Check `2 >= 5` (False).
- **h = 4:** `totalPapers += 0` -> `2`. Check `2 >= 4` (False).
- **h = 3:** `totalPapers += 1` -> `3`. Check `3 >= 3` (**True**). Return `3`.

---

### Answer

```java
3
```

---

## Why Do We Use a Counting Bucket Structure?

Since the maximum possible h-index is naturally capped by the total number of papers `n`, sorting citations beyond `n` is redundant. By mapping counts directly to index frequencies in a finite bucket array, we bypass the `O(n log n)` comparison sorting overhead entirely.

This frequency mapping yields:

```text
O(n)
```

which satisfies the optimal runtime constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Populating the buckets array requires a single linear pass `O(n)` through the input data array. Sweeping backward through the finite size buckets array takes at most `n + 1` operations, ensuring a linear time complexity.

---

### Space Complexity

```text
O(n)
```

We allocate a single extra counting bucket array of size `n + 1` to track the distribution of citations.

---

## Key Insight

When the maximum viable answer threshold is strictly bounded by the total number of elements in the input collection, grouping frequencies into a fixed-size array avoids standard comparison sorting costs entirely.

```text
Time  : O(n)
Space : O(n)
```

---

## Similar Problems

1. H-Index II (275) - *Sorted array variant solved using Binary Search*
2. Top K Frequent Elements (347)
3. Maximum Gap (164)
4. Sort Colors (75)
5. First Missing Positive (41)
