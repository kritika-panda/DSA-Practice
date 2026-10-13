# Redundant Connection II

## Problem Statement

In this problem, a rooted tree is a **directed graph** such that, there is exactly one node (the root) for which all other nodes are descendants of this node, plus every node has exactly one parent, except for the root node which has no parents.

The given input is a directed graph that started as a rooted tree with `n` nodes (with values from `1` to `n`), but then exactly one additional directed edge was added. The added edge connects two different vertices and was not an edge that already existed. The resulting graph is given as a 2D integer array `edges` where `edges[i] = [ui, vi]` indicates that there is a directed edge from node `ui` to node `vi`.

Return *an edge that can be removed so that the resulting graph is a rooted tree of `n` nodes*. If there are multiple answers, return the **last** one that appears in the given 2D array `edges`.

The overall runtime complexity must be strictly bounded by:

```text
O(n · α(n))
```

*(where n is the total number of nodes/edges in the graph, and α is the Inverse Ackermann function)*

---

## Example 1

### Input

```text
edges = [[1,2],[1,3],[2,3]]
```

### Output

```text
[2,3]
```

### Explanation

```text
The directed graph looks like this:
  1 ──► 2
  │     │
  ▼     ▼
  3 ◄───┘
Node 3 has two incoming edges (two parents): [1,3] and.
Removing [2,3] results in a valid rooted tree where 1 is the root.
```

---

## Example 2

### Input

```text
edges = [[1,2],[2,3],[3,1],[4,1]]
```

### Output

```text
[3,1]
```

### Explanation

```text
The directed graph looks like this:
  4 ──► 1 ──► 2
        ▲     │
        │     ▼
        └─────3
There is a directed cycle: 1 → 2 → 3 → 1.
Node 1 also has two incoming edges (two parents): [3,1] and.
Removing [3,1] breaks the cycle and resolves the dual-parent issue, yielding a valid rooted tree with 4 as the root.
```

---

# Intuition

This problem elevates *Redundant Connection I* into a directed graph space, adding an extra layer of structural constraints. In a valid directed rooted tree:
1. Every node except the root must have **exactly one parent** (In-degree == 1). The root has an In-degree of 0.
2. There must be **absolutely zero directed cycles**.

When an extra directed edge is injected into a tree, it can trigger two types of structural anomalies:
*   **Case A (Dual Parents Only):** A node ends up with two incoming edges (In-degree == 2). No directed cycle is formed.
*   **Case B (Directed Cycle Only):** The extra edge points back to the root or an ancestor node higher up. Every node still has exactly one parent, but a directed loop is closed.
*   **Case C (Dual Parents AND Directed Cycle):** A node gets two parents *and* a directed cycle is formed. In this scenario, one of the two incoming edges to that dual-parent node is part of the cycle, and the other is a legal branch from outside.

We can solve this problem comprehensively using a **Disjoint Set Union (DSU)** structural scanning pass:
1.  **First Scan (Track Parents):** Maintain an array `parentOf[i]` to log the parent of each node. As we step through `edges`, if we find a node `v` that already has a parent registered, we have discovered a **Dual Parent configuration**. We record the two offending edges as `edge1` and `edge2`, and tentatively skip adding `edge2` to our DSU network.
2.  **Second Scan (Detect Cycles via DSU):** Process the edges sequentially into a DSU. If `find(u) == find(v)`, adding the edge `u → v` will form a directed cycle path layer.
    *   *If we had previously found a dual parent:* Since we deliberately skipped `edge2`, the cycle must be caused by `edge1`. Therefore, `edge1` is our true culprit—return it.
    *   *If no dual parent exists (Case B):* The current edge is the exact item closing the directed cycle—return it instantly.
3.  If the DSU cycle loop pass completes cleanly with zero errors, it means our assumption to skip `edge2` was perfectly correct—return `edge2`.

---

# State Definition

Let:

```text
int[] parentOf = new int[n + 1]; // Maps a node index to its immediate parent node
int[] dsuParent = new int[n + 1]; // Core DSU structural representation array
```

---

# Recurrence / Structural Condition Logic

```text
For each edge (u, v) in edges:
    if parentOf[v] != 0:
        edge1 = [parentOf[v], v]
        edge2 = [u, v]
        skip edge2 from immediate DSU insertion
    else:
        parentOf[v] = u

For each edge (u, v) in edges (skipping edge2):
    if dsu.find(u) == dsu.find(v):
        return edge1 != null ? edge1 : current_edge
return edge2
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By analyzing incoming degrees first to isolate dual-parent properties, and running a single-pass DSU cycle validation sequence optimized via **Path Compression**, each edge configuration is processed in near constant time. This resolves the directed tree violations inside an optimized **O(n · α(n))** tracking envelope.

---

## Java Code (Two-tier Directed DSU Architecture)

```java
class Solution {

