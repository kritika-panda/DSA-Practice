# Introduction to Segment Trees

## Problem Statement

A **Segment Tree** is an advanced, highly efficient tree-like data structure used for storing information about intervals or segments. It allows answering **range queries** (e.g., finding the sum, minimum, or maximum in a subarray) and performing **element updates** in an array dynamically.

A standard array handles point updates in O(1) time, but calculating range properties takes O(n) time. Conversely, a prefix sum array answers range queries in O(1) time, but modifying elements takes O(n) time. A Segment Tree bridges this gap perfectly by completing both operations in **logarithmic time**.

A standard Segment Tree implementation should support the following core operations efficiently:

```text
1. build(int[] arr)
2. update(int index, int value)
3. query(int left, int right)
```

The overall run time complexity for queries and updates should be:

```text
O(log(n))
```

---

## Structure and Representation

A Segment Tree is a full binary tree. Each node represents a specific interval or segment of the original array:
- The **Root Node** represents the entire array segment `[0 ... n-1]`.
- The **Leaf Nodes** represent individual single-element segments `[i ... i]`.
- An internal node representing `[L ... R]` divides its tracking responsibility symmetrically between its two children: the left child handles `[L ... mid]` and the right child handles `[mid+1 ... R]`.

### Array Representation

For an input array of size `n`, the Segment Tree can contain up to `4 * n` nodes. It is typically implemented as a flat array where for any node at index `i` (0-indexed):
- **Left Child Index** = `2 * i + 1`
- **Right Child Index** = `2 * i + 2`

---

## Visual Understanding

Suppose we construct a **Sum-based Segment Tree** for the following array:
```text
arr = [1, 3, 5, 7]
```

The resulting structural hierarchy looks like this:

```text
               [0...3] (Sum = 16)
               /     \
       [0...1] (Sum=4) [2...3] (Sum=12)
       /     \         /     \
   [0..0]=1  [1..1]=3  [2..2]=5  [3..3]=7
```

- If we want to query the range `[1 ... 3]`, the tree combines the precalculated values from node `[1...1]` (3) and node `[2...3]` (12) to return `15` in \(O(\log n)\) time.
- If we update index `2` to `10`, only the nodes on the direct path from leaf `[2..2]` to the root are altered (`[2...3]` updates to 17, root updates to 28).

---

## Core Operations

### 1. Build Operation
We construct the tree recursively using a bottom-up approach. We split the current segment into halves until we reach the leaf nodes (where L == R). We assign the leaf node the value of the matching array index, and as the recursion unpacks, parent nodes aggregate the values of their children.

### 2. Update Operation
When an array element changes, we perform a binary search path walk down the tree to locate the corresponding leaf node. After modifying the leaf node, we backtrack to the root, updating the aggregated segment values for all parent nodes along the path.

### 3. Query Operation
To evaluate a range query `[queryLeft ... queryRight]`, we traverse the tree and classify how the current node's segment `[nodeLeft ... nodeRight]` intersects with our target range:
- **Complete Overlap:** If the node's segment is completely within the query range, we immediately return the node's precalculated value.
- **No Overlap:** If the segments are completely disjoint, we return an identity value (e.g., `0` for sum queries, `Integer.MAX_VALUE` for minimum queries).
- **Partial Overlap:** If the segments overlap partially, we recursively query both children and merge their results.

---

## Java Solution

