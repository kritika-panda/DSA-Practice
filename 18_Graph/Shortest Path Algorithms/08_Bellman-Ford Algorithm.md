# Bellman-Ford Algorithm

## Problem Statement

Given a directed, weighted graph with `V` vertices (labeled from `0` to `V-1`) and `E` edges, along with a **source vertex**, find the shortest path from the source vertex to all other vertices. 

Unlike Dijkstra's algorithm, the graph can contain **negative edge weights**. Additionally, your implementation must be able to detect if the graph contains a **negative-weight cycle** (a cycle whose total edge weight sum is negative). If such a cycle is reachable from the source node, a shortest path cannot be determined because you can infinitely loop through the cycle to decrease the path cost to negative infinity. In this scenario, return an indicator flagging the cycle.

The graph is provided as a 2D integer array `edges` where `edges[i] = [u, v, w]` indicates a directed edge from node `u` to node `v` with a weight cost of `w`.

The overall runtime complexity must be strictly bounded by:

```text
O(V × E)
```

---

## Example 1

### Input

```text
V = 4
edges = [,
  [1, 2, -2],
,
  [0, 3, 10]
]
source = 0
```

### Output

```text
distance = [0, 5, 3, 6]
```

### Explanation

```text
- Distance to 0: 0 (Source)
- Distance to 1: 0 → 1 (Cost = 5)
- Distance to 2: 0 → 1 → 2 (Cost = 5 + (-2) = 3)
- Distance to 3: 0 → 1 → 2 → 3 (Cost = 3 + 3 = 6). Note that the direct path 0 → 3 costs 10, which is suboptimal.
```

---

## Example 2 (Negative Cycle Detection)

### Input

```text
V = 3
edges = [,
  [1, 2, -3],
  [2, 1, -1]
]
source = 0
```

### Output

```text
"Negative Cycle Detected"
```

### Explanation

```text
Looping between nodes 1 and 2 yields a cycle cost of (-3) + (-1) = -4. 
Every time the route passes through 1 → 2 → 1, the total distance drops endlessly towards negative infinity. A stable shortest path matrix cannot be resolved.
```

---

# Intuition

The **Bellman-Ford Algorithm** is a classic dynamic programming approach based on the **principle of relaxation**. 

In any graph with `V` vertices, the longest possible shortest path without a cycle can contain at most `V - 1` edges. Therefore, if we loop through all edges and relax them sequentially, a total of `V - 1` iteration passes is mathematically sufficient to guarantee that the shortest distances propagate to all reachable nodes.

1. **Distance Table Initialization:** Create an array `dist` of size `V` initialized to infinity (`1e8` or a safe maximum constant), setting `dist[source] = 0`.
2. **V - 1 Relaxation Cycles:** Run an outer loop exactly `V - 1` times. In each pass, loop through every directed edge `(u, v, w)` in the `edges` array:
   * If the starting node `u` is currently reachable (`dist[u] != infinity`):
   * Check if traveling through `u` to `v` yields a lower cost than previously found:
     ```text
     if (dist[u] + w < dist[v]) -> dist[v] = dist[u] + w
     ```
3. **The Negative Cycle Check:** Run a final, **V-th iteration pass** across all edges. If any edge can *still* be relaxed (i.e., `dist[u] + w < dist[v]`), it means a path cost is still decreasing after `V - 1` edges. This is a mathematical guarantee that a **negative-weight cycle** exists.

---

# State Definition

Let:

```text
int[] dist = new int[V]; // Tracks the minimal accumulated path cost to each vertex row
```

---

# Recurrence / Relaxation Condition

```text
Loop V - 1 times:
    For each edge (u, v, w) in edges list:
        If dist[u] != INF and dist[u] + w < dist[v]:
            dist[v] = dist[u] + w

For the V-th pass:
    If dist[u] != INF and dist[u] + w < dist[v]:
        Flag Negative Cycle Cycle
```

---

# Optimal Structural Graph Shortest-Path Solution

## Idea

