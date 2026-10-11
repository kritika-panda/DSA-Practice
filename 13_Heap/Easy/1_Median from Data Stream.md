# Find Median from Data Stream

## Problem Statement

The **median** is the middle value in an ordered integer list. If the size of the list is even, there is no middle value, and the median is the mean of the two middle values.

Implement the `MedianFinder` class:
- `MedianFinder()` Initializes the `MedianFinder` object.
- `void addNum(int num)` Adds the integer `num` from the data stream to the data structure.
- `double findMedian()` Returns the median of all elements so far. Answers within `10^-5` of the actual answer will be accepted.

The overall run time complexity for each insertion should be:

```text
O(log(n))
```

---

## Examples

### Example 1

**Input**

```java
MedianFinder medianFinder = new MedianFinder();
medianFinder.addNum(1);    // arr = [1]
medianFinder.addNum(2);    // arr = [1, 2]
medianFinder.findMedian(); // return 1.5 (i.e., (1 + 2) / 2)
medianFinder.addNum(3);    // arr = [1, 2, 3]
medianFinder.findMedian(); // return 2.0
```

---

## Brute Force Approach

Append each incoming value to a dynamic list, sort the list on every query, and locate the middle element(s).

### Steps

1. Maintain an internal list of integers `numsList`.
2. `addNum(num)`: Append `num` straight to the end of `numsList`.
3. `findMedian()`: Sort `numsList` in non-decreasing order using a standard library sorting call. 
4. If the size is odd, return the element at index `size / 2`. If even, return the average of elements at `(size / 2) - 1` and `size / 2`.

### Complexity

```text
Time Complexity: O(1) for addNum, O(n * log(n)) per findMedian query
Space Complexity: O(n)
```

Sorting the entire dataset dynamically on every query fails when processing highly dense real-time data streams. The problem requires keeping the list balanced during the insertion step.

---

# Optimal Approach: Two Heaps (Max-Heap & Min-Heap)

## Key Idea

Instead of maintaining a fully sorted array, we can conceptually divide the stream data into two equal sorted halves:
1. **Left Half (Smaller numbers):** We want easy access to the largest number in this half. This is handled by a **Max-Heap**.
2. **Right Half (Larger numbers):** We want easy access to the smallest number in this half. This is handled by a **Min-Heap**.

```text
[ Max-Heap (Small Half) ]  <-- max() | min() -->  [ Min-Heap (Large Half) ]
```

When a number arrives, we balance its insertion between the heaps using these structural invariants:
- Any element in the Max-Heap must be less than or equal to any element in the Min-Heap.
- The Max-Heap is allowed to hold at most **one more element** than the Min-Heap (i.e., `maxHeap.size() >= minHeap.size()` and their size difference cannot exceed 1).

### Balancing Rule
1. Push `num` into the Max-Heap first.
2. To filter the largest element properly, take the top of the Max-Heap and transfer it straight to the Min-Heap.
3. If the Min-Heap's size becomes larger than the Max-Heap's size, pull the top of the Min-Heap back to the Max-Heap to restore our size constraint.

To calculate the median:
- If total size is odd, the median is simply the top of the Max-Heap.
- If total size is even, the median is the average of the top elements of both heaps.

---

## Visual Understanding

Suppose we add the numbers `1`, `2`, and `3` into the stream:

1. **AddNum(1):**
   - Push to Max-Heap -> `[1]`. Move top to Min-Heap -> `minHeap: [1]`.
   - `minHeap.size() > maxHeap.size()` -> Pull `1` back to Max-Heap.
   - State: `maxHeap: [1]`, `minHeap: []`. Median = `1.0`.

2. **AddNum(2):**
   - Push to Max-Heap -> `maxHeap: [2, 1]`. Move top (`2`) to Min-Heap -> `minHeap: [2]`.
   - Sizes are balanced (`1 == 1`).
   - State: `maxHeap: [1]`, `minHeap: [2]`. Median = `(1 + 2) / 2.0 = 1.5`.

3. **AddNum(3):**
   - Push to Max-Heap -> `maxHeap: [3, 1]`. Move top (`3`) to Min-Heap -> `minHeap: [3, 2]`.
   - `minHeap.size() > maxHeap.size()` -> Pull `2` back to Max-Heap.
   - State: `maxHeap: [2, 1]`, `minHeap: [3]`. Median = `2.0`.

---

## Partition Variables

Let:

```java
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
```

---

### Border Elements

