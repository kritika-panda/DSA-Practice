# Connecting Cities With Minimum Cost

## Problem Statement

There are `n` cities labeled from `1` to `n`. You are given the connections as a 2D integer array `connections` where `connections[i] = [xi, yi, costi]` indicates that the cost of connecting city `xi` and city `yi` together is `costi`. A connection is bidirectional.

Return *the **minimum cost** to connect all the `n` cities such that there is at least one path between each pair of cities*. If it is impossible to connect all the `n` cities, return `-1`.

The overall runtime complexity must be strictly bounded by:

```text
O(E * log(V))
```

*(where V is the number of cities and E is the number of connections/edges)*

---

## Example 1

### Input

```text
n = 3
connections = [[1, 2, 5], [1, 3, 6], [2, 3, 1]]
```

### Output

```text
6
```

### Explanation

```text
We can choose connections [1, 2, 5] and [2, 3, 1] to form a single connected component.
Total cost = 5 + 1 = 6. 
This is the minimum cost path to span all 3 cities.
```

---

## Example 2

### Input

```text
n = 4
connections = [[1, 2, 3], [3, 4, 4]]
```

### Output

```text
-1
```

### Explanation

```text
There is no connection between the group {1, 2} and the group {3, 4}. 
It is impossible to connect all the cities together, so return -1.
```

---

# Intuition

This problem maps directly to finding the **Minimum Spanning Tree (MST)** of an undirected, weighted graph. A Minimum Spanning Tree is a subset of edges that connects all vertices together without any cycles while minimizing the total edge weight sum.

We can solve this problem greedily using **Kruskal's Algorithm** paired with a **Disjoint Set Union (DSU / Union-Find)** data structure:
1.  **Greedy Edge Sorting:** Sort all available `connections` in ascending order based on their connection `cost`. This ensures that we always evaluate the cheapest possible connections first.
2.  **Cycle Interception (Union-Find):** Iterate through the sorted edges one by one. For each connection linking city `u` and city `v`:
    *   Find the absolute representative/parent root of both `u` and `v`.
    *   **If their roots mismatch:** They belong to separate disconnected components. Merging them will not introduce a cycle. We link them together using the `.union()` method, add the edge's cost to our total accumulator, and decrement our remaining components counter.
    *   **If their roots match:** They are already connected through an alternative path. Adding this edge would form an invalid cycle—ignore it completely.
3.  **Connectivity Check:** We keep track of how many edges have been successfully added to our MST. A valid MST connecting `n` vertices must contain exactly `n - 1` edges. If our edge counter hits `n - 1`, return the total cost. Otherwise, return `-1`.

---

# State Definition

Let:

```text
int[] parent = new int[n + 1]; // Array mapping each node element to its subset root parent
int components = n;            // Counter checking the number of independent subsets remaining
```

---

# Recurrence / Pointer Transition Condition

```text
For each sorted edge (u, v, cost):
    rootU = find(u)
    rootV = find(v)
    If rootU != rootV:
        union(rootU, rootV)
        totalCost += cost
        edgesAdded++
```

---

# Optimal Structural Minimum Spanning Tree Solution

## Idea

