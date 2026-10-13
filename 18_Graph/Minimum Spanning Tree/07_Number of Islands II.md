# Number of Islands II

## Problem Statement

You are given:

```text
n rows
m columns
```

Initially:

```text
All cells are water (0).
```

A list of operations is given.

Each operation converts:

```text
(row, col)
```

from:

```text
0 → 1
```

(water → land)

After each operation, return the number of islands present.

---

## Example

### Input

```text
n = 4
m = 5

operators =

[
 [1,1],
 [0,1],
 [3,3],
 [3,4]
]
```

### Output

```text
[1,1,2,2]
```

---

## Visualization

### Operation 1

```text
0 0 0 0 0
0 1 0 0 0
0 0 0 0 0
0 0 0 0 0
```

Islands:

```text
1
```

---

### Operation 2

```text
0 1 0 0 0
0 1 0 0 0
0 0 0 0 0
0 0 0 0 0
```

Connected vertically.

Still:

```text
1 Island
```

---

### Operation 3

```text
0 1 0 0 0
0 1 0 0 0
0 0 0 0 0
0 0 0 1 0
```

Islands:

```text
2
```

---

### Operation 4

```text
0 1 0 0 0
0 1 0 0 0
0 0 0 0 0
0 0 0 1 1
```

Connected horizontally.

Still:

```text
2
```

Answer:

```text
[1,1,2,2]
```

---

# Brute Force Approach

After every operation:

```text
Run DFS/BFS
Count Islands
```

Complexity:

```text
K × O(N×M)
```

where:

```text
K = operations
```

Too expensive.

---

# Key Observation

Initially:

```text
All cells are water.
```

Each operation:

```text
Adds exactly one land cell.
```

Instead of recounting islands:

```text
Maintain island count dynamically.
```

This is exactly what:

```text
Disjoint Set Union (DSU)
```

is designed for.

---

# DSU Intuition

Treat each cell as a node.

For a grid:

```text
m columns
```

Convert:

```java
(row,col)
```

into:

```java
node = row * m + col
```

---

Example:

```text
0 1 2
3 4 5
6 7 8
```

for:

```text
3 x 3 grid
```

---

# Core Idea

Whenever new land appears:

```text
Island Count++
```

because it forms a new island.

---

Now check 4 neighbors:

```text
Up
Down
Left
Right
```

If neighboring land exists:

```text
Union both cells
```

---

If union succeeds:

```text
Two islands merge
```

Therefore:

```text
Island Count--
```

---

# Important Observation

Suppose:

```text
Current Islands = 5
```

Adding a new land cell:

```text
Islands = 6
```

Now it connects two components:

```text
Islands = 5
```

because two islands merged.

---

# Algorithm

## Step 1

Maintain:

```java
visited[][]

DSU
```

---

## Step 2

For every operation:

```java
(row,col)
```

If already land:

```text
Answer remains same
```

---

## Step 3

Mark current cell as land.

```java
count++
```

---

## Step 4

Check all 4 neighbors.

---

## Step 5

If neighboring land exists:

```java
find(parent1)
find(parent2)
```

---

If parents are different:

```java
union(node1,node2)

count--
```

---

## Step 6

Store island count.

---

# Dry Run

```text
n=3
m=3

operations

(0,0)
(0,1)
```

---

### Add (0,0)

```text
1 0 0
0 0 0
0 0 0
```

Islands:

```text
1
```

---

### Add (0,1)

```text
1 1 0
0 0 0
0 0 0
```

Initially:

```text
count = 2
```

Neighbor:

```text
(0,0)
```

already land.

Union:

```text
count--
```

Final:

```text
1
```

Answer:

```text
[1,1]
```

---

# Java Solution (DSU)

```java
import java.util.*;

class DisjointSet {

    int[] parent;
    int[] size;

    DisjointSet(int n) {

        parent = new int[n];
        size = new int[n];

        for(int i=0;i<n;i++) {

            parent[i] = i;
            size[i] = 1;
        }
    }

    int findUPar(int node) {

        if(node == parent[node])
            return node;

        return parent[node]
                = findUPar(parent[node]);
    }

    void unionBySize(int u, int v) {

        int pu = findUPar(u);
        int pv = findUPar(v);

        if(pu == pv)
            return;

        if(size[pu] < size[pv]) {

            parent[pu] = pv;
            size[pv] += size[pu];
        }
        else {

            parent[pv] = pu;
            size[pu] += size[pv];
        }
    }
}

class Solution {

    public List<Integer> numOfIslands(
            int n,
            int m,
            int[][] operators) {

        DisjointSet ds =
                new DisjointSet(n * m);

        boolean[][] vis =
                new boolean[n][m];

        List<Integer> ans =
                new ArrayList<>();

        int count = 0;

        int[] dr = {-1,0,1,0};
        int[] dc = {0,1,0,-1};

        for(int[] op : operators) {

            int row = op[0];
            int col = op[1];

            if(vis[row][col]) {

                ans.add(count);
                continue;
            }

            vis[row][col] = true;

            count++;

            int nodeNo =
                    row * m + col;

            for(int k=0;k<4;k++) {

                int nr = row + dr[k];
                int nc = col + dc[k];

                if(nr >= 0 &&
                   nc >= 0 &&
                   nr < n &&
                   nc < m &&
                   vis[nr][nc]) {

                    int adjNode =
                            nr * m + nc;

                    if(ds.findUPar(nodeNo)
                       != ds.findUPar(adjNode)) {

                        count--;

                        ds.unionBySize(
                                nodeNo,
                                adjNode
                        );
                    }
                }
            }

            ans.add(count);
        }

        return ans;
    }
}
```

---

# Complexity Analysis

Let:

```text
K = Number of Operations
```

---

### DSU Operations

```text
Find  -> O(α(N))

Union -> O(α(N))
```

where:

```text
α(N)
```

is Inverse Ackermann Function.

Practically:

```text
≈ O(1)
```

---

### Time Complexity

For every operation:

```text
Check 4 directions
```

Therefore:

```text
O(K × 4 × α(N))

≈ O(K)
```

---

### Space Complexity

```text
Visited Array = O(N×M)

Parent Array = O(N×M)

Size Array = O(N×M)
```

Overall:

```text
O(N×M)
```

---

# Why DSU Works?

Every land cell represents a node.

Whenever two adjacent lands meet:

```text
They belong to same island.
```

DSU efficiently merges:

```text
Connected Components
```

Each component represents:

```text
One Island
```

Therefore:

```text
Number of Islands
=
Number of Connected Components
```

---

# Similar DSU Problems

### Number of Provinces

```text
DSU on graph nodes
```

---

### Accounts Merge

```text
DSU on emails
```

---

### Most Stones Removed

```text
DSU on rows & columns
```

---

### Making A Large Island

```text
DSU + Grid
```

---

# Interview Takeaways

### Pattern Recognition

```text
Dynamic Connectivity
+
Merge Components
=
DSU
```

### Core Observation

```text
New Land
⇒ count++

Successful Union
⇒ count--
```

### Cell to Node Conversion

```java
node = row * m + col;
```

### Complexity

```text
Time  : O(K × α(N))

≈ O(K)

Space : O(N × M)
```

### Most Important Insight

```text
An Island is simply a
Connected Component.

DSU allows us to maintain
the number of connected
components dynamically.
```
