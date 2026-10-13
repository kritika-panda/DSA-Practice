# Number of Ways to Arrive at Destination

## Problem Statement

You are in a city that consists of `n` intersections numbered from `0` to `n - 1` with **bidirectional roads** between some intersections. The inputs are given as a 2D integer array `roads` where `roads[i] = [ui, vi, timei]` indicates that there is a road between intersections `ui` and `vi` that takes `timei` minutes to travel.

You want to know in how many ways you can travel from intersection `0` to intersection `n - 1` in the **absolute shortest amount of time**.

Return *the **number of ways** you can arrive at your destination in the shortest amount of time*. Since the answer may be huge, return it **modulo 10⁹ + 7**.

The overall runtime complexity must be strictly bounded by:

```text
O(E * log(V))
```

*(where V is the number of intersections and E is the number of roads)*

---

## Example 1

### Input

```text
n = 7
roads = [[0,6,7],[0,1,2],[1,2,3],[1,3,3],[6,3,3],[3,5,1],[6,5,1],[2,5,1],[0,4,5],[4,6,2]]
```

### Output

```text
4
```

### Explanation

```text
The absolute shortest time to go from intersection 0 to intersection 6 is 7 minutes.
The 4 paths that take exactly 7 minutes are:
1. 0 → 6 (time = 7)
2. 0 → 4 → 6 (time = 5 + 2 = 7)
3. 0 → 1 → 2 → 5 → 6 (time = 2 + 3 + 1 + 1 = 7)
4. 0 → 1 → 3 → 5 → 6 (time = 2 + 3 + 1 + 1 = 7)
```

---

## Example 2

### Input

```text
n = 2
roads = [[1,0,10]]
```

### Output

```text
1
```

---

# Intuition

