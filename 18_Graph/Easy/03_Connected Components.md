# Connected Components in Graph

## What are Connected Components?

A **Connected Component** is a group of vertices in an undirected graph where:

- Every vertex is reachable from every other vertex in the same group.
- No vertex in the group is connected to vertices of another group.

Simply put:

```text
A connected component is a maximal connected subgraph.
```

---

# Example 1

Graph:

```text
0 ----- 1

2 ----- 3

4
```

Visualization:

```text
Component 1: {0,1}

Component 2: {2,3}

Component 3: {4}
```

Number of Connected Components:

```text
3
```

---

# Example 2

Graph:

```text
      0
     / \
    1   2
   / \
  3   4
```

All vertices are reachable from one another.

Components:

```text
{0,1,2,3,4}
```

Answer:

```text
1 Connected Component
```

---

# Why Are Connected Components Important?

Many graph problems are actually asking:

```text
How many disconnected groups exist?
```

Applications:

- Social Networks
- Computer Networks
- Road Networks
- Island Counting
- Friend Circles
- Cluster Detection
- Network Reliability

---

# Key Observation

If graph is disconnected:

```text
BFS/DFS from one node
cannot visit every node
```

Example:

```text
0 ----- 1

2 ----- 3
```

Running DFS from:

```text
0
```

Visits:

```text
0,1
```

Misses:

```text
2,3
```

Therefore:

```text
Run DFS/BFS from every unvisited node.
```

---

# Core Algorithm

```text
components = 0

for every node:

      if node not visited:

            DFS(node)

            components++
```

Or:

```text
components = 0

for every node:

      if node not visited:

            BFS(node)

            components++
```

---

# Intuition

Suppose:

```text
0 ----- 1

2 ----- 3

4
```

Initially:

```text
visited = {}
components = 0
```

---

## Start DFS(0)

Visit:

```text
0 → 1
```

Visited:

```text
{0,1}
```

Component Found:

```text
{0,1}
```

Increment:

```text
components = 1
```

---

## Move to Node 2

Not visited.

Run DFS(2)

Visit:

```text
2 → 3
```

Visited:

```text
{0,1,2,3}
```

Component Found:

```text
{2,3}
```

Increment:

```text
components = 2
```

---

## Move to Node 4

Not visited.

Run DFS(4)

Visit:

```text
4
```

Component Found:

```text
{4}
```

Increment:

```text
components = 3
```

---

Final Answer:

```text
3 Connected Components
```

---

# DFS Solution

## Java Code

```java
class Solution {

    private void dfs(int node,
                     List<List<Integer>> graph,
                     boolean[] visited) {

        visited[node] = true;

        for (int neighbor : graph.get(node)) {

            if (!visited[neighbor]) {

                dfs(neighbor, graph, visited);
            }
        }
    }

    public int countComponents(int V,
                               List<List<Integer>> graph) {

        boolean[] visited = new boolean[V];

        int components = 0;

        for (int i = 0; i < V; i++) {

            if (!visited[i]) {

                dfs(i, graph, visited);

                components++;
            }
        }

        return components;
    }
}
```

---

# BFS Solution

## Java Code

```java
class Solution {

    private void bfs(int start,
                     List<List<Integer>> graph,
                     boolean[] visited) {

        Queue<Integer> queue = new LinkedList<>();

        queue.offer(start);

        visited[start] = true;

        while (!queue.isEmpty()) {

            int node = queue.poll();

            for (int neighbor : graph.get(node)) {

                if (!visited[neighbor]) {

                    visited[neighbor] = true;

                    queue.offer(neighbor);
                }
            }
        }
    }

    public int countComponents(int V,
                               List<List<Integer>> graph) {

        boolean[] visited = new boolean[V];

        int components = 0;

        for (int i = 0; i < V; i++) {

            if (!visited[i]) {

                bfs(i, graph, visited);

                components++;
            }
        }

        return components;
    }
}
```

---

# Dry Run

Graph:

```text
0 ----- 1

2 ----- 3 ----- 4

5
```

Adjacency List:

