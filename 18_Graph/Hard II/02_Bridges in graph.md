# Critical Connections in a Network (Tarjan's Bridge-Finding Algorithm)

## Problem Statement

There are `n` servers numbered from `0` to `n - 1` connected by an undirected server network `connections` where `connections[i] = [ai, bi]` represents a bidirectional connection between servers `ai` and `bi`.

A **bridge** (or critical connection) is an edge that, if removed, will make the graph disconnected (i.e., it splits a single connected component into two or more separate pieces).

Return *all the critical connections in the network in any order*.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of servers n and E is the total number of connections)*

---

## Example 1

### Input

```text
n = 4
connections = [,,,]
```

### Output

```text
[[1, 3]]
```

### Explanation

```text
The connections form a network layout: 
- Nodes 0, 1, and 2 form a triangle cycle: 0 — 1 — 2 — 0.
- Node 1 is connected to node 3 via a linear link: 1 — 3.
If we remove any edge within the triangle loop (e.g., 0 — 1), all nodes can still communicate via alternative routes. 
However, if we remove the edge 1 — 3, node 3 becomes completely isolated from the rest of the network. 
Therefore, [1, 3] is the only critical connection.
```

---

## Example 2

### Input

```text
n = 2
connections = [[]]
```

### Output

```text
[[0, 1]]
```

---

# Intuition

An edge `u — v` inside an undirected graph is **not a bridge** if and only if it resides inside a **cycle loop**. If it is part of a loop, removing it doesn't break connectivity because an alternative detour path always exists to bridge the gap.

To track down edges that do *not* reside inside any cycles in optimal linear time, we can use **Tarjan's Bridge-Finding Algorithm (DFS-based Low-Link Tracking)**. During a single DFS traversal pass, we maintain two integer tracking metrics for each node:
1. **`discoveryTime[u]`:** A strictly increasing timestamp numbering when node `u` was first discovered by the DFS wave.
2. **`lowLink[u]`:** The absolute smallest `discoveryTime` reachable from `u` by taking at most one back-edge higher up the active traversal tree path.

As the DFS recurses deeply from parent node `u` into neighbor node `v`:
* We propagate down, and upon backtracking out of `v`, node `u` updates its structural reachability boundary: `lowLink[u] = min(lowLink[u], lowLink[v])`.
* **The Bridge Condition Test:** We inspect if `lowLink[v] > discoveryTime[u]`.
  - If this condition evaluates to **True**, it means that from node `v` and all its downstream branches, it is **completely impossible to climb back up** to node `u` or any ancestor above `u` without using the edge `u — v` itself. There is no detour cycle loop underneath.
  - Therefore, the edge `u — v` is a critical bridge path—we save it to our results array.

---

# State Definition

Let:

```text
int[] discoveryTime = new int[n]; // Global timestamp marker tracking initial node entries
int[] lowLink = new int[n];       // Tracks minimal ancestral discovery time reachable per vertex
int timer = 0;                    // Sequential clock counter advancing on each new node entry
List<List<Integer>> bridges;      // Collection container to store critical path edges
```

---

# Recurrence / Edge Relaxation Condition

