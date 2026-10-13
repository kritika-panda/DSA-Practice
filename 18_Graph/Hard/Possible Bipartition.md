# Possible Bipartition

## Problem Statement

We want to split a group of `n` people (labeled from `1` to `n`) into two groups of **any size**. Each person may dislike some other people, and they should not go into the same group.

Given the integer `n` and the 2D integer array `dislikes` where `dislikes[i] = [ai, bi]` indicates that the person `ai` does not like the person `bi`, return `true` *if and only if it is possible to split everyone into two groups in this way*.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of people n, and E is the number of dislike pairs)*

---

## Example 1

### Input

```text
n = 4
dislikes = [,,]
```

### Output

```text
true
```

### Explanation

```text
We can partition the people into two mutually exclusive groups:
Group 1: {1, 4}
Group 2: {2, 3}

No two people within the same group dislike each other, so this split is valid.
```

---

## Example 2

### Input

```text
n = 3
dislikes = [,,]
```

### Output

```text
false
```

### Explanation

```text
The dislikes form a triangle dependency: 1 dislikes 2, 2 dislikes 3, and 3 dislikes 1.
It is mathematically impossible to split these 3 people into 2 groups without a conflict.
```

---

# Intuition

This problem can be mapped directly to finding if an undirected graph is **Bipartite (2-Colorable)**. 
*   Each person represents a **Vertex / Node**.
*   Each dislike pair `[a, b]` represents an **Undirected Edge** connecting them, indicating they cannot share the same group assignment.

An undirected graph is bipartite if and only if we can paint its vertices using two alternating colors (e.g., `1` and `-1`) such that any two connected nodes have completely opposite colors. If we attempt to color the graph and find an adjacent pair sharing the same color, it means the graph contains an odd-length cycle, breaking the bipartition requirement.

We can solve this efficiently using **Breadth-First Search (BFS)** graph coloring:
1.  **Graph Construction:** Build an adjacency list representing the bidirectional dislike graph. Since inputs are 1-indexed, we shift them to 0-indexed values safely.
2.  **Color Tracker Allocation:** Initialize an integer array `color` of size `n` with `0`, representing that all vertices start uncolored.
3.  **Disconnected Components Loop:** Sweep through all vertices from `0` to `n - 1`. If an uncolored node is found, initialize its color to `1`, push it onto a BFS queue, and begin the wave propagation.
4.  **Alternating Propagation:** Pop a node `curr`. For each neighbor:
    *   **If uncolored (`0`):** Paint it with the exact opposite color (`-color[curr]`) and offer it to the queue.
    *   **If already colored:** Verify that its color does *not* match `color[curr]`. If they are equal, a conflict has occurred—return `false`.
5.  If the entire graph is colored cleanly without collisions, return `true`.

---

# State Definition

Let:

```text
int[] color = new int[n];                  // State array: 0 = Uncolored, 1 = Group A, -1 = Group B
Queue<Integer> queue = new LinkedList<>(); // BFS queue driving the propagation wave
```

---

# Recurrence / Expansion Condition

```text
For each neighbor of curr popped from the queue:
    If color[neighbor] == 0:
        color[neighbor] = -color[curr]
        queue.offer(neighbor)
    Else if color[neighbor] == color[curr]:
        Collision detected! (Return false)
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By wrapping the data inside an adjacency list format and utilizing a queue-driven BFS, each node and edge is evaluated a constant number of times. This resolves the bipartite split checks inside an optimized **O(V + E)** timeline.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

class Solution {

    public boolean possibleBipartition(int n, int[][] dislikes) {
        // Step 1: Initialize the undirected adjacency list graph structure
        List<Integer>[] adj = new ArrayList[n];
        for (int i = 0; i < n; i++) {
            adj[i] = new ArrayList<>();
        }

        // Convert 1-indexed inputs to 0-indexed layout natively
        for (int[] edge : dislikes) {
            int u = edge[0] - 1;
            int v = edge[1] - 1;
            adj[u].add(v);
            adj[v].add(u);
        }

        // 0 = Uncolored, 1 = Group A, -1 = Group B
        int[] color = new int[n];

        // Step 2: Traverse all nodes to handle potential disconnected components safely
        for (int i = 0; i < n; i++) {
            if (color[i] == 0) {
                Queue<Integer> queue = new LinkedList<>();
                queue.offer(i);
                color[i] = 1; // Seed initial node color

                // Step 3: Propagate alternating colors across the component
                while (!queue.isEmpty()) {
                    int curr = queue.poll();

                    for (int neighbor : adj[curr]) {
                        // Case A: Neighbor has not been colored yet -> Paint with opposite color
                        if (color[neighbor] == 0) {
                            color[neighbor] = -color[curr];
                            queue.offer(neighbor);
                        } 
                        // Case B: Neighbor shares the same color as the current node -> Conflict!
                        else if (color[neighbor] == color[curr]) {
                            return false;
                        }
                    }
                }
            }
        }

        return true;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> Where V is n and E is the size of dislikes. Graph initialization takes O(V + E), and the BFS processes each node and edge exactly once.
Space Complexity: O(V + E) -> Space allocated to house the adjacency list maps, color state tracker array, and the BFS queue container.
```

---

# Dry Run

## Input

```text
n = 4, dislikes = [,,]
Edges: 0 — 1, 1 — 2, 2 — 3
```

---

### BFS Execution Trace

```text
1. i = 0 -> uncolored. queue = [0], color[0] = 1
2. Pop 0: neighbor 1 is uncolored -> color[1] = -1. queue = [1]
3. Pop 1: neighbors are 0 and 2.
   - color[0] (1) != color[1] (-1) -> Valid.
   - neighbor 2 is uncolored -> color[2] = 1. queue = [2]
4. Pop 2: neighbors are 1 and 3.
   - color[1] (-1) != color[2] (1) -> Valid.
   - neighbor 3 is uncolored -> color[3] = -1. queue = [3]
5. Pop 3: checks clear. Queue empties cleanly.

Final colors: color = [1, -1, 1, -1] -> Returns true.
```

---

# Visualization

## Bipartite Color Alternation Layout

```text
   (1) [Color: 1] ───────────────► (2) [Color: -1]
                                      │
                                      ▼
   (4) [Color: -1] ◄────────────── (3) [Color: 1]
   
   Vertices are split successfully into two non-colliding coloring boundaries.
```

---

# Graph Paradigm Pattern

## Identify State

```text
Undirected graph conflict validation solved via chromatic 2-color BFS alternation sweeps.
```

---

## Choices

```text
Paint unvisited neighbors with opposite color values. Catch identical color overlaps to intercept odd cycles.
```

---

## Key Takeaways

✅ Core fundamental graph-theory bipartition puzzle.

✅ The problem is identical to **Is Graph Bipartite? (LeetCode 785)**, wrapped inside a real-world word narrative. Any graph can be split into two independent sets if and only if it contains zero odd-length cycles.

✅ Models real-world system engines like **bipartite assignment matching** (e.g., pairing drivers with passengers, linking tasks to processors), checking compatibility networks, or managing scheduling conflicts.

✅ Common Graph Design Pattern:

```text
Chromatic 2-Color Graph Network Validation via BFS Alternation
```

### Similar Problems

- Is Graph Bipartite? (LeetCode 785)
- Flower Planting With No Adjacent (LeetCode 1042)
- Shortest Path to Get All Keys (LeetCode 864)
- Redundant Connection (LeetCode 684)
