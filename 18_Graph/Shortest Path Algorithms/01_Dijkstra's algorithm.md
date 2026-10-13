# Dijkstra's Algorithm

## Problem Statement

Given a weighted graph with `V` vertices and `E` edges, find the **shortest distance** from a source node to all other nodes.

Conditions:

- Edge weights must be **non-negative**
- Graph can be directed or undirected

---

## Example

### Input

```text
      4
   (0)----- (1)
    | \       |
   1|  \2     |1
    |   \     |
    |    \    |
   (2)-----(3)
       5
```

Source:

```text
0
```

### Output

```text
Distance from source 0

Node 0 -> 0
Node 1 -> 3
Node 2 -> 1
Node 3 -> 4
```

---

# What is Dijkstra's Algorithm?

Dijkstra's Algorithm is used to find:

```text
Single Source Shortest Path (SSSP)
```

from a source node to all other nodes in a graph with:

```text
Non-Negative Edge Weights
```

---

# Core Idea

Whenever we reach a node:

```text
Current Distance = dist[u]
```

and traverse an edge:

```text
u ----w----> v
```

then a better distance may exist:

```text
dist[u] + w
```

If:

```text
dist[u] + w < dist[v]
```

update:

```text
dist[v] = dist[u] + w
```

This process is called:

```text
Relaxation
```

---

# Greedy Observation

Among all unvisited nodes:

```text
Pick the node having minimum distance.
```

Why?

Because all weights are non-negative.

Therefore:

```text
The first time a node is removed
from the priority queue,
its shortest distance is finalized.
```

This greedy property makes Dijkstra work.

---

# Data Structures Used

## Distance Array

```java
int[] dist
```

Stores shortest distance from source.

---

## Priority Queue (Min Heap)

```java
PriorityQueue<Pair>
```

Stores:

```text
(distance, node)
```

Always extracts:

```text
minimum distance node
```

---

# Algorithm Steps

## Step 1

Initialize:

```java
dist[source] = 0
```

All others:

```java
∞
```

---

## Step 2

Insert source into Min Heap.

```text
(0, source)
```

---

## Step 3

Remove minimum distance node.

```text
(currDistance, node)
```

---

## Step 4

Visit all neighbors.

For every edge:

```text
u ----w----> v
```

check:

```java
if(dist[u] + w < dist[v])
```

then:

```java
dist[v] = dist[u] + w
```

push updated node into PQ.

---

## Step 5

Repeat until PQ becomes empty.

---

# Dry Run

Graph:

```text
0 --4--> 1
|        |
1        1
|        |
v        v
2 --5--> 3

0 --2--> 3
```

---

### Initially

```text
dist = [0, ∞, ∞, ∞]

PQ = {(0,0)}
```

---

### Pop 0

```text
Node = 0
Distance = 0
```

Relax:

```text
1 => 4
2 => 1
3 => 2
```

```text
dist = [0,4,1,2]
```

PQ:

```text
(1,2)
(2,3)
(4,1)
```

---

### Pop 2

```text
Node = 2
Distance = 1
```

Relax:

```text
3 via 2 = 6
```

No update.

---

### Pop 3

```text
Node = 3
Distance = 2
```

Relax:

```text
1 via 3 = 3
```

Better than 4.

Update:

```text
dist[1]=3
```

---

### Pop 1

Final distances:

```text
[0,3,1,2]
```

---

# Visualization

```text
           4
      0 -------> 1
      |          ^
      |          |
      1          1
      |          |
      v          |
      2 -------> 3
           5
```

Shortest distances from 0:

```text
0 → 0 = 0

0 → 2 = 1

0 → 3 = 2

0 → 3 → 1 = 3
```

---

# Java Solution (Priority Queue)

