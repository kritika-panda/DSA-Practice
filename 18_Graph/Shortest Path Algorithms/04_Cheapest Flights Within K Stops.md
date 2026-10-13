# Cheapest Flights Within K Stops

## Problem Statement

There are `n` cities connected by some number of flights. You are given an array `flights` where `flights[i] = [from_i, to_i, price_i]` indicates that there is a flight from city `from_i` to city `to_i` with a cost of `price_i`.

You are also given three integers `src`, `dst`, and `k`, return *the **cheapest price** from `src` to `dst` with at most `k` stops.* If there is no such route, return `-1`.

The overall runtime complexity must be strictly bounded by:

```text
O(k * E)
```

*(where E is the total number of flight routes inside the flights array)*

---

## Example 1

### Input

```text
n = 4
flights = [[0,1,100],[1,2,100],[2,0,100],[1,3,600],[2,3,200]]
src = 0, dst = 3, k = 1
```

### Output

```text
700
```

### Explanation

```text
The optimal path with at most 1 stop is: 0 → 1 → 3 (Price = 100 + 600 = 700).
Note that 0 → 1 → 2 → 3 is cheaper (100 + 100 + 200 = 400) but requires 2 stops, which violates k = 1.
```

---

## Example 2

### Input

```text
n = 3
flights = [[0,1,100],[1,2,100],[0,2,500]]
src = 0, dst = 2, k = 1
```

### Output

```text
200
```

### Explanation

```text
The optimal path is 0 → 1 → 2 (Price = 100 + 100 = 200) with exactly 1 stop.
```

---

# Intuition

This problem can be interpreted as finding the shortest path in a weighted graph under a **strict step-count constraint**. Standard algorithms like Dijkstra can fail if a sub-optimal, more expensive path gets processed first and locks out a cheaper path that simply has more stops.

Because we need to track shortest paths bounded precisely by a fixed number of edges (`k` stops means at most `k + 1` flights/levels), the **Bellman-Ford Algorithm** (or a **Level-by-Level BFS**) is a perfect choice:
1. **Distance Array Setup:** Create an array `prices` of size `n` initialized to infinity (`Integer.MAX_VALUE`), setting `prices[src] = 0`.
2. **Levelized Relaxation Sweeps:** Instead of standard Bellman-Ford which loops up to `n - 1` times, we loop exactly **`k + 1` times**. Each loop iteration represents executing exactly one level of flight hops from our source.
3. **The Layer Pollution Guard:** To prevent an update in the current iteration from being used immediately to jump further along the same flight layer during a single loop cycle, we create a clone of our price array named `tempPrices` at the start of each iteration.
4. **Edge Relaxation:** For every flight edge linking `u` to `v` with a cost of `price`:
   * If city `u` was reachable in a *previous* level (`prices[u] != infinity`):
   * Relax the edge and update our temporary copy:
     ```text
     tempPrices[v] = min( tempPrices[v], prices[u] + price )
     ```
5. After the `k + 1` iterations complete, copy `tempPrices` back to `prices`. If `prices[dst]` remains infinity, return `-1`. Otherwise, return the calculated cost.

---

# State Definition

Let:

```text
int[] prices = new int[n];      // Array tracking the cheapest prices to each city
int[] tempPrices;               // Layer clone to prevent multi-hop edge relaxation leaks
```

---

# Recurrence / Relaxation Condition

```text
Loop k + 1 times:
    tempPrices = clone(prices)
    For each flight edge (u, v, price) in flights list:
        If prices[u] != INF:
            tempPrices[v] = min(tempPrices[v], prices[u] + price)
    prices = tempPrices
```

---

# Optimal Structural Graph Shortest-Path Solution

## Idea

By processing all flight edges precisely `k + 1` times and utilizing a cloned array backup block to contain level transitions, multi-edge updates are prevented from polluting the active step window. This completes the resource evaluation pass cleanly inside **O(k * E)** timelines.

---

## Java Code (Levelized Bellman-Ford/BFS Paradigm)

