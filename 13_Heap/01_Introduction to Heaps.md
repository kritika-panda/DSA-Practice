# Introduction to Heaps

## Problem Statement

A **Heap** is a specialized, tree-based data structure that satisfies the **Heap Property**. It is a complete binary tree, meaning all levels are completely filled except possibly the lowest level, which is filled from left to right. 

Heaps are primarily used to implement **Priority Queues** because they allow rapid access to the element with the highest priority (the maximum or minimum element) in O(1) time, and support structural updates in logarithmic time.

A standard Heap data structure should support the following core operations efficiently:

```text
1. insert(int value)
2. extractMin() / extractMax()
3. peek()
```

The overall run time complexity for modifications should be:

```text
O(log(n))
```

---

## Structure and Representation

There are two primary types of heaps:
1. **Min-Heap:** The value of each node is greater than or equal to the value of its parent. The absolute **minimum** element sits at the root node.
2. **Max-Heap:** The value of each node is less than or equal to the value of its parent. The absolute **maximum** element sits at the root node.

### Array Representation

Because a heap is a complete binary tree, it can be stored efficiently as a flat, single-dimensional array without wasting space. For any element at index `i` (0-indexed):
- **Parent Index:** `(i - 1) / 2`
- **Left Child Index:** `2 * i + 1`
- **Right Child Index:** `2 * i + 2`

---

## Visual Understanding

Suppose we construct a **Min-Heap** with the following elements: `[3, 8, 5, 12, 10, 7]`.

The corresponding logical binary tree structure looks like this:

```text
                 3 (Root - Absolute Minimum)
               /   \
              8     5
             / \   /
            12 10 7
```

Mapped to a flat array representation:
```text
Index:  0  1  2  3   4   5
Value: [3, 8, 5, 12, 10, 7]
```

- If we need to fetch the minimum element (`peek()`), we read index `0` instantly in O(1) time.
- If we look at element `8` at index `1`, its children are located at indices `3` and `4` (`12` and `10`), which are safely greater than `8`.

---

## Core Operations

### 1. Insert Operation (Up-Heap / Heapify-Up)
We append the new element to the very end of the array (maintaining the complete binary tree structure). Since this new value might violate the heap property, we perform a **Heapify-Up** step: we compare it with its parent and swap them if it is smaller (for Min-Heap). We repeat this process up the branch until the structure stabilizes.

### 2. Extract (Min/Max) Operation (Down-Heap / Heapify-Down)
To remove the highest-priority element, we save the root node value at index `0`. We then take the very last element of the array and copy it directly to index `0` to plug the hole, shrinking the array size by 1. Since this new root likely violates the heap property, we perform a **Heapify-Down** step: we compare the parent with its two children and swap it with the smaller child (for Min-Heap). We trickle the value down until the heap balance is restored.

---

## Java Solution

