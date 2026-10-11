# Range Maximum Query

## Problem Statement

Given an integer array `nums`, handle multiple queries of two types:

1. **Update** the value of an element at a given index in `nums`.
2. Return the **maximum value** among the elements of `nums` between indices `left` and `right` inclusive.

Implement the `RangeMaxQuery` class:
- `RangeMaxQuery(int[] nums)` Initializes the object with the integer array `nums`.
- `void update(int index, int val)` Updates the value of `nums[index]` to be `val`.
- `int queryMax(int left, int right)` Returns the maximum value within the range `[left ... right]` inclusive.

The overall run time complexity for both updates and queries should be:

```text
O(log(n))
```

---

## Examples

### Example 1

**Input**

```java
RangeMaxQuery rmq = new RangeMaxQuery(new int[]{1, 8, 3, 9, 4});
rmq.queryMax(0, 2); // return 8 (max of 1, 8, 3)
rmq.update(2, 10);  // nums = [1, 8, 10, 9, 4]
rmq.queryMax(1, 3); // return 10 (max of 8, 10, 9)
```

---

## Brute Force Approach

Use a standard array for point updates and a simple linear scan loop for range calculations.

### Steps

1. `update(index, val)`: Modify the target array position directly via `nums[index] = val`.
2. `queryMax(left, right)`: Initialize a maximum tracking variable to `Integer.MIN_VALUE`. Loop from index `left` to `right` and track the largest value encountered.

### Complexity

```text
Time Complexity: O(1) for update, O(n) for queryMax
Space Complexity: O(1) auxiliary space
```

If the system processes millions of range maximum queries across large, frequently changing datasets, a linear scan per query triggers severe performance bottlenecks. We can optimize this by maintaining balance using a Segment Tree.

---

# Optimal Approach: Segment Tree

## Key Idea

A **Segment Tree** divides an array range into continuous halves and caches the aggregate range properties (in this case, the maximum value) at structural parent nodes. This allows us to balance update and query runtimes at a logarithmic scale.

1. Construct a flat tree array of size `4 * n` recursively. The leaf nodes store individual elements, while the internal nodes store the maximum value of their two children.
2. For an `update` query, perform a binary path walk down the tree branches to find the matching leaf node, update its value, and re-calculate all parent maximum boundaries while backtracking up to the root.
3. For a `queryMax` query, traverse down the tree and check how the current node's segment boundaries intersect with the query range `[left ... right]`. Complete overlaps return immediately, while partial overlaps branch down to inspect both child nodes, returning the maximum of the two outcomes.

---

## Visual Understanding

Suppose we initialize the Segment Tree with `nums = [1, 8, 3, 9]`:

```text
               [0...3] (Max = 9)
               /     \
       [0...1] (Max=8) [2...3] (Max=9)
       /     \         /     \
   [0..0]=1  [1..1]=8  [2..2]=3  [3..3]=9
```

- When calling `queryMax(0, 2)`, the algorithm queries both `[0...1]` (returns 8) and `[2...3]`, which breaks down to look at `[2..2]` (returns 3). The maximum of 8 and 3 is `8`.
- When calling `update(2, 10)`, the algorithm paths straight down to leaf `[2..2]`, updates its value from 3 to 10, and propagates the change back up. The node `[2...3]` adjusts its maximum to `10`, and the root node `[0...3]` updates its maximum to `10`.

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

When evaluating range intersections during a query call, non-overlapping segments return the identity element for maximum operations (`Integer.MIN_VALUE`):

```java
if (qr < start || ql > end) {
    return Integer.MIN_VALUE; // Segment is completely outside the target range
}
```

---

## How to Move Binary Search

*(Note: This design framework replaces standard value-range searches with a recursive coordinate interval division framework to achieve sub-linear query and modification updates).*

---

## Java Solution

```java
class RangeMaxQuery {

    private final int[] tree;
    private final int n;

    public RangeMaxQuery(int[] nums) {
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

        // Store the maximum of both child segments
        tree[node] = Math.max(tree[leftChild], tree[rightChild]);
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

        // Recompute maximum during backtracking
        tree[node] = Math.max(tree[2 * node + 1], tree[2 * node + 2]);
    }

    // Step 3: Query segment range maximums in O(log n) time
    public int queryMax(int left, int right) {
        if (n == 0) return Integer.MIN_VALUE;
        return queryRange(0, 0, n - 1, left, right);
    }

    private int queryRange(int node, int start, int end, int ql, int qr) {
        // Case 1: No overlap
        if (qr < start || ql > end) {
            return Integer.MIN_VALUE;
        }

        // Case 2: Complete overlap
        if (ql <= start && end <= qr) {
            return tree[node];
        }

        // Case 3: Partial overlap
        int mid = start + (end - start) / 2;
        int leftMax = queryRange(2 * node + 1, start, mid, ql, qr);
        int rightMax = queryRange(2 * node + 2, mid + 1, end, ql, qr);

        return Math.max(leftMax, rightMax);
    }
}
```

---

## Dry Run

### Input Operations

```java
RangeMaxQuery rmq = new RangeMaxQuery(new int[]{1, 8, 3, 9});
rmq.update(2, 10);
rmq.queryMax(1, 2);
```

---

### Step Execution Traversal

1. **`update(2, 10)`**:
   - Descends from root `[0...3]` -> Moves to right child `[2...3]` -> Moves to leaf `[2...2]`.
   - Replaces leaf value `3` with `10`.
   - Backtracks to `[2...3]`, re-calculating the maximum: `Math.max(10, 9) = 10`.
   - Backtracks to root `[0...3]`, re-calculating the maximum: `Math.max(8, 10) = 10`.

2. **`queryMax(1, 2)`**:
   - Commences at root `node=0` managing segment `[0...3]`. Partial overlap with `[1...2]`.
   - Left child `[0...1]` partial overlap -> drills down to leaf `[1...1]`, complete overlap -> returns `8`.
   - Right child `[2...3]` partial overlap -> drills down to leaf `[2...2]`, complete overlap -> returns `10`.
   - Root merges results: `Math.max(8, 10) = 10`.

---

### Answer

```java
10
```

---

## Why Do We Use a Segment Tree?

While static arrays can be preprocessed into a Sparse Table to query maximums in O(1) time, Sparse Tables cannot handle runtime updates efficiently, costing \(O(n \log n)\) to rebuild. A Segment Tree provides a balanced, dynamic data layout where both mutations and range inquiries are completed within deterministic logarithmic limits.

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
| **`RangeMaxQuery` (Init)** | **O(n)** | Walks and configures all tree node segments exactly once. |
| **`update`** | **O(log n)** | Follows a strict, single path down to the target leaf node. |
| **`queryMax`** | **O(log n)** | At most 4 nodes are evaluated at any given tree layer depth. |

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
Time  : O(log n) per update / maximum query
Space : O(n) continuous array memory layout
```

---

## Similar Problems

1. Range Sum Query - Mutable (307)
2. Range Minimum Query (RMQ)
3. Sliding Window Maximum (239)
4. Online Majority Element In Subarray (1157)
5. Falling Squares (699)