The boundary sizes are kept locked using conditional size adjustment updates during heap insertions:

```java
if (minHeap.size() > maxHeap.size()) {
    maxHeap.offer(minHeap.poll());
}
```

---

## Correct Partition Condition

The current total element count dictates how the final median calculation is structured:

```java
if (maxHeap.size() > minHeap.size()) {
    return maxHeap.peek();
} else {
    return (maxHeap.peek() + minHeap.peek()) / 2.0;
}
```

---

## How to Move Binary Search

*(Note: This optimal configuration swaps out standard binary range tracking for two interacting Priority Queue heaps to find the median point of dynamic data streams in logarithmic time).*

---

## Java Solution

```java
import java.util.Collections;
import java.util.PriorityQueue;

class MedianFinder {

    private final PriorityQueue<Integer> maxHeap; // Tracks the smaller half of numbers (Max-Heap)
    private final PriorityQueue<Integer> minHeap; // Tracks the larger half of numbers (Min-Heap)

    public MedianFinder() {
        // Max-Heap requires reversing natural order sorting priorities
        this.maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        this.minHeap = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        // Step 1: Offer element to max-heap to filter through its max sorting boundary
        maxHeap.offer(num);
        
        // Step 2: Balance the element to the min-heap container
        minHeap.offer(maxHeap.poll());

        // Step 3: Enforce size invariant (max-heap size >= min-heap size)
        if (minHeap.size() > maxHeap.size()) {
            maxHeap.offer(minHeap.poll());
        }
    }
    
    public double findMedian() {
        // If odd, the middle element is securely sitting on top of the max-heap
        if (maxHeap.size() > minHeap.size()) {
            return maxHeap.peek();
        } 
        // If even, compute the mean between the two middle boundary values
        else {
            return (maxHeap.peek() + minHeap.peek()) / 2.0;
        }
    }
}
```

---

## Dry Run

### Input Operations

```java
MedianFinder finder = new MedianFinder();
finder.addNum(1);
finder.addNum(2);
finder.findMedian();
```

---

### Step Execution Traversal

1. **`addNum(1)`**:
   - `maxHeap.offer(1)` -> maxHeap: `[1]`.
   - `minHeap.offer(maxHeap.poll())` -> minHeap: `[1]`, maxHeap: `[]`.
   - `minHeap.size() (1) > maxHeap.size() (0)` -> `maxHeap.offer(minHeap.poll())`.
   - End state: maxHeap: `[1]`, minHeap: `[]`.

2. **`addNum(2)`**:
   - `maxHeap.offer(2)` -> maxHeap: `[2, 1]`.
   - `minHeap.offer(maxHeap.poll())` -> minHeap: `[2]`, maxHeap: `[1]`.
   - `minHeap.size() (1) > maxHeap.size() (1)` evaluates to false. No shifts.
   - End state: maxHeap: `[1]`, minHeap: `[2]`.

3. **`findMedian()`**:
   - Heap sizes match (`1 == 1`). Even count logic path triggers.
   - Computes `(maxHeap.peek() + minHeap.peek()) / 2.0` -> `(1 + 2) / 2.0`.
   - Returns `1.5`.

---

### Answer

```java
1.5
```

---

## Why Is the Insertion Time Complexity Logarithmic?

Instead of maintaining a fully sorted sequence via standard arrays—which imposes a costly linear time barrier for insertion—elements are managed across two heap trees. Element updates and structural balancing adjustments are completed along isolated branch structures, keeping insertion time strictly within logarithmic limits.

This dual-heap layout yields:

```text
O(log n) per addition, O(1) per median lookup
```

which satisfies the optimal scaling constraints cleanly.

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Reason |
| :--- | :--- | :--- |
| **`addNum`** | **O(log n)** | The element passes through heap restructuring calls, which take logarithmic time. |
| **`findMedian`** | **O(1)** | Direct retrieval of root values using peak looks. |

---

### Space Complexity

```text
O(n)
```

The memory footprint scales linearly as the dual heaps store all numbers received from the data stream.

---

## Key Insight

Dividing an ordered stream evenly between a Max-Heap and a Min-Heap shifts sorting overhead to insertion time, enabling constant-time access to the middle boundary values.

```text
Time  : O(log n) per insertion update
Space : O(n) streaming memory allocation
```

---

## Similar Problems

1. Kth Largest Element in a Stream (703)
2. Sliding Window Median (480)
3. IPO (502)
4. Top K Frequent Elements (347)
5. Find Median from Data Stream II
