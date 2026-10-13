# Number of Provinces

## Problem Statement

You are given an:

```text
n x n adjacency matrix
```

called:

```java
isConnected[i][j]
```

where:

```text
isConnected[i][j] = 1
```

means:

```text
City i is directly connected to City j
```

and

```text
isConnected[i][j] = 0
```

means:

```text
No direct connection
```

A **Province** is a group of directly or indirectly connected cities.

Return:

```text
Total Number of Provinces
```

---

# LeetCode 547 - Number of Provinces

## Example 1

Input:

```text
[
 [1,1,0],
 [1,1,0],
 [0,0,1]
]
```

Visualization:

```text
City 0 ----- City 1

City 2
```

Connected Groups:

```text
{0,1}
{2}
```

Output:

```text
2
```

---

## Example 2

Input:

```text
[
 [1,0,0],
 [0,1,0],
 [0,0,1]
]
```

Visualization:

```text
0

1

2
```

Output:

```text
3
```

---

# Understanding Province

Province simply means:

```text
Connected Component
```

in an Undirected Graph.

---

## Example

```text
0 ----- 1 ----- 2

3 ----- 4

5
```

Components:

```text
{0,1,2}
{3,4}
{5}
```

Answer:

```text
3 Provinces
```

---

# Key Insight

The given matrix is actually representing an:

```text
Undirected Graph
```

Example:

```text
[
 [1,1,0],
 [1,1,1],
 [0,1,1]
]
```

can be represented as:

```text
0 ----- 1 ----- 2
```

So the problem becomes:

```text
Count Connected Components
```

---

# Approach 1: DFS

## Idea

For every city:

```text
If not visited

      Start DFS

      Province++
```

Each DFS explores one complete province.

---

# Dry Run

Matrix:

```text
[
 [1,1,0],
 [1,1,0],
 [0,0,1]
]
```

Graph:

```text
0 ----- 1

2
```

---

### Initial

```text
visited = [F,F,F]

count = 0
```

---

### i = 0

Not visited.

Run DFS.

Visit:

```text
0 → 1
```

visited:

```text
[T,T,F]
```

Province completed.

```text
count = 1
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
2
```

Province completed.

```text
count = 2
```

---

Answer:

```text
2
```

---

# DFS Solution

## Java Code

```java
class Solution {

    private void dfs(int city,
                     int[][] isConnected,
                     boolean[] visited) {

        visited[city] = true;

        for (int neighbor = 0;
             neighbor < isConnected.length;
             neighbor++) {

            if (isConnected[city][neighbor] == 1
                && !visited[neighbor]) {

                dfs(neighbor, isConnected, visited);
            }
        }
    }

    public int findCircleNum(int[][] isConnected) {

        int n = isConnected.length;

        boolean[] visited = new boolean[n];

        int provinces = 0;

        for (int city = 0; city < n; city++) {

            if (!visited[city]) {

                dfs(city, isConnected, visited);

                provinces++;
            }
        }

        return provinces;
    }
}
```

---

# DFS Visualization

Graph:

```text
      0
     /
    1

    2 --- 3
```

Traversal:

```text
DFS(0)

0 → 1

Province #1
```

Next unvisited:

```text
DFS(2)

2 → 3

Province #2
```

Answer:

```text
2 Provinces
```

---

# Complexity Analysis

Let:

```text
n = Number of Cities
```

Matrix Size:

```text
n × n
```

### Time Complexity

```text
O(n²)
```

Reason:

For every city we scan:

```text
all n neighbors
```

Hence:

```text
n × n = O(n²)
```

---

### Space Complexity

```text
O(n)
```

For:

```text
visited[]
recursion stack
```

---

# Approach 2: BFS

Same logic.

Just replace DFS with BFS.

---

# BFS Algorithm

```text
For every city

      If not visited

           BFS(city)

           provinces++
```

---

# BFS Code

