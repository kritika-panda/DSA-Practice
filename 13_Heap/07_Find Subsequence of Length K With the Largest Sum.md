# Find Subsequence of Length K With the Largest Sum

## Problem Statement

Given an integer array `nums` and an integer `k`, return a **subsequence** of `nums` of length `k` such that it has the **largest sum**. If there are multiple valid subsequences, return any of them.

A **subsequence** is an array that can be derived from another array by deleting some or no elements without changing the order of the remaining elements.

The overall run time complexity should be:

```text
O(n * log(k))
```

*(where n is the total number of elements in the nums array)*

---

## Examples

### Example 1

**Input**

```java
nums = [2, 1, 3, 3]
k = 2
```

**Output**

```java
[3, 3]
```

**Explanation**

The possible subsequences of length 2 and their sums are:
- `[2, 1]` -> Sum = 3
- `[2, 3]` -> Sum = 5
- `[2, 3]` -> Sum = 5
- `[1, 3]` -> Sum = 4
- `[1, 3]` -> Sum = 4
- `[3, 3]` -> Sum = 6
The subsequence with the largest sum is `[3, 3]`.

---

### Example 2

**Input**

```java
nums = [-1, -2, 3, 4]
k = 3
```

**Output**

```java
[-1, 3, 4]
```

**Explanation**

The subsequence `[-1, 3, 4]` has the largest sum of \(6\).

---

### Example 3

**Input**

```java
nums = [3, 4, 3, 3]
k = 2
```

**Output**

```java
[4, 3]
```

**Explanation**

`[3, 4]` and `[4, 3]` both yield the highest sum of \(7\). `[4, 3]` preserves the original input sequence positions.

---

## Brute Force Approach

Generate all possible subsequences of size `k`, sort or evaluate their sums, and choose one with the absolute maximum value.

### Steps

1. Use backtracking or recursion to generate all combinations of subsets of size `k`.
2. For each generated subsequence, sum up its elements.
3. Track the absolute maximum sum encountered and its corresponding array elements.
4. Return the optimal subsequence.

### Complexity

```text
Time Complexity: O(n! / (k! * (n - k)!))
Space Complexity: O(k) for the recursion path tracking
```

This exponential combination framework results in a Time Limit Exceeded (TLE) error for larger inputs. The problem requires a more optimized selection strategy.

---

# Optimal Approach: Bounded Min-Heap by Value & Sorted by Index

## Key Idea

To find the subsequence with the largest sum, we must greedily select the **top `k` absolute largest values** from the array. However, because the output must be a valid *subsequence*, we must retain the elements in their **original relative order** matching the input array.

We can achieve this cleanly using a two-step framework with a Min-Heap:
1. **Find Top K Values:** Traverse the `nums` array and maintain a Min-Heap capped at a maximum size of `k`. We store elements as pairs containing both their `value` and their original `index`. If the heap size exceeds `k`, we eject the element with the smallest value.
2. **Restore Subsequence Order:** Once the top `k` elements are isolated, we extract them from the heap into a list and sort this list strictly by their original `index` positions. 

Finally, we map these sorted pairs back into a simple primitive integer array.

---

## Visual Understanding

Suppose `nums = [2, 1, 3, 3]` and `k = 2`:

1. **Stream elements to Bounded Min-Heap (Sorted by value):**
   - Push `(2, idx:0)` -> Heap: `[(2, 0)]`
   - Push `(1, idx:1)` -> Heap: `[(1, 1), (2, 0)]`
   - Push `(3, idx:2)` -> Heap size = 3. Evict smallest value `(1, 1)`. Heap: `[(2, 0), (3, 2)]`
   - Push `(3, idx:3)` -> Heap size = 3. Evict smallest value `(2, 0)`. Heap: `[(3, 2), (3, 3)]`

2. **Sort by Index:**
   - Elements extracted: `[(3, 2), (3, 3)]`. 
   - Sorting by index yields the same order because `2 < 3`.

3. **Rebuild Output Array Values:**
   - Index 2 value = `3`. Index 3 value = `3`. Output = `[3, 3]`.

---

## Partition Variables

Let:

