# Path With Maximum Probability

## Problem Statement

You are given an undirected weighted graph of `n` nodes (0-indexed), represented by an edge list where `edges[i] = [a, b]` is an undirected edge connecting the nodes `a` and `b` with a probability of success of traversing that edge `succProb[i]`.

Given two nodes `start` and `end`, find the path with the **maximum probability of success** to go from `start` to `end` and return *its success probability*.

If there is no path from `start` to `end`, return **0**. Your answer will be accepted if it differs from the correct answer by at most **10⁻⁵**.

The overall runtime complexity must be strictly bounded by:

```text
O(E * log(V))
```

*(where V is the number of nodes n, and E is the size of the edges array)*

---

## Example 1

### Input

```text
n = 3
edges = [,,]
succProb = [0.5, 0.5, 0.2]
start = 0, end = 2
```

### Output

```text
0.25000
```

### Explanation

```text
There are two paths from start to end:
Path 1: 0 → 2 (Probability = 0.2)
Path 2: 0 → 1 → 2 (Probability = 0.5 × 0.5 = 0.25)
The maximum probability is 0.25.
```

---

## Example 2

### Input

```text
n = 3
edges = [,]
succProb = [0.5, 0.5]
start = 0, end = 2
```

### Output

```text
0.00000
```

### Explanation

```text
There is no path connecting node 0 and node 2, so return 0.
```

---

# Intuition

This problem can be structurally rephrased as a variation of the Single-Source Shortest Path (SSSP) problem. However, instead of **minimizing a sum of weights** (like distances or costs), we want to **maximize a product of fractions** (probabilities between `0.0` and `1.0`).

Since multiplying numbers between `0` and `1` strictly decreases the product, the greediness property of **Dijkstra's Algorithm** holds perfectly. Instead of picking the *minimum* distance using a Min-Heap, we can invert the sorting priority and use a **Max-Heap (PriorityQueue)** to always extract the path with the *maximum accumulated probability* first:

1. **Probability Table Allocation:** Create a 1D array `maxProb` of size `n` initialized to `0.0`, setting `maxProb[start] = 1.0` (as we start there with a 100% chance).
2. **Max-Heap Configuration:** Initialize a Max-Heap storing paired entries: `(node, current_probability)`. Seed it with our starting source node `(start, 1.0)`.
3. **Greedy Traversal Sweeps:** Pop the vertex with the highest accumulated probability from the heap. If we hit our target `end` node, this probability is guaranteed to be optimal—return it immediately. If its probability is strictly less than the value recorded in `maxProb[node]`, skip it.
4. **Edge Relaxation:** Loop through all adjacent neighbors. If traveling through the current node yields a strictly higher probability than the neighbor's previously recorded maximum, execute the relaxation step:
   ```text
   maxProb[neighbor] = maxProb[curr] * edgeProbability
   ```
   Push `(neighbor, maxProb[neighbor])` onto the Max-Heap.

---

# State Definition

Let:

```text
double[] maxProb = new double[n];                                         // Tracks maximum success probability to reach each vertex
PriorityQueue<double[]> maxHeap = new PriorityQueue<>((a, b) -> Double.compare(b[1], a[1])); // Sorting elements descending by probability
```

---

# Recurrence / Edge Relaxation Condition

```text
For each adjacent undirected neighbor of curr with edge probability p:
    If maxProb[curr] * p > maxProb[neighbor]:
        maxProb[neighbor] = maxProb[curr] * p
        maxHeap.offer([neighbor, maxProb[neighbor]])
```

---

# Optimal Structural Graph Shortest-Path Solution

## Idea

By pre-building the graph using an adjacency list structure and leveraging a Max-Heap to extract the maximum active probability vectors greedily, vertex and edge coordinates are calculated optimally. This tracks probability bounds within crisp **O(E * log(V))** parameters.

---

## Java Code (Modified Dijkstra Max-Product Paradigm)

