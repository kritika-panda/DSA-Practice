# Dynamic Programming (DP) - Complete Introduction

---

# What is Dynamic Programming?

Dynamic Programming (DP) is an optimization technique used to solve problems that have:

1. **Overlapping Subproblems**
2. **Optimal Substructure**

Instead of solving the same subproblem multiple times, we solve it once and store the answer for future use.

---

## Simple Definition

DP = Recursion + Memoization

or

DP = Storing previously computed results to avoid repeated work.

---

# Why Do We Need DP?

Consider finding:

```text
fib(5)
```

Recursive definition:

```text
fib(n) = fib(n-1) + fib(n-2)
```

Recursion Tree:

```text
                fib(5)
              /        \
         fib(4)       fib(3)
        /     \       /     \
   fib(3) fib(2) fib(2) fib(1)
     /  \
fib(2) fib(1)
```

Notice:

```text
fib(3)
fib(2)
fib(1)
```

are calculated multiple times.

This causes enormous repetition.

---

## Repeated Calculations

```text
fib(2) calculated 3 times
fib(1) calculated 5 times
fib(0) calculated 3 times
```

As input grows:

```text
fib(40)
fib(50)
fib(100)
```

the computation becomes extremely expensive.

---

## DP Solution

Store answers:

```text
fib(2) = 1
fib(3) = 2
fib(4) = 3
```

The next time we need them:

```text
Use stored value
instead of recomputing.
```

---

# Two Essential Conditions for DP

---

## 1. Overlapping Subproblems

A problem has overlapping subproblems if the same subproblem appears again and again.

Example:

```text
fib(5)

fib(3)
appears multiple times.
```

Visualization:

```text
fib(5)
 ├─ fib(4)
 │   ├─ fib(3)
 │   └─ fib(2)
 │
 └─ fib(3)
```

Same problem:

```text
fib(3)
```

is solved twice.

---

## 2. Optimal Substructure

The optimal solution can be built from optimal solutions of smaller subproblems.

Example:

```text
fib(5)

=
fib(4) + fib(3)
```

If:

```text
fib(4)
fib(3)
```

are optimal,

then:

```text
fib(5)
```

is also optimal.

---

# DP Approaches

There are two major approaches.

---

# 1. Memoization (Top-Down DP)

Start with recursion.

Store answers.

---

## Flow

```text
Problem
   ↓
Recursion
   ↓
Store Result
   ↓
Reuse Result
```

---

## Example

```java
int solve(int n, int[] dp) {

    if (n <= 1) {
        return n;
    }

    if (dp[n] != -1) {
        return dp[n];
    }

    return dp[n] =
            solve(n - 1, dp)
            + solve(n - 2, dp);
}
```

---

## Why Called Top-Down?

Because we start from:

```text
n
```

and move toward:

```text
0
```

---

# 2. Tabulation (Bottom-Up DP)

Start from base cases.

Build answers iteratively.

---

## Flow

```text
Base Cases
    ↓
Small Answers
    ↓
Bigger Answers
    ↓
Final Answer
```

---

## Example

```java
int[] dp = new int[n + 1];

dp[0] = 0;
dp[1] = 1;

for (int i = 2; i <= n; i++) {
    dp[i] = dp[i - 1] + dp[i - 2];
}
```

---

## Why Called Bottom-Up?

Because we start from:

```text
0
1
```

and grow toward:

```text
n
```

---

# DP Workflow

Whenever you see a DP problem, follow this exact process.

---

# Step 1: Find the State

The most important step.

Ask:

```text
What uniquely represents the problem?
```

Examples:

### Fibonacci

```text
f(i)

=
Fibonacci at index i
```

---

### Climbing Stairs

```text
f(i)

=
Ways to reach step i
```

---

### Frog Jump

```text
f(i)

=
Minimum energy required
to reach stone i
```

---

### House Robber

```text
f(i)

=
Maximum money obtainable
up to house i
```

---

# Step 2: Find Choices

Ask:

```text
How did I reach this state?
```

Examples:

### Frog Jump

```text
Come from:

i-1
i-2
```

---

### Climbing Stairs

```text
Move:

1 step
2 steps
```

---

### Decode Ways

```text
Take:

1 digit
2 digits
```

---

# Step 3: Write Recurrence

Create mathematical relation.

Example:

### Fibonacci

```text
f(i)
=
f(i-1)
+
f(i-2)
```

---

### Frog Jump

```text
f(i)
=
min(
    f(i-1)+cost1,
    f(i-2)+cost2
)
```

---

### House Robber

