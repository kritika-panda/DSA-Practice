# Convert Min Heap to Max Heap

## Problem Statement

Given an array `nums` of size `n` representing a valid **Min-Heap**, convert it in-place into a valid **Max-Heap**. 

The conversion must be done in-place, and the resulting array must satisfy the Max-Heap property: the value of every parent node must be greater than or equal to the values of its child nodes.

The overall run time complexity should be:

```text
O(n)
```

*(where n is the total number of elements in the nums array)*

---

## Examples

### Example 1

**Input**

```java
nums = [3, 5, 9, 6, 8, 20, 10, 12, 18, 9]
```

**Output**

```java
[20, 18, 10, 12, 9, 9, 3, 5, 6, 8]
```

**Explanation**

The original array is a valid Min-Heap. After rearranging the elements into a Max-Heap format:
- Node at index `0` (`20`) is greater than its children at indices `1` and `2` (`18` and `10`).
- Node at index `1` (`18`) is greater than its children at indices `3` and `4` (`12` and `9`).
Every parent node satisfies the Max-Heap condition. Note that multiple valid Max-Heaps can be formed from the same elements; any valid arrangement is accepted.

---

### Example 2

**Input**

```java
nums = [1, 2, 3]
```

**Output**

```java
[3, 2, 1]
```

**Explanation**

`3` becomes the root, with left child `2` and right child `1`. Since `3 >= 2` and `3 >= 1`, it is a valid Max-Heap.

---

### Example 3

**Input**

```java
nums = [7]
```

**Output**

```java
[7]
```

---

## Brute Force Approach

Extract all elements from the Min-Heap array, insert them one by one into a brand-new Max-Heap data structure, and copy the elements back.

### Steps

1. Create a new Max-Heap (Priority Queue with a reverse comparator).
2. Iterate through each element in the `nums` array and push it into the Max-Heap.
3. Pop elements out of the Max-Heap one by one from index `0` up to `n - 1`.

### Complexity

```text
Time Complexity: O(n * log(n)) // as each insertion takes O(log n) time
Space Complexity: O(n) to maintain the external heap structure
```

