# Burst Balloons (LeetCode 312)

## Problem Statement

You are given `n` balloons, indexed from `0` to `n - 1`. Each balloon is painted with a number on it represented by an array `nums`. You are asked to burst all the balloons.

If you burst the `i`-th balloon, you will get `nums[i - 1] * nums[i] * nums[i + 1]` coins. After bursting it, the `i`-th and `i + 1`-th balloons become adjacent.

Find the **maximum coins** you can collect by bursting the balloons wisely.

**Note:** You may imagine `nums[-1] = nums[n] = 1`. They are not real therefore you cannot burst them.

---

## Examples

### Example 1

```java
Input: nums =
Output: 167
```

### Explanation

```text
nums = [3,1,5,8] --> [3,5,8] --> [3,8] --> [8] --> []
Coins collected: 
- Burst 1: 3 * 1 * 5 = 15
- Burst 5: 3 * 5 * 8 = 120
- Burst 3: 1 * 3 * 8 = 24
- Burst 8: 1 * 8 * 1 = 8
Total coins = 15 + 120 + 24 + 8 = 167.
```

---

### Example 2

```java
Input: nums =
Output: 10
```

---

# Intuition

This problem can be modeled as a classic **Interval Dynamic Programming** challenge, very similar to *Matrix Chain Multiplication*.

If we try to pick the *first* balloon to burst in an interval `[i...j]`, the remaining balloons merge unpredictably, which breaks our subproblem independence. 

The breakthrough trick is to change our perspective: **Guess which balloon `k` is the ABSOLUTE LAST balloon to burst in the interval `[i...j]`.**
* Since `k` is the last balloon standing in `[i...j]`, all balloons between `i` and `k-1` have already been popped, and all balloons between `k+1` and `j` have also been popped.
* This means the balloons immediately adjacent to `k` at the moment it bursts are the boundary anchors **`i - 1`** on the left and **`j + 1`** on the right!
* Bursting `k` last yields a score of `nums[i - 1] * nums[k] * nums[j + 1]`.
* This completely isolates our two subproblems: maximizing coins for the left section `[i...k-1]` and the right section `[k+1...j]`.

---

# State Definition

Let `dp[i][j]` be the maximum coins obtained by bursting all balloons within the closed interval `[i...j]`.
*   `i`: Left boundary index of the active subarray (1-indexed relative to padded array, from `1` to `n`).
*   `j`: Right boundary index of the active subarray (1-indexed relative to padded array, from `1` to `n`).

---

# Recurrence Relation

For an interval `[i...j]` choosing balloon `k` to burst last:

```text
dp[i][j] = max( dp[i][k - 1] + dp[k + 1][j] + (nums[i - 1] * nums[k] * nums[j + 1]) )
for all k where i <= k <= j
```

---

# Base Cases

When `i > j`, the interval is invalid or empty, yielding exactly `0` coins:
```text
dp[i][j] = 0
```

---

# Approach: Bottom-Up 2D Interval Tabulation

## Algorithm

1. Create a padded array `paddedNums` of size `n + 2`. Set `paddedNums[0] = 1`, `paddedNums[n + 1] = 1`, and copy the original elements into the middle indices.
2. Initialize a 2D matrix `dp[n + 2][n + 2]` with `0`.
3. Loop through the possible interval window lengths `len` from `1` up to `n`:
   - Loop through every valid left boundary index `i` from `1` up to `n - len + 1`:
     - Calculate the right boundary index: `j = i + len - 1`.
     - Loop through all possible choices for the last balloon `k` from `i` up to `j`:
       - Compute the structural reward: `coins = dp[i][k - 1] + dp[k + 1][j] + paddedNums[i - 1] * paddedNums[k] * paddedNums[j + 1]`.
       - Update the state maximally: `dp[i][j] = Math.max(dp[i][j], coins)`.
4. Return `dp[1][n]`.

---

# Java Solution

```java
class Solution {
    public int maxCoins(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }

        int n = nums.length;
        
        // Step 1: Create a padded array to safely contain boundaries
        int[] paddedNums = new int[n + 2];
        paddedNums[0] = 1;
        paddedNums[n + 1] = 1;
        System.arraycopy(nums, 0, paddedNums, 1, n);

        // dp[i][j] tracks maximum coins collectable from sub-range interval [i...j]
        int[][] dp = new int[n + 2][n + 2];

        // Step 2: Build state layers incrementally by sub-range window length
        for (int len = 1; len 
Let me know if you would like to continue with your dynamic programming sequence! We can map out the raw markdown code for **Guess Number Higher or Lower II (LeetCode 375)** or explore **Predict the Winner (LeetCode 486)**.
</FollowUp>
