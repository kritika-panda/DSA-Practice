# Floyd-Warshall Algorithm

## Problem Statement

Given a weighted directed graph with `V` vertices (labeled from `0` to `V-1`) and an adjacency matrix representation `matrix[][]`, find the shortest distance between **all pairs of vertices**. 

The graph is provided as a 2D square matrix of size `V x V`, where `matrix[i][j]` indicates the weight of the directed edge from vertex `i` to vertex `j`. If there is no direct edge between vertex `i` and vertex `j`, `matrix[i][j]` is marked with a sentinel value representing infinity (`-1` or `1e9`). The diagonal elements `matrix[i][i]` are always `0`.

Unlike Dijkstra's or Bellman-Ford algorithms which compute paths from a single source, this algorithm resolves paths between **every possible source and destination combination** simultaneously. It can handle negative edge weights, but the graph must not contain negative-weight cycles.

The overall runtime complexity must be strictly bounded by:

```text
O(V³)
```

---

## Example 1

### Input

```text
V = 4
matrix = [
  [0, 3, INF, 7],
  [8, 0, 2, INF],
  [INF, INF, 0, 1],
  [2, INF, INF, 0]
]
```

### Output

```text
[,
 ,
 ,
  [2, 5, 7, 0]
]
```

### Explanation

```text
- Shortest path from 0 to 2 is 0 → 1 → 2 with cost 3 + 2 = 5 (better than INF).
- Shortest path from 0 to 3 is 0 → 1 → 2 → 3 with cost 3 + 2 + 1 = 6 (better than direct 7).
- Shortest path from 1 to 0 is 1 → 2 → 3 → 0 with cost 2 + 1 + 2 = 4 (better than direct 8).
```

---

# Intuition

The **Floyd-Warshall Algorithm** is a classic **All-Pairs Shortest Path (APSP)** algorithm built around a multi-stage **Dynamic Programming** paradigm.

The core philosophy revolves around a simple question: *Can we improve the shortest distance between vertex `i` and vertex `j` by routing through an intermediate vertex `k`?*

1. **The Intermediate Wave:** We iterate an outer loop `k` from `0` up to `V-1`. In each stage `k`, we consider vertex `k` as a potential stepping stone.
2. **Matrix Relaxation:** For every pair of source vertex `i` and destination vertex `j`, we evaluate if the current known distance `matrix[i][j]` can be optimized by splitting the journey into two parts: traveling from `i` to `k`, and then from `k` to `j`.
3. **The Core Update Formula:** If both halves are reachable, we take the minimum value:
   ```text
   matrix[i][j] = min( matrix[i][j], matrix[i][k] + matrix[k][j] )
   ```
4. By processing the intermediate node `k` in the absolute outermost loop, the algorithm progressively considers larger subsets of allowed bridge vertices, ensuring that by the time `k` reaches `V-1`, all possible routing configurations have been fully explored and optimized.

---

# State Definition

Let:

```text
matrix[i][j] // In-place 2D DP state table tracking the shortest distance from node i to node j
```

---

# Recurrence / Matrix Update Condition

```text
For each intermediate vertex k from 0 to V-1:
    For each source vertex i from 0 to V-1:
        For each destination vertex j from 0 to V-1:
            If matrix[i][k] != INF and matrix[k][j] != INF:
                matrix[i][j] = min(matrix[i][j], matrix[i][k] + matrix[k][j])
```

---

# Optimal Structural All-Pairs Shortest-Path Solution

## Idea

By reusing the input adjacency matrix directly as our 2D DP state table, the algorithm eliminates the memory allocation overhead of auxiliary graph containers. The three nested loops run exactly `V` times each, bounding the entire execution strictly inside a clean, predictable **O(V³)** time envelope.

---

## Java Code

