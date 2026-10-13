# Course Schedule II

## Problem Statement

There are a total of `numCourses` courses you have to take, labeled from `0` to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [ai, bi]` indicates that you **must** take course `bi` first if you want to take course `ai`.
- For example, the pair `[0, 1]` indicates that to take course `0` you have to first take course `1`.

Return *the ordering of courses you should take to finish all courses*. If there are many valid answers, return **any** of them. If it is impossible to finish all courses, return **an empty array**.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V is the number of courses and E is the number of prerequisite relations)*

---

## Example 1

### Input

```text
numCourses = 2
prerequisites = [[1,0]]
```

### Output

```text
[0,1]
```

### Explanation

```text
There are a total of 2 courses to take. To take course 1 you should have finished course 0. 
So the correct course order is.
```

---

## Example 2

### Input

```text
numCourses = 4
prerequisites = [[1,0],[2,0],[3,1],[3,2]]
```

### Output

```text
[0,1,2,3]
```

### Explanation

```text
There are a total of 4 courses to take. To take course 3 you should have finished both courses 1 and 2.
Both courses 1 and 2 should be taken after you finished course 0.
So one correct course order is. Another valid ordering is.
```

---

## Example 3

### Input

```text
numCourses = 1
prerequisites = []
```

### Output

```text
[0]
```

---

# Intuition

This problem is a direct extension of *Course Schedule I*. Instead of just checking if a valid schedule exists, we need to **reconstruct and output the actual chronological sequence of courses**.

This is equivalent to finding a valid **Topological Sort** of a Directed Acyclic Graph (DAG). We can implement this optimally using **Kahn's Algorithm (BFS-based In-Degree Pruning)**:
1. **In-Degree Setup:** Calculate how many incoming dependencies (`inDegree`) each course has. An edge points from `prereq → course`.
2. **Seed Zero Dependencies:** Push all courses with an initial `inDegree == 0` into a FIFO queue. These courses have no prerequisites and can be taken immediately.
3. **Sequential Processing:** While the queue has elements, pop a course `curr`. Add it to our `order` array tracking list. For each course `neighbor` that depends on `curr`:
   - Decrement its dependency counter: `inDegree[neighbor]--`.
   - If its counter hits `0`, all its prerequisites have been fulfilled. Push it onto the queue.
4. **Deadlock Verification:** If we finish processing and our tracking array contains exactly `numCourses` elements, return the array. If it contains fewer, the graph has a cyclic dependency deadlock—return an empty array.

---

# State Definition

Let:

```text
int[] inDegree = new int[numCourses];       // Tracks incoming prerequisite edges per node
Queue<Integer> queue = new LinkedList<>(); // Buffer for courses containing 0 remaining constraints
int[] order = new int[numCourses];         // Array capturing the final sorted sequence
int index = 0;                             // Running marker index for the output array layout
```

---

# Recurrence / Pointer Transition Condition

```text
For each course node 'neighbor' depending on 'curr' popped from the queue:
    inDegree[neighbor]--
    If inDegree[neighbor] == 0:
        queue.offer(neighbor)
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By setting up an adjacency list map representation and executing a single sequential FIFO queue sweep, each course vertex and dependency edge is processed exactly once. This resolves and records the course scheduling sequences in optimal **O(V + E)** time.

---

## Java Code (Kahn's Algorithm)

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

class Solution {

    public int[] findOrder(int numCourses, int[][] prerequisites) {
        // Step 1: Initialize the directed adjacency list graph structure
        List<Integer>[] adj = new ArrayList[numCourses];
        for (int i = 0; i < numCourses; i++) {
            adj[i] = new ArrayList<>();
        }

        int[] inDegree = new int[numCourses];

        // Step 2: Build the graph links and compute incoming in-degrees
        for (int[] edge : prerequisites) {
            int course = edge[0];
            int prereq = edge[1];
            adj[prereq].add(course); // Directed edge points from prereq -> course
            inDegree[course]++;
        }

        // Step 3: Seed our BFS queue with all courses that have zero dependencies
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0) {
                queue.offer(i);
            }
        }

        int[] order = new int[numCourses];
        int index = 0;

        // Step 4: Iteratively prune dependencies and record the sequence
        while (!queue.isEmpty()) {
            int curr = queue.poll();
            order[index++] = curr; // Collect the course into our schedule timeline

            // Process all courses that depend on the completed course
            for (int neighbor : adj[curr]) {
                inDegree[neighbor]--;
                
                // If all prerequisite constraints are resolved, unlock the course
                if (inDegree[neighbor] == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        // Step 5: If index equals numCourses, we found a valid path. Else, return empty array.
        return (index == numCourses) ? order : new int[0];
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> Where V is numCourses and E is the size of prerequisites. Graph building takes O(V + E) time, and the queue processing steps visit each vertex and edge exactly once.
Space Complexity: O(V + E) -> Storage required for the adjacency list layout, the in-degree tracker, and the BFS queue container.
```

---

# Dry Run

## Input

```text
numCourses = 3, prerequisites = [[1, 0], [2, 1]]
Edges: 0 → 1, 1 → 2
```

---

### Execution Trace

```text
1. Compute In-Degrees: inDegree = [0, 1, 1]
2. Seed Queue: Course 0 has inDegree == 0 -> queue = [0]
3. Loop 1:
   - Pop 0 -> order[0] = 0. index = 1.
   - Neighbor 1: inDegree drops from 1 to 0 -> queue = [1]
4. Loop 2:
   - Pop 1 -> order[1] = 1. index = 2.
   - Neighbor 2: inDegree drops from 1 to 0 -> queue = [2]
5. Loop 3:
   - Pop 2 -> order[2] = 2. index = 3.
   - No neighbors. Queue empty.

Check: index (3) == numCourses (3) -> Returns.
```

---

# Visualization

## Linear Schedule Compilation Track

```text
  [In: 0]           [In: 1]           [In: 1]
    (0) ─────────────► (1) ───────────► (2)
     │                  │
  Recorded           Recorded          Recorded
  order[0]=0         order[1]=1        order[2]=2
```

---

# Graph Paradigm Pattern

## Identify State

```text
Topological array sequence compilation utilizing dependency checking across a linear BFS queue pipeline.
```

---

## Choices

```text
Peel off completed prerequisite bounds. Append unlocked vertex references to the final timeline array as soon as their dependencies clear.
```

---

## Key Takeaways

✅ Standard directed graph topological sequence reconstruction layout.

✅ Kahn's algorithm dynamically tracks and records nodes in precise topological priority order, while simultaneously verifying the structural integrity of the DAG.

✅ Mimics low-level compile and execution optimization chains such as **instruction pipelining**, scheduling database transaction scripts, or computing cell update cascades in spreadsheet models.

✅ Common Graph Design Pattern:

```text
Chronological Sequence Compilation via Indegree Dependency Pruning
```

### Similar Problems

- Course Schedule (LeetCode 207)
- Alien Dictionary (LeetCode 269)
- Sequence Reconstruction (LeetCode 444)
- Minimum Height Trees (LeetCode 310)