```java
import java.util.*;

class Solution {

    static class Pair {
        int node;
        int distance;

        Pair(int node, int distance) {
            this.node = node;
            this.distance = distance;
        }
    }

    public int[] dijkstra(int V,
                          ArrayList<ArrayList<ArrayList<Integer>>> adj,
                          int src) {

        PriorityQueue<Pair> pq =
                new PriorityQueue<>(
                        (a, b) -> a.distance - b.distance);

        int[] dist = new int[V];

        Arrays.fill(dist, Integer.MAX_VALUE);

        dist[src] = 0;

        pq.offer(new Pair(src, 0));

        while (!pq.isEmpty()) {

            Pair current = pq.poll();

            int node = current.node;
            int distance = current.distance;

            if (distance > dist[node])
                continue;

            for (ArrayList<Integer> edge : adj.get(node)) {

                int neighbor = edge.get(0);
                int weight = edge.get(1);

                if (distance + weight < dist[neighbor]) {

                    dist[neighbor] = distance + weight;

                    pq.offer(
                        new Pair(
                            neighbor,
                            dist[neighbor]
                        )
                    );
                }
            }
        }

        return dist;
    }
}
```

---

# Time Complexity

Using Min Heap:

Each edge may cause:

```text
One insertion into PQ
```

Total:

```text
O(E log V)
```

---

# Space Complexity

Distance Array:

```text
O(V)
```

Priority Queue:

```text
O(V)
```

Adjacency List:

```text
O(V + E)
```

Overall:

```text
O(V + E)
```

---

# Why Priority Queue?

Without PQ:

Finding minimum node each time:

```text
O(V)
```

Repeated V times:

```text
O(V²)
```

---

With PQ:

```text
Extract Min = O(log V)
```

So:

```text
O(E log V)
```

which is much faster for sparse graphs.

---

# Dijkstra Using TreeSet

Some implementations use:

```java
TreeSet<Pair>
```

instead of a Priority Queue.

Advantages:

```text
Supports deletion of old entries.
```

Complexity:

```text
O(E log V)
```

---

# Why Doesn't Dijkstra Work for Negative Weights?

Example:

```text
A ----2----> B

A ----5----> C

C ----(-10)---> B
```

Shortest path:

```text
A → C → B

= 5 + (-10)
= -5
```

Dijkstra may finalize:

```text
B = 2
```

before discovering:

```text
-5
```

Thus producing an incorrect answer.

---

# Bellman-Ford vs Dijkstra

| Feature | Dijkstra | Bellman Ford |
|----------|-----------|-------------|
| Negative Weights | ❌ | ✅ |
| Detect Negative Cycle | ❌ | ✅ |
| Greedy | ✅ | ❌ |
| Time Complexity | O(E log V) | O(VE) |
| Faster | ✅ | ❌ |

---

# Dijkstra vs BFS

## BFS

Used when:

```text
All edges have equal weight
```

Example:

```text
Weight = 1
```

Complexity:

```text
O(V + E)
```

---

## Dijkstra

Used when:

```text
Different positive weights exist
```

Example:

```text
2, 5, 7, 10 ...
```

Complexity:

```text
O(E log V)
```

---

# Important Variations Asked in Interviews

### 1. Network Delay Time

LeetCode 743

```text
Pure Dijkstra
```

---

### 2. Path With Minimum Effort

LeetCode 1631

```text
Modified Dijkstra
```

---

### 3. Cheapest Flights Within K Stops

LeetCode 787

```text
Dijkstra + Stops Constraint
```

---

### 4. Number of Ways to Arrive at Destination

LeetCode 1976

```text
Dijkstra + Path Counting
```

---

### 5. Minimum Multiplications to Reach End

```text
Graph + Dijkstra/BFS
```

---

### 6. Print Shortest Path

```text
Dijkstra + Parent Array
```

---

# Interview Takeaways

### Pattern Recognition

```text
Shortest Path
+
Weighted Graph
+
No*-Negative Weights
=
D*jkstra
```

### Core Formula

```j*va*if(dist[u] + weight*< dist[v])
{
    dist[v] =*dist[u] + weight;
}
```

###*Complexity

```text
Time* :*O(E log V)

Space : O(V + E)
```

*##*Most Important Fact*
```text
Dijkstra works only for
*ON-NEGATIVE edge weights.
```
````*
