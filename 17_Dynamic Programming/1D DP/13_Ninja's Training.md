# Ninja's Training

## Problem Statement

A ninja is planning a `N` days long training schedule. Each day, the ninja can perform any one of three activities: **Running**, **Fighting**, or **Learning Practice**. Each activity on a specific day yields a certain merit point.

The merit points gained by the ninja for each activity on each day are given in a 2D array `points[][]` of size `N x 3`.

However, the ninja cannot perform the **same activity on two consecutive days**.

Find the maximum total merit points the ninja can earn over `N` days.

---

## Example 1

### Input

```text
N = 3
points = [[1, 2, 5], [3, 1, 1], [3, 3, 3]]
```

### Output

```text
11
```

### Explanation

```text
Day 0: Choose Activity 2 (Points = 5)
Day 1: Choose Activity 0 (Points = 3)
Day 2: Choose Activity 1 (Points = 3)

Total: 5 + 3 + 3 = 11
```

---

## Example 2

### Input

```text
N = 3
points = [[10, 40, 70], [20, 50, 80], [30, 60, 90]]
```

### Output

```text
210
```

### Explanation

```text
Day 0: Choose Activity 2 (Points = 70)
Day 1: Choose Activity 1 (Points = 50)
Day 2: Choose Activity 2 (Points = 90)

Total: 70 + 50 + 90 = 210
```

---

# Intuition

At every day, we have three choices, but our choice is constrained by what we performed on the next (or previous) day.

### Choice 1

Perform Activity 0 (Running).

Allowed only if the next day's activity was **not** 0.

---

### Choice 2

Perform Activity 1 (Fighting).

Allowed only if the next day's activity was **not** 1.

---

### Choice 3

Perform Activity 2 (Learning).

Allowed only if the next day's activity was **not** 2.

---

# State Definition

Let:

```text
f(day, last)
=
Maximum merit points obtainable from 'day' onwards 
given that the activity performed on the day immediately 
preceding this window was 'last'.
```

*Note: For the terminal day boundary, `last = 3` represents that no activity restriction has been set yet.*

---

# Recurrence

For a given `day` and a forbidden `last` activity:

```text
f(day, last) = max( points[day][i] + f(day + 1, i) )
```

for all `0 <= i <= 2` where `i != last`.

---

# Recursive Solution

## Base Cases

### Reached End

```text
day == N

return 0
```

Why?

Because no more days remain to accumulate merit points.

---

## Java Code

```java
class Solution {

    private int solve(int day, int last, int[][] points) {

        if (day == points.length) {
            return 0;
        }

        int maxPoints = 0;

        for (int i = 0; i < 3; i++) {
            if (i != last) {
                int currentPoints = points[day][i] + solve(day + 1, i, points);
                maxPoints = Math.max(maxPoints, currentPoints);
            }
        }

        return maxPoints;
    }

    public int ninjaTraining(int[][] points) {
        return solve(0, 3, points);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(3^N)
Space Complexity: O(N)
```

---

# Memoization (Top-Down DP)

## Idea

Store state values inside a 2D array `dp[N][4]` to bypass duplicate recursive lookups.

---

## Java Code

```java
import java.util.Arrays;

class Solution {

    private int solve(int day, int last, int[][] points, int[][] dp) {

        if (day == points.length) {
            return 0;
        }

        if (dp[day][last] != -1) {
            return dp[day][last];
        }

        int maxPoints = 0;

        for (int i = 0; i < 3; i++) {
            if (i != last) {
                int currentPoints = points[day][i] + solve(day + 1, i, points, dp);
                maxPoints = Math.max(maxPoints, currentPoints);
            }
        }

        return dp[day][last] = maxPoints;
    }

    public int ninjaTraining(int[][] points) {
        int n = points.length;
        int[][] dp = new int[n][4];
        for (int[] row : dp) {
            Arrays.fill(row, -1);
        }

        return solve(0, 3, points, dp);
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N * 4) -> O(N)
Space Complexity : O(N * 4) + O(N) recursion stack -> O(N)
```

---

# Tabulation (Bottom-Up DP)

## DP Definition

```text
dp[day][last]
=
Maximum merit points obtainable from day 0 up to 'day' 
given that the activity performed on day 'day' is 'last'.
```

---

## Base Case

For `day = 0`, we initialize all possible historical options:

```text
dp[0][0] = max(points[0][1], points[0][2])
dp[0][1] = max(points[0][0], points[0][2])
dp[0][2] = max(points[0][0], points[0][1])
dp[0][3] = max(points[0][0], max(points[0][1], points[0][2]))
```

---

## Transition

For any day from `1` to `N - 1`:

```text
dp[day][last] = max( points[day][i] + dp[day - 1][i] )
```

for all choices `i` where `i != last`.

---

## Java Code

