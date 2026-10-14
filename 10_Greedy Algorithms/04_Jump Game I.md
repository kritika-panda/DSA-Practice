# Jump Game I (LeetCode 55)

## Problem Statement

You are given an integer array `nums`.

Each element represents your maximum jump length from that position.

Determine whether you can reach the last index starting from index `0`.

Return:

```text
true  -> if last index is reachable
false -> otherwise
```

---

## Examples

### Example 1

```java
Input: nums = [2,3,1,1,4]

Output: true
```

### Explanation

```text
Index 0 -> jump 1 step to index 1

Index 1 -> jump 3 steps to index 4

Reached last index.
```

---

### Example 2

```java
Input: nums = [3,2,1,0,4]

Output: false
```

### Explanation

```text
Index 0 -> can reach indices 1,2,3

Index 3 contains 0

Cannot move further.

Index 4 is unreachable.
```

---

# Intuition

At every index:

```text
nums[i]
```

tells us the maximum distance we can jump.

Instead of trying every possible jump (which leads to exponential recursion), we only track:

```text
Farthest index reachable so far
```

If we can keep extending this reachable range, we can eventually reach the last index.

---

# Greedy Observation

Suppose:

```text
nums = [2,3,1,1,4]
```

From index `0`:

```text
Reachable till index 2
```

At index `1`:

```text
Reachable till max(2, 1+3)
= 4
```

Since:

```text
4 >= last index
```

answer is:

```text
true
```

---

# Key Idea

Maintain:

```java
maxReach
```

which stores the farthest index reachable till now.

For each index:

```java
maxReach = Math.max(maxReach, i + nums[i]);
```

If at any point:

```java
i > maxReach
```

then current index itself is unreachable.

Hence:

```text
Return false
```

---

# Approach

## Step 1

Initialize:

```java
maxReach = 0;
```

---

## Step 2

Traverse array.

For every index:

```java
i
```

check:

```java
if (i > maxReach)
```

Current index cannot be reached.

Return:

```java
false
```

---

## Step 3

Update farthest reachable position:

```java
maxReach = Math.max(maxReach, i + nums[i]);
```

---

## Step 4

If traversal completes:

```java
Return true
```

---

# Dry Run

## Example

```java
nums = [2,3,1,1,4]
```

### Initial

```text
maxReach = 0
```

---

### Index 0

```text
0 <= maxReach

maxReach = max(0,0+2)
         = 2
```

---

### Index 1

```text
1 <= 2

maxReach = max(2,1+3)
         = 4
```

---

### Index 2

```text
2 <= 4

maxReach = max(4,2+1)
         = 4
```

---

### Index 3

```text
3 <= 4

maxReach = max(4,3+1)
         = 4
```

---

### Index 4

```text
4 <= 4

Reached last index.
```

Answer:

```text
true
```

---

# Failure Case Dry Run

```java
nums = [3,2,1,0,4]
```

### Index 0

```text
maxReach = 3
```

### Index 1

```text
maxReach = 3
```

### Index 2

```text
maxReach = 3
```

### Index 3

```text
maxReach = 3
```

### Index 4

```text
4 > maxReach
```

Current index is unreachable.

Return:

```text
false
```

---

# Java Solution

```java
class Solution {
    public boolean canJump(int[] nums) {

        int maxReach = 0;

        for (int i = 0; i < nums.length; i++) {

            if (i > maxReach) {
                return false;
            }

            maxReach = Math.max(maxReach, i + nums[i]);
        }

        return true;
    }
}
```

---

# Why Greedy Works?

At every position we only care about:

```text
How far can we reach?
```

Not:

```text
Which exact path did we take?
```

If an index is within the current reachable range:

```text
It can contribute to extending the range.
```

Therefore keeping track of the farthest reachable index is sufficient.

---

# Correctness Proof

Let:

```text
maxReach
```

be the farthest reachable index after processing positions up to `i`.

For every reachable index:

```text
i
```

we update:

```text
maxReach = max(maxReach, i + nums[i])
```

Thus:

```text
maxReach always stores the furthest position
reachable from any processed index.
```

If:

```text
i > maxReach
```

then no previous position can reach `i`.

Therefore reaching the end becomes impossible.

If traversal completes successfully, every index up to the last index is reachable.

Hence the algorithm is correct.

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

## 1. Recursion

Try all possible jumps.

```text
Time: O(2^N)
Space: O(N)
```

TLE

---

## 2. Dynamic Programming

Store whether each index can reach the end.

```text
Time: O(N²)
Space: O(N)
```

Accepted but not optimal.

---

## 3. Greedy (Optimal)

Track farthest reachable index.

```text
Time: O(N)
Space: O(1)
```

Best solution.

---

# Pattern Recognition

This problem is usually identified by:

```text
✓ Array contains jump lengths
✓ Need to determine reachability
✓ Asking for true/false
✓ Farthest reachable position matters
✓ Greedy range expansion
```

---

# Similar Problems

1. Jump Game II
2. Jump Game III
3. Jump Game IV
4. Jump Game VII
5. Gas Station
6. Minimum Number of Refueling Stops
7. Video Stitching
8. Minimum Taps to Water a Garden

---

# Takeaway

```text
Keep track of the farthest index reachable.

If current index exceeds that range,
the journey stops.

Otherwise keep expanding the reachable range.
```

**Pattern:** Greedy + Reachability Tracking
**Time Complexity:** O(N)
**Space Complexity:** O(1)
