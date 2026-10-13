# Shortest Path in a Directed Acyclic Graph (DAG)

## Problem Statement

Given a Directed Acyclic Graph (DAG) with `V` vertices (labeled from `0` to `V-1`) and `E` weighted edges, find the shortest path from a given **source vertex** to all other vertices. If a vertex is unreachable from the source, its shortest distance should be marked as infinity (or `-1` depending on the protocol).

The graph is represented as an adjacency list where `adj[i]` contains pairs of `(neighbor, weight)` representing a directed edge from node `i` to `neighbor` with a specific edge cost.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of vertices and E is the number of directed edges)*

---

## Example

### Input

```text
V = 4, source = 0
edges = [
  0 -> [(1, 2), (2, 4)],
  1 -> [(2, 1)],
  2 -> [(3, 3)],
  3 -> []
]
```

### Output

```text
distance = [0, 2, 3, 6]
```

### Explanation

```text
- Distance to 0: 0 (Source)
- Distance to 1: 0 → 1 (Cost = 2)
- Distance to 2: 0 → 1 → 2 (Cost = 2 + 1 = 3). Note that the direct path 0 → 2 costs 4, which is suboptimal.
- Distance to 3: 0 → 1 → 2 → 3 (Cost = 2 + 1 + 3 = 6)
```

---

# Intuition

While finding the shortest path in a general graph with non-negative weights requires **Dijkstra's Algorithm** (\(O(E \log V)\)), we can leverage the unique structural property of a **DAG** to solve it even faster in **linear \(O(V + E)\) time**.

Because a DAG contains absolutely zero cycles, we can find a **Topological Sort** of its vertices. A topological sort arranges the nodes in a linear hierarchy where every directed edge `u → v` flows strictly from left to right. 

1. **Topological Sort Execution:** Sort the graph using Kahn's algorithm or DFS post-order stack sorting. This guarantees that when we process vertex `u`, we have already evaluated all possible paths that could ever lead into `u`.
2. **Distance Table Initialization:** Create a `distance` array tracking size `V` initialized to infinity (`Integer.MAX_VALUE`), setting `distance[source] = 0`.
3. **Linear Relaxation Pass:** Iterate through the vertices sequentially in their exact topological order:
   * If the current vertex `u` is reachable (`distance[u] != infinity`):
   * Inspect all its outgoing edges pointing to `v` with weight `w`.
   * **The Relaxation Step:** Update the neighbor's distance if taking this step offers a shorter path than previously found:
     ```text
     distance[v] = min( distance[v], distance[u] + w )
     ```

By relaxing edges strictly according to topological priority, each edge is evaluated exactly once, eliminating the need for a heavy priority queue.

---

# State Definition

Let:

```text
int[] distance = new int[V];    // Distance mapping array initialized to infinity
Stack<Integer> topoStack;       // Collection containing the ordered linear topological sequence
```

---

# Recurrence / Relaxation Condition

```text
For each vertex u popped from the topological sorting sequence:
    If distance[u] != INF:
        For each neighbor v of u with edge weight w:
            if (distance[u] + w < distance[v]):
                distance[v] = distance[u] + w
```

---

# Optimal Structural Graph Shortest-Path Solution

## Idea

By pre-sorting the graph topological frame using a standard DFS path-traversal state tracker, we establish dependency hierarchies upfront. A subsequent linear array sweep relaxes edge weights cleanly, computing all shortest paths inside an optimal **O(V + E)** timeline.

---

## Java Code (Topological Sort + Edge Relaxation)

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Stack;

class Solution {

    // Helper tuple class to store weighted edge connections cleanly
    private static class Edge {
        int target;
        int weight;

        Edge(int target, int weight) {
            this.target = target;
            this.weight = weight;
        }
    }

