# Range Sum Query - Mutable

## Problem Statement

Given an integer array `nums`, handle multiple queries of two types:

1. **Update** the value of an element in `nums`.
2. Return the **sum** of the elements of `nums` between indices `left` and `right` inclusive.

Implement the `NumArray` class:
- `NumArray(int[] nums)` Initializes the object with the integer array `nums`.
- `void update(int index, int val)` Updates the value of `nums[index]` to be `val`.
- `int sumRange(int left, int right)` Returns the sum of the elements of `nums` between indices `left` and `right` inclusive (i.e., `nums[left] + nums[left + 1] + ... + nums[right]`).

The overall run time complexity for both updates and queries should be:

```text
O(log(n))
```

---

## Examples

### Example 1

**Input**

```java
NumArray numArray = new NumArray(new int[]{1, 3, 5});
numArray.sumRange(0, 2); // return 9 (1 + 3 + 5)
numArray.update(1, 2);   // nums = [1, 2, 5]
numArray.sumRange(0, 2); // return 8 (1 + 2 + 5)
```

---

## Brute Force Approach

Use a standard array for point updates and a simple linear scan loop for range calculations.

### Steps

1. `update(index, val)`: Modify the target array position directly via `nums[index] = val`.
2. `sumRange(left, right)`: Initialize a sum tracking variable to 0. Loop from index `left` to `right` and accumulate the values.

### Complexity

```text
Time Complexity: O(1) for update, O(n) for sumRange
Space Complexity: O(1) auxiliary space
```

If the system processes millions of range summary requests across large datasets, a linear scan per query triggers severe performance bottlenecks. We can optimize this by maintaining balance using a Segment Tree.

---

# Optimal Approach: Segment Tree

## Key Idea

A **Segment Tree** divides an array range into continuous halves and caches the sum aggregations at structural parent nodes. This allows us to balance update and query runtimes at a logarithmic scale.

1. Construct a flat tree array of size `4 * n` recursively. The leaf nodes store individual elements, while the internal nodes store the sum of their children.
2. For an `update` query, perform a binary path walk down the tree branches to find the matching leaf node, update its value, and re-aggregate all parent segment values while backtracking up to the root.
3. For a `sumRange` query, traverse down the tree and check how the current node's segment boundaries intersect with the query range `[left ... right]`. Complete overlaps return immediately, while partial overlaps branch down to inspect both child nodes.

---

## Visual Understanding

Suppose we initialize the Segment Tree with `nums = `:

```text
               [0...2] (Sum = 9)
               /     \
       [0...1] (Sum=4) [2...2] (Sum=5)
       /     \
   [0..0]=1  [1..1]=3
```

- When calling `sumRange(0, 2)`, the root node `[0...2]` completely overlaps with our query range and immediately returns its precalculated value `9` in \(O(1)\) time.
- When calling `update(1, 2)`, the algorithm paths straight down to leaf `[1..1]`, updates its value from 3 to 2, and propagates the change back up. The node `[0...1]` adjusts to `3`, and the root node `[0...2]` updates to `8`.

---

## Partition Variables

Let:

```java
int[] tree;
int n;
```

---

### Border Elements

The internal nodes map left and right child relationships explicitly using arithmetic index equations:

```java
int leftChild = 2 * node + 1;
int rightChild = 2 * node + 2;
```

---

## Correct Partition Condition

When evaluating range intersections during a query call, non-overlapping segments return the identity element for addition (`0`):

```java
if (qr < start || ql > end) {
    return 0; // Segment is completely outside the target range
}
```

---

## How to Move Binary Search

*(Note: This design framework replaces standard value-range searches with a recursive coordinate interval division framework to achieve sub-linear query and modification updates).*

---

## Java Solution

