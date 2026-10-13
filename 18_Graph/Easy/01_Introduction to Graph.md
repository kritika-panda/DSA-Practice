# Graphs - Introduction

## What is a Graph?

A **Graph** is a non-linear data structure consisting of:

- **Vertices (Nodes)** → Individual entities
- **Edges** → Connections between vertices

A graph is represented as:

```text
G = (V, E)

V = Set of Vertices
E = Set of Edges
```

Example:

```text
    A
   / \
  B---C
   \
    D
```

Vertices:

```text
V = {A, B, C, D}
```

Edges:

```text
E = {(A,B), (A,C), (B,C), (B,D)}
```

---

# Why Graphs?

Graphs help model relationships between objects.

Common Examples:

| Problem | Graph Representation |
|----------|--------------------|
| Facebook Friends | Users = Nodes, Friendships = Edges |
| Google Maps | Cities = Nodes, Roads = Edges |
| LinkedIn | Professionals = Nodes, Connections = Edges |
| Web Pages | Pages = Nodes, Hyperlinks = Edges |
| Computer Networks | Devices = Nodes, Connections = Edges |
| Flights | Airports = Nodes, Flights = Edges |

---

# Terminologies

## 1. Vertex (Node)

Individual element of a graph.

```text
A, B, C, D
```

are vertices.

---

## 2. Edge

Connection between two vertices.

```text
A ---- B
```

Edge:

```text
(A,B)
```

---

## 3. Adjacent Vertices

Two vertices connected by an edge.

Example:

```text
A ---- B
```

A and B are adjacent.

---

## 4. Degree of a Vertex

Number of edges connected to a vertex.

Example:

```text
    A
   / \
  B---C
```

Degree:

```text
deg(A) = 2
deg(B) = 2
deg(C) = 2
```

---

## 5. Path

Sequence of vertices connected through edges.

Example:

```text
A → B → D
```

Path Length:

```text
Number of edges traversed.
```

Example:

```text
A → B → D

Length = 2
```

---

## 6. Cycle

A path that starts and ends at the same vertex.

Example:

```text
A → B → C → A
```

Cycle exists.

---

## 7. Connected Graph

A graph where every node can be reached from every other node.

Example:

```text
A --- B --- C --- D
```

Connected.

---

## 8. Disconnected Graph

Some vertices cannot be reached.

```text
A --- B

C --- D
```

Disconnected.

---

# Types of Graphs

---

# 1. Undirected Graph

Edges have no direction.

```text
A ----- B
```

Means:

```text
A ↔ B
```

Both directions possible.

Example:

```text
A ----- B
 \     /
   C
```

---

# 2. Directed Graph (Digraph)

Edges have direction.

```text
A → B
```

Meaning:

```text
You can travel from A to B
but not necessarily B to A.
```

Example:

```text
A → B
↓
C
```

Used in:

- Instagram followers
- Task scheduling
- Dependency graphs

---

# 3. Weighted Graph

Every edge contains a weight/cost.

Example:

```text
A --5-- B
|       |
3       2
|       |
C --4-- D
```

Edge weights:

```text
(A,B) = 5
(A,C) = 3
(B,D) = 2
(C,D) = 4
```

Used in:

- Shortest path
- Network routing
- GPS systems

---

# 4. Unweighted Graph

Every edge has equal weight.

```text
A --- B
|
C
```

Each edge cost = 1.

---

# 5. Cyclic Graph

Contains at least one cycle.

```text
A
| \
|  \
C---B
```

Cycle:

```text
A → B → C → A
```

---

# 6. Acyclic Graph

Contains no cycles.

```text
A
|
B
|
C
|
D
```

No cycle.

---

# 7. Directed Acyclic Graph (DAG)

Directed graph with no cycles.

```text
A → B → D
↓
C
```

Applications:

- Build systems
- Task scheduling
- Topological sorting
- Dependency management

---

# Special Graphs

---

# Tree

A connected acyclic graph.

Properties:

```text
For n nodes:

Edges = n - 1
```

Example:

```text
      A
     / \
    B   C
   /
  D
```

---

# Complete Graph

Every vertex connected to every other vertex.

Example:

```text
A ----- B
|\     /|
| \   / |
|  \ /  |
|  / \  |
| /   \ |
|/     \|
C ----- D
```

For n vertices:

```text
Edges = n(n-1)/2
```

---

