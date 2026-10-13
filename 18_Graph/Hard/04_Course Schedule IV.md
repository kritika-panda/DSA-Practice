# Course Schedule IV

## Problem Statement

There are a total of `numCourses` courses you have to take, labeled from `0` to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [ai, bi]` indicates that you **must** take course `ai` before course `bi`.

Prerequisite relationships are **transitive**. If course `a` is a prerequisite of course `b`, and course `b` is a prerequisite of course `c`, then course `a` is a prerequisite of course `c`.

You are also given an array `queries` where `queries[j] = [uj, vj]`. For the `j`-th query, answer whether course `uj` is a prerequisite of course `vj`.

Return *a boolean array `answer`, where `answer[j]` is the answer to the `j`-th query*.

The overall runtime complexity must be bounded by:

```text
O(V³ + Q)
```

*(where V is numCourses and Q is the number of queries)*

---

## Example 1

### Input

```text
numCourses = 2
prerequisites = [[1,0]]
queries = [[0,1],[1,0]]
```

### Output

```text
[false, true]
```

### Explanation

```text
Course 1 is a prerequisite of course 0. 
Query 1: Is 0 a prerequisite of 1? -> false.
Query 2: Is 1 a prerequisite of 0? -> true.
```

---

## Example 2

### Input

```text
numCourses = 3
prerequisites = [[1,2],[1,0],[2,0]]
queries = [[1,0],[1,2]]
```

### Output

```text
[true, true]
```

---

# Intuition

This problem can be structurally rephrased as a graph-theory reachability task: **Is there a directed path from node `u` to node `v`?**
*   Each course represents a **Vertex / Node**.
*   Each prerequisite relationship `[a, b]` represents a **Directed Edge** from `a → b`.
*   The transitive requirement means we need to compute the **Transitive Closure** of the graph.

Because `numCourses` is relatively small (V ≤ 100) and we need to answer multiple arbitrary reachability queries, the **Floyd-Warshall Algorithm** is a perfect design choice. 

Floyd-Warshall is a classic dynamic programming algorithm typically used to find all-pairs shortest paths, but by swapping the numeric distance summation with a simple **logical OR** operator, it can find all-pairs reachability instead. 

We maintain a 2D boolean grid `isPrereq[i][j]`. We iterate through every possible intermediate vertex `k`. For every pair of source `i` and destination `j`, if `i` can reach `k` and `k` can reach `j`, then `i` can definitely reach `j`. Once this 2D reachability matrix is precomputed, we can answer each query in instant O(1) constant time.

---

# State Definition

Let:

```text
boolean[][] isPrereq = new boolean[numCourses][numCourses]; // Grid tracking reachability matrices
```

---

# Recurrence / State Transition Condition

```text
For each intermediate node k, source node i, and destination node j:
    isPrereq[i][j] = isPrereq[i][j] || (isPrereq[i][k] && isPrereq[k][j])
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By precomputing the transitive closure using a nested three-tier matrix loop, the full reachability map is built up front. This allows subsequent batch query lookups to resolve instantly in O(1) time, keeping the query processing phase strictly optimal at **O(V³ + Q)**.

---

## Java Code (Floyd-Warshall Transitive Closure)

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    public List<Boolean> checkIfPrerequisite(int numCourses, int[][] prerequisites, int[][] queries) {
        // Step 1: Pre-allocate the 2D boolean reachability matrix table
        boolean[][] isPrereq = new boolean[numCourses][numCourses];

        // Step 2: Record all direct prerequisite edge paths
        for (int[] edge : prerequisites) {
            int u = edge[0];
            int v = edge[1];
            isPrereq[u][v] = true; // u is a direct prerequisite of v
        }

        // Step 3: Compute the Transitive Closure using the Floyd-Warshall DP paradigm
        // The outer loop MUST track the intermediate bridge vertex 'k'
        for (int k = 0; k < numCourses; k++) {
            for (int i = 0; i < numCourses; i++) {
                for (int j = 0; j < numCourses; j++) {
                    // If i can reach k AND k can reach j, then i can reach j
                    if (isPrereq[i][k] && isPrereq[k][j]) {
                        isPrereq[i][j] = true;
                    }
                }
            }
        }

        List<Boolean> result = new ArrayList<>();

        // Step 4: Answer each incoming query in instant O(1) constant time
        for (int[] query : queries) {
            int u = query[0];
            int v = query[1];
            result.add(isPrereq[u][v]);
        }

        return result;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V³ + Q) -> Where V is numCourses and Q is the size of queries. Precomputing the table requires three nested loops up to V, running in O(V³) time. Answering the Q queries takes O(1) per lookup, adding O(Q).
Space Complexity: O(V²)     -> Storage footprint utilized for the 2D boolean reachability tracking grid.
```

---

# Dry Run

## Input

```text
numCourses = 3, prerequisites = [[1,2],[2,0]], query = [[1,0]]
Direct links: 1 → 2, 2 → 0
```

---

### Floyd-Warshall Execution Trace

```text
Initial Matrix: isPrereq[1][2] = true, isPrereq[2][0] = true

When the intermediate loop k hits vertex 2:
- i = 1, j = 0 loop evaluation:
  Check: isPrereq[1][2] (true) && isPrereq[2][0] (true) -> TRUE!
  Matrix updates: isPrereq[1][0] becomes true.

Query [1,0] reads isPrereq[1][0] directly -> returns true.
```

---

# Visualization

## Transitive Dependency Chain Bridge

```text
    Direct                Direct
     Edge                  Edge
  (1) ───► (2)         (2) ───► (0)
   │                             ▲
   └─────────────────────────────┘
          Transitive Bridge Path
       Discovered via intermediate node (2)
```

---

# Graph Paradigm Pattern

## Identify State

```text
All-pairs reachability path tracking compiled using a boolean transitive closure matrix.
```

---

## Choices

```text
Evaluate each node k as a potential path bridge. Merge independent dependency links forward across the grid in-place.
```

---

## Key Takeaways

✅ Advanced variation of graph dependency validation built around multi-source queries.

✅ Converting the numerical minimum addition logic of the traditional Floyd-Warshall algorithm into a bitwise/logical AND-OR network provides a robust framework for transitive connectivity maps.

✅ Standard real-world model used inside **Access Control Lists (ACL)** to determine hierarchical permissions, security group authorization chains, or type-inheritance tree tracking in modern object-oriented runtime engines.

✅ Common Graph Design Pattern:

```text
All-Pairs Reachability via Floyd-Warshall Matrix Closure
```

### Similar Problems

- Course Schedule (LeetCode 207)
- Course Schedule II (LeetCode 210)
- Network Delay Time (LeetCode 743)
- Find City With Smallest Number of Neighbors (LeetCode 1334)
