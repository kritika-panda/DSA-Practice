# Maximum Path Sum

## Problem Statement

Given the root of a binary tree, return the **maximum path sum**.

A path is defined as:

- Any sequence of nodes connected by edges.
- The path can start and end at any node.
- The path must contain at least one node.
- The path does not need to pass through the root.

The goal is to find the path whose node values add up to the maximum possible sum.

---

## Example 1

### Input

```text
       1
      / \
     2   3
```

### Output

```text
6
```

### Explanation

Path:

```text
2 → 1 → 3
```

Sum:

```text
2 + 1 + 3 = 6
```

---

## Example 2

### Input

```text
          -10
          /  \
         9   20
            /  \
           15   7
```

### Output

```text
42
```

### Explanation

Path:

```text
15 → 20 → 7
```

Sum:

```text
15 + 20 + 7 = 42
```

The path does not pass through the root.

---

# Difference Between Diameter and Maximum Path Sum

## Diameter

```text
Longest path in terms of edges.
```

Example:

```text
4 → 2 → 1 → 3
```

Count:

```text
3 edges
```

---

## Maximum Path Sum

```text
Maximum sum of node values.
```

Example:

```text
15 → 20 → 7
```

Sum:

```text
42
```

---

# Key Observation

For every node we need two different values.

---

## 1. Path Through Current Node

```text
leftGain + node + rightGain
```

Example:

```text
      20
     /  \
   15    7
```

Path Sum:

```text
15 + 20 + 7 = 42
```

This path may become the answer.

---

## 2. Contribution To Parent

A parent can only choose one side.

```text
max(leftGain, rightGain) + node.val
```

Why?

Because a path cannot split upward.

Valid:

```text
      10
      /
     5
    /
   2
```

Invalid:

```text
      Parent
         |
         10
        /  \
       5    7
```

We cannot send both branches upward.

---

# Most Important Insight

Negative paths never help.

Example:

```text
      5
     /
   -10
```

Including:

```text
5 + (-10)
```

makes the sum smaller.

Therefore:

```java
Math.max(0, gain)
```

Ignore negative contributions.

---

# Bottom-Up DFS Strategy

For every node:

### Step 1

Get maximum contribution from left subtree.

```java
leftGain = Math.max(0, dfs(left))
```

---

### Step 2

Get maximum contribution from right subtree.

```java
rightGain = Math.max(0, dfs(right))
```

---

### Step 3

Compute path passing through current node.

```java
currentPath =
leftGain
+ rightGain
+ node.val
```

Update global maximum.

---

### Step 4

Return contribution to parent.

```java
node.val +
max(leftGain, rightGain)
```

---

# Visualization

```text
          -10
          /  \
         9   20
            /  \
           15   7
```

---

### Node 15

```text
Left  = 0
Right = 0

Path Sum = 15

Return = 15
```

---

### Node 7

```text
Path Sum = 7

Return = 7
```

---

### Node 20

```text
Left Gain  = 15
Right Gain = 7

Path Through Node

= 15 + 20 + 7
= 42
```

Maximum becomes:

```text
42
```

Return to parent:

```text
20 + max(15,7)

= 35
```

---

### Node 9

```text
Return = 9
```

---

### Node -10

```text
Left Gain  = 9
Right Gain = 35

Path Through Node

= 9 + (-10) + 35

= 34
```

Maximum still:

```text
42
```

Answer:

```text
42
```

---

# Dry Run

## Input

```text
       1
      / \
     2   3
```

---

### Node 2

```text
Left  = 0
Right = 0

Path = 2

Max = 2

Return = 2
```

---

### Node 3

```text
Path = 3

Max = 3

Return = 3
```

---

### Node 1

```text
Left Gain  = 2
Right Gain = 3

Current Path

= 2 + 1 + 3
= 6
```

Maximum:

```text
6
```

Return:

```text
1 + max(2,3)
= 4
```

Answer:

