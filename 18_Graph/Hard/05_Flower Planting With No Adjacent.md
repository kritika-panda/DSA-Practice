# Flower Planting With No Adjacent

## Problem Statement

You have `n` gardens, labeled from `1` to `n`, and an array `paths` where `paths[i] = [xi, yi]` describes a bidirectional path between garden `xi` and garden `yi`. In each garden, you want to plant one of 4 types of flowers.

However, you must choose a flower type for each garden such that any two gardens connected by a path have **different types of flowers**.

Return *any valid array `answer` where `answer[i]` is the flower type planted in the `(i+1)`-th garden*. The flower types are denoted by `1`, `2`, `3`, or `4`. It is guaranteed an answer exists because each garden is connected to **at most 3 paths**.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of gardens and E is the number of paths)*

---

## Example 1

### Input

```text
n = 3
paths = [[1,2],[2,3],[3,1]]
```

### Output

```text
[1,2,3]
```

### Explanation

```text
Gardens 1 and 2 have different types [1, 2].
Gardens 2 and 3 have different types [2, 3].
Gardens 3 and 1 have different types [3, 1].
This configuration satisfies all path constraints.
```

---

## Example 2

### Input

```text
n = 4
paths = [[1,2],[3,4]]
```

### Output

```text
[1,2,1,2]
```

---

# Intuition

This problem can be structurally rephrased as a classic **Graph 4-Coloring** problem. 

The most critical piece of information in the problem statement is that **each garden has at most 3 paths connected to it**. Because the degree of any vertex is capped at `3`, and we have **4 available flower types (colors)**, a simple **Greedy Algorithm** is mathematically guaranteed to find a valid arrangement without any backtracking:
1. When we arrive at any garden `i`, we examine all of its adjacent neighbors that have already been assigned a flower.
2. We flag the colors of those neighbors as "unavailable" or "taken".
3. Since a garden can have at most 3 neighbors, at most 3 unique flower types can be blocked. This leaves **at least 1 type completely free** out of our 4 options.
4. We greedily pick the lowest available flower type for garden `i` and move forward. 

---

# State Definition

Let:

```text
int[] result = new int[n]; // Array mapping each 0-indexed garden to its flower type (1-4)
```

---

# Recurrence / Pointer Transition Condition

```text
For each garden i from 0 to n-1:
    Determine colors taken by neighbors of i
    Assign the first color from {1, 2, 3, 4} that is NOT taken
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By pre-building the network relations using an adjacency list and executing a direct greedy assignment loop from `0` to `n - 1`, each vertex and undirected edge is evaluated a constant number of times. This completely sidesteps backtracking recursion overhead, solving the 4-coloring constraints inside a fast, linear **O(V + E)** timeline.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    public int[] gardenNoAdj(int n, int[][] paths) {
        // Step 1: Initialize the undirected adjacency list graph structure
        List<Integer>[] adj = new ArrayList[n];
        for (int i = 0; i < n; i++) {
            adj[i] = new ArrayList<>();
        }

        // Step 2: Build the graph edges (converting 1-indexed input to 0-indexed layout)
        for (int[] path : paths) {
            int u = path[0] - 1;
            int v = path[1] - 1;
            adj[u].add(v);
            adj[v].add(u);
        }

        int[] result = new int[n];

        // Step 3: Greedily assign flower types to each garden sequentially
        for (int i = 0; i < n; i++) {
            // boolean flag array to mark colors taken by neighbors (size 5 for 1-based color options)
            boolean[] colorTaken = new boolean[5];

            // Inspect all connected neighbors
            for (int neighbor : adj[i]) {
                int neighborColor = result[neighbor];
                if (neighborColor != 0) {
                    colorTaken[neighborColor] = true; // Mark neighbor's color as blocked
                }
            }

            // Step 4: Pick the first valid flower type that is completely free
            for (int color = 1; color <= 4; color++) {
                if (!colorTaken[color]) {
                    result[i] = color;
                    break; // Assignment complete for current garden; break inner loop
                }
            }
        }

        return result;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> Where V is n and E is the size of paths. Building the adjacency list costs O(V + E). The assignment loop runs V times, and since each vertex has a maximum degree of 3, the inner neighbor checks perform a constant number of operations.
Space Complexity: O(V + E) -> Footprint required to store the adjacency list maps and the final results array layout.
```

---

# Dry Run

## Input

```text
n = 3, paths = [[1,2],[2,3],[3,1]]
Edges: 0 — 1, 1 — 2, 2 — 0
```

---

### Greedy Assignment Trace

```text
Initial: result =

1. i = 0 (Garden 1):
   - Neighbors: 1, 2. Their colors are 0, 0.
   - Available colors: 1, 2, 3, 4 -> Pick 1.
   - result =

2. i = 1 (Garden 2):
   - Neighbors: 0, 2. Neighbor 0 has color 1. Neighbor 2 has color 0.
   - colorTaken = true
   - Available colors: 2, 3, 4 -> Pick 2.
   - result =

3. i = 2 (Garden 3):
   - Neighbors: 1, 0. Neighbor 1 has color 2. Neighbor 0 has color 1.
   - colorTaken = true, colorTaken = true
   - Available colors: 3, 4 -> Pick 3.
   - result =

Returns array sequence .
```

---

# Visualization

## Greedy Color Assignment Grid

```text
     (1) [Picked: 1]
     / \
    /   \
   /     \
 (2)─────(3)
[Picked: 2] [Picked: 3]

Maximum 3 connections means a 4th color option is always free to break ties!
```

---

# Graph Paradigm Pattern

## Identify State

```text
Bounded degree graph coloring solved via a localized greedy resource assignment sweep.
```

---

## Choices

```text
Block colors chosen by adjacent neighbors. Claim the lowest remaining free index key in-place.
```

---

## Key Takeaways

✅ Standard graph-theory coloring and resource constraint optimization puzzle.

✅ While general graph coloring is an NP-Hard problem requiring heavy exponential backtracking, establishing a strict **Degree Boundary Condition (Δ ≤ 3)** guarantees that a localized linear greedy approach will always find a solution.

✅ Direct real-world implementation model used inside **cell-tower frequency assignment matrix tools** (preventing adjacent towers from broadcasting on colliding channels), map labeling design pipelines, or scheduling independent tasks.

✅ Common Graph Design Pattern:

```text
Greedy Vertex Coloring under Bounded Degree Constraints
```

### Similar Problems

- Is Graph Bipartite? (2-Coloring - LeetCode 785)
- Possible Bipartition (LeetCode 886)
- Redundant Connection (LeetCode 684)
- Clone Graph (LeetCode 133)
