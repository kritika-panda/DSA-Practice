# Maximum Vacation Days (LeetCode 568)

## Problem Statement

LeetCode wants to give one of its best employees the option to travel among `n` cities to maximize the total number of vacation days. The cities are labeled from `0` to `n - 1`. 

The employee can stay in a city for a week and then travel to another city on Monday morning. You are given a 2D binary matrix `flights` of size `n x n`, where `flights[i][j] = 1` indicates a direct flight exists from city `i` to city `j`, and `flights[i][j] = 0` indicates no flight exists. Note that `flights[i][i] = 0` natively, but the employee can always choose to stay in their current city without taking a flight.

You are also given a 2D integer matrix `days` of size `n x k`, where `days[i][j]` represents the maximum number of vacation days the employee can get in city `i` during week `j`.

Initially, the employee starts in **city 0** on Monday morning of **week 0**. Return *the maximum number of vacation days the employee can get during the `k` weeks*.

---

## Examples

### Example 1

```java
Input: flights = [[0,1,1],[1,0,1],[1,1,0]], days = [[1,3,1],[6,0,3],[3,3,3]]
Output: 12
```

### Explanation

```text
Cities = 3, Weeks = 3. Initial start = City 0.
An optimal schedule to accumulate 12 vacation days is:
- Week 0: Stay in City 0 → Get days[0][0] = 1 vacation day.
- Week 1: Fly from City 0 to City 1 → Get days[1][1] = 3 vacation days.
- Week 2: Fly from City 1 to City 2 → Get days[2][2] = 3 vacation days.
Wait, let's look at another path configuration:
- Week 0: Fly from City 0 to City 1 → Get days[1][0] = 6 vacation days.
- Week 1: Stay in City 1 → Get days[1][1] = 3 vacation days.
- Week 2: Fly from City 1 to City 2 → Get days[2][2] = 3 vacation days.
Total vacation days = 6 + 3 + 3 = 12. Maximum vacation days = 12.
```

---

### Example 2

```java
Input: flights = [[0,1,1],[1,0,1],[1,1,0]], days = [[7,0,0],[0,7,0],[0,0,7]]
Output: 21
```

### Explanation

```text
An optimal schedule to accumulate 21 vacation days is:
- Week 0: Stay in City 0 → Get days[0][0] = 7 vacation days.
- Week 1: Fly from City 0 to City 1 → Get days[1][1] = 7 vacation days.
- Week 2: Fly from City 1 to City 2 → Get days[2][2] = 7 vacation days.
Total vacation days = 7 + 7 + 7 = 21.
```

---

# Intuition

This problem can be effectively modeled as a **Layered State-Space Dynamic Programming (DP)** problem. At the beginning of any week `w`, the maximum vacation days we can achieve depends strictly on which city `c` we are currently residing in and the choices we made in the previous weeks.

Let's break down the choices for week `w`:
1. **Fly to a new city `nextCity`:** We can transition from our current city `c` to a destination city `nextCity` if a flight exists (`flights[c][nextCity] == 1`).
2. **Stay in the same city:** We can choose to remain in city `c` for week `w`.

Instead of thinking about where we can go *forward* from a city, it is computationally cleaner to solve this **backward** (from week `k-1` down to week `0`) or **forward using incoming links** to track the optimal subproblem states.

---

# State Definition

Let `dp[w][c]` be the maximum vacation days obtainable from week `w` to week `k - 1` given that the employee is at city `c` at the start of week `w`.
*   `w`: The current week index (from `0` to `k - 1`).
*   `c`: The current city index (from `0` to `n - 1`).

---

# Recurrence Relation

To compute `dp[w][c]`, we look ahead to the choices available for week `w + 1`:

```text
dp[w][c] = days[c][w] + max( dp[w + 1][nextCity] ) 
for all nextCity where (flights[c][nextCity] == 1 OR nextCity == c)
```

---

# Base Cases

