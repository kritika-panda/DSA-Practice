# Kth Largest Element in an Array

## Problem Statement

Given an integer array `nums` and an integer `k`, return *the `k`-th largest element in the array*.

Note that it is the `k`-th largest element in the **sorted order**, not the `k`-th distinct element.

Can you solve it without sorting in O(n) time complexity?

The overall runtime complexity must be:

```text
O(n)
```

*(where n is the total number of elements inside the nums array)*

---

## Examples

### Example 1

**Input**

```java
nums = [3,2,1,5,6,4]
k = 2
```

**Output**

```java
5
```

**Explanation**

The sorted array is `[1,2,3,4,5,6]`. The 2nd largest element is `5`.

---

### Example 2

**Input**

```java
nums = [3,2,3,1,2,4,5,5,6]
k = 4
```

**Output**

```java
4
```

**Explanation**

The sorted array is `[1,2,2,3,3,4,5,5,6]`. The 4th largest element is `4`.

---

## Brute Force Approach

Sort the entire array in descending order and return the element sitting at index `k - 1`.

### Steps

1. Sort the input array `nums` using the default primitive sorting algorithm.
2. The array is now arranged in ascending order.
3. Access and return the element from the back: `nums[nums.length - k]`.

### Complexity

```text
Time Complexity: O(n * log(n)) // due to sorting the entire array
Space Complexity: O(1) if sorting in place
```

Sorting the entire array is inefficient because it computes the exact order of *all* elements, when we only care about the relative position of a single element. We can optimize this process down to **linear time** by using a PriorityQueue or a selection algorithm.

---

# Optimal Approach: Min-Heap / PriorityQueue Bounded Slicing

## Key Idea

We can solve this problem in **linear time** by dynamically tracking the `k` largest elements using a **Min-Heap (PriorityQueue)**.

Instead of throwing all elements into a giant heap, we cap the heap size to exactly `k`:
1. Initialize a `PriorityQueue<Integer>` acting as a Min-Heap.
2. Iterate through each number in `nums`:
   - Push the current number onto the min-heap.
   - **The Cap Filter:** If the heap's size exceeds `k`, eject the smallest element from the top of the heap using `.poll()`.
3. Because the smallest elements are continually filtered out, after scanning the entire array, the min-heap will retain exactly the `k` largest elements from the dataset.
4. The element sitting at the top of the min-heap will be the absolute smallest among these `k` largest elements, which is mathematically the **k-th largest element** of the entire array.

---

## Visual Understanding

Suppose `nums = [3,2,1,5,6,4]` and `k = 2`:

- **Initial State:** `minHeap = []`. Iterate through elements:
- **Element 3:** Offer to heap. `minHeap = [3]`. Size (1) <= 2.
- **Element 2:** Offer to heap. `minHeap = [2, 3]`. Size (2) <= 2.
- **Element 1:** Offer to heap. `minHeap = [1, 2, 3]`. Size (3) > 2! Poll top element (`1`). `minHeap = [2, 3]`.
- **Element 5:** Offer to heap. `minHeap = [2, 3, 5]`. Size (3) > 2! Poll top element (`2`). `minHeap = [3, 5]`.
- **Element 6:** Offer to heap. `minHeap = [3, 5, 6]`. Size (3) > 2! Poll top element (`3`). `minHeap = [5, 6]`.
- **Element 4:** Offer to heap. `minHeap = [4, 5, 6]`. Size (3) > 2! Poll top element (`4`). `minHeap = [5, 6]`.

Loop finishes. The top element of the min-heap is **`5`**. This is our 2nd largest element.

---

## Partition Variables

Let:

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
```

---

### Border Elements

Capping the dynamic collection buffer ensures that element insertions and balancing operations execute within tightly bounded thresholds:

```java
minHeap.offer(num);
if (minHeap.size() > k) {
    minHeap.poll(); // Keep heap capacity strictly limited to k
}
```

---

## Correct Partition Condition

The linear insertion pass sweeps through all numbers from left to right:

```java
for (int num : nums) {
    minHeap.offer(num);
    if (minHeap.size() > k) {
        minHeap.poll();
    }
}
return minHeap.peek();
```

---

## Java Solution

```java
import java.util.PriorityQueue;

class Solution {

    public int findKthLargest(int[] nums, int k) {
        if (nums == null || nums.length == 0 || k > nums.length) {
            return 0;
        }

        // Default constructor initializes an ascending Min-Heap layout
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();

        // Step 1: Scan all numbers linearly from left to right
        for (int num : nums) {
            minHeap.offer(num);

            // Step 2: Cap the heap capacity to k elements
            // This drops smaller elements and ensures log(k) adjustment steps
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }

        // Step 3: The root of the min-heap is the k-th largest element
        return minHeap.peek();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Bounded Heap Scan** | **O(n * log(k))** | **O(k)** | Traverses the array of `n` elements. Since the heap size never crosses `k`, each insertion and deletion costs \(O(\log k)\) time. The heap tracks up to `k` elements in memory. |

*(Note: While QuickSelect achieves an average time complexity of O(n), it degrades to O(n²) in the worst case with heavy recursion. The bounded Min-Heap approach guarantees a highly stable, predictable performance profile under all adversarial inputs).*

---

## Key Insight

Limiting the heap container to size `k` automatically discards unnecessary lower element ranges, tracking relative ranks inside highly optimized sub-linear bounds.

```text
Time  : O(n * log(k)) stable performance timeline
Space : O(k) auxiliary storage space allocation
```

---

## Similar Problems

1. Top K Frequent Elements (347)
2. Kth Smallest Element in a Sorted Matrix (378)
3. K Closest Points to Origin (973)
4. Find Median from Data Stream (295)
5. Third Maximum Number (414)
