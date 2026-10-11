# Check if an Array Represents a Min Heap

## Problem Statement

Given an array `nums` of size `n`, check if the given array represents a valid **Min-Heap**.

A binary array represents a valid Min-Heap if it satisfies the complete binary tree structural property (which holds true by nature of array indexing) and the **Heap Property**: the value of every node must be less than or equal to the values of its child nodes.

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
nums = [3, 8, 5, 12, 10, 7]
```

**Output**

```java
true
```

**Explanation**

Let's test the child links according to the array representation:
- Node at index `0` (value `3`) has children at indices `1` and `2` (values `8` and `5`). Since `3 <= 8` and `3 <= 5`, it is valid.
- Node at index `1` (value `8`) has children at indices `3` and `4` (values `12` and `10`). Since `8 <= 12` and `8 <= 10`, it is valid.
- Node at index `2` (value `5`) has a child at index `5` (value `7`). Since `5 <= 7`, it is valid.
Every node satisfies the Min-Heap condition.

---

### Example 2

**Input**

```java
nums = [10, 15, 14, 25, 30, 11]
```

**Output**

```java
false
```

**Explanation**

Node at index `2` (value `14`) has a left child at index `2 * 2 + 1 = 5` (value `11`).  
Since `14 > 11`, the Min-Heap property is violated.

---

### Example 3

**Input**

```java
nums = [5]
```

**Output**

```java
true
```

**Explanation**

An array with a single element has no child branches, making it a valid heap by default.

---

## Brute Force Approach

Iterate through every single index of the array, calculate its left and right child positions, and check if they satisfy the property.

### Steps

1. Run a loop with index `i` from `0` to `n - 1`.
2. Compute `leftChild = 2 * i + 1` and `rightChild = 2 * i + 2`.
3. If `leftChild < n` and `nums[i] > nums[leftChild]`, return `false`.
4. If `rightChild < n` and `nums[i] > nums[rightChild]`, return `false`.
5. If the loop completes without structural property failures, return `true`.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(1)
```

While this linear loop is functionally efficient, we can optimize the execution slightly by stopping the traversal at the **last non-leaf node**.

---

# Optimal Approach: Internal Node Scanning

## Key Idea

Leaf nodes do not have any children, so they can never violate the heap property. Therefore, we do not need to inspect them.

In an array-based complete binary tree of size `n`, all leaf nodes reside in the second half of the array, and the **last non-leaf node** is located exactly at index:

```text
lastNonLeafIndex = (n - 2) / 2
```

We only need to loop from index `0` up to `lastNonLeafIndex`. For each node, we verify that it is smaller than or equal to its left child, and if a right child exists, smaller than or equal to its right child. Bypassing the leaf nodes eliminates half of the comparison steps.

---

## Visual Understanding

Suppose we evaluate `nums = [3, 8, 5, 12, 10, 7]` where `n = 6`:

The last non-leaf node index is `(6 - 2) / 2 = 2` (value `5`).
We only inspect indices `0, 1, and 2`:

- **i = 0 (`val = 3`):** Left child `8`, Right child `5`. Valid.
- **i = 1 (`val = 8`):** Left child `12`, Right child `10`. Valid.
- **i = 2 (`val = 5`):** Left child `7`, Right child out of bounds. Valid.

No violations found within the internal structural nodes. Return `true`.

---

## Partition Variables

Let:

```java
int n = nums.length;
int lastNonLeaf = (n - 2) / 2;
```

---

### Border Elements

The loop boundary caps the scanning pointers precisely to avoid parsing empty terminal child references:

```java
for (int i = 0; i <= (n - 2) / 2; i++)
```

---

## Correct Partition Condition

If any active parent element violates the minimum threshold check against its sub-nodes:

```java
if (nums[i] > nums[leftChild] || (rightChild < n && nums[i] > nums[rightChild])) {
    return false;
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with a direct non-leaf segment linear scan to validate parent-child invariants in optimal linear time).*

---

## Java Solution

```java
class Solution {

    public boolean isMinHeap(int[] nums) {
        if (nums == null || nums.length <= 1) {
            return true;
        }

        int n = nums.length;
        
        // Scan only the internal nodes (from root up to the last non-leaf node)
        for (int i = 0; i <= (n - 2) / 2; i++) {
            int leftChild = 2 * i + 1;
            int rightChild = 2 * i + 2;

            // Check if parent violates property against the left child
            if (nums[i] > nums[leftChild]) {
                return false;
            }

            // Check if parent violates property against the right child (if it exists)
            if (rightChild < n && nums[i] > nums[rightChild]) {
                return false;
            }
        }

        return true;
    }
}
```

---

## Dry Run

### Input

```java
nums = [10, 15, 14, 25, 30, 11]
```

---

### Step Execution Traversal

- **Initialization:** `n = 6`. Loop bounds: `i <= (6 - 2) / 2` \(\implies\) `i <= 2`.
- **i = 0 (`val = 10`):** `leftChild = 1 (15)`, `rightChild = 2 (14)`. `10 <= 15` and `10 <= 14` hold true.
- **i = 1 (`val = 15`):** `leftChild = 3 (25)`, `rightChild = 4 (30)`. `15 <= 25` and `15 <= 30` hold true.
- **i = 2 (`val = 14`):** `leftChild = 5 (11)`. `nums[2] > nums[5]` \(\implies\) `14 > 11` evaluates to **true**.
- Constraint violation intercepted. Return `false`.

---

### Answer

```java
false
```

---

## Why Is the Time Complexity Linear?

The algorithm scans the nodes of the tree exactly once in a single directional pass. By pruning the traversal window to ignore leaf nodes, the processing loop touches exactly \(\lfloor n/2 \rfloor\) elements, avoiding any full recursive call stacks or item re-sorting penalties.

This linear extraction layout yields:

```text
O(n)
```

which satisfies the absolute optimal complexity constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Where `n` is the total number of elements in the array. The algorithm executes at most `n / 2` validation loop iterations.

---

### Space Complexity

```text
O(1)
```

The validation runs entirely in-place. No extra auxiliary arrays, maps, or recursive stacks are allocated.

---

## Key Insight

Since leaf nodes do not have children, they can never violate a heap condition. Restricting structural validation exclusively to internal parent nodes cuts the total comparison checks in half.

```text
Time  : O(n) execution runtime
Space : O(1) auxiliary memory footprint
```

---

## Similar Problems

1. Check if an Array Represents a Max Heap
2. Implement Min Heap
3. Kth Largest Element in an Array (215)
4. Validate Binary Search Tree (98)
5. Complete Binary Tree Checker (958)
