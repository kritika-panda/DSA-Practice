# Jump Game II (LeetCode 45)

## Problem Statement

You are given a 0-indexed array `nums`.

Each element represents the maximum jump length from that position.

Your goal is to reach the last index in the minimum number of jumps.

Return the minimum number of jumps needed.

Assume that you can always reach the last index.

---

## Examples

### Example 1

```java
Input: nums = [2,3,1,1,4]

Output: 2
```

### Explanation

```text
Index 0 → Index 1
Index 1 → Index 4

Total jumps = 2
```

---

### Example 2

```java
Input: nums = [2,3,0,1,4]

Output: 2
```

### Explanation

```text
Index 0 → Index 1
Index 1 → Index 4

Total jumps = 2
```

---

# Intuition

Unlike Jump Game I:

```text
Jump Game I:
Can we reach the end?
```

Here we need:

```text
Minimum jumps required to reach the end.
```

Trying every possible jump leads to:

```text
Exponential recursion
```

Instead, think of each jump as expanding a reachable range.

---

# Key Greedy Observation

Consider:

```text
nums = [2,3,1,1,4]
```

From index `0`:

```text
Reachable range = [1,2]
```

Inside this range:

```text
Index 1 can reach till 4
Index 2 can reach till 3
```

Best future position:

```text
4
```

So after one jump, our next reachable range becomes:

```text
[3,4]
```

This is similar to a BFS level traversal.

---

# Range-Based Greedy

Maintain:

```java
currentEnd
```

Represents:

```text
Current jump boundary
```

Maintain:

```java
farthest
```

Represents:

```text
Farthest index reachable
within current jump range
```

Whenever:

```java
i == currentEnd
```

we have exhausted the current jump.

So:

```java
jumps++;
currentEnd = farthest;
```

---

# Approach

## Step 1

Initialize:

```java
jumps = 0;
currentEnd = 0;
farthest = 0;
```

---

## Step 2

Traverse till:

```java
n - 2
```

No need to process the last index.

---

## Step 3

Update farthest reach.

```java
farthest = Math.max(farthest, i + nums[i]);
```

---

## Step 4

When current range ends:

```java
i == currentEnd
```

take a jump.

```java
jumps++;
currentEnd = farthest;
```

---

# Dry Run

## Input

```java
nums = [2,3,1,1,4]
```

---

### Initial

```text
jumps = 0
currentEnd = 0
farthest = 0
```

---

### Index 0

```text
farthest = max(0,0+2)
         = 2
```

Range ends:

```text
i == currentEnd
```

Take jump:

```text
jumps = 1
currentEnd = 2
```

---

### Index 1

```text
farthest = max(2,1+3)
         = 4
```

---

### Index 2

```text
farthest = max(4,2+1)
         = 4
```

Range ends:

```text
i == currentEnd
```

Take jump:

```text
jumps = 2
currentEnd = 4
```

Reached destination.

Answer:

```text
2
```

---

# Visualization

```text
nums = [2,3,1,1,4]

Jump 1:

0
|------|

Reachable:
1,2

Jump 2:

Choose best position
inside range

1 can reach 4

0 → 1 → 4

Total jumps = 2
```

---

# Java Solution

```java
class Solution {
    public int jump(int[] nums) {

        int jumps = 0;
        int currentEnd = 0;
        int farthest = 0;

        for (int i = 0; i < nums.length - 1; i++) {

            farthest = Math.max(
                farthest,
                i + nums[i]
            );

            if (i == currentEnd) {

                jumps++;
                currentEnd = farthest;
            }
        }

        return jumps;
    }
}
```

---

# Why Does This Work?

Think of indices within:

```text
[currentStart, currentEnd]
```

as one BFS level.

All those indices are reachable using the same number of jumps.

While exploring the current range, we compute:

```text
farthest reachable position
for the next level
```

When the range is exhausted:

```text
Take one jump
Move to next range
```

Exactly like BFS, but without explicitly using a queue.

---

# Correctness Proof

At every step:

```text
currentEnd
```

represents the farthest position reachable with the current number of jumps.

While processing all indices in this range, we compute:

```text
farthest
```

which represents the farthest position reachable with one additional jump.

Once the current range is exhausted:

```text
A new jump becomes mandatory.
```

Taking the jump earlier cannot improve the answer.

Therefore each jump is used optimally and the total number of jumps is minimized.

---

# Complexity Analysis

Let:

```text
N = nums.length
```

### Time Complexity

Single traversal:

```text
O(N)
```

---

### Space Complexity

```text
O(1)
```

---

# Alternative Approaches

## Recursion

Try every possible jump.

```text
Time: O(2^N)
```

TLE

---

## Dynamic Programming

```java
dp[i] = minimum jumps to reach i
```

Time:

```text
O(N²)
```

Space:

```text
O(N)
```

---

## Greedy (Optimal)

Range expansion:

```text
Time  : O(N)
Space : O(1)
```

---

# Jump Game I vs Jump Game II

| Feature | Jump Game I | Jump Game II |
|----------|------------|------------|
| Goal | Reach end? | Minimum jumps |
| Return Type | Boolean | Integer |
| Greedy State | maxReach | farthest + currentEnd |
| Complexity | O(N) | O(N) |
| Idea | Reachability | BFS Levels / Range Expansion |

---

# Pattern Recognition

Use this pattern when:

```text
✓ Need minimum jumps
✓ Each position gives jump length
✓ Want shortest path in array
✓ Range expansion is possible
✓ BFS can be optimized greedily
```

---

# Similar Problems

1. Jump Game I
2. Jump Game III
3. Jump Game IV
4. Jump Game VII
5. Video Stitching
6. Minimum Number of Refueling Stops
7. Minimum Taps to Water a Garden
8. Furthest Building You Can Reach

---

# Takeaway

```text
Instead of choosing the next jump immediately:

1. Explore the entire current range.
2. Find the farthest position reachable.
3. When the range ends, take one jump.
4. Repeat.

This mimics BFS levels and guarantees
the minimum number of jumps.
```

**Pattern:** Greedy + BFS Level Traversal

**Time Complexity:** O(N)

**Space Complexity:** O(1)
