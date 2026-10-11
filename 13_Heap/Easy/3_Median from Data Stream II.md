# Sliding Window Median (Find Median from Data Stream II)

## Problem Statement

Given an integer array `nums` and an integer `k`, there is a sliding window of size `k` which is moving from the very left of the array to the very right. You can only see the `k` numbers in the window. Each time the sliding window moves right by one position.

Return the median array for each window in the stream. 

The overall run time complexity should be:

```text
O(n * log(k))
```

---

## Examples

### Example 1

**Input**

```java
nums =
k = 3
```

**Output**

```java
```

**Explanation**

Window position     | Window Elements  | Median
------------------- | ---------------- | ------

                    | `[1, 3, -1]`     | `1.0`
                    | `[3, -1, -3]`    | `-1.0`
                    | `[-1, -3, 5]`    | `-1.0`
                    | `[-3, 5, 3]`     | `3.0`
                    | `[5, 3, 6]`      | `5.0`
                    | `[3, 6, 7]`      | `6.0`

---

### Example 2

**Input**

```java
nums =
k = 1
```

**Output**

```java
```

---

## Brute Force Approach

Extract each sliding window of size `k`, sort it from scratch, and compute the median.

### Steps

1. Run a loop from `0` to `n - k`.
2. Extract the subarray block of size `k`.
3. Sort the block using standard library sorting.
4. Compute the median based on whether `k` is odd or even and store it.

### Complexity

```text
Time Complexity: O(n * k * log(k))
Space Complexity: O(k)
```

Sorting every sliding window window block independently from scratch causes severe performance penalties. We can optimize this down to log-linear time by adapting the Two Heaps approach with lazy deletion.

---

# Optimal Approach: Two Heaps with Lazy Deletion

## Key Idea

Just like the original **Find Median from Data Stream** problem, we split our active dataset into two halves using a **Max-Heap** (left side) and a **Min-Heap** (right side). 

However, standard Priority Queues do not support removing arbitrary elements efficiently—calling `remove(element)` takes \(O(k)\) time, which would degrade our algorithm to \(O(n \cdot k)\). 

To preserve an \(O(\log k)\) performance limit, we use **Lazy Deletion**:
1. When an old element falls out of the sliding window, we increment its entry count in a **Hash Map** of invalid pending elements instead of purging it from the heaps instantly.
2. We keep track of the absolute balance of valid elements between our two heaps (`balance = leftHeap.size() - rightHeap.size()`).
3. Whenever an invalid pending element floats to the top of either heap during subsequent queries or insertions, we pop it off immediately using a clean loop check.

---

## Visual Understanding

Suppose `k = 3` and the window moves from `[1, 3, -1]` to `[3, -1, -3]`. The element `1` exits while `-3` enters.

- **Initial State:** `maxHeap: `, `minHeap: ` -> Median = `1.0`.
- **Exit `1`:** `1` is inside `maxHeap`. We record `1` into our lazy deletion map. `balance` drops because a left element left.
- **Enter `-3`:** `-3` is smaller than `minHeap.peek()`, so it goes into `maxHeap`. `balance` re-adjusts.
- **Prune Heaps:** If `1` floats to the top of `maxHeap`, we clear it using our map records.

---

## Partition Variables

Let:

```java
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
Map<Integer, Integer> lazyInvalidated = new HashMap<>();
```

---

### Border Elements

To protect calculations from 32-bit integer overflow limits during median calculations, typecasting to `long` or `double` boundaries is enforced:

```java
double median = ((double)maxHeap.peek() + minHeap.peek()) / 2.0;
```

---

## Correct Partition Condition

The top invalid items are popped continuously before reading current median statistics:

```java
while (!maxHeap.isEmpty() && lazyInvalidated.getOrDefault(maxHeap.peek(), 0) > 0) {
    lazyInvalidated.put(maxHeap.peek(), lazyInvalidated.get(maxHeap.peek()) - 1);
    maxHeap.poll();
}
```

---

## How to Move Binary Search

*(Note: This design approach replaces traditional value range search with a sliding window dual-priority queue system featuring lazy eviction tracking to resolve median constraints in log-linear time).*

---

## Java Solution

