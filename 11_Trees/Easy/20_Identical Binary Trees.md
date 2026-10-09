# Check if Two Binary Trees are Identical

## Problem Statement

Given two binary trees, determine whether they are **identical**.

Two binary trees are considered identical if:

1. They have the same structure.
2. Corresponding nodes contain the same value.

---

## Example 1

### Tree 1

```text
       1
      / \
     2   3
```

### Tree 2

```text
       1
      / \
     2   3
```

### Output

```text
true
```

Both trees have the same structure and the same values.

---

## Example 2

### Tree 1

```text
       1
      / \
     2   3
```

### Tree 2

```text
       1
      / \
     3   2
```

### Output

```text
false
```

Values at corresponding positions differ.

---

## Example 3

### Tree 1

```text
       1
      /
     2
```

### Tree 2

```text
       1
        \
         2
```

### Output

```text
false
```

Structures are different.

---

# Approach 1: Recursive DFS (Optimal)

## Key Observation

Two trees are identical if:

```text
1. Current nodes have same value
2. Left subtrees are identical
3. Right subtrees are identical
```

This naturally leads to a recursive solution.

---

## Recursive Conditions

### Case 1: Both nodes are null

```java
p == null && q == null
```

Return:

```text
true
```

Both trees ended at the same position.

---

### Case 2: One node is null

```java
p == null || q == null
```

Return:

```text
false
```

Structures differ.

---

### Case 3: Values differ

```java
p.val != q.val
```

Return:

```text
false
```

Node values differ.

---

### Case 4: Recursively compare

```java
isSameTree(p.left, q.left)
&&
isSameTree(p.right, q.right)
```

---

## Algorithm

```text
isSameTree(p, q)

if both are null
    return true

if one is null
    return false

if values differ
    return false

return
    isSameTree(left subtree)
    &&
    isSameTree(right subtree)
```

---

## Java Solution

```java
class Solution {

    public boolean isSameTree(TreeNode p, TreeNode q) {

        if (p == null && q == null) {
            return true;
        }

        if (p == null || q == null) {
            return false;
        }

        if (p.val != q.val) {
            return false;
        }

        return isSameTree(p.left, q.left)
            && isSameTree(p.right, q.right);
    }
}
```

---

# Dry Run

## Input

```text
Tree 1

       1
      / \
     2   3

Tree 2

       1
      / \
     2   3
```

---

### Step 1

Compare:

```text
1 vs 1
```

Equal

Recurse on:

```text
2 vs 2
3 vs 3
```

---

### Step 2

Compare:

```text
2 vs 2
```

Equal

Recurse on:

```text
null vs null
null vs null
```

Returns:

```text
true
```

---

### Step 3

Compare:

```text
3 vs 3
```

Equal

Recurse on:

```text
null vs null
null vs null
```

Returns:

```text
true
```

---

### Final

```text
true && true
```

Result:

```text
true
```

---

# Visualization

```text
Compare

      1                1
     / \              / \
    2   3            2   3
```

---

```text
1 == 1 ✅

Check Left

2 == 2 ✅

Check Right

3 == 3 ✅
```

---

```text
All nodes matched
```

Return:

```text
true
```

---

# Complexity Analysis

## Time Complexity

```text
O(N)
```

Where:

```text
N = Number of nodes
```

Every node is visited exactly once.

---

## Space Complexity

### Recursive Stack

```text
O(H)
```

Where:

```text
H = Height of tree
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

# Approach 2: Iterative BFS

Use a queue to compare nodes level by level.

---

## Algorithm

```text
Put both roots into queue

while queue not empty

    remove two nodes

    if both null
        continue

    if one null
        return false

    if values differ
        return false

    add left children
    add right children

return true
```

---

## Java Solution

```java
import java.util.*;

class Solution {

    public bo*lean isSameTree(TreeNode p, TreeNo*e q) {

        Queue<TreeNode> qu*ue = new LinkedList<>();

        *ueue.offer(p);
        queue.offer*q);

        while (!queue.isEmpty*)) {

            TreeNode first =*queue.poll();
            TreeNode*second = queue.poll();

          * if (first == null && second == nu*l) {
                continue;
   *        }

            if (first =* null || second == null) {
       *        return false;
            *

            if (first.val != sec*nd.val) {
                return f*lse;
            }

            qu*ue.offer(first.left);
            *ueue.offer(second.left);

        *   queue.offer(first.right);
     *      queue.offer(second.right);
 *      }

        return true;
    *
}
```

---

# Why Does the Recurs*ve Solution Work?

For two trees t* be identical:

```text
Root value* must match
```

AND

```text
Left*subtrees must match
```

AND

```t*xt
Right subtrees must match
```

*he recursive solution checks exact*y these three conditions for every*node.

If any comparison fails:

`*`text
return false
```

Otherwise:*
```text
return true
```

---

# E*ge Cases

## Both Trees Empty

```*ext
p = null
q = null
```

Output:*
```text
true
```

---

## One Tre* Empty

```text
p = null
q = [1]
`*`

Output:

```text
false
```

---*
## Different Values

```text
    *       1
   /       /
  2       3
*``

Output:

```text
false
```

--*

## Different Structure

```text
*ree 1

    1
   /
  2

Tree 2

   *1
     \
      2
```

Output:

```*ext
false
```

---

# Interview Qu*stions

## Q1. What makes two tree* identical?

Two trees are identic*l when:

```text
Structure is same*AND
Corresponding values are same
*``

---

## Q2. Can preorder*travers*ls alone determine identical*trees?

❌ No

Different trees can *ave the same preorder traversal.

*tructure comparison is also requir*d.

---

## Q3. Why is time comple*ity O(N)?

Each node is visited ex*ctly once.

Therefore:

```text
O(*)
```

---

## Q4. Which approach *s preferred?

✅ Recursive DFS

Bec*use:

- Cleaner
- Easier to unders*and
- Matches tree structure natur*lly

---

# Related Problems

- Sy*metric Tree
- Subtree of Another T*ee
- Invert Binary Tree
- Same Tre* (LeetCode 100)
- Check if Two Tre*s are Mirror Images
- Serialize an* Deserialize Binary Tree

---

# K*y Takeaway

To determine whether t*o binary trees are identical, comp*re:

```text
1. Current node value*
2. Left subtrees
3. Right subtree*
```

The recursive DFS approach i* the most elegant solution, visiti*g each node once and achieving:

`*`text
Time Complexity  : O(N)
Spac* Complexity : O(H)
```

where **H** is the height of the tree.