This problem merges two vital algorithmic concepts: **Shortest Path Generation (Dijkstra's Algorithm)** and **Combinatorial Path Counting (Dynamic Programming)**.

When exploring a graph with positive variable edge weights, **Dijkstra's Algorithm** with a Min-Heap (PriorityQueue) helps find the shortest distances greedily. To count the total *unique ways* to hit that exact minimum distance without extra structural scans, we introduce a tracking array `ways[]`:
1. **State Matrix Allocation:** Create a long array `dist[]` initialized to infinity (`Long.MAX_VALUE`), setting `dist[0] = 0`. Create an integer array `ways[]` initialized to `0`, setting `ways[0] = 1`.
2. **Greedy Traversal (Min-Heap):** Insert the pair `(0, dist=0)` into the priority queue. While the heap has elements, pop the entry `(curr, currentDist)`.
3. **The Layer Check Optimization:** If `currentDist > dist[curr]`, a better path to `curr` was already finalized. Skip processing to block redundant evaluations.
4. **Relaxation and Counting Combinations:** For each adjacent neighbor `v` with a travel edge cost `w`:
   * **Case A (Shorter Path Found):** If `currentDist + w < dist[v]`, we have found a strictly better shortcut route to node `v`. We update the distance `dist[v] = currentDist + w`, reset the neighbor's paths to inherit its parent's options directly (`ways[v] = ways[curr]`), and offer `(v, dist[v])` to our Min-Heap.
   * **Case B (Alternative Shortest Path Match):** If `currentDist + w == dist[v]`, we have reached node `v` via an alternative path that ties its existing shortest time metric. Instead of pushing it to the heap again, we add the current node's options to the neighbor's accumulated ways sum:
     ```text
     ways[v] = (ways[v] + ways[curr]) % MOD
     ```

---

# State Definition

Let:

```text
long[] dist = new long[n];                                              // Maps minimal traveling time to each vertex
int[] ways = new int[n];                                                // Tracks the count of shortest paths to each vertex
PriorityQueue<long[]> minHeap = new PriorityQueue<>((a, b) -> Long.compare(a[1], b[1])); // Min-heap holding [node, distance]
```

---

# Recurrence / Edge Relaxation Condition

```text
For each neighbor v of curr with edge cost w:
    If currentDist + w < dist[v]:
        dist[v] = currentDist + w
        ways[v] = ways[curr]
        minHeap.offer([v, dist[v]])
    Else if currentDist + w == dist[v]:
        ways[v] = (ways[v] + ways[curr]) % MOD
```

---

# Optimal Structural Graph Shortest-Path Solution

## Idea

By utilizing Dijkstra's min-heap relaxation sweeps to evaluate routes greedily and implementing an in-place combination counting step, path tracking and topology mapping execute together seamlessly. This handles all shortest-path evaluations inside strict **O(E * log(V))** parameters.

---

## Java Code (Dijkstra + DP Combo)

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;

class Solution {

    // Helper data container mapping out structural edge weights
    private static class NodePair {
        int target;
        long weight;

        NodePair(int target, long weight) {
            this.target = target;
            this.weight = weight;
        }
    }

    public int countPaths(int n, int[][] roads) {
        int MOD = 1000000007;

        // Step 1: Build the undirected graph adjacency list framework
        List<List<NodePair>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            adj.add(new ArrayList<>());
        }
        for (int[] road : roads) {
            int u = road[0];
            int v = road[1];
            long time = road[2];
            adj.get(u).add(new NodePair(v, time));
            adj.get(v).add(new NodePair(u, time));
        }

        // Step 2: Pre-allocate and initialize state matrices
        long[] dist = new long[n];
        Arrays.fill(dist, Long.MAX_VALUE);
        dist[0] = 0; // Cost to source is zero

        int[] ways = new int[n];
        ways[0] = 1; // Exactly 1 baseline starting path configuration

        // Min-heap tracking: [node, current_distance] sorted in ascending order
        PriorityQueue<long[]> minHeap = new PriorityQueue<>((a, b) -> Long.compare(a[1], b[1]));
        minHeap.offer(new long[]{0, 0});

        // Step 3: Run the greedy Dijkstra exploration and counting wave
        while (!minHeap.isEmpty()) {
            long[] cell = minHeap.poll();
            int curr = (int) cell[0];
            long currentDist = cell[1];

            // Optimization Guard: Filter out stale, suboptimal heap records
            if (currentDist > dist[curr]) {
                continue;
            }

            // Relax adjacent paths
            for (NodePair neighborEdge : adj.get(curr)) {
                int v = neighborEdge.target;
                long w = neighborEdge.weight;

                // Case A: Strictly shorter path found to vertex v
                if (currentDist + w < dist[v]) {
                    dist[v] = currentDist + w;
                    ways[v] = ways[curr]; // Inherit ways directly from parent
                    minHeap.offer(new long[]{v, dist[v]});
                } 
                // Case B: Duplicate optimal path alternative matched
                else if (currentDist + w == dist[v]) {
                    ways[v] = (ways[v] + ways[curr]) % MOD; // Aggregate combinatorics sum
                }
            }
        }

        // Step 4: Extract the compiled options count for the terminal destination node
        return ways[n - 1];
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(E * log(V)) -> Where E is the number of roads and V is the number of intersections (n). Edge sorting and heap extraction dominate the runtime, requiring O(E log V) steps, while the dynamic path calculation pass takes constant O(1) time per edge.
Space Complexity: O(V + E)       -> Space allocated to house the bidirectional adjacency lists, the dist tracking array, the ways array, and priority queue buffers.
```

---

# Dry Run

## Input

```text
n = 3, roads = [[0,1,2],[1,2,2],[0,2,4]]
Paths: 0 → 1 → 2 (Cost = 2+2=4), and 0 → 2 (Cost = 4)
```

---

### Execution Trace

```text
Initial State: dist = [0, INF, INF], ways = [1, 0, 0], minHeap = [[0, 0]]

1. Pop [0, dist=0]:
   - Neighbor 1: 0 + 2 < INF -> dist[1] = 2, ways[1] = ways[0] = 1. heap = [[1, 2]]
   - Neighbor 2: 0 + 4 < INF -> dist[2] = 4, ways[2] = ways[0] = 1. heap = [[1, 2], [2, 4]]

2. Pop [1, dist=2]:
   - Neighbor 2: 2 + 2 == dist[2] (4 == 4) -> Alternative path match!
   - ways[2] = (ways[2] + ways[1]) % MOD = (1 + 1) = 2.

3. Pop [2, dist=4]: No remaining unvisited neighbors to expand. Queue empty.

Returns ways[2] = 2.
```

---

# Visualization

## Combinatorial Shortest-Path Relaxation Tree

```text
                     2                  2
               (0) ──────────────► (1) ───────────► (2) [Dst]
                │                                    ▲
                └────────────────────────────────────┘
                                   4
                                   
  Path Option 1: 0 → 1 → 2 (Total time = 4)
  Path Option 2: 0 → 2     (Total time = 4)
  Both paths match the absolute min time boundary (4), yielding exactly 2 ways.
```

---

# Graph Paradigm Pattern

## Identify State

```text
Multi-destination shortest path routing combining Dijkstra Min-Heap edge-weight relaxation with dynamic programming combinatorics multipliers.
```

---

## Choices

```text
Extract minimum vectors greedily. Cascade paths directly on strictly cheaper cuts. Sum parent options inside an in-place modulo wrapper when alternative paths match existing optimal times.
```

---

## Key Takeaways

✅ Advanced integration of Single-Source Shortest Path (SSSP) algorithms with combinatorial counting constraints.
✅ The State Inheritance Rule: Crucially, when a strictly shorter path is resolved, we must overwrite ways[v] = ways[curr] completely instead of incrementing it, because old longer historical options are now completely invalid. We only sum paths when a direct time match occurs.
✅ Direct real-world implementation engine inside high-availability network load-balancers (distributing traffic evenly across all identically performing minimal latency paths), logistics tracking models, or packet switching systems.
✅ Common Graph Design Pattern:
```text 
Shortest Path Combinatorics Counting via Synchronized Dijkstra-DP Relaxation 
```