After the final week (`w == k`), no more vacation days can be collected:
```text
dp[k][c] = 0 for all cities c
```

---

# Approach: Space-Optimized Bottom-Up Tabulation

## Algorithm

1. Initialize a 1D DP array `dp[n]` representing the maximum vacation days obtainable for each city.
2. **Base Case Setup (Week k):** Fill the `dp` array with `0` initially since no days are available after the schedule concludes.
3. **Iterative Layer Construction:** Loop backward through the weeks `w` from `k - 1` down to `0`:
   - Create a temporary array `nextDp[n]` initialized to a minimum sentinel value (e.g., `-1`) to represent unreachable states.
   - Loop through each possible current city `c` from `0` to `n - 1`:
     - Loop through each possible destination city `nextCity` from `0` to `n - 1`:
       - Check if staying or flying is valid: `nextCity == c || flights[c][nextCity] == 1`.
       - If valid, update the temporary buffer greedily:
         ```text
         nextDp[c] = max(nextDp[c], days[c][w] + dp[nextCity])
         ```
   - Shift the week layer window forward: `dp = nextDp`.
4. The answer will be located at `dp[0]` because the employee is forced to start at city 0 on week 0.

---

# Java Solution

```java
import java.util.Arrays;

class Solution {
    public int maxVacationDays(int[][] flights, int[][] days) {
        int n = flights.length;
        int k = days[0].length;

        // dp[c] stores the max vacation days from the current week to the end if we start at city c
        int[] dp = new int[n];

        // Iterating backwards from the last week down to week 0
        for (int w = k - 1; w >= 0; w--) {
            int[] nextDp = new int[n];
            Arrays.fill(nextDp, -1); // -1 represents an unreached/invalid state configuration

            for (int c = 0; c < n; c++) {
                for (int nextCity = 0; nextCity < n; nextCity++) {
                    // Check if we can stay in the same city or if a valid flight path exists
                    if (c == nextCity || flights[c][nextCity] == 1) {
                        if (dp[nextCity] != -1) {
                            int totalDays = days[c][w] + dp[nextCity];
                            nextDp[c] = Math.max(nextDp[c], totalDays);
                        }
                    }
                }
            }
            // Move the rolling DP window array forward to the preceding week layer
            dp = nextDp;
        }

        return dp[0];
    }
}
```

---

## Complexity Analysis

Let:
*   N: Total number of cities (`n`).
*   K: Total number of weeks (`k`).

### Time Complexity
The algorithm processes exactly K weeks. Inside each week layer, it sweeps through all combinations of current cities and destination cities using a nested N × N loop. This keeps execution runtime bounded inside predictable polynomial dimensions:
```text
Time Complexity: O(K × N²)
```

### Space Complexity
By implementing a space-optimized space layout where the previous week's array `nextDp` overwrites the active tracking matrix layer `dp`, we drop the need to allocate a full 2D K × N matrix container. The memory footprint stays locked at constant dimensions relative to city configurations:
```text
Space Complexity: O(N)
```

---

# Key Pattern
This problem belongs to the **Layered State-Space Graph Transition** template inside dynamic programming.

Common indicators:
- Finding a maximum or minimum score matrix over fixed turns or clock cycles.
- Grid or graph-based navigation leveraging a secondary connectivity constraint network (`flights`).
- Layered dependencies where step `w` maps its outputs directly from the computed values of step `w + 1`.

---

# Similar Problems
1. Cheapest Flights Within K Stops (LeetCode 787)
2. Knight Probability in Chessboard (LeetCode 688)
3. Out of Boundary Paths (LeetCode 576)
4. Maximum Path Quality of a Graph (LeetCode 2065)

---

# Takeaway
```text
Model turn-based schedule tracking using a rolling 1D state array.

Traverse the calendar weeks backwards to build state dependencies from the terminal limits.

Filter out invalid path transitions using a sentinel flag matrix to ensure choices are realistic.
```

**Pattern:** Layered State-Space Graph Transition DP
