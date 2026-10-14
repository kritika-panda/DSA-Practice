# Delete and Earn

## Problem Statement

You are given an integer array `nums`.

You want to maximize the number of points earned by performing the following operation any number of times:

1. Pick any number `x` from the array.
2. Earn `x` points.
3. Delete every occurrence of:
   - `x - 1`
   - `x`
   - `x + 1`

Return the maximum points you can earn.

---

## Example 1

### Input

```text
nums = [3,4,2]
```

### Output

```text
6
```

### Explanation

Choose:

```text
2 → earn 2 points

Remaining:
[3,4]

Choose:
4 → earn 4 points
```

Total:

```text
2 + 4 = 6
```

---

## Example 2

### Input

```text
nums = [2,2,3,3,3,4]
```

### Output

```text
9
```

### Explanation

Choose all 3's:

```text
3 + 3 + 3 = 9
```

After choosing 3:

```text
2's are deleted
4's are deleted
```

Total:

```text
9
```

---

# Key Observation

Suppose:

```text
nums = [2,2,3,3,3,4]
```

Instead of thinking about individual elements, think about the total value contributed by each number.

```text
2 → 4 points
3 → 9 points
4 → 4 points
```

Create:

```text
value[2] = 4
value[3] = 9
value[4] = 4
```

Now notice:

```text
Taking 3 prevents taking 2 and 4
```

This becomes identical to the:

# House Robber Problem

```text
House Number: 2   3   4
Money       : 4   9   4
```

You cannot rob adjacent houses.

---

# Transformation to House Robber

## Step 1

Count the total points contributed by each number.

Example:

```text
nums = [2,2,3,3,3,4]
```

Build:

```text
points[2] = 4
points[3] = 9
points[4] = 4
```

---

## Step 2

Apply House Robber DP.

For every number:

```text
Take current number
or
Skip current number
```

---

# DP State

```text
dp[i]
=
Maximum points obtainable considering numbers
from 0 to i
```

---

# Recurrence

## Option 1: Skip

```text
dp[i-1]
```

## Option 2: Take

```text
points[i] + dp[i-2]
```

---

## Formula

```text
dp[i]
=
max(
    dp[i-1],
    points[i] + dp[i-2]
)
```

---

# Recursive Solution

## Java Code

```java
class Solution {

    private int solve(int index, int[] points) {

        if (index == 0) {
            return points[0];
        }

        if (index < 0) {
            return 0;
        }

        int take =
                points[index]
                + solve(index - 2, points);

        int skip =
                solve(index - 1, points);

        return Math.max(take, skip);
    }

    public int deleteAndEarn(int[] nums) {

        int maxNum = 0;

        for (int num : nums) {
            maxNum = Math.max(maxNum, num);
        }

        int[] points = new int[maxNum + 1];

        for (int num : nums) {
            points[num] += num;
        }

        return solve(maxNum, points);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(2^N)
Space Complexity: O(N)
```

Where `N = max(nums)`.

---

