# Dice Roll Simulation (LeetCode 1223)

## Problem Statement

A die simulator generates a random number from `1` to `6` for each roll. You introduced a constraint to the generator such that it cannot roll the number `i` more than `rollMax[i]` times **consecutively**.

Given an array of integers `rollMax` of length `6` and an integer `n`, return *the number of distinct sequences of length `n` that can be obtained*.

Since the answer may be large, return it **modulo 10⁹ + 7**.

Two sequences are considered distinct if at least one element differs from each other.

---

## Examples

### Example 1

```java
Input: n = 2, rollMax = [1,1,2,2,2,3]
Output: 34
```

### Explanation

```text
There are 2 rolls. Each die face has a maximum consecutive limit:
rollMax = [1, 1, 2, 2, 2, 3]

Out of the 6 * 6 = 36 total permutations, 2 combinations are invalid:
- [1, 1] is invalid because face 1 can appear consecutively at most 1 time.
- [2, 2] is invalid because face 2 can appear consecutively at most 1 time.

Distinct valid sequences = 36 - 2 = 34
```

---

### Example 2

```java
Input: n = 2, rollMax = [1,1,1,1,1,1]
Output: 30
```

### Explanation

```text
All consecutive pairings of identical faces ([1,1], [2,2], ..., [6,6]) are blocked.
Remaining distinct sequences = 36 - 6 = 30
```

---

### Example 3

```java
Input: n = 3, rollMax = [1,1,1,2,2,3]
Output: 181
```

---

# Intuition

This problem can be effectively modeled as a **State-Space Dynamic Programming (DP)** problem. When making a choice for the current roll, the validity of the chosen face depends strictly on the face chosen in the *immediate previous roll* and how many times that face has *already appeared consecutively*.

Let's break down the decisions for the current roll:
1. **Choose a different face:** If the face we pick for the current step is different from the previous face, its consecutive count resets back to `1`. This choice is always valid because a change of face breaks any active streak.
2. **Choose the same face:** If we pick the exact same face as the previous step, its consecutive count increments by `1`. This choice is only valid if the new count does not overshoot the maximum limit defined in `rollMax[face]`.

---

# State Definition

To capture all the dependent conditions, we define a 3D state tracking cache `dp[i][lastFace][consecutiveCount]`:
*   `i`: The current roll number index (from `1` to `n`).
*   `lastFace`: The face value selected in the immediate previous step (0-indexed, from `0` to `5`).
*   `consecutiveCount`: The consecutive streak length of `lastFace` up to the current roll.

---

# Recurrence Relation

To find `dp[i][face][count]`, we look back at the states of the previous roll `i - 1`:

```text
For the current roll choosing 'face' with a streak 'count':

1. If count == 1 (Resetting the streak):
   We must sum up all valid states from the previous roll where the face was DIFFERENT.
   dp[i][face][1] = sum( dp[i-1][prevFace][prevCount] ) for all prevFace != face

2. If count > 1 (Continuing the streak):
   The face must match the previous face, and the consecutive count builds up.
   dp[i][face][count] = dp[i-1][face][count - 1] (where count <= rollMax[face])
```

---

# Approach: Bottom-Up 3D Dynamic Programming

## Algorithm

1. Initialize a 3D DP array `dp[n + 1][6][16]` filled with `0` (since max consecutive limit ≤ 15).
2. **Base Case Setup (Roll 1):** For the first roll, each of the 6 faces can appear exactly once with a consecutive streak of `1`.
   ```text
   dp[1][face][1] = 1 for face from 0 to 5
   ```
3. **Iterative State Expansion:** Loop through each roll `i` from `2` up to `n`:
   - Loop through each possible active face `face` from `0` to `5`.
   - Loop through each possible value for the *next* face `nextFace` from `0` to `5`.
   - Loop through each valid historical streak count `count` from `1` to `rollMax[face]`:
     - **Case A (Different Face):** If `nextFace != face`, we reset the streak to 1:
       ```text
       dp[i][nextFace][1] = (dp[i][nextFace][1] + dp[i-1][face][count]) % MOD
       ```
     - **Case B (Same Face):** If `nextFace == face` and `count + 1 <= rollMax[face]`, we continue the streak:
       ```text
       dp[i][face][count + 1] = (dp[i][face][count + 1] + dp[i-1][face][count]) % MOD
       ```
