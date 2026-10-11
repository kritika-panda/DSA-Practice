# Introduction to Lazy Propagation in Segment Trees

## Problem Statement

A standard Segment Tree efficiently handles **point updates** and **range queries** in \(O(\log n)\) time. However, if we need to perform a **range update** (e.g., adding a value v to all elements from index `left` to `right`), a naive approach would update each leaf node individually. This point-by-point strategy degrades range updates to a costly \(O(n \log n)\) time complexity.

To solve this problem, we use **Lazy Propagation** (often referred to as Lazy Loading). It is an optimization technique that defer updates to segments until they are explicitly needed. This allows both range updates and range queries to run in optimal logarithmic time.

A lazy-loaded Segment Tree supports the following core operations efficiently:

```text
1. build(int[] arr)
2. updateRange(int left, int right, int val)
3. queryRange(int left, int right)
```

The overall run time complexity for all dynamic operations becomes:

```text
O(log(n))
```

---

## Key Idea: Deferring Updates

The core philosophy of Lazy Propagation is **greedy laziness**:
- When updating a range `[ql ... qr]`, if the current node's segment `[start ... end]` is **completely inside** the update range, we apply the update to the current node *only* and skip traversing down to its children.
- We cache the pending update value inside a secondary array called the **Lazy Array** at the current node's index position.
- This cached value is passed down to the child nodes later, *only* when a future query or update explicitly forces the algorithm to visit those children.

---

## Visual Understanding

Suppose we have a Sum Segment Tree over an array of size 4 initialized to all 0s, and we want to add `5` to the entire range `[0 ... 3]`.

```text
                  [0...3] (Tree=0, Lazy=5)  <- Apply update here and stop!
                  /     \
          [0...1]       [2...3]             <- Children are NOT visited yet.
          (Lazy=0)      (Lazy=0)
```

- Instead of traversing down to all 4 leaf nodes, the algorithm stops immediately at the root node `[0...3]`. It updates the root value to 4 × 5 = 20 and sets `lazy = 5`.
- The child nodes are left completely unmodified until a later query paths through them. When that happens, the `lazy = 5` value is propagated downward to refresh them before any range reading occurs.

---

## Partition Variables

Let:

```java
int[] tree; // Houses the actual segment tree metrics
int[] lazy; // Houses pending update metrics cached at individual nodes
int n;
```

---

### Border Elements

Before executing any logic at a node, we check the lazy cache to see if there are pending updates that must be applied and pushed down:

```java
if (lazy[node] != 0) {
    tree[node] += (end - start + 1) * lazy[node]; // Apply cached update to sum
    if (start != end) {
        lazy[2 * node + 1] += lazy[node]; // Defer update to left child
        lazy[2 * node + 2] += lazy[node]; // Defer update to right child
    }
    lazy[node] = 0; // Clear current node's lazy state
}
```

---

## Correct Partition Condition

When a range update or query completely encapsulates the active node segment boundaries, processing stops immediately:

```java
if (ql <= start && end <= qr) {
    tree[node] += (end - start + 1) * val;
    if (start != end) {
        lazy[2 * node + 1] += val;
        lazy[2 * node + 2] += val;
    }
    return;
}
```

---

## How to Move Binary Search

*(Note: This design architecture trades immediate execution paths for a deferred caching strategy over binary coordinate segments, ensuring range modifications stay within strict sub-linear limits).*

---

## Java Solution

