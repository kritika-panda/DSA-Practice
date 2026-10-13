# Articulation Points in a Graph (Tarjan's Algorithm)

## Problem Statement

Given an undirected connected graph with `V` vertices and `E` edges, find all the **Articulation Points** (also known as **Cut Vertices**) in the graph. 

An **Articulation Point** is a vertex whose removal (along with all edges connected to it) increases the total number of connected components in the graph. In simpler terms, removing an articulation point will split a single connected graph into two or more disconnected pieces.

The graph is represented using an adjacency list format, where `adj[i]` contains a list of all adjacent vertices for vertex `i`. The final list of articulation points should be returned in sorted ascending order.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of vertices and E is the number of undirected edges)*

---

## Example 1

### Input

```text
V = 5, E = 5
adj = [
  0 ->,
  1 ->,
  2 ->,
  3 ->,
  4 -> [3]
]
```

### Output

```text
[0, 3]
```

### Explanation

```text
The graph consists of a triangle cycle (0 — 1 — 2 — 0) and a linear tail (0 — 3 — 4).
- If we remove node 0, node 3 and node 4 are cut off from nodes 1 and 2. Graph disconnects.
- If we remove node 3, node 4 becomes completely isolated. Graph disconnects.
- Removing any other node (1, 2, or 4) leaves the remaining nodes fully connected.
Therefore, the articulation points are 0 and 3.
```

---

# Intuition

An articulation point represents a crucial bottleneck vertex in an undirected graph. To track down these critical cut vertices in optimal linear time, we can adapt **Tarjan's Low-Link Tracking Algorithm**. During a single DFS traversal pass, we maintain two tracking arrays:
1. **`discoveryTime[u]`:** A strictly increasing timestamp numbering when node `u` was first discovered by the DFS wave.
2. **`lowLink[u]`:** The absolute smallest `discoveryTime` reachable from `u` by taking at most one back-edge higher up the active tree path.

As the DFS recurses deeply from parent node `u` into neighbor node `v`, we look back upon backtracking:
*   **The Non-Root Vertex Test:** If a node `u` is *not* the root of the DFS tree, it is an articulation point if it has a child `v` such that:
    ```text
    lowLink[v] >= discoveryTime[u]
    ```
    This means that from child node `v` and all its downstream branches, it is **completely impossible to climb higher up** than node `u`. If node `u` is removed, child `v` is trapped with no path to reach the ancestors of `u`.
*   **The Root Vertex Test:** If a node `u` *is* the starting root of our DFS tree, the low-link condition doesn't apply because it has no ancestors. Instead, the root is an articulation point if and only if it has **two or more independent children branches** in the DFS tree. Removing the root will separate these branches from each other.

---

# State Definition

Let:

```text
int[] discoveryTime = new int[V]; // Global timestamp marker tracking initial node entries
int[] lowLink = new int[V];       // Tracks minimal ancestral discovery time reachable per vertex
boolean[] isArticulation = new boolean[V]; // Avoids logging the same bottleneck cell multiple times
int timer = 1;                    // Sequential clock counter advancing on each entry step
```

---

# Recurrence / Edge Relaxation Condition