By utilizing Kruskal's algorithm with Path Compression and Union by Rank optimization inside our DSU engine, edge sorting takes \(O(E \log E) = O(E \log V)\) time, and the tracking lookups execute in near constant \(O(E \cdot \alpha(V))\) time (where \(\alpha\) is the inverse Ackermann function). This allows the entire configuration to compile inside optimal **O(E * log(V))** parameters.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    // Disjoint Set Union (DSU) Structural Placeholder Engine
    private static class UnionFind {
        private final int[] parent;
        private final int[] rank;
        private int components;

        public UnionFind(int n) {
            this.parent = new int[n + 1];
            this.rank = new int[n + 1];
            this.components = n;
            for (int i = 1; i <= n; i++) {
                parent[i] = i;
                rank[i] = 1;
            }
        }

        // Find operation with recursive Path Compression optimization
        public int find(int i) {
            if (parent[i] == i) {
                return i;
            }
            return parent[i] = find(parent[i]); // Compresses the path to the root node directly
        }

        // Union operation using Rank optimization to keep tree depths balanced
        public boolean union(int i, int j) {
            int rootI = find(i);
            int rootJ = find(j);

            if (rootI != rootJ) {
                if (rank[rootI] > rank[rootJ]) {
                    parent[rootJ] = rootI;
                } else if (rank[rootI] < rank[rootJ]) {
                    parent[rootI] = rootJ;
                } else {
                    parent[rootJ] = rootI;
                    rank[rootI]++;
                }
                components--; // One independent subset has successfully been merged
                return true;
            }
            return false; // Nodes are already within the same subset; union skipped to prevent a cycle
        }

        public int getComponents() {
            return this.components;
        }
    }

    public int minimumCost(int n, int[][] connections) {
        // Step 1: Sort all connections in ascending order based on their edge weights/costs
        Arrays.sort(connections, (a, b) -> Integer.compare(a[2], b[2]));

        UnionFind dsu = new UnionFind(n);
        int totalCost = 0;
        int edgesAdded = 0;

        // Step 2: Iterate greedily through the cheapest available options
        for (int[] connection : connections) {
            int u = connection[0];
            int v = connection[1];
            int cost = connection[2];

            // If union is successful, no cycle was formed; accept the edge into our MST
            if (dsu.union(u, v)) {
                totalCost += cost;
                edgesAdded++;

                // Optimization: A valid MST over n nodes is completed as soon as n - 1 edges are linked
                if (edgesAdded == n - 1) {
                    return totalCost;
                }
            }
        }

        // Step 3: Check if all cities have been successfully unified into 1 final global component
        return dsu.getComponents() == 1 ? totalCost : -1;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(E * log(V)) -> Where E is the number of connections and V is the number of cities. Sorting the edges dominates the runtime at O(E log E). The subsequent Union-Find operations take near-linear O(E * α(V)) time.
Space Complexity: O(V) -> Required for the parent and rank tracking helper matrices inside the DSU structure.
```

---

# Dry Run

## Input

```text
n = 3, connections = [[1, 2, 5], [1, 3, 6], [2, 3, 1]]
```

---

### Initial State

```text
Sorted connections: [[2, 3, 1], [1, 2, 5], [1, 3, 6]]
dsu: parent = [0, 1, 2, 3], components = 3, totalCost = 0, edgesAdded = 0
```

---

### Kruskal Execution Loop

```text
1. Edge [2, 3, 1] (Cost = 1):
   - find(2) != find(3) -> components merge.
   - dsu.union(2, 3) -> true. parent updates. components = 2.
   - totalCost = 1, edgesAdded = 1.

2. Edge [1, 2, 5] (Cost = 5):
   - find(1) != find(2) -> components merge.
   - dsu.union(1, 2) -> true. parent updates. components = 1.
   - totalCost = 1 + 5 = 6, edgesAdded = 2.
   - Check: edgesAdded (2) == n - 1 (2) -> True!

Loop breaks early. Returns 6.
```

---

# Visualization

## Minimum Spanning Tree Formation Trace

```text
   (1)                (1)                (1)
                        \                  \
                         \ 5                \ 5
                          \                  \
   (2)   (3)   ──►    (2)───(3)   ──►    (2)───(3)
                         1                  1
Initial State       Cheapest Edge     Next Edge Linked
                     Connected        (All Spanned!)
```

---

# Design Pattern

## Identify State

```text
Minimum Spanning Tree (MST) resolution utilizing Kruskal's Greedy algorithm paired with a Disjoint Set Union (DSU) manager.
```

---

## Choices

```text
Sort weights. Accept edges that link distinct subsets (disjoint roots). Eject edges that trigger cycle hits.
```

---

## Key Takeaways

✅ Prototypical graph-theory network optimization architecture.

✅ Demonstrates the core structural engine utilized inside standard **network routing layers** (such as designing an efficient physical fiber-optic cable connection layout among nodes) or cluster grouping systems.

✅ DSU path compression speeds up component matching validation loops, maintaining real-world runtimes inside highly optimized sub-linear bounds.

✅ Common Graph Design Pattern:

```text
Minimum Spanning Tree Tracking via Greedy Edge Consolidation
```