This approach violates the in-place constraint and runs in log-linear time. We can optimize this to linear time using a bottom-up heap construction algorithm (Floyd's Backturning Heapify).

---

# Optimal Approach: Bottom-Up Max-Heapify

## Key Idea

We can ignore the fact that the array is currently a Min-Heap and treat it as a raw, unsorted complete binary tree. We can convert any raw array into a valid heap in linear time using **Floyd's Heap Construction Algorithm**.

Instead of pushing elements from the top down, we fix the heap from the **bottom up**:
1. Leaf nodes already satisfy the Max-Heap property by default because they do not have children. Thus, we skip them completely.
2. We find the **last non-leaf node** in the array, which is located exactly at index `(n - 2) / 2`.
3. We loop backward from this index down to `0`. For each internal node, we trigger a standard **Max-Heapify-Down** function to trickle the value down its branch until it settles into a valid position.

By processing the tree from the bottom up, smaller subtrees are guaranteed to be valid Max-Heaps before their parent nodes are processed.

---

## Visual Understanding

Suppose we convert `nums = [1, 2, 3, 4]`:
```text
Original tree:
        1
       / \
      2   3
     /
    4
```

The last non-leaf node index is `(4 - 2) / 2 = 1` (value `2`).
- **i = 1 (`val = 2`):** Max-Heapify-Down at index 1. Its only child is at index 3 (value `4`). Since `4 > 2`, swap them. Array becomes `[1, 4, 3, 2]`.
- **i = 0 (`val = 1`):** Max-Heapify-Down at root index 0. Children are index 1 (`4`) and index 2 (`3`). The larger child is `4`. Since `4 > 1`, swap index 0 and 1. Array becomes `[4, 1, 3, 2]`.
- Continue trickle-down for value `1` at index 1: Its child is index 3 (`2`). Since `2 > 1`, swap index 1 and 3. Array becomes `[4, 2, 3, 1]`.

The final converted layout `[4, 2, 3, 1]` is a valid Max-Heap.

---

## Partition Variables

Let:

```java
int n = nums.length;
int lastNonLeaf = (n - 2) / 2;
```

---

### Border Elements

The internal nodes map left and right child relationships explicitly using arithmetic index equations:

```java
int leftChild = 2 * index + 1;
int rightChild = 2 * index + 2;
```

---

## Correct Partition Condition

During the Max-Heapify-Down trickle loop, if the largest value among the parent and its children is still the parent itself, the subtree is stable, and we stop trickling:

```java
if (largest == index) {
    break; // The Max-Heap property is satisfied for this subtree
}
```

---

## How to Move Binary Search

*(Note: This optimal solution trades standard binary range partitioning for a bottom-up reverse linear pass over internal nodes to rebuild heap structures in linear time).*

---

## Java Solution

```java
class Solution {

    public void convertMinToMaxHeap(int[] nums) {
        if (nums == null || nums.length <= 1) {
            return;
        }

        int n = nums.length;

        // Start from the last non-leaf node and move backward to the root node (index 0)
        for (int i = (n - 2) / 2; i >= 0; i--) {
            maxHeapifyDown(nums, i, n);
        }
    }

    private void maxHeapifyDown(int[] nums, int index, int n) {
        while (index < n) {
            int largest = index;
            int leftChild = 2 * index + 1;
            int rightChild = 2 * index + 2;

            // Check if the left child is larger than the current largest node
            if (leftChild < n && nums[leftChild] > nums[largest]) {
                largest = leftChild;
            }

            // Check if the right child is larger than the current largest node
            if (rightChild < n && nums[rightChild] > nums[largest]) {
                largest = rightChild;
            }

            // If a child node is larger, swap and trickle down further
            if (largest != index) {
                swap(nums, index, largest);
                index = largest; // Move pointer to the child to continue loop
            } else {
                break; // Max-heap property is satisfied
            }
        }
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

---

## Dry Run

### Input

```java
nums = [3, 5, 9, 6, 8]
```

---

### Step Execution Traversal

- **Initialization:** `n = 5`. Last non-leaf index: `(5 - 2) / 2 = 1` (value `5`).
- **i = 1 (`val = 5`):** `leftChild = 3 (6)`, `rightChild = 4 (8)`. 
  - Largest child is `8` at index 4. Since `8 > 5`, swap index 1 and 4.
  - Array becomes `[3, 8, 9, 6, 5]`.
- **i = 0 (`val = 3`):** `leftChild = 1 (8)`, `rightChild = 2 (9)`.
  - Largest child is `9` at index 2. Since `9 > 3`, swap index 0 and 2.
  - Array becomes `[9, 8, 3, 6, 5]`.
  - Trickle down index 2 (`3`): Children are out of bounds (`2 * 2 + 1 = 5 >= 5`). Loop stops.

Final output array is `[9, 8, 3, 6, 5]`, which is a valid Max-Heap.

---

## Why Is the Time Complexity Linear?

Although the `maxHeapifyDown` function takes \(O(\log n)\) time in the worst case, calling it inside a loop across all internal nodes does *not* result in \(O(n \log n)\) time. 

In a complete binary tree, most nodes sit near the bottom levels. Nodes close to the leaf level can only trickle down at most 1 or 2 steps. Only the root node can trickle down the full height of the tree (\(\log n\) steps). Mathematically, the sum of the heights of all nodes is bounded by a geometric series that evaluates exactly to:

```text
O(n)
```

which satisfies the linear complexity requirement.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Where `n` is the total number of elements in the array. Rebuilding the heap structure from the bottom up takes linear time.

---

### Space Complexity

```text
O(1)
```

The conversion is performed entirely in-place. No extra auxiliary arrays, maps, or recursive stacks are allocated.

---

## Key Insight

By bypassing leaf nodes and restructuring internal subtrees from the bottom up, we leverage the concentration of nodes at lower heights to complete full tree conversions in linear time.

```text
Time  : O(n) in-place runtime
Space : O(1) auxiliary memory footprint
```

---

## Similar Problems

1. Implement Min Heap
2. Check if an Array Represents a Min Heap
3. Kth Largest Element in an Array (215)
4. Merge k Sorted Lists (23)
5. Heap Sort Implementation
