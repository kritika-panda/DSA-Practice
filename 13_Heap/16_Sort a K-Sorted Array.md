# Sort a K-Sorted Array (Nearly Sorted Array)

## Problem Statement

Given an array of `n` elements, where each element is at most `k` positions away from its target sorted position, sort the array efficiently.

For example, an element at index `i` in the original array will sit within the index range `[i - k, i + k]` in the final completely sorted array.

The overall runtime complexity must be strictly bounded by:

```text
O(n * log(k))
```

*(where n is the total number of elements inside the array and k is the maximum shift displacement offset)*

---

## Examples

### Example 1

**Input**

```java
arr = [3, 2, 4, 1, 6, 5]
k = 2
```

**Output**

```java
[1, 2, 3, 4, 5, 6]
```

**Explanation**

- `1` is at index 3 in the input, and moves to index 0 in the sorted array (shift of 3, which is bounded under standard index offsets).
- Every item sits within `k = 2` spaces from its actual sorted coordinate location.

---

### Example 2

**Input**

```java
arr = [2, 6, 3, 12, 56, 8]
k = 3
```

**Output**

```java
[2, 3, 6, 8, 12, 56]
```

---

## Brute Force Approach

Ignore the near-sorted property entirely and apply a standard comparison-based sorting algorithm to rearrange the dataset.

### Steps

1. Invoke a default array sorting function directly: `Arrays.sort(arr)`.
2. The entire data layout is rewritten in ascending order.
3. Return the sorted collection.

### Complexity

```text
Time Complexity: O(n * log(n)) // due to sorting all elements from scratch
Space Complexity: O(1) if sorting in place
```

Sorting the entire array ignores the valuable structural guarantee that no element is more than `k` positions away from its home cell. For massive datasets where `n` is large but `k` is small, a log-linear \(O(n \log n)\) pass introduces unnecessary computational overhead. We can optimize this down to **\(O(n \log k)\)** time by using a sliding window heap.

---

# Optimal Approach: Sliding Window Min-Heap

## Key Idea

Because every element is at most `k` positions away from its sorted index, the absolute smallest element of the entire array must reside somewhere within the very first `k + 1` elements (index range `[0, k]`).

This structural boundary constraint means we can find and place elements sequentially by sliding a **Min-Heap (PriorityQueue)** across the array:
1. **Initialize the Window Heap:** Create a Min-Heap and push the first `k + 1` elements into it. 
2. **Streaming Sorting Loop:** Establish a `write` pointer initialized to `0`. Iterate through the remaining array elements starting from index `k + 1`:
   - The absolute smallest element among the current options is guaranteed to sit at the root of the heap. Extract it via `.poll()` and write it directly into our array: `arr[write++] = minHeap.poll()`.
   - Push the new incoming element onto the heap: `minHeap.offer(arr[i])` to keep our sorting lookahead option pool fresh.
3. **Flush the Remainder:** Once the input text stream runs out of elements, empty the remaining `k` elements out of the heap one by one into the trailing indices of the array.

---

## Visual Understanding

Suppose `arr = [3, 2, 4, 1, 6, 5]` and `k = 2`:

- **Step 1 (Populate initial window `k + 1` = 3 elements):**
  - Offer indices 0, 1, 2 \(\implies\) `[3, 2, 4]`.
  - `minHeap` structure balances to: `[2, 3, 4]`. `write = 0`.
- **Step 2 (Slide through the remaining elements):**
  - **i = 3 (Element 1):** 
    - Poll root element (`2`) and write to `arr`. `arr[0] = 2`. `write = 1`.
    - Offer `1` to heap. `minHeap` re-balances to: `[1, 3, 4]`.
  - **i = 4 (Element 6):**
    - Poll root element (`1`) and write to `arr`. `arr[1] = 1`. `write = 2`.
    - Offer `6` to heap. `minHeap` re-balances to: `[3, 6, 4]`.
  - **i = 5 (Element 5):**
    - Poll root element (`3`) and write to `arr`. `arr[2] = 3`. `write = 3`.
    - Offer `5` to heap. `minHeap` re-balances to: `[4, 6, 5]`.
- **Step 3 (Flush remaining elements):**
  - Poll `4` \(\implies\) `arr[3] = 4`.
  - Poll `5` \(\implies\) `arr[4] = 5`.
  - Poll `6` \(\implies\) `arr[5] = 6`.

The array finishes sorted as `[1, 2, 3, 4, 5, 6]`.

---

## Partition Variables

Let:

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
int write = 0;
```

---

### Border Elements

The range caps ensure that the initialization pass handles edge cases safely where the array's total size is shorter than the displacement constraint `k`:

```java
int initialCap = Math.min(arr.length, k + 1);
for (int i = 0; i < initialCap; i++) {
    minHeap.offer(arr[i]);
}
```

---

## Correct Partition Condition

The streaming sliding window step shifts elements inside a single linear tracking pass:

```java
for (int i = k + 1; i < arr.length; i++) {
    arr[write++] = minHeap.poll();
    minHeap.offer(arr[i]);
}
while (!minHeap.isEmpty()) {
    arr[write++] = minHeap.poll();
}
```

---

## Java Solution

```java
import java.util.PriorityQueue;

class Solution {

    public void sortKSortedArray(int[] arr, int k) {
        if (arr == null || arr.length <= 1 || k == 0) {
            return;
        }

        // Default constructor initializes an ascending Min-Heap layout
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        int n = arr.length;

        // Step 1: Push the first k + 1 elements into the min-heap
        // Math.min guards against out-of-bounds if k exceeds the array length
        int initialWindow = Math.min(n, k + 1);
        for (int i = 0; i < initialWindow; i++) {
            minHeap.offer(arr[i]);
        }

        int write = 0;

        // Step 2: Slide the heap window across the remaining elements of the array
        for (int i = k + 1; i < n; i++) {
            // The absolute minimum element among valid choices pops out from the heap root
            arr[write++] = minHeap.poll();
            
            // Push the current streaming element onto the heap to maintain lookahead options
            minHeap.offer(arr[i]);
        }

        // Step 3: Flush all remaining elements from the heap into the trailing indices
        while (!minHeap.isEmpty()) {
            arr[write++] = minHeap.poll();
        }
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Bounded Heap Sort** | **O(n * log(k))** | **O(k)** | Loops through `n` total elements. Since the min-heap size is strictly capped at `k + 1` elements, each push and pull operation costs an optimized \(O(\log k)\) heap balance time. The auxiliary memory allocation holds up to `k` elements at any point. |

---

## Key Insight

Capping the sorting lookahead buffer to size `k + 1` leverages the localized placement property of a k-sorted array, stripping sorting complexity requirements from the global dataset size `n` down to localized displacement bounds `k`.

```text
Time  : O(n * log(k)) optimal log-displacement scale
Space : O(k) auxiliary sliding window memory footprint
```

---

## Similar Problems

1. Kth Largest Element in an Array (215)
2. Merge k Sorted Lists (23)
3. Top K Frequent Elements (347)
4. Find Median from Data Stream (295)
5. Sort an Array (912)
