# Contains Duplicate III

## Problem Statement

Given an integer array `nums` and two integers `indexDiff` and `valueDiff`, return `true` if there are two distinct indices `i` and `j` in the array such that:

1. `abs(i - j) <= indexDiff`
2. `abs(nums[i] - nums[j]) <= valueDiff`

Return `false` otherwise.

The overall run time complexity should be:

```text
O(n)
```

---

## Examples

### Example 1

**Input**

```java
nums = [1, 2, 3, 1]
indexDiff = 3
valueDiff = 0
```

**Output**

```java
true
```

**Explanation**

We can choose `i = 0` and `j = 3`.  
The index difference is `abs(0 - 3) = 3 <= 3`.  
The value difference is `abs(nums[0] - nums[3]) = abs(1 - 1) = 0 <= 0`.  
Both conditions are satisfied, so we return `true`.

---

### Example 2

**Input**

```java
nums = [1, 5, 9, 1, 5, 9]
indexDiff = 2
valueDiff = 3
```

**Output**

```java
false
```

**Explanation**

No pair of elements satisfies both conditions simultaneously within a sliding window of index distance 2.

---

### Example 3

**Input**

```java
nums = [8, 7, 15, 2]
indexDiff = 1
valueDiff = 1
```

**Output**

```java
true
```

**Explanation**

We can choose `i = 0` and `j = 1`.  
The index difference is `abs(0 - 1) = 1 <= 1`.  
The value difference is `abs(8 - 7) = 1 <= 1`.  
Both conditions are satisfied, so we return `true`.

---

## Brute Force Approach

Iterate through every pair of elements within the index window constraints using nested loops.

### Steps

1. Run an outer loop with pointer `i` from `0` to `n - 1`.
2. Run an inner loop with pointer `j` from `i + 1` up to `i + indexDiff`.
3. For each pair, check if `abs(nums[i] - nums[j]) <= valueDiff`.
4. Return `true` if a match is found; otherwise, return `false` after checking all pairs.

### Complexity

```text
Time Complexity: O(n * indexDiff)
Space Complexity: O(1)
```

If `indexDiff` is close to `n`, this approaches an O(n²) time complexity. Using a self-balancing binary search tree (like `TreeSet` in Java) inside a sliding window improves this to `O(n log(indexDiff))`, which still falls short of strict linear time.

---

# Optimal Approach: Bucket Sort / Sliding Window Strategy

## Key Idea

We can achieve a linear time complexity using **Bucket Sorting** alongside a **Sliding Window**.

We can map values to buckets of width `valueDiff + 1`. This mathematical grouping guarantees that:
1. If two numbers fall into the **same bucket**, their absolute difference is at most `valueDiff`.
2. If two numbers fall into **adjacent buckets**, their absolute difference *might* be at most `valueDiff`, so we check them explicitly.
3. If two numbers are separated by **more than one bucket**, their difference is strictly greater than `valueDiff`.

To enforce the index window constraint (`indexDiff`), we only maintain buckets for elements currently inside the sliding window. When an element falls out of the window, we remove its corresponding bucket.

---

## Visual Understanding

Suppose:

```java
nums = [1, 5, 9, 1]
indexDiff = 3
valueDiff = 3
```

Bucket width = `valueDiff + 1 = 4`.

```text
Bucket 0: handles values [0, 3]
Bucket 1: handles values [4, 7]
Bucket 2: handles values [8, 11]
```

Let's process the array elements sequentially:
- **i = 0 (`nums[0] = 1`):** Maps to `1 / 4 = 0`. Add `1` to Bucket 0.
- **i = 1 (`nums[1] = 5`):** Maps to `5 / 4 = 1`. Bucket 1 is empty. Check neighbor Bucket 0 (`abs(5 - 1) = 4 > 3`) and neighbor Bucket 2 (empty). Add `5` to Bucket 1.
- **i = 2 (`nums[2] = 9`):** Maps to `9 / 4 = 2`. Bucket 2 is empty. Check neighbor Bucket 1 (`abs(9 - 5) = 4 > 3`). Add `9` to Bucket 2.
- **i = 3 (`nums[3] = 1`):** Maps to `1 / 4 = 0`. **Bucket 0 already contains an element (`1`)!** Since it maps to the exact same bucket, we found our valid pair. Return `true`.

---

## Partition Variables

Let:

```java
Map<Long, Long> buckets = new HashMap<>();
long bucketSize = (long) valueDiff + 1;
```

---

### Border Elements

To handle negative numbers correctly under integer division, we remap them using a helper formula so they group seamlessly into consecutive indexing ranges:

```java
private long getBucketId(long val, long bucketSize) {
    return val < 0 ? (val + 1) / bucketSize - 1 : val / bucketSize;
}
```

