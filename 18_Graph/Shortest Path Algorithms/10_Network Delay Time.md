# Network Delay Time

## Problem Statement

You are given a network of `n` directed nodes, labeled from `1` to `n`. You are also given `times`, a list of directed travel times as edges `times[i] = [ui, vi, wi]`, where `ui` is the source node, `vi` is the target node, and `wi` is the time it takes for a signal to travel from source to target.

We will send a signal from a given node `k`. Return *the **minimum time** it takes for all the `n` nodes to receive the signal*. If it is impossible for all the `n` nodes to receive the signal, return `-1`.

The overall runtime complexity must be strictly bounded by:

```text
O(E * log(V))
```

*(where V is the number of nodes n, and E is the size of the times array)*

---

## Example 1

### Input

```text
times = [,,]
n = 4
k = 2
```

### Output

```text
2
```

### Explanation

```text
The signal is sent from node 2:
- Reaches node 1 at time = 1.
- Reaches node 3 at time = 1.
- From node 3, the signal travels to node 4, reaching it at time = 1 + 1 = 2.
The absolute minimum time for all nodes to receive the signal is 2.
```

---

## Example 2

### Input

```text
times = []
n = 2
k = 1
```

### Output

```text
1
```

---

## Example 3

### Input

```text
times = []
n = 2
k = 2
```

### Output

```text
-1
```

### Explanation

```text
Node 2 has an incoming edge from node 1, but has zero outgoing paths to reach node 1. 
Therefore, node 1 cannot receive the signal, so return -1.
```

---

# Intuition

This problem can be structurally rephrased as a classic Single-Source Shortest Path (SSSP) problem: **Find the shortest travel time from source node `k` to all other nodes.** 

The time it takes for *all* nodes to receive the signal is determined by the node that receives the signal **last**. Therefore, the total network delay is simply the **maximum value among the shortest path distances** from the source to every reachable node.

Since the travel times (edge weights) are strictly non-negative, we can solve this optimally using **Dijkstra's Algorithm** with a **Min-Heap (PriorityQueue)**:
1. **Distance Table Initialisation:** Create a 1D array `dist` of size `n + 1` initialized to infinity (`Integer.MAX_VALUE`). Set `dist[k] = 0`.
2. **Min-Heap Configuration:** Initialize a Min-Heap storing paired entries: `(node, current_distance)`. Seed it with our starting source node `(k, 0)`.
3. **Greedy Traversal Sweeps:** Pop the vertex with the lowest accumulated distance from the heap. If its distance is strictly greater than the value recorded in `dist[node]`, it means a more optimal path was already processed—skip it.
4. **Edge Relaxation:** Loop through all adjacent neighbors pointing away from the current node. If travelling through the current node offers a strictly shorter path than the neighbor's previously recorded distance, execute the relaxation step:
   ```text
   dist[neighbor] = dist[curr] + weight
   ```
   Push `(neighbor, dist[neighbor])` onto the Min-Heap.
5. **Final Network Delay Check:** After the Min-Heap empties, scan all node distances from indices `1` to `n`. Locate the maximum distance value. If any node remains at infinity, the network is broken—return `-1`. Otherwise, return the maximum time found.

---

# State Definition

Let:

```text
int[] dist = new int[n + 1];                                           // Table tracking minimal shortest times to each vertex
PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a - b); // Heap tracking [node, distance] sorted ascending
```

---

# Recurrence / Edge Relaxation Condition

```text
For each outgoing directed edge from curr to neighbor with weight w:
    If dist[curr] + w < dist[neighbor]:
        dist[neighbor] = dist[curr] + w
        minHeap.offer([neighbor, dist[neighbor]])
```

---

# Optimal Structural Graph Shortest-Path Solution

## Idea

By pre-building the directed graph using an adjacency list structure and leveraging a Min-Heap to extract the shortest active signal vectors greedily, vertex and edge coordinates are calculated optimally. This tracks network delay thresholds within crisp **O(E * log(V))** parameters.

---

## Java Code (Dijkstra SSSP Paradigm)

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;

class Solution {

    // Helper tuple class to encapsulate network edge weights cleanly
    private static class NetworkEdge {
        int target;
        int weight;

        NetworkEdge(int target, int weight) {
            this.target = target;
            this.weight = weight;
        }
    }

