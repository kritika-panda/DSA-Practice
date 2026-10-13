# Minimum Spanning Tree (MST) - Complete Theory

# What is a Spanning Tree?

Given a connected undirected graph:

```text
V = Vertices
E = Edges
```

A **Spanning Tree** is a subset of edges that:

✅ Connects all vertices

✅ Contains all vertices

✅ Has no cycles

✅ Uses exactly:

```text
V - 1 edges
```

---

## Example

Graph:

```text
      A
     / \
    B---C
```

Edges:

```text
AB
AC
BC
```

Possible spanning tree:

```text
A
| \
B  C
```

Edges used:

```text
AB
AC
```

Number of edges:

```text
3 - 1 = 2
```

---

# What is Minimum Spanning Tree (MST)?

Among all possible spanning trees,

choose the one whose:

```text
Sum of edge weights
```

is minimum.

---

## Example

```text
      1
   A ----- B
   |\      |
  4| \2    |3
   |  \    |
   C ----- D
       5
```

Possible spanning tree:

```text
AB = 1
AD = 2
BD = 3
```

Weight:

```text
1 + 2 + 3 = 6
```

This is the MST.

---

# Properties of MST

## Property 1

For a graph with:

```text
V vertices
```

MST contains:

```text
V - 1 edges
```

always.

---

## Property 2

MST contains:

```text
No cycles
```

because it is a tree.

---

## Property 3

All vertices must be connected.

---

## Property 4

MST is defined only for:

```text
Connected Graphs
```

Otherwise:

```text
Minimum Spanning Forest
```

is formed.

---

# Difference Between MST and Shortest Path

Many interviews ask this.

---

## MST

Goal:

```text
Connect all vertices
with minimum total cost
```

Example:

```text
Power cable network
Road network
Communication network
```

---

## Shortest Path

Goal:

```text
Find shortest route between nodes
```

Example:

```text
Source → Destination
```

---

### Comparison

| Feature | MST | Shortest Path |
|----------|---------|---------|
| Connect all nodes | ✅ | ❌ |
| Root node needed | ❌ | ✅ |
| Total graph cost minimized | ✅ | ❌ |
| Distance from source minimized | ❌ | ✅ |
| Algorithms | Prim, Kruskal | Dijkstra, Bellman-Ford |

---

# When to Use MST?

Whenever question says:

```text
Connect all cities
Connect all houses
Connect all computers
Connect all islands
Minimum wiring cost
Minimum road construction cost
```

Think:

```text
MST
```

---

# Two Famous MST Algorithms

```text
1. Prim's Algorithm

2. Kruskal's Algorithm
```

---

# Prim's Algorithm

## Idea

Very similar to Dijkstra.

Start from any node.

Repeatedly choose:

```text
Minimum weight edge
that connects a new node
```

---

## Visualization

```text
     2
 A ------ B
 |        |
3|        |1
 |        |
 C ------ D
     4
```

Start:

```text
A
```

Choose:

```text
AB = 2
```

Now:

```text
{A,B}
```

Choose:

```text
BD = 1
```

Now:

```text
{A,B,D}
```

Choose:

```text
AC = 3
```

All nodes connected.

---

MST Cost:

```text
2 + 1 + 3

= 6
```

---

## Prim's Algorithm Steps

### Step 1

Start from any node.

```text
Node 0
```

---

### Step 2

Push all outgoing edges.

---

### Step 3

Pick minimum weight edge.

---

### Step 4

If node already visited:

```text
Skip
```

---

### Step 5

Otherwise:

```text
Add to MST
Add its edges
```

---

### Step 6

Continue until:

```text
All nodes visited
```

---

# Prim's Complexity

Using Min Heap:

```text
Time = O(E log V)
```

Space:

```text
O(V)
```

---

# Prim's Java Template

```java
PriorityQueue<Pair> pq =
    new PriorityQueue<>(
        (a,b) -> a.weight - b.weight
    );

pq.offer(new Pair(0,0));

while(!pq.isEmpty()) {

    Pair curr = pq.poll();

    if(visited[curr.node])
        continue;

    visited[curr.node] = true;

    mstWeight += curr.weight;

    for(Pair neighbor : adj.get(curr.node)) {

        if(!visited[neighbor.node]) {
            pq.offer(
                new Pair(
                    neighbor.node,
                    neighbor.weight
                )
            );
        }
    }
}
```