```java
import java.util.ArrayList;
import java.util.List;
import java.util.PriorityQueue;

class Solution {

    // Helper tuple class to encapsulate network edge probabilities cleanly
    private static class GraphEdge {
        int target;
        double prob;

        GraphEdge(int target, double prob) {
            this.target = target;
            this.prob = prob;
        }
    }

    public double maxProbability(int n, int[][] edges, double[] succProb, int start, int end) {
        // Step 1: Pre-build the undirected graph framework using an Adjacency List
        List<List<GraphEdge>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            adj.add(new ArrayList<>());
        }
        for (int i = 0; i < edges.length; i++) {
            int u = edges[i][0];
            int v = edges[i][1];
            double p = succProb[i];
            adj.get(u).add(new GraphEdge(v, p));
            adj.get(v).add(new GraphEdge(u, p));
        }

        // Step 2: Allocate and initialize the probability tracking status array
        double[] maxProb = new double[n];
        maxProb[start] = 1.0; // Baseline source initialization (100% probability at start)

        // Max-Heap sorting elements in descending order: [node_id, current_accumulated_probability]
        PriorityQueue<double[]> maxHeap = new PriorityQueue<>((a, b) -> Double.compare(b[1], a[1]));
        maxHeap.offer(new double[]{start, 1.0});

        // Step 3: Run greedy Dijkstra path propagation loops
        while (!maxHeap.isEmpty()) {
            double[] cell = maxHeap.poll();
            int curr = (int) cell[0];
            double currentProb = cell[1];

            // Optimization Guard: If we hit our destination, return the probability instantly
            if (curr == end) {
                return currentProb;
            }

            // Filter out stale, out-of-date heap records
            if (currentProb < maxProb[curr]) {
                continue;
            }

            // Relax adjacent undirected edges
            for (GraphEdge edge : adj.get(curr)) {
                int neighbor = edge.target;
                double edgeProb = edge.prob;

                // Standard relaxation step condition check (maximizing products)
                if (maxProb[curr] * edgeProb > maxProb[neighbor]) {
                    maxProb[neighbor] = maxProb[curr] * edgeProb;
                    maxHeap.offer(new double[]{neighbor, maxProb[neighbor]});
                }
            }
        }

        // Step 4: Destination is unreachable from the start node
        return 0.0;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(E * log(V)) -> Where E is the size of edges and V is the number of nodes (n). Building the adjacency list takes linear O(E) steps. The Dijkstra max-heap operations extract and relax edges in logarithmic balancing steps, bounding the runtime tightly within O(E log V).
Space Complexity: O(V + E)       -> Space footprint allocated to store the adjacency list configurations, maxProb table tracking mask, and priority queue buffers.
```

---

# Dry Run

## Input

```text
n = 3, edges = [,,], succProb = [0.5, 0.5, 0.2], start = 0, end = 2
```

---

### Execution Trace

```text
Initial state: maxProb = [1.0, 0.0, 0.0], maxHeap = [[0, prob=1.0]]

1. Pop [0, prob=1.0]:
   - Edge 0→1 (p=0.5): 1.0 * 0.5 > 0.0 -> maxProb[1] = 0.5, maxHeap = [[1, prob=0.5]]
   - Edge 0→2 (p=0.2): 1.0 * 0.2 > 0.0 -> maxProb[2] = 0.2, maxHeap = [[1, prob=0.5], [2, prob=0.2]]

2. Pop [1, prob=0.5] (Highest probability extracted greedily):
   - Edge 1→2 (p=0.5): 0.5 * 0.5 = 0.25 > maxProb[2] (0.2) -> maxProb[2] = 0.25, maxHeap = [[2, prob=0.25], [2, prob=0.2]]

3. Pop [2, prob=0.25]: 
   - Matches destination coordinate (curr == end) -> TRUE!
   - Returns 0.25 immediately.

The routine successfully isolates the path maximizing the success product.
```

---

# Visualization

## Maximum Probability Propagation Wave

```text
                 0.5                    0.5
           (0) ───────────────► (1) ───────────────► (2) [Dst]
            │                                         ▲
            └─────────────────────────────────────────┘
                               0.2
                               
  Path Option 1: 0 → 2     (Probability = 0.2)
  Path Option 2: 0 → 1 → 2 (Probability = 0.5 × 0.5 = 0.25)  ◄── Greedily Picked!
```

---

# Graph Paradigm Pattern

## Identify State

```text
Single-source max-product path optimization resolved via a greedy Max-Heap edge-weight relaxation Dijkstra matrix model.
```

## Choices

```text
Extract wavefront elements greedily via a Max-Heap structure. Multiply path step probabilities internally. Maximize global cell limits across the active tracker matrix.
```

---

## Key Takeaways

✅ Advanced variation of shortest path graph algorithms.

