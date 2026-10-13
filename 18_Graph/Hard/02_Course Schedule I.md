# Course Schedule

## Problem Statement

There are a total of `numCourses` courses you have to take, labeled from `0` to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [ai, bi]` indicates that you **must** take course `bi` first if you want to take course `ai`.
- For example, the pair `[0, 1]` indicates that to take course `0` you have to first take course `1`.

Return `true` if you can finish all courses. Otherwise, return `false`.

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
true
```

### Explanation

```text
There are a total of 2 courses to take. 
To take course 1 you should have finished course 0. So it is possible.
```

---

## Example 2

### Input

```text
numCourses = 2
prerequisites = [[1,0],[0,1]]
```

### Output

```text
false
```

### Explanation

```text
There are a total of 2 courses to take. 
To take course 1 you should have finished course 0, and to take course 0 you should also have finished course 1. This creates a circular dependency loop, making it impossible.
```

---

# Intuition

This problem can be structurally rephrased as a graph-theory puzzle: **Can we find a valid Topological Sort of a Directed Graph?** 
- Each course represents a **Vertex / Node**.
- Each prerequisite relationship `[a, b]` represents a **Directed Edge** stretching from `b → a`.

A valid topological ordering is mathematically achievable if and only if **the directed graph contains absolutely zero cycles (is a Directed Acyclic Graph - DAG)**. If there is a cycle (e.g., `0 → 1 → 0`), a deadlock occurs because no course in that loop can be completed first.

We can solve this problem elegantly using **Kahn's Algorithm (BFS-based In-Degree Pruning)**:
1. **In-Degree Setup:** Maintain an array `inDegree` where `inDegree[i]` stores the count of incoming prerequisite constraints pointing to course `i`.
2. **Seed Zero Dependencies:** Scan the `inDegree` array. Any course with an initial `inDegree == 0` has no prerequisites—push it straight onto a BFS queue.
3. **Prune Constraints Iteratively:** While the queue is active, pop a course `curr`. This signifies that we have successfully completed this course. Increment a global completed course counter. For each course `neighbor` that depends on `curr`:
   - Decrement its incoming dependency counter: `inDegree[neighbor]--`.
   - If its constraint count hits `0`, all its prerequisites have been fulfilled. Push it onto the queue.
4. **Final Check:** If our final completed courses counter matches `numCourses`, then a full conflict-free schedule was resolved. Return `true`. If the counter falls short, a cycle deadlocked part of the graph—return `false`.

---

# State Definition

Let:

```text
int[] inDegree = new int[numCourses];       // Tracks incoming prerequisite edges per node
Queue<Integer> queue = new LinkedList<>(); // Buffer for courses containing 0 remaining constraints
int completedCoursesCount = 0;             // Global counter of validly processed courses
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

By setting up an adjacency list map representation and executing a single sequential FIFO queue sweep, each course vertex and dependency edge is processed exactly once. This resolves the course scheduling dependencies in optimal **O(V + E)** time.

---

## Java Code (Kahn's Algorithm)

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

class Solution {

    public boolean canFinish(int numCourses, int[][] prerequisites) {
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

        int completedCoursesCount = 0;

        // Step 4: Iteratively prune dependencies using the BFS queue
        while (!queue.isEmpty()) {
            int curr = queue.poll();
            completedCoursesCount++;

            // Process all courses that depend on the completed course
            for (int neighbor : adj[curr]) {
                inDegree[neighbor]--;
                
                // If all prerequisite constraints are resolved, unlock the course
                if (inDegree[neighbor] == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        // Step 5: If we cleared all courses, no cyclic lock occurred
        return completedCoursesCount == numCourses;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> Where V is numCourses and E is the size of prerequisites. Building the adjacency list takes O(V + E) steps, and Kahn's algorithm processes each vertex and edge exactly once.
Space Complexity: O(V + E) -> Storage required for the adjacency list structure, the in-degree array, and the BFS queue container.
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
   - Pop 0 -> completedCoursesCount = 1.
   - Neighbor 1: inDegree drops from 1 to 0 -> queue = [1]
4. Loop 2:
   - Pop 1 -> completedCoursesCount = 2.
   - Neighbor 2: inDegree drops from 1 to 0 -> queue = [2]
5. Loop 3:
   - Pop 2 -> completedCoursesCount = 3.
   - No neighbors. Queue empty.

Check: completedCoursesCount (3) == numCourses (3) -> Returns true.
```

---

# Visualization

## In-Degree Pruning Track

```text
  [In: 0]           [In: 1]           [In: 1]
    (0) ─────────────► (1) ───────────► (2)
     │                  │
   Popped             Unlocked
  [In: 0]            [In: 0]
```

---

# Graph Paradigm Pattern

## Identify State

```text
Directed cycle screening via in-degree verification across a chronological linear BFS queue.
```

---

## Choices

```text
Peel off fulfilled dependency counts. Unlock succeeding vertices into the active queue stream the moment constraints reach zero.
```

---

## Key Takeaways

✅ Core fundamental directed graph scheduling optimization problem.

✅ Kahn's algorithm doubles as an elegant directed cycle detection engine—if a cycle is present anywhere in the graph, the in-degrees of the elements trapped inside that cycle will never hit 0, causing them to stay locked out of the queue.

✅ Direct real-world implementation of **Task Scheduling managers** (like package installations in `npm`/`pip`, build script dependencies in `Maven`/`CMake`, or execution pipelines in data orchestration engines like Airflow).

✅ Common Graph Design Pattern:

```text
Dependency Resolution via Monotonic In-Degree Pruning Loops
```

### Similar Problems

- Course Schedule II (LeetCode 210)
- Alien Dictionary (LeetCode 269)
- Sequence Reconstruction (LeetCode 444)
- Minimum Height Trees (LeetCode 310)
