# Shortest Path in an Undirected Graph with Unit Weights

## Problem Statement

Given an undirected graph with `V` vertices (labeled from `0` to `V-1`) and `E` unweighted edges (or edges with a uniform **unit weight of 1**), find the shortest path from a given **source vertex** to all other vertices. If a vertex is unreachable from the source, its shortest distance should be marked as `-1`.

The graph is represented as an adjacency list where `adj[i]` contains a list of all adjacent vertices for vertex `i`.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of vertices and E is the number of undirected edges)*

---

## Example

### Input

```text
V = 4, source = 0
adj = [
  0 ->,
  1 ->,
  2 ->,
  3 -> [1]
]
```

### Output

```text
distance = [0, 1, 1, 2]
```

### Explanation

```text
- Distance to 0: 0 (Source)
- Distance to 1: 0 — 1 (Cost = 1)
- Distance to 2: 0 — 2 (Cost = 1)
- Distance to 3: 0 — 1 — 3 (Cost = 1 + 1 = 2)
```

---

## Intuition

When all edges in a graph have a uniform **unit weight of 1**, finding the single-source shortest path does *not* require complex shortest-path algorithms like Dijkstra's (\(O(E \log V)\)). Instead, a standard **Breadth-First Search (BFS)** traversal is mathematically guaranteed to find the shortest path in optimal linear time.

The core reason BFS works perfectly here is its **level-order radiation property**:
1. BFS explores the graph layer by layer, moving outward radially from the source.
2. The first time the BFS traversal encounters a node `v`, it is guaranteed to have arrived via the absolute shortest number of hops possible from the source.
3. **Distance Tracker Setup:** Initialize a `distance` array of size `V` filled with `-1`, setting `distance[source] = 0`.
4. **Queue Propagation Loop:** Push the source node onto a FIFO queue. While the queue has elements, pop the front node `curr`. For each unvisited neighbor (`distance[neighbor] == -1`):
   * Compute its shortest distance by adding 1 unit to the parent's cost: 
     ```text
     distance[neighbor] = distance[curr] + 1
     ```
   * Push the neighbor onto the queue to expand the next layer.

---

## State Definition

Let:

```text
int[] distance = new int[V];               // Tracks the minimal unit distance to each vertex
Queue<Integer> queue = new LinkedList<>(); // FIFO queue driving the level-order propagation wave
```

---

## Recurrence / Distance Update Condition

```text
For each neighbor of curr popped from the queue:
    If distance[neighbor] == -1:
        distance[neighbor] = distance[curr] + 1
        queue.offer(neighbor)
```

---

## Optimal Structural Graph Shortest-Path Solution

### Idea

By utilizing a standard queue-driven BFS and tracking visited states directly inside the `distance` array via `-1` placeholders, each vertex and undirected edge is processed a constant number of times. This resolves all node coordinates cleanly within an optimal **O(V + E)** timeline.

---

### Java Code (BFS Layer Relaxation)

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

class Solution {

    public int[] shortestPathUnitWeights(int V, List<List<Integer>> adj, int source) {
        // Step 1: Pre-allocate and initialize the distance table tracking bounds
        int[] distance = new int[V];
        Arrays.fill(distance, -1); // -1 serves cleanly as our unvisited marker sentinel
        
        // Baseline source initialization
        distance[source] = 0;

        // Step 2: Seed the FIFO queue with our source element
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(source);

        // Step 3: Run the level-order BFS propagation wave
        while (!queue.isEmpty()) {
            int curr = queue.poll();

            // Inspect all unvisited adjacent neighbors
            for (int neighbor : adj.get(curr)) {
                // If neighbor is unvisited, this is the absolute shortest hop path to it
                if (distance[neighbor] == -1) {
                    distance[neighbor] = distance[curr] + 1; // Increment unit weight cost
                    queue.offer(neighbor); // Push to queue to expand the next radial layer
                }
            }
        }

        return distance;
    }
}
```

---

### Complexity Analysis

```text
Time Complexity : O(V + E) -> Where V is the number of vertices and E is the number of edges. The BFS visits each node exactly once and traverses all adjacency records once.
Space Complexity: O(V)     -> Storage allocated for the distance array tracker and the maximum memory footprint of the BFS queue.
```

---

## Dry Run

### Input

```text
V = 3, source = 0, Edges: 0 — 1, 1 — 2, 0 — 2
```

---

### Execution Trace

```text
1. Initialize distances: distance = [-1, -1, -1] -> set source: [0, -1, -1]
2. Seed Queue: queue = [0]
3. Loop 1 (Pop 0):
   - Neighbor 1: unvisited -> distance = distance[0] + 1 = 1. queue = [1]
   - Neighbor 2: unvisited -> distance = distance[0] + 1 = 1. queue = [1, 2]
4. Loop 2 (Pop 1):
   - Neighbors are 0, 2. Both are already visited (distance != -1) -> Skip.
5. Loop 3 (Pop 2):
   - Neighbors are 0, 1. Both visited -> Skip. Queue empty.

Returns final shortest path metrics:.
```

---

## Visualization

### Radial BFS Propagation Layers

```text
                   Layer 0 (Dist: 0)
                          (0)
                         /   \
                        /     \
                       ▼       ▼
      Layer 1 (Dist: 1) (1) ──► (2) Layer 1 (Dist: 1)
                        │
                        ▼
      Layer 2 (Dist: 2) (3)
```

---

## Graph Paradigm Pattern

### Identify State

```text
Single-source shortest-path matrix expansion driven by a flat, unweighted level-order BFS pipeline.
```

### Choices

```text
Radiate outwards 1 hop at a time. Lock in target coordinates on the first touch. Ignore historical elements that have already been stamped with a distance.
```

---

## Key Takeaways

✅ Core fundamental graph-theory shortest path framework.

✅ Overwriting the `distance` array using `-1` placeholders acts as an elegant space optimization step that completely skips allocating an explicit boolean `visited[]` array tracker.

✅ Serves as the base model for real-world **routing tracking systems** operating across hop-count layouts (such as finding the fewest friend connections in a social network network map or finding the minimum moves in a gaming maze grid).

✅ Common Graph Design Pattern:

```text
Level-Order Node Distance Mapping via Single-Pass BFS Propagation
```

### Similar Problems

- Word Ladder (LeetCode 127)
- 01 Matrix (LeetCode 542)
- Rotting Oranges (LeetCode 994)
- Shortest Path in Binary Matrix (LeetCode 1091)