```java
class LazySegmentTree {

    private final int[] tree;
    private final int[] lazy;
    private final int n;

    public LazySegmentTree(int[] nums) {
        this.n = nums.length;
        if (n > 0) {
            this.tree = new int[4 * n];
            this.lazy = new int[4 * n];
            buildTree(nums, 0, 0, n - 1);
        } else {
            this.tree = new int;
            this.lazy = new int;
        }
    }

    private void buildTree(int[] nums, int node, int start, int end) {
        if (start == end) {
            tree[node] = nums[start];
            return;
        }
        int mid = start + (end - start) / 2;
        buildTree(nums, 2 * node + 1, start, mid);
        buildTree(nums, 2 * node + 2, mid + 1, end);
        tree[node] = tree[2 * node + 1] + tree[2 * node + 2];
    }

    // Step 1: Push pending lazy values downward to child nodes
    private void pushDown(int node, int start, int end) {
        if (lazy[node] != 0) {
            // Apply the lazy update value to the current node segment sum
            tree[node] += (end - start + 1) * lazy[node];

            // If it is not a leaf node, defer the update value to its children
            if (start != end) {
                lazy[2 * node + 1] += lazy[node];
                lazy[2 * node + 2] += lazy[node];
            }
            
            lazy[node] = 0; // Clear lazy cache at this node
        }
    }

    // Step 2: Update a range [ql, qr] in O(log n) time
    public void updateRange(int ql, int qr, int val) {
        if (n > 0) {
            updateRangeUtil(0, 0, n - 1, ql, qr, val);
        }
    }

    private void updateRangeUtil(int node, int start, int end, int ql, int qr, int val) {
        pushDown(node, start, end); // Process any pending updates first

        // Case 1: No overlap
        if (qr < start || ql > end) {
            return;
        }

        // Case 2: Complete overlap
        if (ql <= start && end <= qr) {
            tree[node] += (end - start + 1) * val;
            if (start != end) {
                lazy[2 * node + 1] += val;
                lazy[2 * node + 2] += val;
            }
            return;
        }

        // Case 3: Partial overlap
        int mid = start + (end - start) / 2;
        updateRangeUtil(2 * node + 1, start, mid, ql, qr, val);
        updateRangeUtil(2 * node + 2, mid + 1, end, ql, qr, val);

        tree[node] = tree[2 * node + 1] + tree[2 * node + 2];
    }

    // Step 3: Query a range [ql, qr] in O(log n) time
    public int queryRange(int ql, int qr) {
        if (n == 0) return 0;
        return queryRangeUtil(0, 0, n - 1, ql, qr);
    }

    private int queryRangeUtil(int node, int start, int end, int ql, int qr) {
        pushDown(node, start, end); // Process any pending updates first

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
        int leftSum = queryRangeUtil(2 * node + 1, start, mid, ql, qr);
        int rightSum = queryRangeUtil(2 * node + 2, mid + 1, end, ql, qr);

        return leftSum + rightSum;
    }
}
```

---

## Dry Run

### Input Operations

```java
LazySegmentTree lst = new LazySegmentTree(new int[]{0, 0, 0, 0});
lst.updateRange(0, 3, 5); // Add 5 to all elements
lst.queryRange(1, 2);     // Request sum from index 1 to 2
```

---

### Step Execution Traversal

1. **`updateRange(0, 3, 5)`**:
   - Commences at root `[0...3]`. The update scope covers the segment completely.
   - Root changes directly to (4 - 0 + 1) × 5 = 20.
   - Sets child lazy elements: `lazy = 5`, `lazy = 5`. Returns instantly without looking at children.

2. **`queryRange(1, 2)`**:
   - Commences at root `[0...3]`. `pushDown` is triggered because `lazy == 0` (no change).
   - Partial overlap forces branching to children `[0...1]` and `[2...3]`.
   - **At left child `[0...1]`:** `pushDown` sees `lazy = 5`. Updates `tree = 2 * 5 = 10`. Defers `5` to its children. Clears `lazy = 0`. Proceeds to partial query bounds.
   - **At right child `[2...3]`:** `pushDown` sees `lazy = 5`. Updates `tree = 2 * 5 = 10`. Defers `5` to its children. Clears `lazy = 0`. Proceeds to partial query bounds.
   - The method aggregates child intersections and returns `5 + 5 = 10`.

---

### Answer

```java
10
```

---

## Why Use Lazy Propagation?

Without lazy loading, updating an interval of size n forces the segment tree to execute point-by-point modifications all the way down to the leaf nodes. This degrades range update operations to \(O(n \log n)\) time. 

Lazy Propagation eliminates this overhead by updating complete overlapping segments instantly and deferring child modifications until they are absolutely necessary, keeping all operations locked at sub-linear logarithmic performance bounds.

```text
Time  : O(log n) per range update / query
Space : O(n) array storage footprint
```

---

## Similar Problems

1. Range Sum Query - Mutable (307)
2. Falling Squares (699)
3. Count of Smaller Numbers After Self (315)
4. My Calendar III (732)
5. Shifting Letters II (2381)
