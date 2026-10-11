# Maximum Gap

## Problem Statement

Given an integer array `nums`, return the maximum difference between two successive elements in its sorted form. If the array contains less than two elements, return `0`.

You must write an algorithm that runs in linear time and uses linear extra space.

The overall run time complexity should be:

```text
O(n)
```

---

## Examples

### Example 1

**Input**

```java
nums =
```

**Output**

```java
3
```

**Explanation**

The sorted form of the array is ``.
The maximum gap exists between successive elements `3` and `6` (gap = 3), or `6` and `9` (gap = 3).

---

### Example 2

**Input**

```java
nums =
```

**Output**

```java
0
```

**Explanation**

The array contains less than two elements, so we return `0`.

---

### Example 3

**Input**

```java
nums =
```

**Output**

```java
99
```

**Explanation**

The sorted form of the array is ``.
The maximum gap is between `1` and `100`, which is `99`.

---

## Brute Force Approach

Sort the array in ascending order using standard comparison-based sorting, then iterate through the array to find the largest difference between adjacent elements.

### Steps

1. Check if the array length is less than 2. If so, return `0`.
2. Sort the array using a standard algorithm like Quick Sort or Merge Sort.
3. Traverse the sorted array from index `1` to `n - 1`.
4. Calculate the difference between `nums[i]` and `nums[i - 1]`.
5. Keep track of the maximum difference found and return it.

### Complexity

```text
Time Complexity: O(n * log(n))
Space Complexity: O(1) or O(n) depending on the sorting implementation
```

Comparison-based sorting fails to meet the strict linear time requirement. The problem requires a non-comparison approach.

---

# Optimal Approach: Pigeonhole / Bucket Sorting Principle

## Key Idea

We can achieve linear time using the **Bucket Sort** or **Pigeonhole Principle**. 

Suppose we have `n` elements ranging from a minimum value `minVal` to a maximum value `maxVal`. The total range of values is `maxVal - minVal`. The maximum gap between any two successive elements in the sorted array must be at least:

```text
gapSize = ceil((maxVal - minVal) / (n - 1))
```

If we choose this value as our bucket width, then the maximum gap **cannot** occur between two elements inside the *same* bucket. It can only occur between the maximum element of one bucket and the minimum element of the next non-empty bucket.

Therefore, for each bucket, we only need to store two values:
1. The **minimum** value in that bucket.
2. The **maximum** value in that bucket.

---

## Visual Understanding

Suppose:

```java
nums =
```

- `n = 4`
- `minVal = 3`, `maxVal = 9`
- `gapSize = ceil((9 - 3) / (4 - 1)) = ceil(6 / 3) = 2`

We create buckets of width `2`:
- **Bucket 0:** Handles values `[3, 4]` -> Contains `[3]` -> `min = 3, max = 3`
- **Bucket 1:** Handles values `[5, 6]` -> Contains `[6]` -> `min = 6, max = 6`
- **Bucket 2:** Handles values `[7, 8]` -> Contains `[]` -> Empty
- **Bucket 3:** Handles values `[9, 10]` -> Contains `[9]` -> `min = 9, max = 9`

Now, scan through the non-empty buckets to find gaps between `currentBucket.min` and `previousBucket.max`:
- Gap 1: `Bucket 1.min (6) - Bucket 0.max (3) = 3`
- Gap 2: `Bucket 3.min (9) - Bucket 1.max (6) = 3` (Bypassing empty Bucket 2)

The maximum gap found is `3`.

---

## Partition Variables

Let:

```java
int minVal = minimum element in nums
int maxVal = maximum element in nums
int bucketSize = Math.max(1, (maxVal - minVal) / (n - 1))
int bucketCount = (maxVal - minVal) / bucketSize + 1
```

---

### Border Elements

We initialize two arrays to represent our buckets:

```java
int[] bucketMin = new int[bucketCount]; // Filled with Integer.MAX_VALUE
int[] bucketMax = new int[bucketCount]; // Filled with Integer.MIN_VALUE
```

An element `num` maps to a bucket index via:

```java
int bucketIdx = (num - minVal) / bucketSize;
```

---

## Correct Partition Condition

When comparing successive non-empty buckets:

```java
if (bucketMin[i] != Integer.MAX_VALUE) {
    maxGap = Math.max(maxGap, bucketMin[i] - previousMax);
    previousMax = bucketMax[i];
}
```

This tracks the max spacing bounds across disjoint buckets.

---

## How to Move Binary Search

