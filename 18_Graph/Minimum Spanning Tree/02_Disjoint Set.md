# Disjoint Set Union (DSU) / Union Find

# What is a Disjoint Set?

A **Disjoint Set Union (DSU)** is a data structure used to maintain a collection of:

```text
Non-overlapping (Disjoint) Sets
```

It efficiently supports:

```text
1. Find Ultimate Parent (Find)
2. Merge Two Sets (Union)
```

---

# Why Do We Need DSU?

Consider the following graph:

```text
1 --- 2 --- 3

4 --- 5
```

Questions:

```text
Are 1 and 3 connected?

Are 2 and 5 connected?

Connect 3 and 4.
```

A naive approach can be expensive.

DSU performs these operations in:

```text
Nearly O(1)
```

---

# Key Operations

## 1. Find

Returns the representative (ultimate parent) of a set.

Example:

```text
1 → 2 → 3

parent[1] = 2
parent[2] = 3
parent[3] = 3
```

Tree:

```text
1
 \
  2
   \
    3
```

Here:

```java
find(1) = 3
find(2) = 3
find(3) = 3
```

---

## 2. Union

Merges two different sets.

Example:

Before:

```text
{1,2,3}

{4,5}
```

After:

```java
union(3,4)
```

Result:

```text
{1,2,3,4,5}
```

---

# DSU Representation

We maintain:

```java
parent[]
```

Initially:

```text
Node:    1 2 3 4 5

Parent:  1 2 3 4 5
```

Every node is its own parent.

---

# Basic Find Operation

```java
public int findUPar(int node) {

    if(parent[node] == node)
        return node;

    return findUPar(parent[node]);
}
```

---

# Problem with Basic Find

Consider:

```text
1 → 2 → 3 → 4 → 5
```

Finding parent of:

```java
find(1)
```

takes:

```text
O(N)
```

This becomes inefficient.

---

# Path Compression

## Idea

While finding the ultimate parent:

```text
Attach every visited node
directly to the root.
```

---

### Before Compression

```text
1 → 2 → 3 → 4 → 5
```

---

### After Compression

```text
      5
   / /|\ \
  1 2 3 4
```

Now:

```java
find(1)
```

takes:

```text
O(1)
```

---

## Path Compression Code

```java
public int findUPar(int node) {

    if(node == parent[node])
        return node;

    return parent[node]
            = findUPar(parent[node]);
}
```

---

# Union by Rank

## Problem

Suppose we always do:

```java
parent[u] = v;
```

Then trees may become:

```text
1 → 2 → 3 → 4 → 5 → 6
```

Very tall.

---

## Solution

Attach the smaller-height tree under the larger-height tree.

---

### Rank

```text
Rank = Approximate Height
```

---

### Example

```text
Tree A (rank = 1)

Tree B (rank = 3)
```

Attach:

```text
A under B
```

---

## Union By Rank Code

```java
public void unionByRank(int u, int v) {

    int pu = findUPar(u);
    int pv = findUPar(v);

    if(pu == pv)
        return;

    if(rank[pu] < rank[pv]) {

        parent[pu] = pv;
    }
    else if(rank[pv] < rank[pu]) {

        parent[pv] = pu;
    }
    else {

        parent[pv] = pu;
        rank[pu]++;
    }
}
```

---

# Union by Size

Instead of maintaining height:

```text
Maintain size of components.
```

---

### Example

```text
Component A = 3 nodes

Component B = 10 nodes
```

Attach:

```text
A under B
```

---

## Union By Size Code

```java
public void unionBySize(int u, int v) {

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
```

---

# Complete DSU Template (Most Used in Interviews)

```java
class DisjointSet {

    int[] parent;
    int[] size;

    public DisjointSet(int n) {

        parent = new int[n + 1];
        size = new int[n + 1];

        for(int i = 0; i <= n; i++) {

            parent[i] = i;
            size[i] = 1;
        }
    }

    public int findUPar(int node) {

        if(node == parent[node])
            return node;

        return parent[node]
                = findUPar(parent[node]);
    }

    public void unionBySize(int u, int v) {

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
```

---

# Dry Run

Initially:

```text
1  2  3  4  5

parent:

1  2  3  4  5
```

---

### Union(1,2)

```text
1
|
2
```

parent:

```text
1  1  3  4  5
```

---

### Union(2,3)

```text
   1
  / \
 2   3
```

parent:

```text
1  1  1  4  5
```

---

### Find(3)

```text
findUPar(3)

= 1
```

---

### Find(2)

```text
findUPar(2)

= 1
```

Since parent is same:

```text
2 and 3 belong to same component.
```

---

# Cycle Detection using DSU

Suppose edge:

```text
u ----- v
```

Check:

```java
if(findUPar(u) == findUPar(v))
```

Then:

```text
Cycle Exists
```

Otherwise:

```java
unionBySize(u, v);
```

---

## Example

```text
1 --- 2
 \   /
   3
```

Process:

```text
(1,2)

(2,3)
```

Now:

```text
find(1) == find(3)
```

When edge:

```text
(1,3)
```

arrives:

```text
Cycle detected.
```

---

# DSU in Kruskal's Algorithm

For every edge:

```java
if(findUPar(u) != findUPar(v))
{
    mstWeight += wt;
    unionBySize(u,v);
}
```

Purpose:

```text
Prevent cycle formation.
```

---

# Complexity Analysis

## Without Optimizations

```text
Find  : O(N)

Union : O(N)
```

---

## With Path Compression

```text
Nearly O(1)
```

---

## With Path Compression + Union by Size/Rank

```text
O(α(N))
```

where:

```text
α(N)
```

is the Inverse Ackermann Function.

---

### Practical Value

```text
α(N) < 5
```

for all realistic input sizes.

Therefore:

```text
Time Complexity ≈ O(1)
```

---

# Rank vs Size

| Feature | Union By Rank | Union By Size |
|----------|---------------|---------------|
| Stores | Height | Number of Nodes |
| Easy for Component Size Queries | ❌ | ✅ |
| Complexity | Same | Same |
| Used in Interviews | ✅ | ✅ |
| Most Common | ❌ | ✅ |

---

# Common Interview Problems on DSU

### Easy

```text
Find if two nodes belong to same component
```

---

### Medium

```text
Kruskal's Algorithm

Redundant Connection

Number of Connected Components
```

---

### Hard

```text
Accounts Merge

Most Stones Removed

Making A Large Island

Number of Islands II
```

---

# Interview Takeaways

## DSU Supports

```text
1. Find Parent

2. Union Sets
```

---

## Optimizations

```text
Path Compression

Union By Rank

Union By Size
```

---

## Complexity

```text
O(α(N))

≈ O(1)
```

---

## Most Important Line

```java
return parent[node]
        = findUPar(parent[node]);
```

This performs **Path Compression**, which is the key reason DSU becomes extremely fast.

---

## Recognition Pattern

Whenever the problem involves:

```text
Connectivity

Components

Cycle Detection

Merging Groups

Kruskal MST
```

Think:

```text
Disjoint Set Union (DSU)
```