---

## Correct Partition Condition

For a calculated `bucketId`, we check for matches using these conditions:

```java
if (buckets.containsKey(bucketId)) return true;
if (buckets.containsKey(bucketId - 1) && Math.abs(val - buckets.get(bucketId - 1)) <= valueDiff) return true;
if (buckets.containsKey(bucketId + 1) && Math.abs(val - buckets.get(bucketId + 1)) <= valueDiff) return true;
```

---

## How to Move Sliding Window

When the loop index `i` matches or exceeds `indexDiff`, the element at the trailing edge of the window (`i - indexDiff`) is out of bounds. We remove its bucket before moving to the next iteration:

```java
if (i >= indexDiff) {
    long oldestBucketId = getBucketId(nums[i - indexDiff], bucketSize);
    buckets.remove(oldestBucketId);
}
```

---

## Java Solution

```java
import java.util.HashMap;
import java.util.Map;

class Solution {

    public boolean containsNearbyAlmostDuplicate(int[] nums, int indexDiff, int valueDiff) {

        if (nums == null || nums.length < 2 || indexDiff <= 0 || valueDiff < 0) {
            return false;
        }

        Map<Long, Long> buckets = new HashMap<>();
        long bucketSize = (long) valueDiff + 1;

        for (int i = 0; i < nums.length; i++) {
            
            long val = (long) nums[i];
            long bucketId = getBucketId(val, bucketSize);

            // Condition 1: Same bucket check
            if (buckets.containsKey(bucketId)) {
                return true;
            }

            // Condition 2: Left adjacent bucket check
            if (buckets.containsKey(bucketId - 1) 
                && Math.abs(val - buckets.get(bucketId - 1)) <= valueDiff) {
                return true;
            }

            // Condition 3: Right adjacent bucket check
            if (buckets.containsKey(bucketId + 1) 
                && Math.abs(val - buckets.get(bucketId + 1)) <= valueDiff) {
                return true;
            }

            // Insert current element value into its bucket
            buckets.put(bucketId, val);

            // Maintain the sliding window size constraint
            if (i >= indexDiff) {
                long oldestBucketId = getBucketId(nums[i - indexDiff], bucketSize);
                buckets.remove(oldestBucketId);
            }
        }

        return false;
    }

    private long getBucketId(long val, long bucketSize) {
        // Adjust division calculation properties for negative integers cleanly
        return val < 0 ? (val + 1) / bucketSize - 1 : val / bucketSize;
    }
}
```

---

## Dry Run

### Input

```java
nums = [1, 5, 9, 1]
indexDiff = 3
valueDiff = 3
```

---

### Initial Values

```java
bucketSize = 3 + 1 = 4
bucketsMap = {}
```

---

### Iteration Steps

- **i = 0:** `val = 1`. `bucketId = 1 / 4 = 0`. Map checks: false. `buckets.put(0, 1)`. Map status: `{0=1}`.
- **i = 1:** `val = 5`. `bucketId = 5 / 4 = 1`. Map checks: adjacent `0` checked (`abs(5-1) = 4 > 3`). `buckets.put(1, 5)`. Map status: `{0=1, 1=5}`.
- **i = 2:** `val = 9`. `bucketId = 9 / 4 = 2`. Map checks: adjacent `1` checked (`abs(9-5) = 4 > 3`). `buckets.put(2, 9)`. Map status: `{0=1, 1=5, 2=9}`.
- **i = 3:** `val = 1`. `bucketId = 1 / 4 = 0`. **`buckets.containsKey(0)` is true!** Condition met. Return `true`.

---

### Answer

```java
true
```

---

## Why Do We Use Buckets and a Sliding Window Together?

Mapping element values to a dynamic bucket structure allows us to verify proximity conditions in constant time `O(1)` instead of performing a linear or logarithmic scan across all values in the active window. By tracking exactly one element per bucket within the sliding window, we keep the data structure compact.

This layout yields:

```text
O(n)
```

which satisfies the optimal complexity constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

We perform a single pass through the array of length `n`. For each element, map lookup, insertion, and eviction operations take `O(1)` average time.

---

### Space Complexity

```text
O(min(n, indexDiff))
```

The Hash Map stores at most `indexDiff + 1` entries at any given point during execution.

---

## Key Insight

By scaling bucket capacities to match the maximum allowed value difference, a range search problem simplifies into a constant-time check of identical or adjacent bucket keys.

```text
Time  : O(n)
Space : O(min(n, indexDiff))
```

---

## Similar Problems

1. Contains Duplicate (217)
2. Contains Duplicate II (219)
3. Maximum Gap (164)
4. Sliding Window Maximum (239)
5. Group Anagrams (49)