By pre-allocating a simple flat primitive array table of size `V` and running an iterative edge relaxation matrix pass, the algorithm avoids the sorting overhead of priority queues completely. This handles negative weights cleanly inside an explicit **O(V × E)** timeline.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    public int[] bellmanFord(int V, int[][] edges, int source) {
        // Step 1: Pre-allocate and initialize the distance table bounds
        int[] dist = new int[V];
        
        // Use a safe upper bound sentinel constant instead of Integer.MAX_VALUE 
        // to prevent potential arithmetic overflow issues on distance additions
        int INF = 100000000; 
        Arrays.fill(dist, INF);
        dist[source] = 0;

        // Step 2: Relax all directed edges exactly V - 1 times
        for (int i = 1; i <= V - 1; i++) {
            for (int[] edge : edges) {
                int u = edge[0];
                int v = edge[1];
                int w = edge[2];

                // Relax the edge only if the parent node u is currently reachable
                if (dist[u] != INF && dist[u] + w < dist[v]) {
                    dist[v] = dist[u] + w;
                }
            }
        }

        // Step 3: Execute a final V-th iteration pass to trap negative-weight cycles
        for (int[] edge : edges) {
            int u = edge[0];
            int v = edge[1];
            int w = edge[2];

            // If an edge can STILL be relaxed on the V-th step, a negative cycle is active
            if (dist[u] != INF && dist[u] + w < dist[v]) {
                // Return a custom flag array indicating an unstable cyclical system graph
                int[] negativeCycleFlag = {-1}; 
                return negativeCycleFlag;
            }
        }

        return dist;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V × E) -> Where V is the number of vertices and E is the total number of edges. The outer loop executes exactly V - 1 times, and the inner loop iterates through all E edges during each pass.
Space Complexity: O(V)     -> Highly memory-efficient. It requires only a flat array of size V to track the compiled shortest path costs.
```

---

# Dry Run

## Input

```text
V = 3, source = 0, edges = [[0, 1, 2], [1, 2, 4], [0, 2, 8]]
```

---

### Execution Trace

```text
Initial State: dist = [0, INF, INF]
V = 3, so relaxation cycles run exactly V - 1 = 2 times.

Iteration 1:
- Edge: dist[0] (0) + 2 < dist[1] (INF) -> dist[1] = 2
- Edge: dist[1] (2) + 4 < dist[2] (INF) -> dist[2] = 6
- Edge: dist[0] (0) + 8 < dist[2] (6)   -> 8 < 6 False, skip.
dist state after Pass 1: [0, 2, 6]

Iteration 2:
- Edge: 0 + 2 < 2 -> False.
- Edge: 2 + 4 < 6 -> False.
- Edge: 0 + 8 < 6 -> False.
dist state after Pass 2: [0, 2, 6]

V-th Pass (Negative Cycle Validation):
- No edges can be relaxed further. Complete.

Returns final shortest path metrics:.
```

---

# Visualization

## Linear Edge Relaxation Convergence

```text
         Pass 1 (Propagates layer 1)          Pass 2 (Stabilizes layer 2)
             2                     4              2                     4
        (0) ───► (1) ──────────────► (2)     (0) ───► (1) ──────────────► (2)
         │                            ▲       │                            ▲
         └────────────────────────────┘       └────────────────────────────┘
                       8                                    8
                dist[2] becomes 6                    dist matrices fully locked
```

---

# Graph Paradigm Pattern

## Identify State

```text
Single-source shortest-path matrix expansion resolved via a level-bounded iterative edge relaxation algorithm.
```

## Choices

```text
Execute exactly V - 1 relaxation sweeps across all raw edges simultaneously. Run a supplementary V-th scan pass to catch negative-weight loops.
```

---

## Key Takeaways

✅ Advanced graph-theory routing algorithm capable of resolving negative edge costs.

✅ **Dijkstra vs Bellman-Ford:** While Dijkstra's greedy priority queue setup runs faster (\(O(E \log V)\)), it will fail or spin infinitely on negative edge weights. Bellman-Ford trades a slightly slower runtime (\(O(V \times E)\)) for complete architectural safety against negative values.

✅ Perfect structural component used for **financial currency arbitrage detection engines** (multiplying exchange rates converts to adding logs, where a negative cycle flags a risk-free profit loop), network routing protocols (like RIP), and distance-vector routers.

✅ Common Graph Design Pattern:

```text
Negative Cycle Detection via Iterative Edge Relaxation Constraints
```

### Similar Problems

- Network Delay Time (Dijkstra variant - LeetCode 743)
- Cheapest Flights Within K Stops (Level-bounded Bellman-Ford - LeetCode 787)
- Path With Minimum Effort (LeetCode 1631)
- Find City With Smallest Number of Neighbors (Floyd-Warshall template - LeetCode 1334)