# Bipartite Graph

Vertices divided into two sets.

No edge exists between vertices of same set.

```text
Set1      Set2

A -------- X
A -------- Y
B -------- X
B -------- Y
```

Applications:

- Job assignment
- Matching problems

---

# Graph Representation

There are two popular ways.

---

# 1. Adjacency Matrix

Suppose:

```text
0 --- 1
|   /
|  /
2
```

Matrix:

```text
     0 1 2
   --------
0 | 0 1 1
1 | 1 0 1
2 | 1 1 0
```

Meaning:

```text
1 = Edge exists
0 = Edge doesn't exist
```

### Space Complexity

```text
O(V²)
```

### Advantages

- Fast edge lookup

```text
O(1)
```

### Disadvantages

- High memory usage

---

# 2. Adjacency List

Same Graph:

```text
0 --- 1
|   /
|  /
2
```

Representation:

```java
0 -> [1,2]
1 -> [0,2]
2 -> [0,1]
```

### Space Complexity

```text
O(V + E)
```

### Advantages

- Memory efficient
- Used in almost all interview problems

### Disadvantages

- Edge lookup slower than matrix

```text
O(degree)
```

---

# Graph Traversal

The two most important graph algorithms.

---

## Breadth First Search (BFS)

Traverses level by level.

Uses:

```text
Queue
```

Example:

```text
    A
   / \
  B   C
 / \
D   E
```

BFS Order:

```text
A B C D E
```

---

## Depth First Search (DFS)

Goes deep before backtracking.

Uses:

```text
Recursion
or
Stack
```

Example:

```text
    A
   / \
  B   C
 /
D
```

DFS:

```text
A B D C
```

---

# Graph Complexity Basics

Let:

```text
V = Vertices
E = Edges
```

---

## BFS Complexity

```text
Time  : O(V + E)
Space : O(V)
```

---

## DFS Complexity

```text
Time  : O(V + E)
Space : O(V)
```

---

# Important Graph Patterns for Interviews

### Traversal

- BFS
- DFS

### Connectivity

- Connected Components
- Number of Islands

### Cycle Detection

- Undirected Graph Cycle
- Directed Graph Cycle

### Shortest Path

- BFS
- Dijkstra
- Bellman Ford
- Floyd Warshall

### Minimum Spanning Tree

- Prim's Algorithm
- Kruskal's Algorithm

### Topological Sorting

- Kahn's Algorithm
- DFS Topological Sort

### Disjoint Set Union (DSU)

- Union Find
- Dynamic Connectivity

### Advanced Graphs

- Bridges
- Articulation Points
- Strongly Connected Components
- Kosaraju
- Tarjan
- Euler Path
- Euler Circuit
- Hamiltonian Path
- Network Flow

---

# Interview Roadmap for Graphs

```text
1. Graph Basics
2. Graph Representation
3. BFS
4. DFS
5. Connected Components
6. Number of Islands
7. Flood Fill
8. Cycle Detection (Undirected)
9. Cycle Detection (Directed)
10. Topological Sort
11. Kahn's Algorithm
12. Bipartite Graph
13. Shortest Path in Unweighted Graph
14. Dijkstra
15. Bellman Ford
16. Floyd Warshall
17. Minimum Spanning Tree
18. Kruskal + DSU
19. Prim's Algorithm
20. Strongly Connected Components
21. Bridges & Articulation Points
22. Advanced Graph Problems
```

---

# Cheat Sheet

```text
Graph = (Vertices + Edges)

Undirected:
A --- B

Directed:
A --> B

Weighted:
A --5--> B

Adjacency Matrix:
Space = O(V²)

Adjacency List:
Space = O(V + E)

BFS:
Queue
O(V+E)

DFS:
Recursion / Stack
O(V+E)

Tree:
Connected + Acyclic

DAG:
Directed + Acyclic

Complete Graph:
Edges = n(n−1)/2

Tree:
Edges = n−1

Most Interview Problems:
Adjacency List + BFS/DFS
```

# Key Interview Insight

Almost every graph problem can be reduced to one of these categories:

```text
1. Traversal (BFS/DFS)
2. Connectivity
3. Cycle Detection
4. Shortest Path
5. Topological Ordering
6. Minimum Spanning Tree
7. Disjoint Set Union
8. Strongly Connected Components
```

Mastering these 8 categories covers nearly all FAANG graph interview questions.
