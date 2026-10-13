# Detect a Cycle in a Directed Graph

## Problem Statement

Given a directed graph with `V` vertices and `E` edges, check whether it contains at least one **cycle**. 

The graph is represented using an adjacency list format, where `adj[i]` contains a list of all vertices that node `i` points to. The graph can be disconnected (i.e., contain multiple separate components).

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of vertices and E is the number of edges)*

---

## Example 1

### Input

```text
V = 4, E = 4
adj = [
  0 -> [1],
  1 -> [2],
  2 -> [3],
  3 -> [1]
]
```

### Output

```text
true
```

### Explanation

```text
The graph contains a directed cycle: 1 → 2 → 3 → 1.
Therefore, return true.
```

---

## Example 2

### Input

```text
V = 3, E = 2
adj = [
  0 -> [1],
  1 -> [2],
  2 -> []
]
```

### Output

```text
false
```

### Explanation

```text
The graph is a linear chain (0 → 1 → 2) with no directed loops.
```

---

# Intuition

Detecting a cycle in a directed graph is fundamentally different from an undirected graph. Because edges are one-way, we cannot simply rely on checking if a neighbor is visited and isn't the parent. A node could be visited multiple times from different independent branches without forming a cycle (known as a cross-edge).

A true directed cycle exists if and only if **we encounter a node that is already active on the current recursion stack**. 

To track this behavior, we maintain two separate tracking arrays during a Depth-First Search (DFS):
1.  **`visited[]`:** A global tracker across the entire graph to remember if a node has ever been fully processed. This avoids redundant searches.
2.  **`recStack[]` (Recursion Stack Tracker):** A localized path tracker. We mark a node as `true` when we enter its recursive frame, and reset it back to `false` when we backtrack out of that frame.

If we look ahead to a neighbor and find it is already marked `true` inside `recStack[]`, we have caught a back-edge loop—meaning a cycle is confirmed.

---

# State Definition

Let:

```text
boolean[] visited = new boolean[V];  // Tracks globally explored nodes across components
boolean[] recStack = new boolean[V]; // Tracks nodes actively open on the current DFS path
```

---

# Recurrence / State Transition Condition

```text
For each neighbor of current node:
    If neighbor is active in recStack:
        Cycle detected! (Return true)
    If neighbor is NOT visited:
        Recurse on dfs(neighbor)
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By checking nodes through an adjacency list sweep and maintaining the dual `visited` and `recStack` bitmask arrays, each vertex and directional edge connection is processed exactly once. This tracks down any hidden loops inside optimal **O(V + E)** time boundaries.

---

## Java Code

```java
import java.util.ArrayList;

class Solution {

    public boolean isCyclic(int V, ArrayList<ArrayList<Integer>> adj) {
        boolean[] visited = new boolean[V];
        boolean[] recStack = new boolean[V];

        // Step 1: Loop through all vertices to handle disconnected graph components safely
        for (int i = 0; i < V; i++) {
            if (!visited[i]) {
                if (dfs(i, adj, visited, recStack)) {
                    return true;
                }
            }
        }

        return false;
    }

    private boolean dfs(int curr, ArrayList<ArrayList<Integer>> adj, boolean[] visited, boolean[] recStack) {
        // Mark the current node as globally visited and actively open on our call path
        visited[curr] = true;
        recStack[curr] = true;

        // Step 2: Traverse all outgoing directed edge neighbors
        for (int neighbor : adj.get(curr)) {
            
            // Case A: Neighbor is already open on the current path stack -> Cycle found!
            if (recStack[neighbor]) {
                return true;
            }
            
            // Case B: Neighbor has not been explored yet -> Recurse deeply
            if (!visited[neighbor]) {
                if (dfs(neighbor, adj, visited, recStack)) {
                    return true;
                }
            }
        }

        // Backtrack: Remove the current node from the active path tracker before returning up
        recStack[curr] = false;
        return false;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> The algorithm inspects every vertex and traverses all directed outgoing adjacency pointers exactly once.
Space Complexity: O(V)     -> Storage allocated for the visited and recStack helper arrays, plus recursive call stack frames.
```

---

# Dry Run

## Input

```text
V = 3, adj = [0 -> [1], 1 -> [2], 2 -> [0]] (A directed ring: 0 → 1 → 2 → 0)
```

---

### DFS Execution Trace

```text
1. i = 0 -> unvisited. Call dfs(0):
   - visited[0] = true, recStack[0] = true
   - Neighbor 1 is unvisited -> Call dfs(1):
     - visited[1] = true, recStack[1] = true
     - Neighbor 2 is unvisited -> Call dfs(2):
       - visited[2] = true, recStack[2] = true
       - Neighbor 0 check: recStack[0] is TRUE! -> Loop detected!
       - Returns true up the call chain immediately.
       
The back-edge loop directly back to node 0 is trapped securely.
```

---

# Visualization

## Cross Edge vs Directed Back-Edge Cycle

```text
    Cross-Edge (Safe)                  Directed Cycle (Loop Found)
      (0) ──► (1)                         (0) ──► (1)
       │       ▲                           ▲       │
       ▼       │                           │       ▼
      (2) ─────┘                          (2) ◄────┘
   recStack is clean                  recStack[0] is true when 
   when evaluated from 2              evaluated from 2 -> Trap!
```

---

# Graph Paradigm Pattern

## Identify State

```text
Directed graph path validation utilizing localized path-bitmask stack arrays during deep recursive sweeps.
```

---

## Choices

```text
Advance down unvisited branches. Push nodes onto the path map. Pop elements off the stack on backtracking. Flag overlapping hits.
```

---

## Key Takeaways

✅ Core fundamental directed graph validation framework.

✅ The presence of a directed loop cycle can also be analyzed using **Kahn's Algorithm (Topological Sort)**—if processing dependencies leaves un-evicted nodes stranded in memory with an in-degree > 0, a cycle is caught.

✅ Standard real-world model used inside **Compiler Design packages** to intercept circular inheritance loops, deadlock trackers inside operating systems, or parsing module dependency structures.

✅ Common Graph Design Pattern:

```text
Directed Cycle Detection via Path Stack Tracking (Back-Edge Interception)
```

### Similar Problems

- Course Schedule (LeetCode 207)
- Course Schedule II (LeetCode 210)
- Find Eventual Safe States (LeetCode 802)
- All Paths from Source Lead to Destination (LeetCode 1059)
