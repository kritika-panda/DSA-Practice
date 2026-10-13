# Find the City With the Smallest Number of Neighbors at a Threshold Distance

## Problem Statement

There are `n` cities numbered from `0` to `n-1`. Given the array `edges` where `edges[i] = [from_i, to_i, weight_i]` represents a bidirectional and weighted edge between cities `from_i` and `to_i`, and given the integer `distanceThreshold`.

Return the city with the smallest number of cities that are reachable through some path and whose distance is **at most** `distanceThreshold`. If there are multiple such cities, return the city with the **greatest number**.

The overall runtime complexity must be strictly bounded by:

```text
O(V³)
```

*(where V is the number of cities n)*

---

## Example 1

### Input

```text
n = 4
edges = [,,,]
distanceThreshold = 4
```

### Output

```text
3
```

### Explanation

```text
The neighboring cities within a distance threshold of 4 for each city are:
- City 0 -> [City 1, City 2] (2 cities)
- City 1 -> [City 0, City 2, City 3] (3 cities)
- City 2 -> [City 0, City 1, City 3] (3 cities)
- City 3 -> [City 1, City 2] (2 cities)

Cities 0 and 3 both have 2 neighboring cities within the threshold. 
Since we must return the city with the greatest number when there is a tie, we return City 3.
```

---

## Example 2

### Input

```text
n = 5
edges = [,,,,]
distanceThreshold = 2
```

### Output

```text
0
```

---

# Intuition

This problem can be split into two sequential phases:
1.  **All-Pairs Shortest Path Evaluation:** We need to compute the absolute shortest distance between *every single pair* of cities to accurately verify if they fall within the `distanceThreshold`.
2.  **Threshold Filtering & Tie-Breaking:** For each city, we count how many other cities are reachable within the distance threshold. We then scan our results to locate the minimum count, picking the higher index if a count tie occurs.

Since the number of cities is relatively small (\(n \le 100\)) and we explicitly require an all-pairs distance matrix, the **Floyd-Warshall Algorithm** is the optimal architectural choice.

We initialize a 2D distance matrix `dist[n][n]` to infinity (`1e9`), setting the main diagonal `dist[i][i] = 0`. We populate the direct bidirectional paths from our `edges` input. We then run three nested loops tracking an intermediate city `k`. For every source `i` and destination `j`, if routing through `k` cuts down travel time, we update `dist[i][j]` in-place.

---

# State Definition

Let:

```text
int[][] dist = new int[n][n]; // 2D state matrix tracking all-pairs shortest paths
```

---

# Recurrence / Matrix Update Condition

```text
For each intermediate city k from 0 to n-1:
    For each source city i from 0 to n-1:
        For each destination city j from 0 to n-1:
            dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
```

---

# Optimal Structural All-Pairs Shortest-Path Solution

## Idea

By precomputing the entire graph distance topology using the Floyd-Warshall DP paradigm and utilizing an in-place step filtration sweep to tally valid neighbors, all constraints resolve seamlessly. The three-tier nested array loops execute cleanly inside an explicit **O(V³)** timeline.

---

## Java Code (Floyd-Warshall + Threshold Counter)