```java
class NumArray {

    private final int[] tree;
    private final int n;

    public NumArray(int[] nums) {
        this.n = nums.length;
        if (n > 0) {
            this.tree = new int[4 * n];
            buildTree(nums, 0, 0, n - 1);
        } else {
            this.tree = new int[0];
        }
    }

    // Step 1: Build the segment tree bottom-up
    private void buildTree(int[] nums, int node, int start, int end) {
        if (start == end) {
            tree[node] = nums[start];
            return;
        }

        int mid = start + (end - start) / 2;
        int leftChild = 2 * node + 1;
        int rightChild = 2 * node + 2;

        buildTree(nums, leftChild, start, mid);
        buildTree(nums, rightChild, mid + 1, end);

        tree[node] = tree[leftChild] + tree[rightChild];
    }
    
    // Step 2: Dynamically update point values in O(log n) time
    public void update(int index, int val) {
        if (n > 0) {
            updateValue(0, 0, n - 1, index, val);
        }
    }

    private void updateValue(int node, int start, int end, int index, int val) {
        if (start == end) {
            tree[node] = val;
            return;
        }

        int mid = start + (end - start) / 2;
        if (index <= mid) {
            updateValue(2 * node + 1, start, mid, index, val);
        } else {
            updateValue(2 * node + 2, mid + 1, end, index, val);
        }

        tree[node] = tree[2 * node + 1] + tree[2 * node + 2];
    }
    
    // Step 3: Query segment range sums in O(log n) time
    public int sumRange(int left, int right) {
        if (n == 0) return 0;
        return queryRange(0, 0, n - 1, left, right);
    }

    private int queryRange(int node, int start, int end, int ql, int qr) {
        // Case 1: No overlap
        if (qr < start || ql > end) {
            return 0;
        }

        // Case 2: Complete overlap
        if (ql <= start && end <= qr) {
            return tree[node];
        }

        // Case 3: Partial overlap
        int mid = start + (end - start) / 2;
        int leftSum = queryRange(2 * node + 1, start, mid, ql, qr);
        int rightSum = queryRange(2 * node + 2, mid + 1, end, ql, qr);

        return leftSum + rightSum;
    }
}
```

---

## Dry Run

### Input Operations

```java
NumArray numArray = new NumArray(new int[]{1, 3, 5});
numArray.update(1, 2);
numArray.sumRange(0, 2);
```

---

### Step Execution Traversal

1. **`update(1, 2)`**:
   - Descends from root `[0...2]` -> Moves to left child `[0...1]` -> Moves to leaf `[1...1]`.
   - Replaces leaf value `3` with `2`.
   - Backtracks to `[0...1]`, re-calculating the sum: `1 + 2 = 3`.
   - Backtracks to root `[0...2]`, re-calculating the sum: `3 + 5 = 8`.

2. **`sumRange(0, 2)`**:
   - Commences at root `node=0` managing segment `[0...2]`.
   - The query parameters `[0...2]` completely cover the node's segment limits.
   - The method intercepts the complete overlap match instantly and returns `tree[0]` which reads `8`.

---

### Answer

```java
8
```

---

## Why Do We Use a Segment Tree?

While a Prefix Sum array computes range sums in \(O(1)\) time, updating an element forces a full \(O(n)\) recomputation of the remaining elements. A Segment Tree provides a balanced layout where both values modifications and interval range inquiries are completed within deterministic logarithmic limits.

This structural balancing model yields:

```text
O(log n) per operation
```

which satisfies the optimal complexity constraints.

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Reason |
| :--- | :--- | :--- |
| **`NumArray` (Init)** | **O(n)** | Walks and configures all tree node segments exactly once. |
| **`update`** | **O(log n)** | Follows a strict, single path down to the target leaf node. |
| **`sumRange`** | **O(log n)** | At most 4 nodes are evaluated at any given tree layer depth. |

---

### Space Complexity

```text
O(n)
```

The underlying data layout allocates up to `4 * n` memory cells to maintain the flattened binary interval segment hierarchy tree.

---

## Key Insight

Trading extra storage to build an explicit binary interval tree breaks the limitations of array modification tasks, securing stable, sub-linear logarithmic runtimes for dynamic datasets.

```text
Time  : O(log n) per update / sum query
Space : O(n) continuous array memory layout
```

---

## Similar Problems

1. Range Sum Query 2D - Mutable (308)
2. Range Minimum Query (RMQ)
3. Count of Smaller Numbers After Self (315)
4. Create Maximum Number (321)
5. Range Sum Query - Immutable (303)
