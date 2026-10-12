# Predict the Winner

## Problem Statement

You are given an integer array `nums`. Two players are playing a game with this array: player 1 and player 2.

Player 1 and player 2 take turns, with player 1 starting first. Both players start with a score of `0`. On each turn, the player takes one of the numbers from either the **start** or the **end** of the array, which reduces the size of the array by `1`. The player adds the chosen number to their score. The game ends when there are no more elements in the array.

Return `true` if Player 1 can win the game. If the scores of both players are equal, player 1 is still the winner, so you should also return `true`. You may assume that both players play optimally.

The overall runtime complexity must be:

```text
O(N²)
```

*(where N is the total number of elements inside the nums array)*

---

## Example 1

### Input

```text
nums = [1, 5, 2]
```

### Output

```text
false
```

### Explanation

```text
Initially, player 1 can choose between 1 and 2. 
If he chooses 2, the array becomes. Then player 2 can choose 5, and the array becomes. 
Finally, player 1 chooses 1. 
Player 1's score: 2 + 1 = 3. Player 2's score: 5. 
Since 3 < 5, player 1 loses, so return false.
```

---

## Example 2

### Input

```text
nums = [1, 5, 233, 7]
```

### Output

```text
true
```

### Explanation

```text
Player 1 chooses 1. The array becomes.
Player 2 can choose between 5 and 7. No matter what player 2 chooses, player 1 can choose 233 on the next turn.
Hence, player 1 can score higher than player 2, so return true.
```

---

# Intuition

Instead of keeping track of both players' absolute scores separately, we can maintain a **relative score net difference** format:
```text
Net Score = (Player 1 Score) - (Player 2 Score)
```
*   Player 1 wants to **maximize** this net difference.
*   Player 2 wants to **minimize** this net difference (which is equivalent to maximizing their own relative net benefit when view shifted).

At any step where an array segment `nums[i...j]` is available, the active player can pick either:
1.  The left boundary element `nums[i]`. The next player then gets the remaining segment `nums[i+1...j]`.
2.  The right boundary element `nums[j]`. The next player then gets the remaining segment `nums[i...j-1]`.

Since both players play optimally, whatever the next player gains must be subtracted from the current player's relative margin. This creates a standard Minimax game optimization structure.

---

# State Definition

Let:

```text
f(i, j)
=
The maximum relative net score difference the current player 
can obtain from the sub-array slice nums[i...j]
```

---

# Recurrence

```text
f(i, j) = max( nums[i] - f(i + 1, j), nums[j] - f(i, j - 1) )
```

---

# Recursive Solution

## Base Cases

### Single Element Remaining

```text
i == j

return nums[i]
```

---

## Java Code

```java
class Solution {

    private int solve(int i, int j, int[] nums) {
        if (i == j) {
            return nums[i];
        }

        // Choice 1: Take the left element, subtract subsequent optimal response
        int pickLeft = nums[i] - solve(i + 1, j, nums);

        // Choice 2: Take the right element, subtract subsequent optimal response
        int pickRight = nums[j] - solve(i, j - 1, nums);

        return Math.max(pickLeft, pickRight);
    }

    public boolean predictTheWinner(int[] nums) {
        if (nums == null || nums.length == 0) return true;
        // Player 1 wins if the max relative score difference is >= 0
        return solve(0, nums.length - 1, nums) >= 0;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(2^N) -> Exponential game tree branching
Space Complexity: O(N) recursion stack memory frames
```

---

# Memoization (Top-Down DP)

## Idea

Store intermediate grid coordination values inside a 2D cache matrix array `dp[n][n]` to bypass evaluating overlapping interval segments.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int i, int j, int[] nums, int[][] dp) {
        if (i == j) {
            return nums[i];
        }

        if (dp[i][j] != Integer.MIN_VALUE) {
            return dp[i][j];
        }

        int pickLeft = nums[i] - solve(i + 1, j, nums, dp);
        int pickRight = nums[j] - solve(i, j - 1, nums, dp);

        return dp[i][j] = Math.max(pickLeft, pickRight);
    }

    public boolean predictTheWinner(int[] nums) {
        if (nums == null || nums.length == 0) return true;
        int n = nums.length;
        
        int[][] dp = new int[n][n];
        for (int[] row : dp) {
            Arrays.fill(row, Integer.MIN_VALUE);
        }

        return solve(0, n - 1, nums, dp) >= 0;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N²)
