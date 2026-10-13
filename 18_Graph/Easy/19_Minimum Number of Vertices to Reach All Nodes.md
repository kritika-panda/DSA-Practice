# Minimum Number of Vertices to Reach All Nodes

## Problem Statement

Given a **directed acyclic graph** with `n` vertices numbered from `0` to `n-1`, and an array `edges` where `edges[i] = [from_i, to_i]` represents a directed edge from node `from_i` to node `to_i`.

Find the **smallest set of vertices** from which all nodes in the graph are reachable. It's guaranteed that a unique solution exists.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of vertices n and E is the total number of directed edges)*

---

## Example 1

### Input

```text
n = 6
edges = [,,,,]
```

### Output

```text
[0, 3]
```

### Explanation

```text
The directed graph dependencies dictate:
- Node 0 can reach nodes 1, 2, and 5.
- Node 3 can reach nodes 4 and 2.
- Nodes 0 and 3 cannot be reached from any other nodes in the graph.
Therefore, the minimum set of vertices required to reach all nodes is.
```

---

## Example 2

### Input

```text
n = 5
edges = [,,,]
```

### Output

```text
[0, 2, 3]
```

---

# Intuition

This problem can be deceptively simple once you analyze the structural properties of a **Directed Acyclic Graph (DAG)**. 

If a node has at least one incoming edge (i.e., **In-Degree > 0**), it means it can be reached from some other vertex in the graph. Therefore, we never need to include it in our starting source set because visiting its ancestor will automatically grant access to it.

Conversely, if a node has **no incoming edges (In-Degree == 0)**, it is completely impossible to reach it from any other vertex in the graph. It is an absolute root source. To ensure that this specific node is covered, it **must** be included in our starting set. 

Since the graph is acyclic, any node with an in-degree greater than 0 can be traced back to a root node with an in-degree of 0. Thus, the minimum set of vertices required to reach every node is exactly the set of all vertices with an **in-degree of 0**.

---

# State Definition

Let:

```text
boolean[] hasIncomingEdge = new boolean[n]; // Tracks whether a vertex has an in-degree > 0
List<Integer> result = new ArrayList<>();   // Collection containing all the root source nodes
```

---

# Recurrence / Structural Condition

```text
For each edge (u, v) in the edges list:
    hasIncomingEdge[v] = true

For each vertex i from 0 to n-1:
    If !hasIncomingEdge[i]:
        Add i to result
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By utilizing a simple boolean status array to track incoming dependencies during a single linear sweep across the raw edge array, we completely avoid the need to build an explicit adjacency list graph or run heavy BFS/DFS traversals. This extracts the exact minimal vertex footprint inside an optimal **O(V + E)** timeline.

---

## Java Code (In-Degree Optimization)

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    public List<Integer> findSmallestSetOfVertices(int n, List<List<Integer>> edges) {
        // Step 1: Pre-allocate a boolean flag array to mark vertices with an In-Degree > 0
        boolean[] hasIncomingEdge = new boolean[n];

        // Step 2: Record incoming edges by inspecting the destination node of each pair
        for (List<Integer> edge : edges) {
            int u = edge.get(0); // Source node
            int v = edge.get(1); // Destination node
            
            hasIncomingEdge[v] = true; // Node v has an incoming edge
        }

        List<Integer> result = new ArrayList<>();

        // Step 3: Sweep all vertices sequentially; any node left unmarked is an absolute root source
        for (int i = 0; i < n; i++) {
            if (!hasIncomingEdge[i]) {
                result.add(i);
            }
        }

        return result;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> Where V is the number of vertices (n) and E is the size of the edges list. We iterate through all E edges once to populate incoming links, and then loop through all V vertices to extract the roots.
Space Complexity: O(V)     -> Requires only a flat boolean array of size V to track the in-degree status of the nodes.
```

---

# Dry Run

## Input

```text
n = 4, edges = [,]
Directed edges: 0 → 1, 1 → 2, 3 → 2
```

---

### Execution Trace

```text
Initial state: hasIncomingEdge = [false, false, false, false]

1. Process edge:
   - Destination is 1 -> hasIncomingEdge[1] = true
2. Process edge:
   - Destination is 2 -> hasIncomingEdge[2] = true
3. Process edge:
   - Destination is 2 -> hasIncomingEdge[2] = true

State array after edge pass: hasIncomingEdge = [false, true, true, false]

4. Sweep nodes 0 to 3:
   - i = 0: !hasIncomingEdge[0] is True -> Add 0
   - i = 1: !hasIncomingEdge[1] is False -> Skip
   - i = 2: !hasIncomingEdge[2] is False -> Skip
   - i = 3: !hasIncomingEdge[3] is True -> Add 3

Returns the minimal source set array:.
```

---

# Visualization

## In-Degree Entry Map

```text
  [In: 0]             [In: 1]             [In: 2]
    (0) ──────────────► (1) ──────────────► (2)
                                             ▲
  [In: 0]                                    │
    (3) ─────────────────────────────────────┘
    
  Nodes 0 and 3 contain 0 incoming priority edges, acting as the absolute system roots.
```

---

# Graph Paradigm Pattern

## Identify State

```text
Directed graph source-set compilation solved via linear in-degree filtering.
```

## Choices

```text
Mark destination indices across incoming edge lists. Tally unmarked nodes to extract system boundaries natively, bypassing the queue overhead of Kahn's sorting loops.
```

---

## Key Takeaways

✅ High-efficiency optimization model for calculating reachability roots on directed acyclic networks.

✅ **The Root Elimination Property:** In a DAG, you can reach all nodes starting from a set of nodes if and only if that set contains all vertices with an in-degree of 0. Running graph searches from these nodes is mathematically redundant since their connectivity is guaranteed by the topology.

✅ Directly maps to real-world infrastructure designs like **finding the minimum distribution servers** needed to broadcast a feed across a dependency tree, isolating roots in database schema configurations, or tracking initialization files in software compiler systems.

✅ Common Graph Design Pattern:

```text
Source Vertex Isolation via Monotonic In-Degree Constraint Filtering
```

### Similar Problems

- Topological Sort / Kahn's Algorithm (Standard base template)
- Course Schedule (LeetCode 207)
- Find Eventual Safe States (LeetCode 802)
- Keys and Rooms (LeetCode 841)