```java
class Solution {

    public int ninjaTraining(int[][] points) {
        int n = points.length;
        int[][] dp = new int[n][4];

        // Base case for Day 0
        dp[0][0] = Math.max(points[0][1], points[0][2]);
        dp[0][1] = Math.max(points[0][0], points[0][2]);
        dp[0][2] = Math.max(points[0][0], points[0][1]);
        dp[0][3] = Math.max(points[0][0], Math.max(points[0][1], points[0][2]));

        // Iterate through all remaining days
        for (int day = 1; day < n; day++) {
            for (int last = 0; last < 4; last++) {
                dp[day][last] = 0;
                for (int task = 0; task < 3; task++) {
                    if (task != last) {
                        int point = points[day][task] + dp[day - 1][task];
                        dp[day][last] = Math.max(dp[day][last], point);
                    }
                }
            }
        }

        return dp[n - 1][3];
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(N * 4 * 3) -> O(N)
Space Complexity : O(N * 4) -> O(N)
```

---

# Space Optimized Solution

## Observation

We only need the previous day's metrics:

```text
dp[day-1]
```

while calculating values for the current day. Therefore, we can reduce the 2D array down to a single 1D array of size 4.

---

## Java Code

```java
class Solution {

    public int ninjaTraining(int[][] points) {
        int n = points.length;
        int[] prev = new int[4];

        // Base case configuration for Day 0
        prev[0] = Math.max(points[0][1], points[0][2]);
        prev[1] = Math.max(points[0][0], points[0][2]);
        prev[2] = Math.max(points[0][0], points[0][1]);
        prev[3] = Math.max(points[0][0], Math.max(points[0][1], points[0][2]));

        for (int day = 1; day < n; day++) {
            int[] temp = new int[4];
            for (int last = 0; last < 4; last++) {
                temp[last] = 0;
                for (int task = 0; task < 3; task++) {
                    if (task != last) {
                        temp[last] = Math.max(temp[last], points[day][task] + prev[task]);
                    }
                }
            }
            prev = temp;
        }

        return prev[3];
    }
}
```

---

# Dry Run

## Input

```text
points = [[10, 40, 70], [20, 50, 80], [30, 60, 90]]
```

---

### Day 0 Initialization

```text
prev[0] = max(40, 70) = 70
prev[1] = max(10, 70) = 70
prev[2] = max(10, 40) = 40
prev[3] = max(10, 40, 70) = 70
```

---

### Day 1

```text
last = 0: max(task 1, task 2) -> max(50 + prev[1], 80 + prev[2]) = max(50 + 70, 80 + 40) = max(120, 120) = 120
last = 1: max(task 0, task 2) -> max(20 + prev[0], 80 + prev[2]) = max(20 + 70, 80 + 40) = max(90, 120) = 120
last = 2: max(task 0, task 1) -> max(20 + prev[0], 50 + prev[1]) = max(20 + 70, 50 + 70) = max(90, 120) = 120
last = 3: max(20 + 70, 50 + 70, 80 + 40) = 120

prev = [120, 120, 120, 120]
```

---

### Day 2

```text
last = 3 (Final evaluation layer):
task 0: 30 + prev[0] = 30 + 120 = 150
task 1: 60 + prev[1] = 60 + 120 = 180
task 2: 90 + prev[2] = 90 + 120 = 210

Max obtainable points = 210
```

---

### Answer

```text
210
```

---

# Visualization

## Choices Trace Grid

```text
Day 0: Task 2 (70)
        ⬇ (task != 2)
Day 1: Task 1 (50)
        ⬇ (task != 1)
Day 2: Task 2 (90)
```

Total Points Accumulated:

```text
70 + 50 + 90 = 210
```

---

# DP Pattern

## Identify State

```text
f(day, last)
=
Max points obtainable from day onwards
given that 'last' activity is restricted
```

---

## Choices

```text
Task 0, Task 1, or Task 2 (excluding 'last')
```

---

## Recurrence

```text
f(day, last) = max( points[day][task] + f(day + 1, task) )
```

---

## Base Cases

```text
f(N, last) = 0
```

---

# Common Edge Cases

## Case 1

```text
N = 1
points = [[5, 10, 2]]
```
Output:
```text 
10 
```


Case 2

```text 
Identical values on a day points = [[2, 3, 2], [4, 1, 1]]
```
Output:
```text 
7 (e.g., Task 1 then Task 0)
```

Key Takeaways

✅ Multi-state optimization puzzle (2D DP state reduction)
✅ At every index, track both item location and previous choice parameters
✅ Space optimization drops constraints from matrix tables to a flat array copy
✅ Common Interview Pattern:
text Maximize Value with Alternating Choice Constraints 

Similar Problems

• House Robber II
• Best Time to Buy and Sell Stock with Cooldown
• Unique Paths II
• Geeks Training