*(Note: This optimal solution leverages the Pigeonhole/Bucket Sorting Principle rather than standard Binary Range Splitting, as calculating exact value boundaries directly resolves the linear layout requirement).*

---

## Java Solution

```java
import java.util.Arrays;

class Solution {

    public int maximumGap(int[] nums) {

        if (nums == null || nums.length < 2) {
            return 0;
        }

        int n = nums.length;
        int minVal = nums[0];
        int maxVal = nums[0];

        // Find global min and max elements
        for (int num : nums) {
            minVal = Math.min(minVal, num);
            maxVal = Math.max(maxVal, num);
        }

        // Handle edge case where all elements are identical
        if (minVal == maxVal) {
            return 0;
        }

        // Calculate bucket size and count
        int bucketSize = (int) Math.ceil((double) (maxVal - minVal) / (n - 1));
        int bucketCount = (maxVal - minVal) / bucketSize + 1;

        int[] bucketMin = new int[bucketCount];
        int[] bucketMax = new int[bucketCount];
        
        Arrays.fill(bucketMin, Integer.MAX_VALUE);
        Arrays.fill(bucketMax, Integer.MIN_VALUE);

        // Distribute elements into buckets
        for (int num : nums) {
            int bucketIdx = (num - minVal) / bucketSize;
            bucketMin[bucketIdx] = Math.min(bucketMin[bucketIdx], num);
            bucketMax[bucketIdx] = Math.max(bucketMax[bucketIdx], num);
        }

        // Traverse buckets to find the maximum gap
        int maxGap = 0;
        int previousMax = minVal;

        for (int i = 0; i < bucketCount; i++) {
            // Skip empty buckets
            if (bucketMin[i] == Integer.MAX_VALUE) {
                continue;
            }
            
            // Gap is between current bucket min and previous bucket max
            maxGap = Math.max(maxGap, bucketMin[i] - previousMax);
            previousMax = bucketMax[i];
        }

        return maxGap;
    }
}
```

---

## Dry Run

### Input

```java
nums =
```

---

### Initial Values

```java
n = 4
minVal = 3
maxVal = 9
bucketSize = ceil(6 / 3) = 2
bucketCount = (9 - 3) / 2 + 1 = 4
```

---

### Bucket Distribution Phase

- **num = 9:** `idx = (9-3)/2 = 3` -> `bucketMin[3]=9, bucketMax[3]=9`
- **num = 3:** `idx = (3-3)/2 = 0` -> `bucketMin[0]=3, bucketMax[0]=3`
- **num = 6:** `idx = (6-3)/2 = 1` -> `bucketMin[1]=6, bucketMax[1]=6`
- **num = 3:** `idx = (3-3)/2 = 0` -> `bucketMin[0]=3, bucketMax[0]=3`

---

### Gap Traversal Phase

```java
maxGap = 0
previousMax = 3
```

- **i = 0:** Non-empty. `maxGap = max(0, 3 - 3) = 0`. `previousMax = 3`.
- **i = 1:** Non-empty. `maxGap = max(0, 6 - 3) = 3`. `previousMax = 6`.
- **i = 2:** Empty bucket. Skipped.
- **i = 3:** Non-empty. `maxGap = max(3, 9 - 6) = 3`. `previousMax = 9`.

---

### Answer

```java
3
```

---

## Why Do We Use the Bucket Sorting Principle?

The math behind the Pigeonhole Principle ensures that if we distribute n numbers across intervals smaller than the uniform gap size, the absolute maximum gap cannot reside inside a single bucket cluster. We drop comparison sorting entirely by assigning element scopes into mapped bucket indices in linear time.

This bucket classification layout yields:

```text
O(n)
```

which satisfies the linear complexity constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Finding the min/max values requires an `O(n)` scan. Populating the buckets takes `O(n)` time, and walking through the finite buckets to extract the maximum gap takes `O(n)` iterations.

---

### Space Complexity

```text
O(n)
```

Two auxiliary bucket tracking arrays (`bucketMin` and `bucketMax`) of at most size `n` are allocated to maintain range summaries.

---

## Key Insight

By establishing a lower-bound size limit for the maximum possible gap mathematically, we turn a global sequence sorting problem into a simpler evaluation of min/max values across separate bucket segments.

```text
Time  : O(n)
Space : O(n)
```

---

## Similar Problems

1. Sort Colors (75)
2. Contains Duplicate III (220)
3. H-Index (274)
4. First Missing Positive (41)
5. Group Anagrams (49)