```text
f(i)
=
max(
    skip,
    take
)
```

---

# Step 4: Identify Base Cases

Smallest solvable inputs.

Example:

### Fibonacci

```text
f(0)=0
f(1)=1
```

---

### Climbing Stairs

```text
f(0)=1
f(1)=1
```

---

### Frog Jump

```text
f(0)=0
```

---

# Step 5: Memoization

Convert recurrence into recursion.

Store computed answers.

---

# Step 6: Tabulation

Convert recursion into iterative solution.

---

# Step 7: Space Optimization

Ask:

```text
Do I really need
the entire DP array?
```

Example:

```text
dp[i]
depends only on

dp[i-1]
dp[i-2]
```

Then:

```text
Array can be removed.
```

---

# Categories of DP Problems

---

# 1. Fibonacci Pattern

State depends on previous states.

Examples:

- Fibonacci Number
- Climbing Stairs
- Min Cost Climbing Stairs
- House Robber
- Delete and Earn

Recurrence:

```text
dp[i]
depends on
dp[i-1]
dp[i-2]
```

---

# 2. Subsequence DP

Choose or don't choose.

Examples:

- Subset Sum
- Partition Equal Subset Sum
- Target Sum
- Knapsack

Pattern:

```text
Take
or
Not Take
```

---

# 3. String DP

Operate on indices of strings.

Examples:

- LCS
- Edit Distance
- Distinct Subsequences
- Decode Ways
- Word Break

State:

```text
dp[i][j]
```

often represents:

```text
answer using
first i and j characters
```

---

# 4. Grid DP

Move in matrix.

Examples:

- Unique Paths
- Minimum Path Sum
- Cherry Pickup

State:

```text
dp[row][col]
```

---

# 5. Interval DP

Solve ranges.

Examples:

- Burst Balloons
- Matrix Chain Multiplication

State:

```text
dp[i][j]
```

Meaning:

```text
answer for interval
i to j
```

---

# 6. DP on Trees

State attached to nodes.

Examples:

- House Robber III
- Diameter Variations
- Tree Matching

State:

```text
DP(node)
```

---

# 7. Bitmask DP

State compressed into bits.

Examples:

- Travelling Salesman
- Assignment Problems

State:

```text
DP(mask)
```

---

# Common DP Patterns

---

## Count Ways

Examples:

```text
Climbing Stairs
Decode Ways
Coin Change II
```

Recurrence:

```text
ways += recursive answer
```

---

## Minimum

Examples:

```text
Frog Jump
Min Cost Climbing Stairs
Minimum Path Sum
```

Recurrence:

```text
ans = min(...)
```

---

## Maximum

Examples:

```text
House Robber
Delete and Earn
Maximum Profit
```

Recurrence:

```text
ans = max(...)
```

---

## Take / Not Take

Examples:

```text
Knapsack
Subset Sum
Target Sum
```

Recurrence:

```text
take
notTake
```

---

# Memoization vs Tabulation

| Feature | Memoization | Tabulation |
|----------|------------|------------|
| Approach | Top Down | Bottom Up |
| Uses Recursion | Yes | No |
| Stack Space | Yes | No |
| Easier to Write | Yes | Sometimes |
| Faster in Practice | Slightly Slower | Faster |
| Interview Preference | Both | Usually Preferred |

---

# Signs a Problem Might Be DP

Ask yourself:

### Question 1

```text
Can the problem be solved recursively?
```

---

### Question 2

```text
Are the same states
being solved repeatedly?
```

---

### Question 3

```text
Can answer be built
from smaller answers?
```

---

If all three are YES:

```text
It's likely a DP problem.
```

---

# Most Important DP Problems (Foundation)

## Beginner

1. Fibonacci Number
2. Climbing Stairs
3. Min Cost Climbing Stairs
4. Frog Jump
5. House Robber
6. Delete and Earn
7. Decode Ways

---

## Intermediate

8. Coin Change
9. Coin Change II
10. Target Sum
11. Partition Equal Subset Sum
12. Unique Paths
13. Longest Common Subsequence
14. Edit Distance
15. Longest Increasing Subsequence

---

## Advanced

16. Burst Balloons
17. Matrix Chain Multiplication
18. House Robber III
19. Digit DP
20. Bitmask DP

---

# Golden Rule for DP

Whenever solving a DP problem, always follow:

```text
1. Find State

2. Find Choices

3. Write Recurrence

4. Define Base Cases

5. Memoization

6. Tabulation

7. Space Optimization
```

Mastering these 7 steps will allow you to solve almost every DP problem asked in FAANG interviews.
