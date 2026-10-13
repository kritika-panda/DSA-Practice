# Graph Traversal Techniques

Graph traversal means visiting all vertices (nodes) of a graph in a systematic manner.

Traversal is the foundation of almost every graph algorithm:

- Cycle Detection
- Connected Components
- Topological Sorting
- Shortest Path
- Minimum Spanning Tree
- Strongly Connected Components
- Network Flow

In interviews, **99% of graph problems begin with either BFS or DFS**.

---

# What is Graph Traversal?

Given a graph:

```text
        0
      /   \
     1     2
    / \   /
   3   4 5
```

Possible traversals:

### BFS

```text
0 1 2 3 4 5
```

### DFS

```text
0 1 3 4 2 5
```

Both visit all vertices but in different orders.

---

# Types of Graph Traversal

There are two fundamental traversal techniques:

## 1. Breadth First Search (BFS)

Visits vertices:

```text
Level by Level
```

---

## 2. Depth First Search (DFS)

Visits vertices:

```text
Depth by Depth
```

---

# BFS (Breadth First Search)

## Core Idea

Explore all neighbors first before moving deeper.

Think of:

```text
Ripple in water
```

When a stone falls:

```text
Center
→ First Ring
→ Second Ring
→ Third Ring
```

BFS behaves similarly.

---

# BFS Visualization

Graph:

```text
        0
      /   \
     1     2
    / \   /
   3   4 5
```

Start from:

```text
0
```

### Level 0

```text
0
```

### Level 1

```text
1 2
```

### Level 2

```text
3 4 5
```

Traversal:

```text
0 1 2 3 4 5
```

---

# Data Structure Used

```text
Queue (FIFO)
```

FIFO:

```text
First In First Out
```

Example:

```text
Insert:
1 2 3

Remove:
1 2 3
```

---

# BFS Algorithm

```text
1. Put source node into queue
2. Mark it visited
3. While queue not empty

       Remove front node

       Visit node

       Add all unvisited neighbors
```

---

# BFS Dry Run

Graph:

```text
        0
      /   \
     1     2
    / \   /
   3   4 5
```

Adjacency List:

```java
0 -> [1,2]
1 -> [0,3,4]
2 -> [0,5]
3 -> [1]
4 -> [1]
5 -> [2]
```

---

### Initial

```text
Queue = [0]
Visited = {0}
```

---

### Remove 0

Visit:

```text
0
```

Add:

```text
1,2
```

Queue:

```text
[1,2]
```

---

### Remove 1

Visit:

```text
0 1
```

Add:

```text
3,4
```

Queue:

```text
[2,3,4]
```

---

### Remove 2

Visit:

```text
0 1 2
```

Add:

```text
5
```

Queue:

```text
[3,4,5]
```

---

### Remove 3

Visit:

```text
0 1 2 3
```

Queue:

```text
[4,5]
```

---

### Remove 4

Visit:

```text
0 1 2 3 4
```

Queue:

```text
[5]
```

---

### Remove 5

Visit:

```text
0 1 2 3 4 5
```

Queue:

```text
[]
```

Done.

---

# BFS Java Implementation

```java
import java.util.*;

class Solution {

    public void bfs(int start, List<List<Integer>> graph) {

        boolean[] visited = new boolean[graph.size()];

        Queue<Integer> queue = new LinkedList<>();

        queue.offer(start);
        visited[start] = true;

        while (!queue.isEmpty()) {

            int node = queue.poll();

            System.out.print(node + " ");

            for (int neighbor : graph.get(node)) {

                if (!visited[neighbor]) {

                    visited[neighbor] = true;
                    queue.offer(neighbor);
                }
            }
        }
    }
}
```

---

# BFS Complexity

Let:

```text
V = Vertices
E = Edges
```

### Time Complexity

```text
O(V + E)
```

Reason:

Each vertex visited once.

Each edge explored once.

---

### Space Complexity

```text
O(V)
```

Because:

```text
Queue
+
Visited Array
```

---

# Applications of BFS

---

## Shortest Path in Unweighted Graph

```text
Minimum edges between nodes.
```

---

## Level Order Traversal

Tree problems.

---

## Multi Source BFS

Starting from multiple nodes.

Example:

```text
Rotten Oranges
Walls and Gates
```

---

## Bipartite Graph Check

---

## Connected Components

---

## Web Crawlers

---

## Social Network Friends Recommendation

---

# DFS (Depth First Search)

## Core Idea

Go as deep as possible before backtracking.

Think:

```text
Maze Exploration
```

Choose one path.

Keep moving.

If dead end:

```text
Go back
```

Try another path.

---

# DFS Visualization

Graph:

```text
        0
      /   \
     1     2
    / \   /
   3   4 5
```

DFS:

```text
0
↓
1
↓
3
↑
1
↓
4
↑
0
↓
2
↓
5
```

Traversal:

```text
0 1 3 4 2 5
```

---

# Data Structure Used

Either:

```text
Recursion
```

or

```text
Stack
```

---

# DFS Algorithm

```text
Visit node

Mark visited

For each neighbor

      If unvisited

            DFS(neighbor)
```

---

# DFS Recursive Dry Run

Graph:

```text
0 -> [1,2]
1 -> [3,4]
2 -> [5]
```

---

### DFS(0)

Visit:

```text
0
```

---

### DFS(1)

Visit:

```text
1
```

---

### DFS(3)

Visit:

```text
3
```

Return.

---

### DFS(4)

Visit:

```text
4
```

Return.

---

Return to:

```text
0
```

---

### DFS(2)

Visit:

```text
2
```

---

### DFS(5)

Visit:

```text
5
```

Traversal:

```text
0 1 3 4 2 5
```

---

# DFS Recursive Java Code

