# Candy (LeetCode 135)

## Problem Statement

There are `n` children standing in a line.

Each child is assigned a rating value given in the integer array `ratings`.

You must distribute candies according to the following rules:

1. Every child must have at least one candy.
2. Children with a higher rating than their immediate neighbor must get more candies.

Return the **minimum number of candies** needed.

---

## Examples

### Example 1

```java
Input: ratings = [1,0,2]

Output: 5
```

### Explanation

```text
Ratings: 1 0 2
Candies: 2 1 2

Total = 5
```

---

### Example 2

```java
Input: ratings = [1,2,2]

Output: 4
```

### Explanation

```text
Ratings: 1 2 2
Candies: 1 2 1

Total = 4
```

---

# Intuition

For every child we need to satisfy:

```text
Higher rating than left neighbor
→ more candies than left neighbor

Higher rating than right neighbor
→ more candies than right neighbor
```

A single traversal cannot satisfy both conditions simultaneously.

Therefore:

```text
1. Left → Right pass
2. Right → Left pass
```

---

# Key Observation

Consider:

```text
Ratings = [1,2,3,4]
```

Candies should be:

```text
1 2 3 4
```

This can be achieved by scanning:

```text
Left → Right
```

because ratings are increasing.

---

Now consider:

```text
Ratings = [4,3,2,1]
```

Candies should be:

```text
4 3 2 1
```

This requires:

```text
Right → Left
```

because ratings are decreasing.

Therefore we need both passes.

---

# Greedy Approach

## Pass 1: Left → Right

If:

```java
ratings[i] > ratings[i - 1]
```

then:

```java
candies[i] = candies[i - 1] + 1;
```

Otherwise:

```java
candies[i] = 1;
```

This satisfies:

```text
Higher than left neighbor
```

condition.

---

## Pass 2: Right → Left

If:

```java
ratings[i] > ratings[i + 1]
```

then:

```java
candies[i] =
Math.max(candies[i], candies[i + 1] + 1);
```

This satisfies:

```text
Higher than right neighbor
```

condition.

---

# Dry Run

## Input

```java
ratings = [1,0,2]
```

---

### Initial Candies

```text
1 1 1
```

---

### Left → Right

Index 1

```text
0 > 1 ? No
```

Candies:

```text
1 1 1
```

Index 2

```text
2 > 0 ? Yes
```

Candies:

```text
1 1 2
```

---

### Right → Left

Index 1

```text
0 > 2 ? No
```

Candies:

```text
1 1 2
```

Index 0

```text
1 > 0 ? Yes
```

Update:

```text
max(1,1+1)
= 2
```

Candies:

```text
2 1 2
```

---

### Total

```text
2 + 1 + 2 = 5
```

Answer:

```text
5
```

---

# Visualization

```text
Ratings : 1 0 2

Left Pass

1 1 2

Right Pass

2 1 2
```

---

# Java Solution

```java
class Solution {
    public int candy(int[] ratings) {

        int n = ratings.length;

        int[] candies = new int[n];

        for (int i = 0; i < n; i++) {
            candies[i] = 1;
        }

        // Left to Right
        for (int i = 1; i < n; i++) {

            if (ratings[i] > ratings[i - 1]) {
                candies[i] = candies[i - 1] + 1;
            }
        }

        // Right to Left
        for (int i = n - 2; i >= 0; i--) {

            if (ratings[i] > ratings[i + 1]) {
                candies[i] = Math.max(
                    candies[i],
                    candies[i + 1] + 1
                );
            }
        }

        int total = 0;

        for (int candy : candies) {
            total += candy;
        }

        return total;
    }
}
```

---

# Why Do We Use Math.max()?

Consider:

```text
Ratings = [1,3,4,5,2]
```

After Left Pass:

```text
Candies = [1,2,3,4,1]
```

Now while moving right-to-left:

```text
5 > 2
```

We need:

```text
Candies[3] > Candies[4]
```

But we already gave:

```text
Candies[3] = 4
```

Using:

```java
Math.max(current, required)
```

ensures we never reduce candies assigned previously.

---

# Correctness Proof

## Left Pass

Ensures:

```text
ratings[i] > ratings[i-1]
⇒ candies[i] > candies[i-1]
```

for every child.

---

## Right Pass

Ensures:

```text
ratings[i] > ratings[i+1]
⇒ candies[i] > candies[i+1]
```

for every child.

---

Combining both passes satisfies:

```text
Higher than left neighbor
Higher than right neighbor
```

simultaneously.

Since we always assign the minimum possible candies while maintaining constraints, the result is optimal.

---

# Complexity Analysis

Let:

```text
N = ratings.length
```

### Time Complexity

```text
Left Pass  = O(N)
Right Pass = O(N)
Summation  = O(N)
```

Overall:

```text
O(N)
```

---

### Space Complexity

```text
O(N)
```

for candy array.

---

# Optimal Space Approach (Interview Follow-Up)

A more advanced greedy solution uses:

```text
Mountain / Slope Method
```

and achieves:

```text
Time  : O(N)
Space : O(1)
```

without storing the candy array.

This is often asked as a follow-up in Google/Uber interviews.

---

# Pattern Recognition

Use this approach when:

```text
✓ Neighbor dependency exists
✓ Higher value requires higher reward
✓ Need minimum assignment
✓ Local increasing/decreasing sequences matter
✓ Two directional constraints exist
```

---

# Similar Problems

1. Trapping Rain Water
2. Gas Station
3. Candy Crush Sequence Problems
4. Distribute Coins
5. Minimum Rewards
6. Longest Mountain in Array
7. Wiggle Subsequence

---

# Takeaway

```text
A single traversal cannot satisfy both
left and right constraints.

Use:

1. Left → Right pass
2. Right → Left pass

and take the maximum required candies
at each position.
```

**Pattern:** Greedy + Two Pass Traversal

**Time Complexity:** O(N)

**Space Complexity:** O(N)