```java
class Solution {

    public void floydWarshall(int[][] matrix) {
        int v = matrix.length;
        int INF = 100000000; // Safe sentinel constant to avoid arithmetic overflow

        // Step 1: Pre-process the input matrix to replace unreachable sentinels (e.g., -1) with INF
        for (int i = 0; i < v; i++) {
            for (int j = 0; j < v; j++) {
                if (matrix[i][j] == -1) {
                    matrix[i][j] = INF;
                }
            }
        }

        // Step 2: Run the three-tier nested loops. 
        // CRUCIAL: The intermediate vertex loop 'k' MUST be the outermost loop.
        for (int k = 0; k < v; k++) {
            for (int i = 0; i < v; i++) {
                for (int j = 0; j < v; j++) {
                    
                    // If a valid bridge path exists through vertex k, attempt relaxation
                    if (matrix[i][k] != INF && matrix[k][j] != INF) {
                        if (matrix[i][k] + matrix[k][j] < matrix[i][j]) {
                            matrix[i][j] = matrix[i][k] + matrix[k][j];
                        }
                    }
                }
            }
        }

        // Step 3: Optional post-processing to map infinity values back to -1 per protocol
        for (int i = 0; i < v; i++) {
            for (int j = 0; j < v; j++) {
                if (matrix[i][j] == INF) {
                    matrix[i][j] = -1;
                }
            }
        }
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V³) -> Dictated entirely by the three nested loops running up to V. It is independent of the number of edges, making it exceptionally fast for dense graphs.
Space Complexity: O(1)   -> In-place matrix mutation. Modifying the input grid directly saves extra memory overhead completely.
```

---

# Dry Run

## Input

```text
V = 3
matrix = [
  [0, 2, INF],
  [INF, 0, 3],
  [1, INF, 0]
]
```

---

### Execution Trace

```text
Initial Matrix (with INF sentinels):
Row 0: [0, 2, INF]
Row 1: [INF, 0, 3]
Row 2: [1, INF, 0]

- When k = 0 (Can we route through 0?): No paths change because no valid connections match.
- When k = 1 (Can we route through 1?):
  - Check i=0, j=2: matrix[0][1] (2) + matrix[1][2] (3) = 5.
  - 5 < matrix[0][2] (INF) -> Relaxed! matrix[0][2] becomes 5.
- When k = 2 (Can we route through 2?):
  - Check i=1, j=0: matrix[1][2] (3) + matrix[2][0] (1) = 4.
  - 4 < matrix[1][0] (INF) -> Relaxed! matrix[1][0] becomes 4.

Final Matrix Result:
Row 0: [0, 2, 5]
Row 1: [4, 0, 3]
Row 2: [1, 3, 0]
```

---

# Visualization

## Multi-Stage Path Compaction

```text
     Initial Configuration                  Bridging Through Node (1)
        2            3                          2            3
   (0) ───► (1) ───────────► (2)           (0) ───► (1) ───────────► (2)
    ▲                         │             │                         ▲
    └─────────────────────────┘             └─────────────────────────┘
                 1                                       5
         (0 to 2 is INF)                       Shortest path (0->1->2) = 5
```

---

# Graph Paradigm Pattern

## Identify State

```text
All-pairs shortest path grid densification resolved via a three-tier localized sub-interval dynamic programming matrix sweep.
```

## Choices

```text
Evaluate each node k sequentially as a system bridge. Overwrite path weights in-place to construct the cumulative structural reachability map.
```

---

## Key Takeaways

✅ Standard core foundational graph matrix optimization template.

✅ **Dijkstra vs Floyd-Warshall:** If you need single-source paths, Dijkstra is much faster (\(O(E \log V)\)). However, if you need all-pairs paths on a dense graph, running Dijkstra from every node costs \(O(V \cdot E \log V)\), which approaches \(O(V^3 \log V)\). Floyd-Warshall delivers the complete all-pairs matrix in a cleaner, more predictable O(V³) execution loop.

✅ Direct real-world implementation engine for **transitive closure compilation** (like *Course Schedule IV*), network distance-vector calculations, managing peer-to-peer connection grids, and city-to-city transportation distance lookup systems.

✅ Common Graph Design Pattern:

```text
All-Pairs Shortest Path Matrix Densification via Tri-Nested Relaxation Loops
```

### Similar Problems

- Course Schedule IV (Reachability variation - LeetCode 1462)
- Find the City With the Smallest Number of Neighbors at a Threshold Distance (LeetCode 1334)
- Network Delay Time (Single-Source - LeetCode 743)
- Minimum Cost to Convert String I (LeetCode 2976)