```java
import java.util.*;

class Solution {

    public void dfs(int node,
                    List<List<Integer>> graph,
                    boolean[] visited) {

        visited[node] = true;

        System.out.print(node + " ");

        for (int neighbor : graph.get(node)) {

            if (!visited[neighbor]) {

                dfs(neighbor, graph, visited);
            }
        }
    }
}
```

---

# DFS Iterative Java Code

Using explicit stack.

```java
import java.util.*;

class Solution {

    public void dfs(int start,
                    List<List<Integer>> graph) {

        Stack<Integer> stack = new Stack<>();

        boolean[] visited = new boolean[graph.size()];

        stack.push(start);

        while (!stack.isEmpty()) {

            int node = stack.pop();

            if (visited[node])
                continue;

            visited[node] = true;

            System.out.print(node + " ");

            for (int neighbor : graph.get(node)) {

                if (!visited[neighbor]) {

                    stack.push(neighbor);
                }
            }
        }
    }
}
```

---

# DFS Complexity

### Time Complexity

```text
O(V + E)
```

---

### Space Complexity

```text
O(V)
```

Because:

```text
Recursion Stack
or
Explicit Stack
```

---

# DFS Applications

---

## Cycle Detection

Undirected Graph

Directed Graph

---

## Topological Sorting

---

## Strongly Connected Components

Kosaraju

Tarjan

---

## Articulation Points

---

## Bridges

---

## Backtracking Problems

---

## Maze Solving

---

## Path Finding

---

# BFS vs DFS

| Feature | BFS | DFS |
|----------|------|------|
| Data Structure | Queue | Stack/Recursion |
| Traversal | Level Wise | Depth Wise |
| Shortest Path | ✅ Yes | ❌ No |
| Memory | Higher | Lower |
| Cycle Detection | Possible | Easier |
| Topological Sort | No | Yes |
| Connected Components | Yes | Yes |
| Tree Traversal | Level Order | Inorder/Preorder/Postorder |
| Path Existence | Yes | Yes |

---

# BFS Example Tree

```text
          1
        /   \
       2     3
      / \   / \
     4  5  6  7
```

Traversal:

```text
1 2 3 4 5 6 7
```

---

# DFS Example Tree

Preorder Style:

```text
1 2 4 5 3 6 7
```

---

# Traversing Disconnected Graph

Interviewers often use disconnected graphs.

Graph:

```text
0 --- 1

2 --- 3

4
```

Starting BFS from:

```text
0
```

Visits only:

```text
0 1
```

Misses:

```text
2 3 4
```

Solution:

Run BFS/DFS from every unvisited node.

---

# DFS for Disconnected Graph

```java
for (int i = 0; i < V; i++) {

    if (!visited[i]) {

        dfs(i, graph, visited);
    }
}
```

---

# BFS for Disconnected Graph

```java
for (int i = 0; i < V; i++) {

    if (!visited[i]) {

        bfs(i, graph, visited);
    }
}
}
```

---

# Connected Components Using Traversal

Graph:

```text
0 --- 1

2 --- 3

4
```

Components:

```text
{0,1}
{2,3}
{4}
```

Answer:

```text
3 Components
```

Algorithm:

```text
components = 0

for each node

    if not visited

        DFS(node)

        components++
```

---

# Traversal on Directed Graph

BFS and DFS remain same.

Difference:

Follow edge direction.

Graph:

```text
0 → 1
↓
2
```

Adjacency List:

```java
0 -> [1,2]
1 -> []
2 -> []
```

DFS:

```text
0 1 2
```

BFS:

```text
0 1 2
```

---

# Traversal Patterns Asked in FAANG

## Pattern 1

Simple BFS Traversal

Problems:

```text
BFS of Graph
Level Order Traversal
```

---

## Pattern 2

Simple DFS Traversal

Problems:

```text
DFS of Graph
Print Components
```

---

## Pattern 3

Multi Source BFS

Problems:

```text
Rotten Oranges
01 Matrix
Walls and Gates
```

---

## Pattern 4

Grid DFS

Problems:

```text
Number of Islands
Flood Fill
Max Area of Island
```

---

## Pattern 5

Cycle Detection DFS

Problems:

```text
Course Schedule
Detect Cycle
```

---

# Common Interview Mistakes

## Mistake 1

Forgetting visited array.

Results:

```text
Infinite Loop
```

---

## Mistake 2

Marking visited after popping.

Preferred:

```java
visited[neighbor] = true;

queue.offer(neighbor);
```

---

## Mistake 3

Not handling disconnected graphs.

---

## Mistake 4

Using BFS when DFS is easier.

Example:

```text
Topological Sort
Bridges
Articulation Points
```

DFS preferred.

---

# FAANG Interview Cheat Sheet

```text
BFS:
Queue
Level-wise traversal
Shortest path in unweighted graph
O(V + E)

DFS:
Recursion / Stack
Depth-wise traversal
Cycle detection
Topological sort
SCC
O(V + E)

Disconnected Graph:
Run traversal from every unvisited node

Multi Source BFS:
Start queue with multiple nodes

Grid Problems:
DFS/BFS over rows and columns

Most Graph Problems:
Adjacency List + BFS/DFS
```

# Key Interview Insight

Graph traversal is the foundation for almost every graph algorithm.

```text
Traversal
├── BFS
│   ├── Shortest Path
│   ├── Multi Source BFS
│   ├── Bipartite Graph
│   └── Level Order Processing
│
└── DFS
    ├── Connected Components
    ├── Cycle Detection
    ├── Topological Sort
    ├── SCC
    ├── Bridges
    └── Articulation Points
```

If you master BFS and DFS thoroughly, nearly 70-80% of graph interview questions become straightforward variations of these two traversals.