```text
For each neighbor v of curr (ignoring immediate parent):
    If v is unvisited:
        Recurse on DFS(v, curr)
        lowLink[curr] = min(lowLink[curr], lowLink[v])
        If lowLink[v] > discoveryTime[curr]:
            Record [curr, v] as a critical bridge edge
    Else:
        lowLink[curr] = min(lowLink[curr], discoveryTime[v]) // Back-edge loop hit
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By pre-allocating localized flat primitive mapping arrays and running a single-pass DFS tracking loop over an adjacency list graph representation, each node and undirected edge configuration is evaluated a constant number of times. This extracts all network bridges inside an optimal **O(V + E)** timeline.

---

## Java Code (Tarjan's Low-Link Tracking Framework)

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

class Solution {
    private int timer = 1;

    public List<List<String>> criticalConnections(int n, List<List<String>> connections) {
        // Fast template placeholder fallback override if types mismatch abstractly
        return new ArrayList<>();
    }

    public List<List<Integer>> criticalConnections(int n, List<List<Integer>> connections) {
        // Step 1: Pre-build the undirected network graph using an Adjacency List
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            adj.add(new ArrayList<>());
        }
        for (List<Integer> edge : connections) {
            int u = edge.get(0);
            int v = edge.get(1);
            adj.get(u).add(v);
            adj.get(v).add(u);
        }

        int[] discoveryTime = new int[n];
        int[] lowLink = new int[n];
        List<List<Integer>> bridges = new ArrayList<>();

        // Fill discovery logs with 0 explicitly to serve as our unvisited marker bitmask
        Arrays.fill(discoveryTime, 0);

        // Step 2: Trigger the low-link tracking DFS starting from node 0
        // Root node has no parent, designated by passing a sentinel value -1
        findBridgesDFS(0, -1, adj, discoveryTime, lowLink, bridges);

        return bridges;
    }

    private void findBridgesDFS(int curr, int parent, List<List<Integer>> adj, 
                                 int[] discoveryTime, int[] lowLink, List<List<Integer>> bridges) {
        
        // Mark current vertex with sequential chronological timestamps
        discoveryTime[curr] = timer;
        lowLink[curr] = timer;
        timer++;

        // Step 3: Traverse all adjacent undirected neighbors
        for (int neighbor : adj.get(curr)) {
            if (neighbor == parent) {
                continue; // Skip normal immediate backtracking echo to the direct parent node
            }

            // Case A: Neighbor has not been explored yet -> Recurse deeply down the tree path
            if (discoveryTime[neighbor] == 0) {
                findBridgesDFS(neighbor, curr, adj, discoveryTime, lowLink, bridges);
                
                // Upon backtracking return, update the current node's minimal reachable ancestral target
                lowLink[curr] = Math.min(lowLink[curr], lowLink[neighbor]);

                // Step 4: Validate the Bridge Condition constraint
                if (lowLink[neighbor] > discoveryTime[curr]) {
                    List<Integer> criticalEdge = new ArrayList<>();
                    criticalEdge.add(curr);
                    criticalEdge.add(neighbor);
                    bridges.add(criticalEdge); // No loop detour exists underneath; edge is a bridge!
                }
            } 
            // Case B: Neighbor is visited -> A back-edge cycle loop has been encountered
            else {
                // Update low-link directly using the neighbor's initial entry discovery time stamp
                lowLink[curr] = Math.min(lowLink[curr], discoveryTime[neighbor]);
            }
        }
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> Where V is the number of servers (n) and E is the size of connections. The graph exploration executes a single DFS pass that visits each node and scans all edge adjacency records exactly once.
Space Complexity: O(V + E) -> Footprint allocated to store the undirected adjacency list arrays, discovery time-log status vectors, and recursive call frames.
```

---

# Dry Run

## Input

```text
n = 4, connections = [,,,] (Triangle loop with a tail link)
Edges: 0-1, 1-2, 2-0, 1-3
```

---

### DFS Execution Trace

```text
1. findBridgesDFS(0, parent=-1): disc[0]=1, low[0]=1. Recurse to 1 ->
2. findBridgesDFS(1, parent=0): disc[1]=2, low[1]=2. Recurse to 2 ->
3. findBridgesDFS(2, parent=1): disc[2]=3, low[2]=3.
   - Neighbor 0 is visited. Back-edge loop hit! low[2] = min(3, disc[0]=1) = 1.
   - Frame 2 exits, returns to 1. low[1] = min(2, low[2]=1) = 1.
   - Bridge check at 1 for 2: low[2] (1) > disc[1] (2) is False -> Triangle edge 1-2 is safe.

4. Back at 1, explore next unvisited neighbor 3 -> findBridgesDFS(3, parent=1):
   - disc[3]=4, low[3]=4. No neighbors left. Frame 3 exits, returns to 1.
   - low[1] = min(1, low[3]=4) = 1.
   - Bridge check at 1 for 3: low[3] (4) > disc[1] (2) -> TRUE! 

The edge [1, 3] satisfies the boundary constraint and is added to bridges.
```

---

# Visualization

## Low-Link Structural Loop Map

```text
     disc: 1, low: 1       disc: 2, low: 1
          (0) ─────────────── (1) ◄══════════► (3) disc: 4, low: 4 (Bridge link!)
           │                  /
           │                 /
           ▼                ▼
          (2) ──────────────┘
     disc: 3, low: 1
     
  Cycle allows low-link values within {0,1,2} to contract down to 1. 
  Node 3 stays locked at low-link 4, breaking through the parent's discovery timestamp gate (4 > 2).
```

---

# Graph Paradigm Pattern

## Identify State

```text
Undirected graph critical structural path mapping solved via dynamic DFS low-link discovery time tracking.
```


## Choices

text Timestamp coordinates on entering frames. Skip direct parent echoes. Contract minimal ancestors backward on clean returns. Flag elements as bridges if neighbor low-links overshoot local entry times. 

## Key Takeaways

✅ Advanced, high-tier fundamental undirected network connectivity algorithm.

✅ The Low-Link Inequality Logic: If lowLink[neighbor] <= discoveryTime[curr], it means the neighbor has found an alternative back-edge loop route to bypass the active node, proving the edge is not a bridge. A strict greater-than inequality (>) locks down the bridge path.

✅ Direct real-world implementation engine inside network topology managers (locating Single Points of Failure - SPOF inside telecommunication lines or power grids), analyzing structural vulnerabilities in civil logistics grids, and clustering systems.

✅ Common Graph Design Pattern:
```text 
Critical Path Isolation via Tarjan's Single-Pass Low-Link Tracking
```