    public int networkDelayTime(int[][] times, int n, int k) {
        // Step 1: Pre-build the directed graph framework using an Adjacency List
        List<List<NetworkEdge>> adj = new ArrayList<>();
        for (int i = 0; i <= n; i++) {
            adj.add(new ArrayList<>());
        }
        for (int[] time : times) {
            int u = time[0];
            int v = time[1];
            int w = time[2];
            adj.get(u).add(new NetworkEdge(v, w));
        }

        // Step 2: Allocate and initialize the distance tracking status matrix bounds
        int[] dist = new int[n + 1];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[k] = 0; // Cost to source node itself is zero

        // Min-Heap sorting elements in ascending order: [node_id, current_accumulated_time]
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> Integer.compare(a[1], b[1]));
        minHeap.offer(new int[]{k, 0});

        // Step 3: Run greedy Dijkstra path propagation loops
        while (!minHeap.isEmpty()) {
            int[] cell = minHeap.poll();
            int curr = cell[0];
            int currTime = cell[1];

            // Optimization Guard: Filter out stale, out-of-date heap records
            if (currTime > dist[curr]) {
                continue;
            }

            // Relax outgoing directed edges
            for (NetworkEdge edge : adj.get(curr)) {
                int neighbor = edge.target;
                int weight = edge.weight;

                // Standard relaxation step condition check
                if (dist[curr] + weight < dist[neighbor]) {
                    dist[neighbor] = dist[curr] + weight;
                    minHeap.offer(new int[]{neighbor, dist[neighbor]});
                }
            }
        }

        int maxDelay = 0;

        // Step 4: Scan all 1-indexed node distances to track down the network threshold limit
        for (int i = 1; i <= n; i++) {
            if (dist[i] == Integer.MAX_VALUE) {
                return -1; // Node is completely unreachable; signal transmission failed
            }
            maxDelay = Math.max(maxDelay, dist[i]);
        }

        return maxDelay;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(E * log(V)) -> Where E is the size of times (edges) and V is the number of nodes (n). Building the adjacency list takes linear O(E) steps. The Dijkstra min-heap operations extract and relax edges in logarithmic balancing steps, bounding the runtime tightly within O(E log V).
Space Complexity: O(V + E)       -> Space footprint allocated to store the directed adjacency list configurations, dist table tracking mask, and priority queue buffers.
```

---

# Dry Run

## Input

```text
times = [,,], n = 4, k = 2
Edges: 2→1 (w=1), 2→3 (w=1), 3→4 (w=1)
```

---

### Execution Trace

```text
Initial state: dist = [INF, INF, 0, INF, INF], minHeap = [[2, time=0]]

1. Pop [2, time=0]:
   - Edge 2→1: 0 + 1 < INF -> dist[1] = 1, minHeap = [[1, time=1]]
   - Edge 2→3: 0 + 1 < INF -> dist[3] = 1, minHeap = [[1, time=1], [3, time=1]]

2. Pop [1, time=1]: No outgoing neighbors to expand.

3. Pop [3, time=1]:
   - Edge 3→4: 1 + 1 < INF -> dist[4] = 2, minHeap = [[4, time=2]]

4. Pop [4, time=2]: No outgoing neighbors. Heap empty.

Final dist table: dist = [INF, 1, 0, 1, 2]
Max valid distance = max(1, 0, 1, 2) = 2.

Returns 2.
```

---

# Visualization

## Signal Propagation Timeline

```text
                 1                      1
           (1) ◄─────── (2) [Source] ───────► (3)
                         (Time: 0)             │
                                               │ 1
                                               ▼
                                              (4)
                                           (Time: 2)
                                           
  Nodes 1 and 3 receive the ping at time 1. Node 4 gets the relay last at time 2.
  Total network delay threshold = 2.
```

---

# Graph Paradigm Pattern

## Identify State

```text
Single-source shortest path optimization resolved via a greedy Min-Heap edge-weight relaxation Dijkstra architecture.
```

## Choices

```text
Extract minimum vectors via a Priority Queue structure. Lower global node limits progressively across the active tracker matrix. Tally peak values from the finalized row array bounds.
```

---

## Key Takeaways

✅ Standard core foundational template for directed weighted graph shortest paths.

✅ The Propagation Limit Rules: The system delay threshold is determined by the maximum value among the shortest paths, not the summation of all paths. Nodes inside alternative parallel paths receive signals concurrently.

✅ Directly maps to real-world system frameworks like packet routing protocols (OSPF/IS-IS mapping network latencies), broadcasting message relays inside mesh networks, or calculating global currency exchange limits.

✅ Common Graph Design Pattern:
text Single-Source Shortest Path Mapping via Min-Heap Priority Queue Relaxation