```java
import java.util.Arrays;
import java.util.NoSuchElementException;

class MinHeap {

    private int[] heap;
    private int size;
    private int capacity;

    public MinHeap(int capacity) {
        this.capacity = capacity;
        this.size = 0;
        this.heap = new int[capacity];
    }

    // Returns the minimum element without removing it
    public int peek() {
        if (size == 0) throw new NoSuchElementException("Heap is empty");
        return heap[0];
    }

    // Step 1: Insert an element into the heap in O(log n) time
    public void insert(int value) {
        if (size == capacity) {
            // Resize array if capacity limit is reached
            capacity *= 2;
            heap = Arrays.copyOf(heap, capacity);
        }
        
        heap[size] = value; // Place element at the end of the complete tree
        size++;
        heapifyUp(size - 1); // Restore the heap property upward
    }

    // Step 2: Remove and return the minimum element in O(log n) time
    public int extractMin() {
        if (size == 0) throw new NoSuchElementException("Heap is empty");
        
        int minItem = heap[0];
        heap[0] = heap[size - 1]; // Overwrite root with the last element
        size--;
        
        heapifyDown(0); // Trickle the value down to restore order
        return minItem;
    }

    private void heapifyUp(int index) {
        int parentIdx = (index - 1) / 2;
        
        // Loop up until we hit the root or the parent value is smaller
        while (index > 0 && heap[index] < heap[parentIdx]) {
            swap(index, parentIdx);
            index = parentIdx;
            parentIdx = (index - 1) / 2;
        }
    }

    private void heapifyDown(int index) {
        int smallest = index;
        int leftChild = 2 * index + 1;
        int rightChild = 2 * index + 2;

        // Check if the left child is smaller than the current smallest node
        if (leftChild < size && heap[leftChild] < heap[smallest]) {
            smallest = leftChild;
        }

        // Check if the right child is smaller than the current smallest node
        if (rightChild < size && heap[rightChild] < heap[smallest]) {
            smallest = rightChild;
        }

        // If a child node is smaller, swap and continue down the tree branch
        if (smallest != index) {
            swap(index, smallest);
            heapifyDown(smallest);
        }
    }

    private void swap(int i, int j) {
        int temp = heap[i];
        heap[i] = heap[j];
        heap[j] = temp;
    }
}
```

---

## Dry Run

### Operations
```java
MinHeap minHeap = new MinHeap(10);
minHeap.insert(5);
minHeap.insert(10);
minHeap.insert(3);
minHeap.extractMin(); // Returns 3
```

---

### Step Execution Traversal

1. **`insert(5)`**: `heap = [5]`, `size = 1`.
2. **`insert(10)`**: `heap = [5, 10]`, `size = 2`. `10 > 5` so `heapifyUp` stops immediately.
3. **`insert(3)`**: 
   - `heap = [5, 10, 3]`, `size = 3`. Appended at index 2.
   - `heapifyUp(2)` runs: parent index is `(2-1)/2 = 0` (value 5).
   - Since `3 < 5`, swap index 2 and index 0 -> `heap = [3, 10, 5]`.
4. **`extractMin()`**:
   - Saves minimum item `3`.
   - Overwrites root with the last item `5` -> `heap = [5, 10, 5]`, `size = 2`.
   - `heapifyDown(0)` runs: left child index 1 (value 10), right child is out of bounds.
   - Since `5 < 10`, no swaps are required. Returns `3`. Final array is `[5, 10]`.

---

## Why Use a Heap Over an Unsorted/Sorted Array?

For problems requiring continuous access to high-priority elements (e.g., dynamic schedules, stream windows), data updates cause trade-offs in linear structures:
- **Unsorted Array:** Fast O(1) insertions, but finding the minimum requires an expensive O(n) search step.
- **Sorted Array:** Fast O(1) lookups, but inserting items forces a costly O(n) element shift overhead.
- **Heap Data Structure:** Balances operations perfectly. It bounds both insertion modifications and top element eviction processes safely within deterministic logarithmic limits.

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Reason |
| :--- | :--- | :--- |
| **`peek`** | **O(1)** | Reads the absolute first slot of the array directly. |
| **`insert`** | **O(log n)** | Moves up a single tree branch matching the tree height \(\log_2(n)\). |
| **`extractMin`** | **O(log n)** | Moves down a single tree branch matching the tree height \(\log_2(n)\). |

---

### Space Complexity

```text
O(n)
```

The data structure allocates continuous memory blocks proportional to the active number of tracked records.

---

## Key Insight

Mapping complete binary tree nodes directly into array indices eliminates explicit pointers, providing predictable, balanced logarithmic boundaries for streaming priority updates.

```text
Time  : O(log n) per structural mutation
Space : O(n) array element storage capacity
```

---

## Similar Problems

1. Kth Largest Element in an Array (215)
2. Top K Frequent Elements (347)
3. Find Median from Data Stream (295)
4. Merge k Sorted Lists (23)
5. Kth Largest Element in a Stream (703)
