# Stone Game VII (LeetCode 1690)

## Problem Statement

Alice and Bob take turns playing a game, with **Alice starting first**.

There are `n` stones arranged in a row. On each turn, a player removes either the **first** or the **last** stone from the row. This player receives points equal to the **sum of the remaining stones** in the row. The game ends when no stones remain.

Bob's goal is to **minimize the score difference**, while Alice's goal is to **maximize the score difference**. Assuming both players play optimally, return *the **difference in score** between Alice and Bob*.

---

## Examples

### Example 1

```java
Input: stones =
Output: 6
```

### Explanation

```text
- Alice removes 5, score = 3 + 1 + 4 + 2 = 10. Row: (Alice: 10, Bob: 0)
- Bob removes 2, score = 3 + 1 + 4 = 8.    Row:    (Alice: 10, Bob: 8)
- Alice removes 3, score = 1 + 4 = 5.        Row:       (Alice: 15, Bob: 8)
- Bob removes 1, score = 4.                Row:          (Alice: 15, Bob: 12)
- Alice removes 4, score = 0.                Row: []           (Alice: 15, Bob: 12)
The optimal score difference is 15 - 12 = 6.
```

---

### Example 2

```java
Input: stones =
Output: 122
```

---

# Intuition

This problem can be classified as a classic **MiniMax Game Theory** puzzle solved using **Interval Dynamic Programming**. 

Instead of tracking Alice and Bob's individual score parameters independently, we can compute the **net relative margin** a player can gain over their opponent given the active subarray boundary `[i...j]`.

When a player faces the array interval `[i...j]`, they have two legal choices:
1. **Remove the leftmost stone `stones[i]`:** The player scores the sum of the remaining interval `[i+1...j]`. However, the opponent gets to play optimally on that remaining interval, reducing our net gain by `dfs(i + 1, j)`.
   \[\text{Net Gain}_A = \text{Sum}(i+1, j) - \text{dfs}(i + 1, j)\]
2. **Remove the rightmost stone `stones[j]`:** The player scores the sum of the remaining interval `[i...j-1]`. The opponent then minimizes our subsequent margin, reducing our net gain by `dfs(i, j - 1)`.
   \[\text{Net Gain}_B = \text{Sum}(i, j-1) - \text{dfs}(i, j - 1)\]

An optimal player will greedily pick the maximum of these two net choices.

---

# State Definition

Let `dp[i][j]` be the maximum net score difference the current player can secure over their opponent from the subarray segment `stones[i...j]`.
*   `i`: Left boundary index of the active stone interval (from `0` to `n - 1`).
*   `j`: Right boundary index of the active stone interval (from `0` to `n - 1`).

---

# Recurrence Relation

To avoid calculating subarray sums inside our loops repeatedly, we can compute the total interval sum dynamically from a **Prefix Sum Array** `pref`:
\[\text{Sum}(x, y) = \text{pref}[y + 1] - \text{pref}[x]\]

```text
dp[i][j] = max(
    Sum(i + 1, j) - dp[i + 1][j], // Choose left stone
    Sum(i, j - 1) - dp[i][j - 1]  // Choose right stone
)
```

---

# Base Cases

When `i == j` (only 1 stone remains), removing it leaves an empty array with a remaining sum of `0`. Thus, no score can be earned:
```text
dp[i][i] = 0 for all 0 <= i < n
```

---

# Approach: Bottom-Up 2D Interval Tabulation

## Algorithm

1. Precompute a `pref` array of size `n + 1` to enable O(1) range sum lookups.
2. Initialize a 2D matrix `dp[n][n]` to `0`.
3. Loop through the possible interval window lengths `len` from `2` up to `n`:
   - Loop through every valid left boundary index `i` from `0` up to `n - len`:
     - Calculate the right boundary index: `j = i + len - 1`.
     - Calculate the remaining sum if `i` is removed: `sumLeft = pref[j + 1] - pref[i + 1]`.
     - Calculate the remaining sum if `j` is removed: `sumRight = pref[j] - pref[i]`.
     - Apply the MiniMax equation: `dp[i][j] = Math.max(sumLeft - dp[i + 1][j], sumRight - dp[i][j - 1])`.
4. Return `dp[0][n - 1]`.

---

# Java Solution

```java
class Solution {
    public int stoneGameVII(int[] stones) {
        int n = stones.length;
        
        // Step 1: Compute Prefix Sums for O(1) interval summation lookups
        int[] pref = new int[n + 1];
        for (int i = 0; i < n; i++) {
            pref[i + 1] = pref[i] + stones[i];
        }

        // dp[i][j] tracks the maximum relative margin gain from interval [i...j]
        int[][] dp = new int[n][n];

        // Step 2: Build state layers incrementally by interval window length
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;

                // Option 1: Remove the leftmost stone at index i
                int sumIfRemoveLeft = pref[j + 1] - pref[i + 1];
                int gainOptionLeft = sumIfRemoveLeft - dp[i + 1][j];

                // Option 2: Remove the rightmost stone at index j
                int sumIfRemoveRight = pref[j] - pref[i];
                int gainOptionRight = sumIfRemoveRight - dp[i][j - 1];

                // Greedily pick the choice that maximizes the relative difference
                dp[i][j] = Math.max(gainOptionLeft, gainOptionRight);
            }
        }

        // Return the final maximized margin score spanning the complete board row
        return dp[0][n - 1];
    }
}
```

---

## Complexity Analysis

### Time Complexity
The algorithm relies on two nested loops: an outer loop spanning the interval length N and an inner loop processing indices up to N. Inside, range sums resolve instantly using the prefix sum cache. This bounds the execution runtime quadratically:
```text
Time Complexity: O(N²)
```

### Space Complexity
The tabulation state matrix requires an allocation array of size N × N:
```text
Space Complexity: O(N²)
```
*(Optimization Note: Since interval layer `len` references updates solely from layer `len - 1`, the grid matrix can be compressed down to a rolling 1D array row of size N, optimizing the runtime space to O(N)).*

---

# Key Pattern
This problem belongs to the **Interval Game Theory / MiniMax Tabulation** template inside dynamic programming.

Common indicators:
- Turn-based two-player competitive matching where players choose from the edges of a subarray (`left` or `right`).
- Scores earned depend on aggregating sub-segments of the remaining elements.
- State evaluation tracks a unified relative margin (`score delta`) rather than absolute totals.

---

# Similar Problems
1. Stone Game (LeetCode 877)
2. Predict the Winner (LeetCode 486)
3. Stone Game II (LeetCode 1140)
4. Burst Balloons (LeetCode 312)

---

# Takeaway
```text
Reframe competitive two-player scores as a single relative difference margin.

Use Prefix Sum arrays to calculate range parameters in O(1) time.

Build the DP tabulation table incrementally by expanding the interval window length.
```

**Pattern:** Interval Game Theory / MiniMax Tabulation DP