4. **Final Reduction Pass:** Sum up all valid states `dp[n][face][count]` across all faces and counts to get the total number of valid sequences.

---

# Java Solution

```java
class Solution {
    public int dieSimulator(int n, int[] rollMax) {
        int MOD = 1000000007;
        
        // 3D array: dp[roll_index][last_face][consecutive_count]
        // Max limit of rollMax[i] is 15, so size 16 for consecutive count is safe
        int[][][] dp = new int[n + 1][6][16];

        // Base case setup: for the 1st roll, each face can appear with a streak of 1
        for (int face = 0; face < 6; face++) {
            dp[1][face][1] = 1;
        }

        // Build states sequentially from roll 2 to n
        for (int i = 2; i <= n; i++) {
            for (int face = 0; face < 6; face++) {
                for (int count = 1; count <= rollMax[face]; count++) {
                    if (dp[i - 1][face][count] == 0) {
                        continue;
                    }

                    // Look ahead and evaluate options for the next roll
                    for (int nextFace = 0; nextFace < 6; nextFace++) {
                        if (nextFace != face) {
                            // Choice 1: Different face selected, streak resets back to 1
                            dp[i][nextFace][1] = (dp[i][nextFace][1] + dp[i - 1][face][count]) % MOD;
                        } else if (count + 1 <= rollMax[face]) {
                            // Choice 2: Same face selected, streak increments safely
                            dp[i][face][count + 1] = (dp[i][face][count + 1] + dp[i - 1][face][count]) % MOD;
                        }
                    }
                }
            }
        }

        // Aggregate all valid state combinations of the n-th layer
        long totalSequences = 0;
        for (int face = 0; face < 6; face++) {
            for (int count = 1; count <= rollMax[face]; count++) {
                totalSequences = (totalSequences + dp[n][face][count]) % MOD;
            }
        }

        return (int) totalSequences;
    }
}
```

---

# Complexity Analysis

Let:
*   N: Total number of rolls.
*   F: Number of die faces (fixed constant of 6).
*   M: Maximum consecutive roll limit (bounded constant ≤ 15).

### Time Complexity
The nested loop architecture processes N rolls. Inside, it scales across F × M × F states. Since F and M are small, fixed upper-bound constants (6 × 15 × 6 = 540 constant steps per layer), the overall runtime scales strictly linearly:
```text
O(N)
```

### Space Complexity
The 3D array uses storage proportional to N × 6 × 16:
```text
O(N)
```
*(Optimization Note: Since layer `i` depends exclusively on values from layer `i - 1`, the row array can be compressed down to a rolling 2D cache layer matrix of size 6 × 16, reducing the space footprint down to an absolute constant O(1)).*

---

# Key Pattern
This problem belongs to the **Multi-Dimensional State-Space Dynamic Programming** template. 

Common indicators:
- Multi-step choices where current validity relies on tracking past selections.
- Multi-parameter state states (Step index + Last Choice + Streak/Frequency).
- Combinatorial sequence counting under progressive constraint checks.

---

# Similar Problems
1. Knight Dialer (LeetCode 935)
2. Student Attendance Record II (LeetCode 552)
3. Numbers With Same Consecutive Differences (LeetCode 967)
4. Out of Boundary Paths (LeetCode 576)

---

# Takeaway
```text
Define states using three dimensions: index, previous choice, and streak length.

Reset the streak count to 1 whenever a different face option breaks the old line.

Filter out and drop lookahead options that violate maximum consecutive constraints.
```

**Pattern:** Multi-Dimensional State-Space Dynamic Programming