```java
0 -> [1]
1 -> [0]

2 -> [3]
3 -> [2,4]
4 -> [3]

5 -> []
```

---

### i = 0

Unvisited.

Run DFS.

Visit:

```text
0 → 1
```

Component:

```text
{0,1}
```

components:

```text
1
```

---

### i = 1

Already visited.

Skip.

---

### i = 2

Unvisited.

Run DFS.

Visit:

```text
2 → 3 → 4
```

Component:

```text
{2,3,4}
```

components:

```text
2
```

---

### i = 3

Visited.

Skip.

---

### i = 4

Visited.

Skip.

---

### i = 5

Unvisited.

Run DFS.

Component:

```text
{5}
```

components:

```text
3
```

---

Answer:

```text
3
```

---

# Complexity Analysis

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

- Every vertex visited once.
- Every edge explored once.

---

### Space Complexity

DFS:

```text
O(V)
```

For:

```text
Visited Array
Recursion Stack
```

---

BFS:

```text
O(V)
```

For:

```text
Visited Array
Queue
```

---

# Connected Components in Matrix Form

Graph:

```text
0 --- 1

2 --- 3
```

Adjacency Matrix:

```text
    0 1 2 3

0   0 1 0 0
1   1 0 0 0
2   0 0 0 1
3   0 0 1 0
```

Components:

```text
{0,1}
{2,3}
```

Answer:

```text
2
```

---

# Visual Understanding

Graph:

```text
A ----- B


C ----- D ----- E


F
```

Component 1:

```text
A ----- B
```

Component 2:

```text
C ----- D ----- E
```

Component 3:

```text
F
```

Answer:

```text
3 Components
```

---

# Number of Islands Connection

The famous problem:

```text
200. Number of Islands
```

is simply:

```text
Connected Components in a Grid
```

Example:

```text
1 1 0

1 0 0

0 1 1
```

Components:

```text
Island 1

1 1
1

Island 2

1 1
```

Answer:

```text
2
```

---

# Connected Components Using DSU (Union Find)

Another approach:

```text
DFS/BFS → O(V+E)

DSU → O(E × α(V))
```

Used when:

```text
Graph changes dynamically

Edges are added repeatedly
```

Examples:

- Number of Provinces
- Redundant Connection
- Accounts Merge
- Dynamic Connectivity

---

# Interview Problems

## Easy

1. DFS of Graph
2. BFS of Graph
3. Number of Provinces
4. Number of Connected Components

---

## Medium

1. Number of Islands
2. Max Area of Island
3. Surrounded Regions
4. Accounts Merge
5. Friend Circles

---

## Hard

1. Dynamic Connectivity
2. Making A Large Island
3. Most Stones Removed
4. Number of Operations to Make Network Connected

---

# Common Interview Mistakes

## Mistake 1

Starting DFS only once.

Wrong:

```java
dfs(0);
```

Correct:

```java
for(int i=0;i<V;i++)
{
    if(!visited[i])
        dfs(i);
}
```

---

## Mistake 2

Forgetting visited array.

Results:

```text
Infinite recursion
Infinite loop
```

---

## Mistake 3

Counting nodes instead of components.

We increment:

```java
components++;
```

only after:

```java
new DFS starts
```

---

# FAANG Cheat Sheet

```text
Connected Component:

A maximal set of vertices
where every vertex is reachable
from every other vertex.

Algorithm:

for every node

    if unvisited

         DFS/BFS(node)

         components++

Time:
O(V + E)

Space:
O(V)

Applications:
- Number of Islands
- Friend Circles
- Provinces
- Network Connectivity
- Cluster Detection

Interview Pattern:
Run DFS/BFS from every unvisited node.
Each new traversal = One Component.
```

# Key Insight

The concept of **Connected Components** is one of the most important graph patterns.

```text
Connected Components
        │
        ├── Number of Provinces
        ├── Number of Islands
        ├── Friend Circles
        ├── Network Connectivity
        ├── DSU Problems
        └── Graph Clustering
```

Whenever an interviewer asks:

```text
"How many disconnected groups exist?"
```

think immediately:

```text
Connected Components + DFS/BFS/DSU
```
