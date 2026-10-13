# Number of Operations to Make Network Connected

## Problem Statement

There are `n` computers numbered from `0` to `n - 1` connected by ethernet cables `connections` where `connections[i] = [ai, bi]` represents a connection between computers `ai` and `bi`. Any computer can reach any other computer directly or indirectly through the network.

You are given an initial computer network `connections`. You can extract certain cables between two directly connected computers, and place them between any two disconnected computers to make them directly connected.

Return *the **minimum number of times** you need to do this in order to make all the computers connected*. If it is impossible, return `-1`.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E) or O(E · α(V))
```

*(where V is the number of computers n, E is the total number of connections, and α is the Inverse Ackermann function)*

---

## Example 1

### Input

```text
n = 4
connections = [,,]
```

### Output

```text
1
```

### Explanation

```text
We can remove the redundant cable between 1 and 2, and connect computers 1 and 3 instead.
Now all components are unified. Total operations = 1.
```

---

## Example 2

### Input

```text
n = 6
connections = [,,,]
```

### Output

```text
-1
```

### Explanation

```text
There are only 4 cables for 6 computers. 
To connect 6 computers, we need at least 5 cables. It is impossible to connect them all.
```

---

# Intuition

This problem can be elegantly solved by recognizing two fundamental rules of graph theory:
1. **The Cable Capacity Rule:** To fully connect `n` independent components into a single unified tree network, you need a minimum of **`n - 1` edges**. If `connections.length < n - 1`, we short-circuit and return `-1` immediately because there simply aren't enough physical cables in the room.
2. **The Components Rule:** If we have a sufficient total number of cables, we can use a **Disjoint Set Union (DSU / Union-Find)** structure to find how many completely separate **Connected Components** exist in the graph. 
   - If there are `C` isolated components, we need exactly **`C - 1` operations** to link them all into a single unified chain network.
   - Since we already confirmed the total cable count is sufficient, we are mathematically guaranteed to have enough redundant cables hidden inside cycles to form these `C - 1` bridge connections.

---

# State Definition

Let:

```text
int[] parent = new int[n]; // Array mapping each node to its subset root parent
int components = n;        // Global counter tracking distinct isolated graph sub-groups
```

---

# Recurrence / Pointer Transition Condition

```text
For each cable connection (u, v) in the network:
    rootU = find(u)
    rootV = find(v)
    If rootU != rootV:
        union(rootU, rootV)
        components-- // Unified one component gap

Return components - 1
```

---

# Optimal Structural Graph Connection Solution

## Idea

By checking total cable counts up front and utilizing a Disjoint Set Union (DSU) optimized with **Path Compression** to track components dynamically, root matching lookups take near-constant \(O(\alpha(V))\) time. This resolves the network connection boundary inside highly optimized **O(E · α(V))** parameters.

---

## Java Code (Disjoint Set Union Framework)

```java
class Solution {

    // Optimized Disjoint Set Union (DSU) data architecture model
    private static class UnionFind {
        private final int[] parent;
        private final int[] rank;
        private int components;

        public UnionFind(int size) {
            this.parent = new int[size];
            this.rank = new int[size];
            this.components = size;
            
            // Initially, every node is its own representative parent root
            for (int i = 0; i < size; i++) {
                parent[i] = i;
                rank[i] = 1;
            }
        }

        // Find operation with recursive Path Compression optimization
        public int find(int i) {
            if (parent[i] == i) {
                return i;
            }
            // Compresses the structure by linking nodes straight to the absolute root
            return parent[i] = find(parent[i]);
        }

        // Union operation optimized using Rank balancing depth parameters
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
                components--; // One independent subset gap successfully bridged
                return true;
            }
            return false; // Nodes already share a parent component (redundant link)
        }

        public int getComponents() {
            return this.components;
        }
    }

    public int makeConnected(int n, int[][] connections) {
        // Fast-fail: If total cables are strictly less than n - 1, full connectivity is impossible
        if (connections.length < n - 1) {
            return -1;
        }

        UnionFind dsu = new UnionFind(n);

        // Process cable connections sequentially to isolate components
        for (int[] connection : connections) {
            int u = connection[0];
            int v = connection[1];
            dsu.union(u, v);
        }

        // Total operations needed to merge C components is C - 1
        return dsu.getComponents() - 1;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(E · α(V)) -> Where E is the total number of connections and V is the number of nodes (n). Iterating through edges takes linear time, and the DSU find/union operations execute in near-constant amortized O(α(V)) timelines.
Space Complexity: O(V)          -> Dedicated space allocated to house the parent and rank mapping helper arrays inside our UnionFind data structure.
```

---

# Dry Run

## Input

```text
n = 4, connections = [,,] (Matching Example 1)
```

---

### DSU Execution Trace

```text
Initial DSU State: parent = [0, 1, 2, 3], components = 4

1. Process edge [0, 1]:
   - find(0) != find(1) -> dsu.union(0, 1) -> true. components becomes 3.
2. Process edge [0, 2]:
   - find(0) != find(2) -> dsu.union(0, 2) -> true. components becomes 2.
3. Process edge [1, 2]:
   - find(1) == find(2) (Both map to root 0) -> false (Redundant cable found!). components stays 2.

Loop ends. 
Total remaining independent components = 2.
Operations needed = components - 1 = 2 - 1 = 1.

Returns 1.
```

---

# Visualization

## Component Unification Mapping

```text
   Initial Components          Redundant Cable Hit         Unified Solution
      (0) ─── (1)                  (0) ─── (1)                (0) ─── (1)
       │                            │     /                    │
       ▼                            ▼    ▼                     ▼
      (2)     (3)                  (2)   (3)                  (2) ─── (3)
                                 [Redundant cable             Bridge placed!
                                  links 1 and 2]
```

---

# Graph Paradigm Pattern

## Identify State

```text
Graph connectivity analysis solved via Disjoint Set Union (DSU) independent components counting.
```

## Choices

```text
Enforce baseline edge limits at the entrance. Link distinct roots to dynamically collapse the components count, extracting the operational bridging cost natively.
```

---

## Key Takeaways

✅ Standard core structural template for graph partitioning and connectivity maximization challenges.

✅ **The Operations Reduction Pattern:** Reframing this problem from "simulating cable swaps" down to a simple count of **Connected Components minus 1** bypasses the need to track where redundant cables are stored, keeping runtime completely linear.

✅ Directly maps to real-world infrastructure systems like **redundancy planning inside localized LAN networks**, routing bridge configurations, power grid line management, or joining cluster servers.

✅ Common Graph Design Pattern:

```text
Network Component Unification via Disjoint Set Union Allocation
```

### Similar Problems

- Redundant Connection (LeetCode 684)
- Number of Provinces (LeetCode 547)
- Graph Valid Tree (LeetCode 261)
- Connecting Cities With Minimum Cost (LeetCode 1135)