```java
import java.util.Collections;
import java.util.HashMap;
import java.util.Map;
import java.util.PriorityQueue;

class Solution {

    public double[] medianSlidingWindow(int[] nums, int k) {
        
        int n = nums.length;
        double[] result = new double[n - k + 1];
        
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder()); // Left smaller half
        PriorityQueue<Integer> minHeap = new PriorityQueue<>(); // Right larger half
        Map<Integer, Integer> lazyEvicted = new HashMap<>(); // Tracks lazy deletions

        // Step 1: Initialize the first sliding window frame
        for (int i = 0; i < k; i++) {
            maxHeap.offer(nums[i]);
        }
        for (int i = 0; i < k / 2; i++) {
            minHeap.offer(maxHeap.poll());
        }

        int idx = 0;
        result[idx++] = getMedian(maxHeap, minHeap, k);

        // Step 2: Slide the window across the stream
        for (int i = k; i < n; i++) {
            int outNum = nums[i - k];
            int inNum = nums[i];
            int balance = 0;

            // Track where the exiting element was located
            balance += (outNum <= maxHeap.peek()) ? -1 : 1;
            lazyEvicted.put(outNum, lazyEvicted.getOrDefault(outNum, 0) + 1);

            // Insert the incoming element into the correct heap partition
            if (!maxHeap.isEmpty() && inNum <= maxHeap.peek()) {
                maxHeap.offer(inNum);
                balance++;
            } else {
                minHeap.offer(inNum);
                balance--;
            }

            // Rebalance the heap sizes based on the net changes
            if (balance < 0) {
                maxHeap.offer(minHeap.poll());
            } else if (balance > 0) {
                minHeap.offer(maxHeap.poll());
            }

            // Prune invalid elements that floated to the top of either heap
            prune(maxHeap, lazyEvicted);
            prune(minHeap, lazyEvicted);

            result[idx++] = getMedian(maxHeap, minHeap, k);
        }

        return result;
    }

    private void prune(PriorityQueue<Integer> heap, Map<Integer, Integer> lazyEvicted) {
        while (!heap.isEmpty() && lazyEvicted.getOrDefault(heap.peek(), 0) > 0) {
            int top = heap.peek();
            lazyEvicted.put(top, lazyEvicted.get(top) - 1);
            heap.poll();
        }
    }

    private double getMedian(PriorityQueue<Integer> maxHeap, PriorityQueue<Integer> minHeap, int k) {
        if (k % 2 != 0) {
            return (double) maxHeap.peek();
        } else {
            return ((double) maxHeap.peek() + minHeap.peek()) / 2.0;
        }
    }
}
```

---

## Dry Run

### Input

```java
nums = ; k = 3
```

---

### Step Execution Traversal

- **First Window Initialization `[1, 3, -1]`:**
  - `maxHeap` populates and splits -> `maxHeap: `, `minHeap: `.
  - `getMedian()` runs -> `k` is odd $\implies$ returns `maxHeap.peek() = 1.0`. `result = 1.0`.
- **Slide Window to include `-3`, evicting `1`:**
  - `outNum = 1`. `1 <= maxHeap.peek() (1)` holds true. `balance = -1`. `lazyEvicted = {1 -> 1}`.
  - `inNum = -3`. `-3 <= maxHeap.peek() (1)` holds true. `maxHeap.offer(-3)`. `balance = 0`.
  - Balance is neutral. Pruning steps run: `maxHeap.peek()` is `1`. It matches our `lazyEvicted` cache! `maxHeap.poll()` removes `1`, map updates to `{1 -> 0}`.
  - New heap state: `maxHeap: `, `minHeap: `.
  - `getMedian()` runs and returns `-1.0`.

---

### Answer

```java
```

---

## Why Is the Time Complexity Logarithmic per Step?

By pairing structural size indicators with a lazy hash map, we eliminate the need to scan the whole queue structure sequentially whenever an element drops out of the sliding window view. Invalid numbers are cleaned up only when they touch heap boundaries, keeping operations within log-linear execution bounds.

This lazy pipeline layout yields:

```text
O(n * log(k))
```

which satisfies the optimal scaling constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n * log(k))
```

The sliding window advances across `n` steps. Each window change triggers heap operations (`offer`, `poll`, or `prune`) bounded strictly by the height of our sliding window size \(\log k\).

---

### Space Complexity

```text
O(n)
```

In the worst-case scenario, the lazy deletion Hash Map holds tracking counters for multiple elements until they float up to heap roots to get cleared.

---

## Key Insight

Using a secondary frequency tracker to defer structural item removals turns an expensive middle-tier search operation into a simple constant-time root cleanup layer.

```text
Time  : O(n * log(k)) execution path
Space : O(n) tracking cache memory footprints
```

---

## Similar Problems

1. Find Median from Data Stream (295)
2. Sliding Window Maximum (239)
3. Bounded Heap Top K Filter Problems
4. Top K Frequent Elements (347)