```java
class Solution {

    private void bfs(int start,
                     int[][] isConnected,
                     boolean[] visited) {

        Queue<Integer> queue =
                new LinkedList<>();

        queue.offer(start);

        visited[start] = true;

        while (!queue.isEmpty()) {

            int city = queue.poll();

            for (int neighbor = 0;
                 neighbor < isConnected.length;
                 neighbor++) {

                if (isConnected[city][neighbor] == 1
                    && !visited[neighbor]) {

                    visited[neighbor] = true;

                    queue.offer(neighbor);
                }
            }
        }
    }

    public int findCircleNum(int[][] isConnected) {

        int n = isConnected.length;

        boolean[] visited = new boolean[n];

        int provinces = 0;

        for (int city = 0; city < n; city++) {

            if (!visited[city]) {

                bfs(city, isConnected, visited);

                provinces++;
            }
        }

        return provinces;
    }
}
```

---

# Complexity (BFS)

### Time

```text
O(n²)
```

### Space

```text
O(n)
```

---

# Approach 3: Union Find (DSU)

This is the most interview-friendly advanced solution.

Idea:

Initially:

```text
Every city is its own province.
```

Example:

```text
0 1 2 3

Provinces = 4
```

Now:

```text
0 connected to 1
```

Union:

```text
{0,1}
```

Provinces:

```text
3
```

Continue merging.

Final number of unique parents:

```text
Answer
```

---

# DSU Solution

```java
class Solution {

    class DSU {

        int[] parent;

        DSU(int n) {

            parent = new int[n];

            for (int i = 0; i < n; i++) {

                parent[i] = i;
            }
        }

        int find(int x) {

            if (parent[x] == x)
                return x;

            return parent[x] =
                    find(parent[x]);
        }

        boolean union(int a, int b) {

            int pa = find(a);
            int pb = find(b);

            if (pa == pb)
                return false;

            parent[pb] = pa;

            return true;
        }
    }

    public int findCircleNum(int[][] isConnected) {

        int n = isConnected.length;

        DSU dsu = new DSU(n);

        int provinces = n;

        for (int i = 0; i < n; i++) {

            for (int j = i + 1; j < n; j++) {

                if (isConnected[i][j] == 1) {

                    if (dsu.union(i, j)) {

                        provinces--;
                    }
                }
            }
        }

        return provinces;
    }
}
```

---

# Why DSU Works

Initially:

```text
0 1 2 3 4

Total Provinces = 5
```

Union:

```text
0-1
```

Provinces:

```text
4
```

Union:

```text
1-2
```

Provinces:

```text
3
```

Union:

```text
3-4
```

Provinces:

```text
2
```

Answer:

```text
2
```

---

# DFS vs BFS vs DSU

| Approach | Time | Space |
|-----------|---------|---------|
| DFS | O(n²) | O(n) |
| BFS | O(n²) | O(n) |
| DSU | O(n²) | O(n) |

---

# Pattern Recognition

Whenever you see:

```text
Cities
States
Provinces
Networks
Friend Circles
Groups
Clusters
Connected Computers
```

Think:

```text
Connected Components
```

Possible Solutions:

```text
1. DFS
2. BFS
3. DSU
```

---

# Similar Problems

### Easy-Medium

- Number of Provinces
- Friend Circles
- Connected Components in Graph

### Medium

- Number of Islands
- Max Area of Island
- Accounts Merge
- Network Connected

### Hard

- Making A Large Island
- Redundant Connection II
- Dynamic Connectivity

---

# Interview Cheat Sheet

```text
Number of Provinces

Observation:
Province = Connected Component

Graph Type:
Undirected Graph

Approach:

for each city

    if unvisited

        DFS/BFS(city)

        provinces++

DFS:
O(n²)

BFS:
O(n²)

DSU:
O(n²)

Most Asked Follow-up:
"Can you solve it using Union Find?"
```

# Key Insight

This is one of the easiest graph problems and is often the **first Connected Components problem asked in interviews**.

```text
Number of Provinces
        ↓
Connected Components
        ↓
DFS / BFS / DSU
```

If you immediately recognize:

```text
Province = Connected Component
```

the problem becomes a straightforward graph traversal problem.