```text
6
```

---

# Recursive Formula

## Current Node Path

```java
leftGain + rightGain + root.val
```

Used to update answer.

---

## Parent Contribution

```java
root.val + Math.max(leftGain, rightGain)
```

Returned to parent.

---

# Optimal Java Solution

```java
class Solution {

    private int maxSum = Integer.MIN_VALUE;

    public int maxPathSum(TreeNode root) {

        dfs(root);

        return maxSum;
    }

    private int dfs(TreeNode root) {

        if (root == null) {
            return 0;
        }

        int leftGain =
                Math.max(0, dfs(root.left));

        int rightGain =
                Math.max(0, dfs(root.right));

        int currentPath =
                leftGain
                + rightGain
                + root.val;

        maxSum = Math.max(
                maxSum,
                currentPath);

        return root.val
                + Math.max(leftGain, rightGain);
    }
}
```

---

# Why Do We Use `Math.max(0, gain)`?

Consider:

```text
       10
      /
    -50
```

Without:

```java
Math.max(0, gain)
```

Contribution:

```text
10 + (-50)
=
-40
```

Worse.

Instead:

```java
Math.max(0, -50)
```

becomes:

```text
0
```

We ignore the negative path.

---

# Why Does It Work?

For every node:

We evaluate the best path that:

```text
Passes through the current node
```

and update a global maximum.

Then we return the best possible branch that can extend upward.

Thus:

```text
Answer Computation
+
Height-like DFS
```

happen simultaneously.

This is a classic Tree DP problem.

---

# Complexity Analysis

## Time Complexity

```text
O(N)
```

Every node is visited once.

---

## Space Complexity

```text
O(H)
```

Where:

```text
H = Height of Tree
```

---

### Balanced Tree

```text
O(log N)
```

---

### Skewed Tree

```text
O(N)
```

---

# Edge Cases

---

## Single Node

```text
5
```

Answer:

```text
5
```

---

## All Negative Nodes

```text
      -3
      / \
    -2  -5
```

Answer:

```text
-2
```

The maximum path contains only one node.

---

## Path Not Through Root

```text
          -10
          /  \
         9   20
            / \
          15   7
```

Answer:

```text
42
```

Path:

```text
15 → 20 → 7
```

---

# Relationship To Other Tree Problems

| Problem | Return Value From DFS |
|----------|----------------------|
| Maximum Depth | Height |
| Balanced Tree | Height / -1 |
| Diameter | Height |
| Maximum Path Sum | Best Contribution |
| Longest Univalue Path | Longest Matching Branch |

---

# Interview Questions

## Q1. Why return only one branch to parent?

Because a path extending upward cannot split.

Valid:

```text
node + left
```

or

```text
node + right
```

Not both.

---

## Q2. Why keep a global answer?

The maximum path may terminate at any node.

It may not include the root.

---

## Q3. Why ignore negative gains?

Negative paths reduce the sum.

Therefore:

```java
Math.max(0, gain)
```

ensures we only take useful branches.

---

## Q4. Is this Top-Down or Bottom-Up DFS?

✅ Bottom-Up DFS

Children are solved first.

Current node uses their results.

---

# Pattern Recognition

Whenever you see:

```text
Binary Tree
Maximum value
Any path
Path can start/end anywhere
```

Think:

```text
Tree DP
Bottom-Up DFS
Global Answer
```

This pattern appears in:

- Maximum Path Sum
- Diameter of Binary Tree
- Longest Univalue Path
- Tree DP
- Path Sum Variants

---

# Key Takeaway

For each node:

```text
Current Path =
leftGain + node.val + rightGain
```

Update the global answer using this path.

Return only:

```text
node.val + max(leftGain, rightGain)
```

to the parent because a path cannot split upward.

This yields the optimal solution:

```text
Time Complexity  : O(N)
Space Complexity : O(H)
```

and is one of the most important **Tree DP** interview problems.
