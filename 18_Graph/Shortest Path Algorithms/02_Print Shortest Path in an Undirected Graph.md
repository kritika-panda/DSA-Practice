# Print Shortest Path in an Undirected Graph

## Problem Statement

Given an undirected graph with `V` vertices (labeled from `0` to `V-1`) and `E` unweighted edges (or edges with a uniform **unit weight of 1**), find and **print the exact path sequence** representing the shortest route from a given **source vertex** to a **destination vertex**. 

If there are multiple shortest paths, return any one of them. If the destination is completely unreachable from the source, return an empty list or `[-1]`.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of vertices and E is the number of undirected edges)*

---

## Example

### Input

```text
V = 4
adj = [
  0 -> [1, 2],
  1 -> [0, 3],
  2 -> [0, 3],
  3 -> [1, 2]
]
source = 0
destination = 3
```

### Output

```text
[0, 1, 3]
```

### Explanation

```text
There are two shortest paths of length 2 from 0 to 3:
1. 0 → 1 → 3
2. 0 → 2 → 3

Both are valid, so returning either [0, 1, 3] or [0, 2, 3] satisfies the constraint.
```

---

## Intuition

When a graph has a uniform edge weight of 1, finding the single-source shortest path is best handled by a standard **Breadth-First Search (BFS)** traversal. Because BFS expands layer by layer, the first time it reaches any node, it does so using the absolute minimum number of steps.

However, to **print the actual path sequence** instead of just returning a distance number, we need a way to trace our steps backward. We can achieve this by maintaining a **`parentOf[]` tracking array**:
1. Create a `parentOf` array of size `V` where `parentOf[v]` stores the index of the node that directly discovered `v`. Initialize all positions to `-1`.
2. Run a standard queue-driven BFS starting from the `source`.
3. When exploring an unvisited neighbor, instead of just marking it visited, we record its parent relationship: `parentOf[neighbor] = curr`.
4. **Path Reconstruction:** Once the BFS loop terminates or hits the `destination`, we check if the destination was reached. If `parentOf[destination] == -1`, it is unreachable.
5. If reachable, we start at `destination` and continuously hop backward to its recorded parent (`curr = parentOf[curr]`) until we land back at the `source`. Reverse this collected list to get the correct forward path sequence.

---

## State Definition

Let:

```text
int[] parentOf = new int[V];               // Tracks the parent node that discovered each vertex
Queue<Integer> queue = new LinkedList<>(); // FIFO queue driving the level-order wave
List<Integer> path = new ArrayList<>();    // List to reconstruct the final route sequence
```

---

## Recurrence / Pointer Transition Condition

```text
For each unvisited neighbor of curr popped from the queue:
    parentOf[neighbor] = curr
    queue.offer(neighbor)
```

---

## Optimal Structural Graph Shortest-Path Solution

### Idea

By utilizing a parent lookup array during a level-order BFS, relationship chains are recorded in constant time. Tracing the parent array backward from destination to source takes time proportional to the path length (at most \(V\) steps). This compiles the entire routing sequence cleanly inside **O(V + E)** boundaries.

---

### Java Code (BFS Path Reconstruction)

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

class Solution {

    public List<Integer> printShortestPath(int V, List<List<Integer>> adj, int source, int destination) {
        // Step 1: Pre-allocate and initialize the parent state tracking matrix
        int[] parentOf = new int[V];
        Arrays.fill(parentOf, -1);
        
        // Self-reference marker for the source to anchor the backward trace loop
        parentOf[source] = source; 

        // Step 2: Seed the FIFO queue with our source element
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(source);

        // Step 3: Run the level-order BFS propagation wave
        while (!queue.isEmpty()) {
            int curr = queue.poll();

            // Early exit optimization: stop searching if we pop the destination
            if (curr == destination) {
                break;
            }

            for (int neighbor : adj.get(curr)) {
                // If neighbor has no registered parent, it is unvisited
                if (parentOf[neighbor] == -1) {
                    parentOf[neighbor] = curr; // Map parent relationship link
                    queue.offer(neighbor);
                }
            }
        }

        List<Integer> shortestPath = new ArrayList<>();

        // Step 4: Check if a valid path structure was resolved
        if (parentOf[destination] == -1) {
            shortestPath.add(-1); // Destination is unreachable
            return shortestPath;
        }

        // Step 5: Trace backwards from destination to source using the parent roadmap
        int currNode = destination;
        while (currNode != source) {
            shortestPath.add(currNode);
            currNode = parentOf[currNode];
        }
        shortestPath.add(source); // Append the root source node anchor

        // Step 6: Invert the list to match chronological forward path order
        Collections.reverse(shortestPath);

        return shortestPath;
    }
}
```

---

### Complexity Analysis

```text
Time Complexity : O(V + E) -> The BFS visits each vertex and edge at most once, taking O(V + E) time. Reconstructing the path by tracking parent links backwards takes O(V) steps in the worst-case linear graph.
Space Complexity: O(V)     -> Storage allocated for the parentOf array, the BFS queue layout, and the temporary path collection frames.
```

---

## Dry Run

### Input

```text
V = 4, source = 0, destination = 3
Edges: 0 — 1, 1 — 3, 0 — 2
```

---

### Execution Trace

```text
1. Initialize Parent Array: parentOf = [-1, -1, -1, -1] -> set source parentOf[0] = 0
2. Queue state: queue = [0]
3. Pop 0: 
   - Neighbor 1 unvisited -> parentOf[1] = 0, queue = [1]
   - Neighbor 2 unvisited -> parentOf[2] = 0, queue = [1, 2]
4. Pop 1:
   - Neighbor 3 unvisited -> parentOf[3] = 1, queue = [2, 3]
5. Pop 2: Neighbors already visited -> Skip.
6. Pop 3: Matches destination! Loop breaks early.

Path Reconstruction:
- Start at 3 -> path = [3]. parentOf[3] = 1
- Move to 1 -> path = [3, 1]. parentOf[1] = 0
- Move to 0 -> loops ends since currNode == source (0). path = [3, 1, 0]

Reverse List: [0, 1, 3]. Output maps perfectly.
```

---

## Visualization

### Parent Back-Pointer Roadmaps

```text
  Forward Discovery Hops:            Backward Reconstructive Trace:
     (0) ──────► (1) ──────► (3)        (0) ◄────── (1) ◄────── (3)
   Source                 Dest        Source                  Start
   
  Reversing the backward collection sequence reconstructs the true forward path timeline.
```

---

## Graph Paradigm Pattern

### Identify State

```text
Path-reconstruction mapping using back-pointer backtracking indices collected during a level-order BFS pass.
```

### Choices

```text
Log structural discovery parents in-place. Skip node updates once a parent is stamped. Reverse trace vectors from terminal to source target anchors.
```

---

## Key Takeaways

✅ Core fundamental graph engineering pattern for path-reconstruction challenges.

✅ Storing **parent node relationships** (`parentOf[child] = parent`) instead of absolute path arrays saves extensive heap allocations, keeping intermediate data steps highly optimal.

✅ Universally used across systems like **GPS route mapping engines** to print turn-by-turn instructions, automated network diagnostics tools, and game-engine pathfinding AI.

✅ Common Graph Design Pattern:

```text
Shortest Path Reconstruction via Back-Pointer Parent Tracking
```

### Similar Problems

- Shortest Path in Undirected Graph with Unit Weights (LeetCode 1D-variant)
- Word Ladder II (Print all shortest path sequences - LeetCode 126)
- Shortest Path in Binary Matrix (LeetCode 1091)
- Cheapest Flights Within K Stops (LeetCode 787)