---

# Kruskal's Algorithm

## Idea

Instead of expanding nodes:

```text
Sort all edges
```

Take edges in increasing order.

---

### Important Rule

```text
Add edge only if
it doesn't form a cycle.
```

---

# Example

Edges:

```text
A-B = 1

B-C = 2

A-C = 3

C-D = 4
```

Sorted:

```text
1
2
3
4
```

---

Take:

```text
AB = 1
```

Take:

```text
BC = 2
```

Skip:

```text
AC = 3
```

because cycle forms.

Take:

```text
CD = 4
```

MST complete.

---

# Why Do We Need DSU?

How do we know:

```text
Cycle forms?
```

Need efficient cycle detection.

Use:

```text
Disjoint Set Union (DSU)
```

also called:

```text
Union Find
```

---

# DSU Operations

## Find

Find ultimate parent.

```java
findUPar(node)
```

---

## Union

Merge sets.

```java
unionByRank(u,v)

or

unionBySize(u,v)
```

---

# Kruskal Steps

### Step 1

Sort edges by weight.

---

### Step 2

Take edge.

```text
u -> v
```

---

### Step 3

Check:

```java
find(u) != find(v)
```

---

If true:

```text
Add edge
Union sets
```

---

Otherwise:

```text
Skip
```

---

# Kruskal Complexity

Sorting:

```text
O(E log E)
```

DSU Operations:

```text
O(E α(V))
```

where:

```text
α(V)
```

is almost constant.

Overall:

```text
O(E log E)
```

---

# Prim vs Kruskal

| Feature | Prim | Kruskal |
|----------|---------|---------|
| Uses | Priority Queue | DSU |
| Works on | Vertices | Edges |
| Similar to | Dijkstra | Greedy Sorting |
| Complexity | O(E log V) | O(E log E) |
| Data Structure | Min Heap | Union Find |
| Best for Dense Graph | ✅ | ❌ |
| Best for Sparse Graph | ❌ | ✅ |

---

# Cut Property (Very Important)

Suppose graph is divided into two parts:

```text
A | B
```

Among all edges crossing the cut:

```text
The minimum edge
always belongs to MST.
```

---

Example:

```text
A ----- B
 \      |
  \     |
   \    |
    C
```

If:

```text
AB = 1
AC = 4
BC = 5
```

Minimum crossing edge:

```text
AB
```

Must be in MST.

---

# Cycle Property

If a cycle exists:

```text
Remove the largest edge.
```

That edge will never be part of MST.

---

Example:

```text
A----1----B

|         |

4         2

|         |

C---------D
```

Largest edge:

```text
4
```

Can be safely removed.

---

# Important Interview Questions

### Easy

```text
MST Weight
Prim Algorithm
Kruskal Algorithm
DSU Basics
```

---

### Medium

```text
Connecting Cities With Minimum Cost

Min Cost to Connect All Points

Optimize Water Distribution in a Village
```

---

### Hard

```text
Critical and Pseudo-Critical Edges in MST

Number of MSTs

Dynamic MST
```

---

# MST Recognition Pattern

If question says:

```text
Connect everything

Minimum cost

Network design

Road construction

Electrical wiring

Internet cables

Water pipelines
```

Think:

```text
Minimum Spanning Tree
```

---

# Interview Takeaways

### MST Definition

```text
Tree connecting all vertices
with minimum total edge weight.
```

### Number of Edges

```text
V - 1
```

### Algorithms

```text
Prim's Algorithm

Kruskal's Algorithm
```

### Time Complexities

```text
Prim:
O(E log V)

Kruskal:
O(E log E)
```

### Core Data Structures

```text
Prim  -> Priority Queue

Kruskal -> DSU (Union Find)
```

### Most Important Fact

```text
MST minimizes
the total cost of connecting
all vertices,

NOT the shortest distance
between two vertices.
```