```java
class SegmentTree {

    private final int[] tree;
    private final int n;

    public SegmentTree(int[] arr) {
        this.n = arr.length;
        // A segment tree for an array of size n can have up to 4*n nodes
        this.tree = new int[4 * n];
        if (n > 0) {
            buildTree(arr, 0, 0, n - 1);
        }
    }

    // Step 1: Build the tree recursively bottom-up
    private void buildTree(int[] arr, int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start]; // Leaf node stores actual array element
            return;
        }

        int mid = start + (end - start) / 2;
        int leftChild = 2 * node + 1;
        int rightChild = 2 * node + 2;

        buildTree(arr, leftChild, start, mid);
        buildTree(arr, rightChild, mid + 1, end);

        // Parent node stores the sum of both children
        tree[node] = tree[leftChild] + tree[rightChild];
    }

    // Step 2: Query a range [ql, qr] in O(log n) time
    public int query(int ql, int qr) {
        return queryRange(0, 0, n - 1, ql, qr);
    }

    private int queryRange(int node, int start, int end, int ql, int qr) {
        // Case 1: No overlap
        if (qr < start || ql > end) {
            return 0; // Identity element for addition
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

    // Step 3: Update an element at a given index in O(log n) time
    public void update(int index, int val) {
        updateValue(0, 0, n - 1, index, val);
    }

    private void updateValue(int node, int start, int end, int index, int val) {
        if (start == end) {
            tree[node] = val; // Update the leaf node target
            return;
        }

        int mid = start + (end - start) / 2;
        if (index <= mid) {
            updateValue(2 * node + 1, start, mid, index, val);
        } else {
            updateValue(2 * node + 2, mid + 1, end, index, val);
        }

        // Recalculate parent values during backtracking
        tree[node] = tree[2 * node + 1] + tree[2 * node + 2];
    }
}
```

---

## Dry Run

### Operations
```java
int[] arr = {1, 3, 5, 7};
SegmentTree st = new SegmentTree(arr);
st.query(1, 3); // Returns 15
st.update(2, 10);
st.query(1, 3); // Returns 20
```

---

### Step Execution Traversal

1. **`query(1, 3)`**:
   - Starts at `root (node=0)` handling `[0...3]`. Partial overlap with `[1...3]`.
   - Branches to **Left Child** `[0...1]`. Partial overlap.
     - Left child `[0...0]` has no overlap -> returns 0.
     - Right child `[1...1]` has complete overlap -> returns 3.
     - Left branch returns `0 + 3 = 3`.
   - Branches to **Right Child** `[2...3]`. Complete overlap -> returns 12.
   - Root merges both branches: `3 + 12 = 15`.

2. **`update(2, 10)`**:
   - Paths down from `[0...3]` -> Right to `[2...3]` -> Left to `[2...2]`.
   - Modifies leaf `[2...2]` from 5 to 10.
   - Backtracks to `[2...3]`, re-aggregating values: `10 + 7 = 17`.
   - Backtracks to `root [0...3]`, re-aggregating values: `4 + 17 = 21`.

---

## Why Use a Segment Tree over a Prefix Sum Array?

While a Prefix Sum array provides exceptional O(1) range queries, it fails for dynamic datasets:
- **Prefix Sum Updates:** Modifying a single element forces a full recomputation of the remaining array, leading to a costly O(n) update penalty.
- **Segment Tree Balance:** A Segment Tree balances performance metrics evenly. It handles updates in \(O(\log n)\) time while keeping range queries locked at \(O(\log n)\) time. This makes it ideal for highly dynamic tracking applications (e.g., live analytics or streaming ranges).

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Reason |
| :--- | :--- | :--- |
| **`Build`** | **O(n)** | Traverses and sets up all tree nodes exactly once. |
| **`Query`** | **O(log n)** | At most 4 nodes are processed at any given depth level. |
| **`Update`** | **O(log n)** | Follows a strict, single structural path down to a leaf node. |

---

### Space Complexity

```text
O(n)
```

The tree tracking array requires up to `4 * n` continuous memory slots to maintain parent-child segment associations.

---

## Key Insight

A Segment Tree trades a static linear memory profile to break array coordinate update limitations, securing predictable, deterministic logarithmic bounds for both point modification and interval aggregation requests.

```text
Time  : O(log n) per Query/Update
Space : O(n) array storage profile
```

---

## Similar Problems

1. Range Sum Query - Mutable (307)
2. Range Minimum Query (RMQ)
3. Count of Smaller Numbers After Self (315)
4. Create Maximum Number (321)
5. Online Majority Element In Subarray (1157)