    public int[] shortestPathInDAG(int V, List<List<Edge>> adj, int source) {
        Stack<Integer> topoStack = new Stack<>();
        boolean[] visited = new boolean[V];

        // Step 1: Linear Topological Sort using standard DFS Post-Order Stack Collection
        for (int i = 0; i < V; i++) {
            if (!visited[i]) {
                findTopoSortDFS(i, adj, visited, topoStack);
            }
        }

        // Step 2: Initialize the optimal distance array bounds
        int[] distance = new int[V];
        Arrays.fill(distance, Integer.MAX_VALUE);
        distance[source] = 0; // Baseline source initialization

        // Step 3: Relax edges sequentially by popping nodes according to topological priority
        while (!topoStack.isEmpty()) {
            int curr = topoStack.pop();

            // Only relax outgoing edges if the current parent node is reachable
            if (distance[curr] != Integer.MAX_VALUE) {
                for (Edge edge : adj.get(curr)) {
                    int neighbor = edge.target;
                    int weight = edge.weight;

                    // Standard relaxation step condition
                    if (distance[curr] + weight < distance[neighbor]) {
                        distance[neighbor] = distance[curr] + weight;
                    }
                }
            }
        }

        // Optional conversion wrapper: map unreachable infinity sentinels to -1
        for (int i = 0; i < V; i++) {
            if (distance[i] == Integer.MAX_VALUE) {
                distance[i] = -1;
            }
        }

        return distance;
    }

    private void findTopoSortDFS(int node, List<List<Edge>> adj, boolean[] visited, Stack<Integer> topoStack) {
        visited[node] = true;

        for (Edge edge : adj.get(node)) {
            if (!visited[edge.target]) {
                findTopoSortDFS(edge.target, adj, visited, topoStack);
            }
        }

        // Post-order placement: push element onto stack after all its dependencies are fully explored
        topoStack.push(node);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> Building the topological sort takes O(V + E) via DFS. The relaxation phase visits each node and updates each edge connection exactly once.
Space Complexity: O(V + E) -> Space allocated to house the adjacency list data structures, visited bitmasks, topological stack, and distance tracking array.
```

---

# Dry Run

## Input

```text
V = 3, source = 0, Edges: 0 → 1 (w=2), 1 → 2 (w=3), 0 → 2 (w=6)
```

---

### Execution Trace

```text
1. Topological Sort returns Stack sequence: [0, 1, 2] (Top: 0)
2. Initialize distances: distance = [0, INF, INF]
3. Pop 0: distance[0] == 0.
   - Neighbor 1: 0 + 2 < INF -> distance[1] = 2
   - Neighbor 2: 0 + 6 < INF -> distance[2] = 6
   - distance array maps to: [0, 2, 6]
4. Pop 1: distance[1] == 2.
   - Neighbor 2: 2 + 3 < 6 -> 5 < 6 (Relaxed!) -> distance[2] = 5
   - distance array maps to: [0, 2, 5]
5. Pop 2: distance[2] == 5. No outgoing edges. Loop terminates.

Returns final shortest path metrics:.
```

---

# Visualization

## Topological Shortest-Path Relaxation Wave

```text
  [Dist: 0]             [Dist: INF -> 2]       [Dist: INF -> 6 -> 5]
     (0) ──────────────►────── (1) ───────────►──────► (2)
      │                         ▲                       ▲
      └─────────────────────────┴───────────────────────┘
                     Edge weight = 6 bypassed by
                    cheaper topological path (2 + 3)
```

---

# Graph Paradigm Pattern

## Identify State

```text
Multi-destination shortest-path allocation using linear edge-weight relaxation over topological priority arrays.
```

---

## Choices

```text
Establish hierarchy through a topological sort. Cascade distance updates forward to neighboring nodes, bypassing the sorting overhead of a min-heap.
```

---

## Key Takeaways

✅ High-efficiency optimization model for calculating single-source shortest paths on directional acyclic configurations.

✅ **Negative Weights Handling:** Because the relaxation follows a strict linear sequence determined by the topological sort rather than greedy minimums, this algorithm **correctly processes negative edge weights** flawlessly, completely immune to the structural routing loops that break Dijkstra's algorithm.

✅ Maps directly to real-world industrial models like **critical path analysis (PERT/CPM charts)** in project management, assembly line tracking pipelines, and financial spreadsheet cell calculation dependency trees.

✅ Common Graph Design Pattern:

```text
Linear Single-Source Shortest Path via Topological Sequence Relaxation
```

### Similar Problems

- Longest Path in a DAG (Invert relaxation comparison: `>` instead of `<`)
- Course Schedule II (LeetCode 210)
- Network Delay Time (Dijkstra variant - LeetCode 743)
- Cheapest Flights Within K Stops (LeetCode 787)
