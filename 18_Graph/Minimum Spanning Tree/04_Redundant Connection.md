# Redundant Connection

## Problem Statement

In this problem, a tree is an **undirected graph** that is connected and has no cycles.

You are given a graph that started as a tree with `n` nodes labeled from `1` to `n`, but then exactly one additional edge was added to it. This added edge connects two different vertices and was not an edge that already existed. The resulting graph is given as a 2D integer array `edges` of length `n` where `edges[i] = [ui, vi]` indicates that there is an undirected edge between nodes `ui` and `vi`.

Return *an edge that can be removed so that the resulting graph is a tree of `n` nodes*. If there are multiple answers, return the **last** one that appears in the given 2D array `edges`.

The overall runtime complexity must be strictly bounded by:

```text
O(n · α(n))
```

*(where n is the total number of nodes/edges in the graph, and α is the Inverse Ackermann function)*

---

## Example 1

### Input

```text
edges = [,]
```

### Output

```text
```

### Explanation

```text
The initial two edges form a tree layout: 1 — 2 — 3.
Adding the edge creates an invalid cycle: 1 — 2 — 3 — 1.
Removing the edge restores the graph into a valid tree.
```

---

## Example 2

### Input

```text
edges = [,,,,]
```

### Output

```text
```

---

# Intuition

A valid undirected tree with `n` nodes must contain exactly `n - 1` edges and contain **absolutely zero cycles**. We are given `n` edges, meaning exactly one edge is redundant and is responsible for closing a cycle.

This layout maps perfectly to a **Disjoint Set Union (DSU / Union-Find)** data structure approach:
1. **Initialize Subsets:** Start by placing each node from `1` to `n` inside its own independent, isolated subset.
2. **Streaming Edge Verification:** Iterate through the `edges` array sequentially from left to right. For each edge connecting node `u` and node `v`:
   * Query the DSU to find the absolute representative root of both nodes: `rootU = find(u)` and `rootV = find(v)`.
   * **The Cycle Interception Condition:** If `rootU == rootV`, it means that nodes `u` and `v` are **already connected** through an existing path inside the tree. Adding this current edge will immediately close a structural loop, forming an invalid cycle.
   * Since we are scanning from left to right and want to return the *last* edge that creates the loop violation, this exact edge is our redundant target—return it instantly.
   * **The Merge Configuration:** If `rootU != rootV`, they are currently disconnected. We link their components together using `union(u, v)` and move forward safely.

---

# State Definition

Let:

```text
int[] parent = new int[n + 1]; // Array mapping each node to its subset root parent
int[] rank = new int[n + 1];   // Array tracking tree depths to keep the layout balanced
```

---

# Recurrence / Pointer Transition Condition

```text
For each edge (u, v) in the edges list:
    rootU = find(u)
    rootV = find(v)
    If rootU == rootV:
        Return [u, v] // Cycle detected! This edge is redundant.
    Else:
        union(rootU, rootV)
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By wrapping component lookup steps inside a DSU engine optimized with **Path Compression** and **Union by Rank**, finding roots takes near constant \(O(\alpha(n))\) time (where α is the inverse Ackermann function). This allows the entire edge stream evaluation pass to compile inside optimal **O(n · α(n))** time parameters.

---

## Java Code (Disjoint Set Union Framework)

```java
class Solution {

    // Optimized Disjoint Set Union (DSU) engine class
    private static class UnionFind {
        private final int[] parent;
        private final int[] rank;

        public UnionFind(int size) {
            this.parent = new int[size + 1];
            this.rank = new int[size + 1];
            // Initially, every node is its own representative parent root
            for (int i = 1; i <= size; i++) {
                parent[i] = i;
                rank[i] = 1;
            }
        }

        // Find operation optimized with recursive Path Compression
        public int find(int i) {
            if (parent[i] == i) {
                return i;
            }
            // Compresses the structure by linking the node straight to the absolute root
            return parent[i] = find(parent[i]);
        }

        // Union operation optimized using Rank balancing parameters
        public boolean union(int i, int j) {
            int rootI = find(i);
            int rootJ = find(j);

            // If roots match, they already belong to the same component (cycle detected)
            if (rootI == rootJ) {
                return false;
            }

            // Tree balancing via Rank metrics
            if (rank[rootI] > rank[rootJ]) {
                parent[rootJ] = rootI;
            } else if (rank[rootI] < rank[rootJ]) {
                parent[rootI] = rootJ;
            } else {
                parent[rootJ] = rootI;
                rank[rootI]++;
            }

            return true; // Successfully unified two distinct components
        }
    }

    public int[] findRedundantConnection(int[][] edges) {
        int n = edges.length;
        UnionFind dsu = new UnionFind(n);

        // Scan edges sequentially from left to right
        for (int[] edge : edges) {
            int u = edge;
            int v = edge;

            // If union returns false, it means u and v were already linked (cycle found)
            if (!dsu.union(u, v)) {
                return edge; // Return the last redundant edge breaking the tree structure
            }
        }

        return new int; // Placeholder fall-back array configuration
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(n · α(n)) -> Where n is the total number of nodes/edges. The loop runs n times, and each Union-Find call executes in near-constant O(α(n)) amortized time due to path compression and rank optimization.
Space Complexity: O(n)          -> Memory allocated to store the parent and rank mapping helper arrays inside our UnionFind data structure.
```

---

# Dry Run

## Input

```text
edges = [,]
```

---

### DSU Execution Trace

```text
Initial DSU State: parent = , rank = all 1

1. Process edge :
   - find(1) = 1, find(2) = 2. Mismatch!
   - dsu.union(1, 2) returns true. parent becomes .

2. Process edge :
   - find(2) = 1, find(3) = 3. Mismatch!
   - dsu.union(2, 3) returns true. parent becomes .

3. Process edge :
   - find(1) = 1, find(3) = 1. MATCH ENCOUNTERED!
   - Since both share the absolute root parent 1, they are already part of the same component.
   - dsu.union(1, 3) returns false. Loop breaks.

Returns redundant connection edge .
```

---

# Visualization

## Streaming Graph Cycle Formation

```text
     (1)                  (1)                  (1)
                            \                 /   \
                             \               /     \
                              \             ▼       ▼
     (2) ─── (3)   ──►    (2)───(3)   ──►  (2) ─── (3)
  
    Initial State        Edge Added           Edge  Closes Cycle
                         Component grows      (Redundant Link Identified!)
```

---

# Graph Paradigm Pattern

## Identify State

```text
Graph structural integrity verification utilizing Disjoint Set Union (DSU) cycle trapping.
```

---

## Choices

```text
Stream paths sequentially. Link disjoint component roots. Intercept and isolate matching root hits to flag cycle boundaries instantly.
```

---

## Key Takeaways

✅ Foundational graph-theory connectivity tracking paradigm.

✅ Utilizing a Disjoint Set Union structure allows graph cycles to be verified on-the-fly without the high overhead of running complete iterative BFS/DFS searches across the entire edge matrix repeatedly.

✅ Directly maps to real-world infrastructure systems like **network loop prevention rules** (e.g., the Spanning Tree Protocol in network switches), dead-lock avoidance tools, or dynamic cluster grouping engines.

✅ Common Graph Design Pattern:

```text
On-the-Fly Cycle Detection via Disjoint Set Union (DSU) Validation
```

### Similar Problems

- Redundant Connection II (Directed graph variant - LeetCode 685)
- Number of Provinces (LeetCode 547)
- Graph Valid Tree (LeetCode 261)
- Connecting Cities With Minimum Cost (LeetCode 1135)