```text
For each neighbor v of curr (ignoring immediate parent):
    If v is unvisited:
        Recurse on DFS(v, curr)
        lowLink[curr] = min(lowLink[curr], lowLink[v])
        If parent != -1 AND lowLink[v] >= discoveryTime[curr]:
            isArticulation[curr] = true
        childCount++
    Else:
        lowLink[curr] = min(lowLink[curr], discoveryTime[v]) // Back-edge loop hit

If parent == -1 AND childCount > 1:
    isArticulation[curr] = true // Root vertex validation rule
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By utilizing specialized flat state tracking matrices during a level-order DFS traversal, relationship hierarchies are established up front. An embedded boolean flag array prevents duplicate results when a node anchors multiple bottleneck children, resolving cut vertices in clean **O(V + E)** time.

---

## Java Code (Tarjan's Cut-Vertex Tracking)

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

class Solution {
    private int timer = 1;

    public ArrayList<Integer> articulationPoints(int V, ArrayList<ArrayList<Integer>> adj) {
        int[] discoveryTime = new int[V];
        int[] lowLink = new int[V];
        boolean[] isArticulation = new boolean[V];

        // Fill discovery logs with 0 explicitly to serve as our unvisited marker bitmask
        Arrays.fill(discoveryTime, 0);

        // Loop through all nodes to handle potential disconnected components safely
        for (int i = 0; i < V; i++) {
            if (discoveryTime[i] == 0) {
                // Initial root node has no parent, designated by passing a sentinel value -1
                findCutVerticesDFS(i, -1, adj, discoveryTime, lowLink, isArticulation);
            }
        }

        ArrayList<Integer> result = new ArrayList<>();
        
        // Collect and sort all true articulation point markers sequentially
        for (int i = 0; i < V; i++) {
            if (isArticulation[i]) {
                result.add(i);
            }
        }

        // Return [-1] if no articulation points exist in the entire graph
        if (result.isEmpty()) {
            result.add(-1);
        }

        return result;
    }

    private void findCutVerticesDFS(int curr, int parent, ArrayList<ArrayList<Integer>> adj, 
                                    int[] discoveryTime, int[] lowLink, boolean[] isArticulation) {
        
        discoveryTime[curr] = timer;
        lowLink[curr] = timer;
        timer++;

        int children = 0; // Tracks independent DFS tree branches sprouting from current node

        for (int neighbor : adj.get(curr)) {
            if (neighbor == parent) {
                continue; // Skip immediate backtracking echo to the direct parent node
            }

            // Case A: Neighbor has not been explored yet -> Recurse deeply down the tree path
            if (discoveryTime[neighbor] == 0) {
                children++;
                findCutVerticesDFS(neighbor, curr, adj, discoveryTime, lowLink, isArticulation);
                
                // Upon backtracking return, update current node's minimal reachable ancestor
                lowLink[curr] = Math.min(lowLink[curr], lowLink[neighbor]);

                // Condition 1: Non-root vertex check (Greater-than-or-equal-to condition)
                if (parent != -1 && lowLink[neighbor] >= discoveryTime[curr]) {
                    isArticulation[curr] = true;
                }
            } 
            // Case B: Neighbor is visited -> A back-edge cycle loop has been encountered
            else {
                lowLink[curr] = Math.min(lowLink[curr], discoveryTime[neighbor]);
            }
        }

        // Condition 2: Root vertex check (Must have more than 1 independent branch)
        if (parent == -1 && children > 1) {
            isArticulation[curr] = true;
        }
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> Where V is the number of vertices and E is the number of undirected edges. Tarjan's algorithm runs a single DFS traversal pass that inspects each vertex and edge a constant number of times.
Space Complexity: O(V)     -> Memory allocated to maintain the tracking arrays (discoveryTime, lowLink, isArticulation) and the maximum recursion stack depth frames.
```

---

# Dry Run

## Input

```text
V = 4, adj = [0->[1], 1->[0,2], 2->[1,3], 3->[2]] (A simple linear chain: 0 — 1 — 2 — 3)
```

---

### DFS Execution Trace

```text
1. findCutVerticesDFS(0, parent=-1): Root node. disc=1, low=1. Recurse to 1 -> children = 1.
2. findCutVerticesDFS(1, parent=0): disc=2, low=2. Recurse to 2 -> children = 1.
3. findCutVerticesDFS(2, parent=1): disc=3, low=3. Recurse to 3 -> children = 1.
4. findCutVerticesDFS(3, parent=2): Terminal tail node. disc=4, low=4. Frame 3 exits, returns to 2.

Backtracking Restorations:
- At node 2: low = min(3, lowLink[3]=4) = 3. Check: lowLink[3] (4) >= disc[2] (3) -> TRUE!
  isArticulation[2] becomes true.
- At node 1: low = min(2, lowLink[2]=3) = 2. Check: lowLink[2] (3) >= disc[1] (2) -> TRUE!
  isArticulation[1] becomes true.
- At node 0 (Root): low = min(1, lowLink[1]=2) = 1. Check Root rule: children (1) > 1 -> False.
  isArticulation[0] stays false.

Sorted result list gathered:.
```

---

# Visualization

## Low-Link Constraint Boundary Map

```text
    Root Node (children=1)           Cut Vertex Block               Cut Vertex Block
     (0) ───────────────────────► (1) ═════════════════════════► (2) ───────────────────────► (3)
   disc: 1, low: 1              disc: 2, low: 2               disc: 3, low: 3              disc: 4, low: 4
                                 lowLink[2] >= disc[1]         lowLink[3] >= disc[2]
                                      (3 >= 2)                      (4 >= 3)
                                  
  Since no back-edges exist to close a loop, children can never reduce their low-links below 
  their parent's discovery timestamp, flagging parents 1 and 2 as crucial articulation points.
```

---

# Graph Paradigm Pattern

## Identify State

```text 
Undirected graph critical structural vertex mapping solved via dynamic DFS low-link discovery time tracking.
```
