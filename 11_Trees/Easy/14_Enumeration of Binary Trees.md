# Enumeration of Binary Trees

## What is Enumeration of Binary Trees?

**Enumeration of Binary Trees** refers to finding the number of distinct binary trees that can be formed using a given number of nodes.

This is a classical counting problem in Data Structures and is closely related to **Catalan Numbers**.

---

# Problem Statement

Given `n` distinct nodes, determine:

```text
How many different binary trees can be formed?
```

The arrangement of nodes matters because different structures represent different binary trees.

---

# Example

## n = 1

Only one possible tree:

```text
    1
```

Number of Binary Trees:

```text
1
```

---

## n = 2

Possible Trees:

### Tree 1

```text
    1
     \
      2
```

### Tree 2

```text
      2
     /
    1
```

Number of Binary Trees:

```text
2
```

---

## n = 3

Possible Binary Tree Structures:

```text
1)      1
         \
          2
           \
            3
```

```text
2)      1
         \
          3
         /
        2
```

```text
3)        2
        /   \
       1     3
```

```text
4)        3
         /
        1
         \
          2
```

```text
5)        3
         /
        2
       /
      1
```

Number of Trees:

```text
5
```

---

# Pattern

| n | Number of Binary Trees |
|---|----------------------|
| 0 | 1 |
| 1 | 1 |
| 2 | 2 |
| 3 | 5 |
| 4 | 14 |
| 5 | 42 |
| 6 | 132 |
| 7 | 429 |

These numbers are known as:

```text
Catalan Numbers
```

---

# Understanding the Recurrence

Suppose we choose a node as root.

Then:

```text
Left Subtree + Right Subtree
```

must together contain the remaining nodes.

For every possible root:

```text
Total Trees
=
Trees(Left)
×
Trees(Right)
```

Summing over all possible roots:

```text
C(n)
=
Σ C(i) × C(n-1-i)
```

where:

```text
0 ≤ i < n
```

---

# Catalan Number Formula

The number of distinct Binary Trees with `n` nodes is:

```text
Cn = (2n)! / ((n + 1)! × n!)
```

This is the nth Catalan Number.

---

# Examples

## n = 3

Using Formula:

```text
C3

= (2 × 3)! / ((3 + 1)! × 3!)

= 6! / (4! × 3!)

= 720 / (24 × 6)

= 5
```

Answer:

```text
5
```

---

## n = 4

```text
C4

= 8! / (5! × 4!)

= 40320 / (120 × 24)

= 14
```

Answer:

```text
14
```

---

# Dynamic Programming Approach

## Recurrence

```text
dp[0] = 1
dp[1] = 1
```

For every number of nodes:

```text
dp[n]
=
Σ dp[left] × dp[right]
```

where:

```text
right = n - 1 - left
```

---

# Java Solution (DP)

```java
class Solution {

    public int countBinaryTrees(int n) {

        int[] dp = new int[n + 1];

        dp[0] = 1;
        dp[1] = 1;

        for (int nodes = 2; nodes <= n; nodes++) {

            for (int left = 0; left < nodes; left++) {

                int right = nodes - 1 - left;

                dp[nodes] += dp[left] * dp[right];
            }
        }

        return dp[n];
    }
}
```

---

# Dry Run

## n = 3

Initialize:

```text
dp = [1, 1, 0, 0]
```

---

### nodes = 2

```text
left = 0, right = 1

dp[2] += 1 × 1

dp[2] = 1
```

```text
left = 1, right = 0

dp[2] += 1 × 1

dp[2] = 2
```

Current:

```text
dp = [1, 1, 2, 0]
```

---

### nodes = 3

```text
left = 0, right = 2

dp[3] += 1 × 2 = 2
```

```text
left = 1, right = 1

dp[3] += 1 × 1 = 1
```

```text
left = 2, right = 0

dp[3] += 2 × 1 = 2
```

Final:

```text
dp[3] = 5
```

Answer:

```text
5
```

---

# Recursive View

For:

```text
n = 3
```

Possible root choices:

```text
Root = 1

Left Nodes = 0
Right Nodes = 2

Trees = C0 × C2
```

```text
Root = 2

Left Nodes = 1
Right Nodes = 1

Trees = C1 × C1
```

```text
Root = 3

Left Nodes = 2
Right Nodes = 0

Trees = C2 × C0
```

Total:

```text
(1×2) + (1×1) + (2×1)

= 5
```

---

# Number of Binary Search Trees

An interesting fact:

```text
Number of Binary Trees
=
Catalan Number
```

and

```text
Number of BSTs with n distinct keys
=
Catalan Number
```

Thus:

| Nodes | BSTs |
|---------|---------|
| 1 | 1 |
| 2 | 2 |
| 3 | 5 |
| 4 | 14 |
| 5 | 42 |

---

# Complexity Analysis

## DP Solution

### Time Complexity

```text
O(n²)
```

Nested loops.

---

### Space Complexity

```text
O(n)
```

DP array.

---

# Important Interview Concepts

Enumeration of Binary Trees is commonly used in:

- Catalan Number problems
- Unique Binary Search Trees
- Counting Full Binary Trees
- Counting Expression Trees
- Dynamic Programming on Trees
- Recursive Tree Structure Problems

---

# Common Catalan Number Applications

1. Number of Binary Trees
2. Number of Binary Search Trees
3. Valid Parentheses Combinations
4. Polygon Triangulation
5. Mountain-Valley Sequences
6. Dyck Paths
7. Expression Tree Counting

---

# Quick Revision Sheet

| n | Catalan Number |
|---|---|
| 0 | 1 |
| 1 | 1 |
| 2 | 2 |
| 3 | 5 |
| 4 | 14 |
| 5 | 42 |
| 6 | 132 |
| 7 | 429 |

---

# Key Takeaways

- Enumeration of Binary Trees means counting the number of distinct binary tree structures for `n` nodes.
- The count follows the **Catalan Number** sequence.
- Recurrence:

```text
C(n) = Σ C(i) × C(n-1-i)
```

- Formula:

```text
Cn = (2n)! / ((n + 1)! × n!)
```

- DP Solution:
  - Time Complexity = **O(n²)**
  - Space Complexity = **O(n)**

- This concept frequently appears in interviews under:
  - Unique BSTs
  - Catalan Numbers
  - DP Counting Problems
  - Tree Enumeration Problems