```java
import java.util.Arrays;

class Solution {

    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        // Step 1: Pre-allocate and initialize the price tracking matrix bounds
        int[] prices = new int[n];
        Arrays.fill(prices, Integer.MAX_VALUE);
        prices[src] = 0; // Baseline source initialization

        // Step 2: Run edge relaxation exactly k + 1 times (k stops implies at most k + 1 flights)
        for (int i = 0; i <= k; i++) {
            // Clone the prices array to isolate updates to the current level layer explicitly
            int[] tempPrices = Arrays.copyOf(prices, n);

            for (int[] flight : flights) {
                int u = flight[0];
                int v = flight[1];
                int price = flight[2];

                // Relax the edge only if the parent vertex u was reached in a previous hop level
                if (prices[u] != Integer.MAX_VALUE) {
                    if (prices[u] + price < tempPrices[v]) {
                        tempPrices[v] = prices[u] + price;
                    }
                }
            }

            // Sync the temporary level evaluations back into our master price table
            prices = tempPrices;
        }

        // Step 3: Check if the destination city was successfully reached within the stop constraints
        return (prices[dst] == Integer.MAX_VALUE) ? -1 : prices[dst];
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(k * E) -> Where k is the number of maximum stops allowed and E is the number of flight paths. The outer loop runs exactly k + 1 times, and the inner loop iterates through every single edge in the graph.
Space Complexity: O(n)     -> Space allocated to house the prices and tempPrices tracking arrays of size matching the city counts.
```

---

# Dry Run

## Input

```text
n = 3, flights = [[0,1,100],[1,2,100],[0,2,500]], src = 0, dst = 2, k = 0
```

---

### Execution Trace

```text
Initial State: prices = [0, INF, INF]
k = 0 implies we run the outer loop exactly 1 time (i = 0).

Iteration i = 0:
- tempPrices initialized to [0, INF, INF]
- Process: prices[0] != INF -> tempPrices[1] = min(INF, 0 + 100) = 100
- Process: prices[1] is INF (reads old array!) -> skip relaxation leak.
- Process: prices[0] != INF -> tempPrices[2] = min(INF, 0 + 500) = 500

Sync back: prices becomes [0, 100, 500]

Returns prices[2] = 500. (Since k=0 stops, the 2-flight path 0→1→2 is blocked).
```

---

# Visualization

## Levelized Edge Relaxation Boundaries

```text
                100                   100
           (0) ──────────────────► (1) ─────────────────► (2) [Dst]
            │                                              ▲
            └──────────────────────────────────────────────┘
                                  500
                                  
  Hop Limit 1 (k=0): Can only reach (2) via direct edge = 500
  Hop Limit 2 (k=1): Chain opens up connecting 100 + 100 = 200
```

---

# Graph Paradigm Pattern

## Identify State

```text
Step-constrained shortest path matrix optimization resolved via a level-cloned Bellman-Ford edge relaxation algorithm.
```

---

## Choices

```text
Bound loop execution strictly to match step limit capacities. Duplicate tracking bits at layer starts to block current-cycle multi-hop data bleeding.
```

---

## Key Takeaways

✅ Advanced graph-theory shortest path optimization with explicit combinatorial step limits.

✅ Using a **cloned temporary array** at every level pass is mandatory—it isolates the algorithm's field of view to exactly 1 hop per cycle, preventing Bellman-Ford from greedily expanding down an entire multi-edge path during a single loop execution pass.

✅ Direct real-world implementation engine for **airline routing logistics architectures**, budget constraint paths with transfer bounds, and freight delivery networks with maximum hub stop restrictions.

✅ Common Graph Design Pattern:

```text
Step-Constrained Shortest Path via Levelized Edge Relaxation Sweeps
```

### Similar Problems

- Network Delay Time (Standard Dijkstra - LeetCode 743)
- Path With Minimum Effort (LeetCode 1631)
- Bellman-Ford Algorithm (Standard template)
- Shortest Path Faster Algorithm (SPFA template)
