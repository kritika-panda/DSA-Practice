# Kth Largest Element in a Stream

## Problem Statement

Design a class to find the `k`-th largest element in a stream. Note that it is the `k`-th largest element in the sorted order, not the `k`-th distinct element.

Implement the `KthLargest` class:
- `KthLargest(int k, int[] nums)` Initializes the object with the integer `k` and the stream of integers `nums`.
- `int add(int val)` Appends the integer `val` to the stream and returns the element representing the `k`-th largest element in the stream.

The overall run time complexity for each insertion should be:

```text
O(log(k))
```

---

## Examples

### Example 1

**Input**

```java
KthLargest kthLargest = new KthLargest(3, new int[]{4, 5, 8, 2});
kthLargest.add(3);   // return 4
kthLargest.add(5);   // return 5
kthLargest.add(10);  // return 5
kthLargest.add(9);   // return 8
kthLargest.add(4);   // return 8
```

**Explanation**

Stream initialization: `nums = [4, 5, 8, 2]`, `k = 3`.
- `add(3)` -> Stream becomes `[4, 5, 8, 2, 3]`. The 3rd largest is `4`.
- `add(5)` -> Stream becomes `[4, 5, 8, 2, 3, 5]`. The 3rd largest is `5`.
- `add(10)` -> Stream becomes `[4, 5, 8, 2, 3, 5, 10]`. The 3rd largest is `5`.
- `add(9)` -> Stream becomes `[4, 5, 8, 2, 3, 5, 10, 9]`. The 3rd largest is `8`.
- `add(4)` -> Stream becomes `[4, 5, 8, 2, 3, 5, 10, 9, 4]`. The 3rd largest is `8`.

---

## Brute Force Approach

Append each incoming value to a dynamic list, sort the list in descending order, and return the element at index `k - 1`.

### Steps

1. Maintain an internal list of integers `streamList`.
2. `KthLargest(k, nums)`: Copy all elements from `nums` into `streamList`.
3. `add(val)`: Append `val` to `streamList`.
4. Sort `streamList` in non-increasing order using standard library sorting.
5. Return the element at index `k - 1`.

### Complexity

```text
Time Complexity: O(n * log(n)) per addition
Space Complexity: O(n)
```

Sorting the entire collection from scratch on every single insertion creates a severe performance bottleneck. We can optimize this by tracking only the top `k` elements using a Min-Heap.

---

# Optimal Approach: Bounded Min-Heap (Priority Queue)

## Key Idea

Instead of tracking all elements in the stream, we can use a **Min-Heap (Priority Queue)** to store exactly the `k` largest elements encountered so far.

By restricting the heap size to `k`, the smallest element among our top `k` pool will always sit right at the top of the heap. This top element represents exactly the `k`-th largest element of the entire stream.

- During initialization, we push all elements from `nums` into the Min-Heap. If the heap size exceeds `k`, we repeatedly pop the smallest elements off the top until the size matches `k`.
- When `add(val)` is called, we push `val` into the heap. If the heap size exceeds `k`, we pop the top element.
- The `k`-th largest element is instantly retrieved in O(1) time by reading the root of the heap (`peek()`).

---

## Visual Understanding

Suppose we initialize the class with `k = 3` and `nums = [4, 5, 8, 2]`:

1. **Stream Initialization to Min-Heap (Max Size = 3):**
   - Push `4` -> Heap: `[4]`
   - Push `5` -> Heap: `[4, 5]`
   - Push `8` -> Heap: `[4, 5, 8]`
   - Push `2` -> Heap: `[2, 4, 8, 5]`. Size is 4. Evict top (`2`). Heap: `[4, 5, 8]`

2. **Add(3):**
   - Push `3` -> Heap: `[3, 4, 8, 5]`. Size is 4. Evict top (`3`). Heap: `[4, 5, 8]`
   - `peek()` returns `4`.

3. **Add(5):**
   - Push `5` -> Heap: `[4, 5, 8, 5]`. Size is 4. Evict top (`4`). Heap: `[5, 5, 8]`
   - `peek()` returns `5`.

---

## Partition Variables

Let:

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
int k;
```

---

### Border Elements

The boundary check ensures the heap elements never grow beyond the targeted top `k` pool constraint during streaming insertions:

```java
if (minHeap.size() > k) {
    minHeap.poll(); // Evicts the smallest overall element out of the top-k pool
}
```

---

## Correct Partition Condition

The root node of our bounded Min-Heap is guaranteed to be the exact `k`-th largest item:

```java
return minHeap.peek();
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partition logic with a bounded Priority Queue heap filter to handle insertion streams in logarithmic time).*

---

## Java Solution

```java
import java.util.PriorityQueue;

class KthLargest {

    private final PriorityQueue<Integer> minHeap;
    private final int k;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        this.minHeap = new PriorityQueue<>(k);

        // Populate the heap with initial array values
        for (int num : nums) {
            add(num);
        }
    }
    
    public int add(int val) {
        // Step 1: Insert the streaming element into the min-heap
        minHeap.offer(val);

        // Step 2: Maintain a strict heap size capacity boundary of k
        if (minHeap.size() > k) {
            minHeap.poll(); // Evict the absolute smallest element
        }

        // Step 3: The top element is now the k-th largest element in the stream
        return minHeap.peek();
    }
}
```

---

## Dry Run

### Input Operations

```java
KthLargest kthLargest = new KthLargest(3, new int[]{4, 5, 8, 2});
kthLargest.add(3);
kthLargest.add(5);
```

---

### Step Execution Traversal

1. **Initialization**:
   - Items `4, 5, 8, 2` pass through `add()`.
   - Heap fills up to `[2, 4, 8, 5]`.
   - Size exceeds 3, triggering `minHeap.poll()` to remove `2`.
   - Initial heap state remains: `[4, 5, 8]`.

2. **`add(3)` Call**:
   - `3` is inserted -> Heap: `[3, 4, 8, 5]`.
   - Size evaluates to 4. `minHeap.poll()` drops `3`.
   - `minHeap.peek()` runs and returns `4`.

3. **`add(5)` Call**:
   - `5` is inserted -> Heap: `[4, 5, 8, 5]`.
   - Size evaluates to 4. `minHeap.poll()` drops `4`.
   - `minHeap.peek()` runs and returns `5`.

---

### Answer

```java
5
```

---

## Why Is the Insertion Time Complexity Logarithmic?

By maintaining a maximum capacity restriction of `k` inside our tree structure, insertion heap-rebalancing overhead stays bounded at \(O(\log k)\) regardless of how many millions of items are processed by the stream over time. This completely avoids full-array comparison sort costs.

This bounded size optimization yields:

```text
O(log(k)) per addition
```

which satisfies the optimal latency constraint.

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Reason |
| :--- | :--- | :--- |
| **`KthLargest` (Init)** | **O(n * log(k))** | Iterates through `n` starting items, performing \(O(\log k)\) heap insertions. |
| **`add`** | **O(log(k))** | The element is pushed into a heap capped strictly at a maximum size of `k`. |

---

### Space Complexity

```text
O(k)
```

The Priority Queue allocates and references memory for exactly `k` items at any given point during execution.

---

## Key Insight

Isolating our viewport strictly to the top `k` items using a Min-Heap converts a global tracking problem into a constant-time retrieval step of the heap's root element.

```text
Time  : O(log k) per streaming addition
Space : O(k) memory window storage footprint
```

---

## Similar Problems

1. Kth Largest Element in an Array (215)
2. Top K Frequent Elements (347)
3. Find K Pairs with Smallest Sums (373)
4. K Closest Points to Origin (973)
5. Find Median from Data Stream (295)