```java
import java.util.Arrays;

class Solution {

    public int findTheCity(int n, int[][] edges, int distanceThreshold) {
        // Safe upper bound constant sentinel to prevent arithmetic overflow on matrix additions
        int INF = 100000000; 
        int[][] dist = new int[n][n];

        // Step 1: Initialize the distance tracking table
        for (int i = 0; i < n; i++) {
            Arrays.fill(dist[i], INF);
            dist[i][i] = 0; // Cost to stay in the same city is zero
        }

        // Step 2: Populate the bidirectional weighted edge costs
        for (int[] edge : edges) {
            int u = edge[0];
            int v = edge[1];
            int weight = edge[2];
            dist[u][v] = weight;
            dist[v][u] = weight;
        }

        // Step 3: Compute All-Pairs Shortest Paths via Floyd-Warshall DP framework
        for (int k = 0; k < n; k++) {
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (dist[i][k] + dist[k][j] < dist[i][j]) {
                        dist[i][j] = dist[i][k] + dist[k][j];
                    }
                }
            }
        }

        int optimalCity = -1;
        int minNeighborCount = Integer.MAX_VALUE;

        // Step 4: Evaluate the reachable neighbor counts for each city sequentially
        for (int i = 0; i < n; i++) {
            int reachableCount = 0;
            
            for (int j = 0; j < n; j++) {
                if (i != j && dist[i][j] <= distanceThreshold) {
                    reachableCount++;
                }
            }

            // Tie-Breaking condition: pick the city with the smaller count. 
            // If counts are equal, the >= comparison guarantees that the higher index wins.
            if (reachableCount <= minNeighborCount) {
                minNeighborCount = reachableCount;
                optimalCity = i;
            }
        }

        return optimalCity;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V³) -> The Floyd-Warshall phase is governed by three nested loops running up to V (n) exactly. The final counting phase takes quadratic O(V²) steps, keeping the entire runtime strictly bounded inside O(V³) parameters.
Space Complexity: O(V²) -> Dedicated heap memory space allocated to hold the V x V integer shortest-path matrix table.
```

---

# Dry Run

## Input

```text
n = 3, distanceThreshold = 3, edges = [,,]
Direct weights: 0-1 (w=2), 1-2 (w=2), 0-2 (w=5)
```

---

### Execution Trace

```text
1. Compute Floyd-Warshall:
   - dist[0][2] = min(5, dist[0][1] + dist[1][2]) = min(5, 2 + 2) = 4.
   Matrix fully processed.

2. Tally Threshold Neighbors (dist[i][j] <= 3):
   - City 0: reachable is City 1 (dist=2). Count = 1.
   - City 1: reachable is City 0 (dist=2) and City 2 (dist=2). Count = 2.
   - City 2: reachable is City 1 (dist=2). Count = 1.

3. Locate Optimal Selection:
   - City 0 has count 1 -> minNeighborCount = 1, optimalCity = 0.
   - City 1 has count 2 > 1 -> Skip.
   - City 2 has count 1 == 1 -> Ties! reachableCount (1) <= minNeighborCount (1) is True.
     optimalCity updates to 2.

Returns City 2.
```

---

# Visualization

## Distance Network Compaction Layer

```text
       2             2
  (0) ◄────────► (1) ◄────────► (2)
   ▲                             ▲
   └─────────────────────────────┘
          5 (Optimized to 4)
          
  At threshold = 3, City 0 cannot reach City 2 directly (dist 4 > 3).
  City 0 and City 2 both see only 1 neighbor inside their valid travel boundary.
```

---

# Graph Paradigm Pattern

## Identify State

```text
All-pairs shortest path boundary filtering solved via a multi-stage dynamic programming matrix compaction pass.
```

## Choices

```text
Compress edge limits using intermediate vertices k. Run a secondary threshold validation pass across rows to filter neighbor aggregates.
```

---

## Key Takeaways

✅ Advanced integration of All-Pairs Shortest Path (APSP) layouts with constraint filtering.

✅ **Tie-Breaking Flag:** Implementing a broad less-than-or-equal-to step operator (`reachableCount <= minNeighborCount`) while iterating from index `0` up to `n-1` naturally handles the tie-breaking constraint without allocating complex conditional secondary markers.

✅ Direct real-world implementation engine inside **logistics hub optimization frameworks** (locating central warehouse hubs with the least traffic bottleneck risk zones), planning emergency vehicle dispatch zones, or configuring cellular tower nodes.

✅ Common Graph Design Pattern:

```text
Graph Topology Reduction via All-Pairs Matrix Relaxation and Threshold Filtering
```

### Similar Problems

- Floyd-Warshall Algorithm (Standard base template)
- Course Schedule IV (Reachability closure - LeetCode 1462)
- Network Delay Time (Single-Source Dijkstra - LeetCode 743)
- Minimum Cost to Convert String I (LeetCode 2976)