# Memoization (Top-Down DP)

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int index, int[] points, int[] dp) {

        if (index == 0) {
            return points[0];
        }

        if (index < 0) {
            return 0;
        }

        if (dp[index] != -1) {
            return dp[index];
        }

        int take =
                points[index]
                + solve(index - 2, points, dp);

        int skip =
                solve(index - 1, points, dp);

        return dp[index] =
                Math.max(take, skip);
    }

    public int deleteAndEarn(int[] nums) {

        int maxNum = 0;

        for (int num : nums) {
            maxNum = Math.max(maxNum, num);
        }

        int[] points = new int[maxNum + 1];

        for (int num : nums) {
            points[num] += num;
        }

        int[] dp = new int[maxNum + 1];
        Arrays.fill(dp, -1);

        return solve(maxNum, points, dp);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(MaxValue)
Space Complexity : O(MaxValue)
```

---

# Tabulation (Bottom-Up DP)

## DP Definition

```text
dp[i]
=
Maximum points obtainable considering values
from 0 to i
```

---

## Base Cases

```text
dp[0] = points[0]

dp[1] = Math.max(
            points[0],
            points[1]
        );
```

---

## Transition

```text
dp[i]
=
max(
    dp[i-1],
    points[i] + dp[i-2]
)
```

---

## Java Code

```java
class Solution {

    public int deleteAndEarn(int[] nums) {

        int maxNum = 0;

        for (int num : nums) {
            maxNum = Math.max(maxNum, num);
        }

        int[] points = new int[maxNum + 1];

        for (int num : nums) {
            points[num] += num;
        }

        int[] dp = new int[maxNum + 1];

        dp[0] = points[0];

        if (maxNum >= 1) {
            dp[1] = Math.max(points[0], points[1]);
        }

        for (int i = 2; i <= maxNum; i++) {

            dp[i] =
                    Math.max(
                            dp[i - 1],
                            points[i] + dp[i - 2]
                    );
        }

        return dp[maxNum];
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(MaxValue)
Space Complexity : O(MaxValue)
```

---

# Space Optimized Solution

## Observation

For:

```text
dp[i]
```

we only need:

```text
dp[i-1]
dp[i-2]
```

---

## Java Code

```java
class Solution {

    public int deleteAndEarn(int[] nums) {

        int maxNum = 0;

        for (int num : nums) {
            maxNum = Math.max(maxNum, num);
        }

        int[] points = new int[maxNum + 1];

        for (int num : nums) {
            points[num] += num;
        }

        int prev2 = points[0];

        if (maxNum == 0) {
            return prev2;
        }

        int prev1 =
                Math.max(
                        points[0],
                        points[1]
                );

        for (int i = 2; i <= maxNum; i++) {

            int current =
                    Math.max(
                            prev1,
                            points[i] + prev2
                    );

            prev2 = prev1;
            prev1 = current;
        }

        return prev1;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(MaxValue)
Space Complexity : O(1)
```

---

# Dry Run

## Input

```text
nums = [2,2,3,3,3,4]
```

Build Points Array:

```text
points[2] = 4
points[3] = 9
points[4] = 4
```

Visualization:

```text
Index : 0  1  2  3  4

Points: 0  0  4  9  4
```

---

### Initialize

```text
prev2 = 0

prev1 = 0
```

---

### i = 2

```text
current

=
max(
    0,
    4 + 0
)

= 4
```

---

### i = 3

```text
current

=
max(
    4,
    9 + 0
)

= 9
```

---

### i = 4

```text
current

=
max(
    9,
    4 + 4
)

= 9
```

---

### Answer

```text
9
```

---

# DP Pattern

## State

```text
dp[i]
=
Maximum points obtainable
using values 0...i
```

---

## Choices

```text
1. Skip current number

2. Take current number
```

---

## Recurrence

```text
dp[i]
=
max(
    dp[i-1],
    points[i] + dp[i-2]
)
```

---

## Why House Robber?

Because:

```text
Taking value i
prevents taking:

i - 1
i + 1
```

This is exactly equivalent to:

```text
Rob house i
cannot rob adjacent houses
```

---

# Pattern Recognition

Whenever you see:

```text
Take current item
and lose adjacent items
```

Think:

✅ House Robber Pattern

Examples:

- House Robber
- Delete and Earn
- Sticker Problems
- Maximum Sum Non-Adjacent Elements

---

# Key Takeaways

✅ Convert frequencies into total points

✅ Delete and Earn = House Robber after preprocessing

✅ Most important insight is the transformation

✅ Interview favorite because it looks different but uses a familiar DP pattern

### Similar Problems

- House Robber I
- House Robber II
- Maximum Sum of Non-Adjacent Elements
- Stickers to Rob Houses
- Maximum Earnings From Taxi
- Weighted Interval Scheduling