Space Complexity : O(N²) + O(N) recursion stack
```

---

# Tabulation (Bottom-Up DP)

## DP Definition

```text
dp[i][j] = max relative score difference achievable from sub-array nums[i...j]
```

---

## Base Case

Diagonal elements represent single card choices:
```text
dp[i][i] = nums[i]
```

---

## Transition

We must iterate from the bottom row up, filling columns from left to right to respect the sub-interval dependency structures:

```text
dp[i][j] = max( nums[i] - dp[i + 1][j], nums[j] - dp[i][j - 1] )
```

---

## Java Code

```java
class Solution {

    public boolean predictTheWinner(int[] nums) {
        if (nums == null || nums.length == 0) return true;
        int n = nums.length;
        
        int[][] dp = new int[n][n];

        // Base case setup
        for (int i = 0; i < n; i++) {
            dp[i][i] = nums[i];
        }

        // Build states from bottom rows to top rows
        for (int i = n - 2; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                int pickLeft = nums[i] - dp[i + 1][j];
                int pickRight = nums[j] - dp[i][j - 1];
                dp[i][j] = Math.max(pickLeft, pickRight);
            }
        }

        return dp[n - 1][n - 1] >= 0;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N²)
Space Complexity : O(N²)
```

---

# Space Optimized Solution

## Observation

To compute rows, each row state `dp[i]` only depends on values from the current row `dp[i]` and the row immediately below it `dp[i+1]`. Therefore, the 2D matrix can be compressed down to a single 1D rolling array `dp` of size `n` which updates iteratively in-place.

---

## Java Code

```java
class Solution {

    public boolean predictTheWinner(int[] nums) {
        if (nums == null || nums.length == 0) return true;
        int n = nums.length;
        
        // 1D rolling array tracking active interval metrics
        int[] dp = new int[n];

        // Base Case setup
        for (int i = 0; i < n; i++) {
            dp[i] = nums[i];
        }

        // Collapse matrix steps backward in-place
        for (int i = n - 2; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                // dp[j] (new) = max(nums[i] - dp[j] (old from bottom), nums[j] - dp[j-1] (from left))
                dp[j] = Math.max(nums[i] - dp[j], nums[j] - dp[j - 1]);
            }
        }

        return dp[n - 1] >= 0;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N²)
Space Complexity : O(N) -> Compressed 1D lookup buffer array size
```

---

# Dry Run

```text
nums = [1, 5, 2]
```

### Initial State (Single element bases)

```text
dp = [1, 5, 2]
```

### i = 1 (Row 1 Loop)

```text
j = 2: dp[2] = max(nums[1] - dp[2], nums[2] - dp[1]) 
             = max(5 - 2, 2 - 5) = max(3, -3) = 3
dp updates to: [1, 5, 3]
```

### i = 0 (Row 0 Loop)

```text
j = 1: dp[1] = max(nums[0] - dp[1], nums[1] - dp[0])
             = max(1 - 5, 5 - 1) = max(-4, 4) = 4

j = 2: dp[2] = max(nums[0] - dp[2], nums[2] - dp[1])
             = max(1 - 3, 2 - 4) = max(-2, -2) = -2
```

### Final Answer

```text
dp[2] = -2. Since -2 < 0, returns false.
```

---

# DP Pattern

## Identify State

```text
f(i, j)
=
Maximum net relative score margin achievable from the interval slice nums[i...j]
```

---

## Choices

```text
1. Pick the leftmost card nums[i] and hand over the remaining range [i + 1, j]
2. Pick the rightmost card nums[j] and hand over the remaining range [i, j - 1]
```

---

## Recurrence

```text
f(i, j) = max( nums[i] - f(i + 1, j), nums[j] - f(i, j - 1) )
```

---

## Base Cases

```text
f(i, i) = nums[i]
```

---

# Key Takeaways
✅ Prototypical Game Theory Interval Minimax optimization puzzle.
✅ Keeping tracking states structured as a relative net delta score removes the requirement to allocate additional state coordinates tracking whose turn it is.
✅ Space compression structures flatten the 2D range matrix layout down to a single 1D rolling array layer O(N) with perfect accuracy.
✅ Common Interview Pattern:
text Interval Selection Optimization with Alternating Opponent Minimax Choices 

# Similar Problems

• Stone Game (LeetCode 877)
• Stone Game II (LeetCode 1140)
• Burst Balloons (LeetCode 312)
• Guess Number Higher or Lower II (LeetCode 375)
