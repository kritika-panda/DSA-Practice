# Detect a Cycle in an Undirected Graph

## Problem Statement

Given an undirected graph with `V` vertices and `E` edges, check whether it contains at least one **cycle**. 

The graph is represented using an adjacency list format, where `adj[i]` contains a list of all adjacent vertices for vertex `i`. The graph can be disconnected (i.e., contain multiple separate components).

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of vertices and E is the number of edges)*

---

## Example 1

### Input

```text
V = 5, E = 5
adj = [,
 ,
 ,
 ,
  [1, 3]
]
```

### Output

```text
true
```

### Explanation

```text
The graph contains a cycle: 1 → 2 → 3 → 4 → 1.
Therefore, return true.
```

---

## Example 2

### Input

```text
V = 4, E = 3
adj = [,
 ,
 ,
  [2]
]
```

### Output

```text
false
```

### Explanation

```text
The graph is a linear tree structure (0 — 1 — 2 — 3) and contains no cycles.
```

---

# Intuition

We can detect a cycle in an undirected graph by performing either a **Depth-First Search (DFS)** or a **Breadth-First Search (BFS)** traversal while tracking the path's history. 

In an undirected graph, an edge connects two nodes bidirectionally. When we travel from node `A` to node `B`, node `B` will naturally list node `A` as its neighbor. This back-reference is normal and does *not* indicate a cycle.

A true cycle is detected if and only if:
*   We visit an adjacent neighbor that has **already been marked as visited**.
*   That visited neighbor is **not the immediate parent node** that we just came from.

To manage this safely across graphs that contain multiple disconnected components, we loop through all vertices from `0` to `V - 1`. If a node is unvisited, we launch our traversal tracking the active `node` alongside its `parent` coordinate.

---

# State Definition

Let:

```text
boolean[] visited = new boolean[V]; // Array tracking visited state flags across vertices
```

---

# Recurrence / State Transition Condition

```text
For each neighbor of current node:
    If neighbor is NOT visited:
        Recurse on dfs(neighbor, current_node)
    Else if neighbor != parent:
        Cycle detected! (Return true)
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By processing the nodes through an adjacency list sweep and passing the immediate `parent` variable along our call stack frames, each vertex and edge is evaluated a constant number of times. This tracks down any hidden loops inside optimal **O(V + E)** time boundaries.

---

## Java Code (DFS Approach)

```java
import java.util.ArrayList;

class Solution {

    public boolean isCycle(int V, ArrayList<ArrayList<Integer>> adj) {
        boolean[] visited = new boolean[V];

        // Step 1: Loop through all vertices to handle disconnected graph components safely
        for (int i = 0; i < V; i++) {
            if (!visited[i]) {
                // Initial root node has no parent, designated by passing a sentinel value -1
                if (dfs(i, -1, adj, visited)) {
                    return true;
                }
            }
        }

        return false;
    }

    private boolean dfs(int curr, int parent, ArrayList<ArrayList<Integer>> adj, boolean[] visited) {
        // Mark the current vertex as visited
        visited[curr] = true;

        // Step 2: Traverse all adjacent neighbors of the current vertex
        for (int neighbor : adj.get(curr)) {
            
            // Case A: Neighbor has not been explored yet -> Recurse deeply
            if (!visited[neighbor]) {
                if (dfs(neighbor, curr, adj, visited)) {
                    return true;
                }
            } 
            // Case B: Neighbor is visited and is NOT the immediate parent -> Cycle found!
            else if (neighbor != parent) {
                return true;
            }
        }

        return false;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> The algorithm inspects every single vertex and traverses all edge adjacency references exactly once.
Space Complexity: O(V)     -> Incurred by the boolean visited tracking array and the recursive call stack depth in the worst-case skewed graph scenario.
```

---

# Dry Run

## Input

```text
V = 3, E = 3
adj = [[1, 2], [0, 2], [0, 1]] (A triangle loop: 0 — 1 — 2 — 0)
```

---

### DFS Execution Trace

```text
1. i = 0 -> unvisited. Call dfs(curr=0, parent=-1):
   - visited[0] = true
   - Neighbor 1: unvisited -> Call dfs(curr=1, parent=0):
     - visited[1] = true
     - Neighbor 0: visited, but neighbor (0) == parent (0) -> Skip.
     - Neighbor 2: unvisited -> Call dfs(curr=2, parent=1):
       - visited[2] = true
       - Neighbor 0: visited! Check: neighbor (0) != parent (1) -> TRUE!
       - Cycle Detected! Returns true.
       
The routine catches the cycle and propagates the true flag out instantly.
```

---

# Visualization

## Undirected Parent vs Cycle Back-edge

```text
     (0)  ◄──── Parent tracking filters this normal back-reference out
     / \
    ▼   ▼
   (1)─►(2) ─── Re-encountering an already visited node that is 
                NOT the parent confirms a structural loop cycle!
```

---

# Graph Paradigm Pattern

## Identify State

```text
Undirected graph verification utilizing parent-pointer boundary checks during DFS tracking sweeps.
```

---

## Choices

```text
Advance down unvisited paths. Filter parent echoes out. Flag any other visited neighbor overlaps as cycle hits.
```

---

## Key Takeaways

✅ Baseline fundamental graph-theory cycle isolation framework.

✅ Passing a trailing parent index register allows the engine to distinguish trivial bidirectional backtracking edges from genuine structural loops.

✅ Serves as the core diagnostic platform utilized inside automated system networks to validate network topologies, verify if a graph reduces down to a **Valid Tree**, or map loop bounds.

✅ Common Graph Design Pattern:

```text
Undirected Component Loop Isolation via Parent Tracker Anchoring
```

### Similar Problems

- Course Summary / Cycle Detection in Directed Graph (LeetCode 207)
- Graph Valid Tree (LeetCode 261)
- Redundant Connection (LeetCode 684)
- Number of Operations to Make Network Connected (LeetCode 1319)