    // Disjoint Set Union (DSU) Engine with Path Compression and flat rank configurations
    private static class UnionFind {
        private final int[] parent;

        public UnionFind(int size) {
            this.parent = new int[size + 1];
            for (int i = 1; i <= size; i++) {
                parent[i] = i;
            }
        }

        public int find(int i) {
            if (parent[i] == i) {
                return i;
            }
            return parent[i] = find(parent[i]); // Path compression optimization
        }

        public boolean union(int i, int j) {
            int rootI = find(i);
            int rootJ = find(j);

            if (rootI == rootJ) {
                return false; // Cycle closed! Nodes already share a root component
            }
            parent[rootI] = rootJ;
            return true;
        }
    }

    public int[] findRedundantDirectedConnection(int[][] edges) {
        int n = edges.length;
        int[] parentOf = new int[n + 1];
        
        int[] edge1 = null;
        int[] edge2 = null;

        // Step 1: Track incoming degrees to look for a Dual-Parent anomaly
        for (int[] edge : edges) {
            int u = edge[0];
            int v = edge[1];

            if (parentOf[v] != 0) {
                // Node v has two incoming edges! Record both candidate options
                edge1 = new int[]{parentOf[v], v}; // Previous edge
                edge2 = new int[]{u, v};           // Current edge
                break; // A graph can contain at most one extra edge; stop tracking
            }
            parentOf[v] = u;
        }

        UnionFind dsu = new UnionFind(n);

        // Step 2: Use DSU to verify cycle footprints, ignoring edge2 tentatively
        for (int[] edge : edges) {
            int u = edge[0];
            int v = edge[1];

            // Tentatively skip inserting edge2 into our DSU validation structure
            if (edge2 != null && edge[0] == edge2[0] && edge[1] == edge2[1]) {
                continue;
            }

            // If union fails, a cycle loop condition has been tripped
            if (!dsu.union(u, v)) {
                if (edge1 != null) {
                    // A cycle exists even without edge2! edge1 must be the true culprit
                    return edge1;
                }
                // Case B: No dual-parent exists; the cycle is directly closed by this edge
                return edge;
            }
        }

        // Step 3: If no cycles were tripped, our assumption to prune edge2 was correct
        return edge2;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(n · α(n)) -> Splitting the graph check into parent tracking scans and an optimized DSU cycle validation pass takes near-linear time relative to the number of nodes.
Space Complexity: O(n)          -> Required to house the explicit parentOf metadata bitmask and DSU representative tracking arrays.
```

---

# Dry Run

## Input

```text
edges = [[1,2], [2,3], [3,1], [4,1]] (Matching Example 2)
```

---

### Step 1: Parent Mapping Scan

```text
- edge: parentOf[2] = 1
- edge: parentOf[3] = 2
- edge: parentOf[1] = 3
- edge: parentOf[1] is already 3! Dual-parent found.
  edge1 = [3,1]
  edge2 = [4,1]
```

---

### Step 2: DSU Cycle Traversal (Skipping edge2 `[4,1]`)

```text
- edge: dsu.union(1, 2) -> sets parent[1] = 2
- edge: dsu.union(2, 3) -> sets parent[2] = 3
- edge: dsu.find(3) == 3, dsu.find(1) == 3 -> MATCH! Cycle closed.
  Since edge1 != null, return edge1 -> [3,1]

Output matches the required sequence:.
```

---

# Visualization

## Multi-Property Directed Anomalies Check

```text
    Dual Parents + Directed Cycle              Isolated Cycle Only
            (4)                                    (1) ──► (2)
             │                                      ▲       │
             ▼                                      │       ▼
            (1) ──► (2)                            (4) ◄───(3)
             ▲       │
             │       ▼                     Every node has 1 parent, but
            (3) ◄────┘                     Kahn's or DSU catches the loop loop.
   Node 1 has 2 parents, and 
one parent edge [3,1] closes the loop.
```

## Graph Paradigm Pattern

Identify State

```text 
Directed rooted-tree structural integrity verification combining In-degree count evaluation with DSU back-edge trapping. 
```

## Choices

```text 
Isolate vertices with duplicate parents first. Prune lookahead links. Run a selective DSU cycle search pass to resolve topological priority clashes. 
```

## Key Takeaways

✅ Advanced, high-tier directed graph structural verification algorithm.
✅ Bypassing multi-path confusion requires decoupling the problem: identifying the node with an In-degree of 2 isolates the structural choices down to a binary choice model, which can then be cleanly resolved using a simple DSU cycle pass.
✅ Simulates real-world systems layouts like compiler semantic validation check engines tracking circular dependency trees, database schema inheritance model verifications, or garbage collection graph tracing layers.
✅ Common Graph Design Pattern:
text Directed Tree Integrity Verification via In-Degree Isolation and DSU Cycle Pruning 