```java
PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]);
List<int[]> topKList = new ArrayList<>();
```

---

### Border Elements

The heap boundary conditions cap elements strictly to a maximum capacity of `k`:

```java
if (minHeap.size() > k) {
    minHeap.poll(); // Evicts the absolute smallest value pair out of the top-k viewport
}
```

---

## Correct Partition Condition

The extracted pair elements must be sorted by their index boundaries to restore the subsequence layout property:

```java
Collections.sort(topKList, (a, b) -> a[1] - b[1]);
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitions with a bounded Priority Queue filter coupled with index-based sorting to isolate elements in log-linear time).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.PriorityQueue;

class Solution {

    public int[] maxSubsequence(int[] nums, int k) {
        if (nums == null || nums.length == 0 || k <= 0) {
            return new int[0];
        }

        // Step 1: Maximize values using a Min-Heap capped at size k
        // Each entry stores an array of: {value, original_index}
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]);

        for (int i = 0; i < nums.length; i++) {
            minHeap.offer(new int[]{nums[i], i});
            if (minHeap.size() > k) {
                minHeap.poll(); // Evict the pair with the smallest value
            }
        }

        // Step 2: Unroll heap items into a list to restore original order
        List<int[]> topKList = new ArrayList<>();
        while (!minHeap.isEmpty()) {
            topKList.add(minHeap.poll());
        }

        // Sort the top-k list strictly based on original array index order
        Collections.sort(topKList, (a, b) -> a[1] - b[1]);

        // Step 3: Reconstruct the flat subsequence values array
        int[] result = new int[k];
        for (int i = 0; i < k; i++) {
            result[i] = topKList.get(i)[0];
        }

        return result;
    }
}
```

---

## Dry Run

### Input

```java
nums =;
k = 2;
```

---

### Step Execution Traversal

- **Heap Insertion Phase:**
  - `i = 0 (val 3)` -> Heap: `[[3, 0]]`
  - `i = 1 (val 4)` -> Heap: `[[3, 0], [4, 1]]`
  - `i = 2 (val 3)` -> Size = 3. Smallest value `3` at index 0 drops out. Heap: `[[3, 2], [4, 1]]`
  - `i = 3 (val 3)` -> Size = 3. Smallest value `3` at index 2 drops out. Heap: `[[3, 3], [4, 1]]`
- **Sorting Phase:**
  - `topKList = [[3, 3], [4, 1]]`.
  - Sorted by index values (`1 < 3`): `[[4, 1], [3, 3]]`.
- **Value Reconstruction:** Extracts values at indices 1 and 3 -> `[4, 3]`.

---

### Answer

```java
[4, 3]
```

---

## Why Is the Time Complexity Logarithmic per Element?

By capping the capacity of our priority data layout strictly to `k`, balancing updates are limited by the height of our targeted subset space \(\log k\), completely ignoring the overall size of the remaining background pool elements. The subsequent sort takes \(O(k \log k)\) time, keeping the overall execution well within log-linear bounds.

This bounded size filtering yields:

```text
O(n * log(k))
```

which satisfies the optimal constraint cleanly.

---

## Complexity Analysis

### Time Complexity

```text
O(n * log(k))
```

Traversing the `n` array elements takes \(O(n \log k)\) time since the heap size is capped at `k`. Extracting and sorting the final `k` elements takes \(O(k \log k)\) time, leading to an overall runtime of \(O(n \log k)\).

---

### Space Complexity

```text
O(k)
```

The Priority Queue and index sorting list allocate and track elements for exactly `k` node pairs at any point during execution.

---

## Key Insight

Isolating high-priority values using a bounded heap removes value-extraction bottlenecks, while an index-based sorting pass restores the original subsequence structure without any index tracking regressions.

```text
Time  : O(n * log(k)) execution path
Space : O(k) auxiliary memory tracking layout
```

---

## Similar Problems

1. Kth Largest Element in a Stream (703)
2. Kth Largest Element in an Array (215)
3. Top K Frequent Elements (347)
4. K Closest Points to Origin (973)
5. Max Sum of a Pair With Equal Sum of Digits (2342)
