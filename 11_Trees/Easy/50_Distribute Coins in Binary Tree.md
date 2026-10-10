# Distribute Coins in Binary Tree

## Problem Statement

You are given the `root` of a binary tree with `N` nodes, where each node in the tree has `node.val` coins. There are `N` coins in total throughout the whole tree.

In one move, we may choose two adjacent nodes and move one coin from one node to another. A move may be from parent to child, or from child to parent.

Return the **minimum number of moves** required to make every node have exactly one coin.

---

## Example

### Binary Tree

```text
           3
          / \
         0   0
```

### Input

```text
root = [3, 0, 0]
```

### Output

```text
2
```

Because:

```text
From the root of the tree, we move one coin to its left child, 
and one coin to its right child. 
Total moves = 1 + 1 = 2.
```

---

## Another Example

### Binary Tree

```text
           0
          / \
         3   0
```

### Input

```text
root = [0, 3, 0]
```

### Output

```text
3
```

Because:

```text
1. Move 2 coins from the left child to the root (2 moves). Left child now has 1 coin.
2. Move 1 coin from the root to the right child (1 move). Right child now has 1 coin.
Total moves = 2 + 1 = 3.
```

---

# Key Balance Property

```text
Balance = Node.val - 1 + Left_Balance + Right_Balance
```

Instead of tracking where each individual coin goes, we can track the **net excess or deficit** (balance) of coins at each subtree. 

* A balance of `0` means the subtree has exactly the right number of coins.
* A positive balance `+X` means the subtree has `X` extra coins that **must** exit through its root node.
* A negative balance `-X` means the subtree is short of `X` coins that **must** enter through its root node.

In either case, exactly `|X|` coins must cross the edge connecting this subtree to its parent node.

---

# Intuition

We can use a **bottom-up post-order traversal (`Left -> Right -> Root`)** to compute the coin balance of each subtree.

At each node, we ask its left and right child subtrees how many coins they need or have extra:
1. `leftBalance = traverse(root.left)`
2. `rightBalance = traverse(root.right)`

The total number of moves crossing into the parent node from these subtrees is simply the absolute values of their balances:
```text
moves += abs(leftBalance) + abs(rightBalance)
```

The current node then calculates its own balance to pass up to its parent:
```text
currentBalance = root.val - 1 + leftBalance + rightBalance
```
The `-1` accounts for the single coin that the current node needs to keep for itself.

---

# Visualization

Evaluating the second example tree bottom-up:

```text
           0 (Root)
          / \
         3   0
```

```text
1. Evaluate Left Child (3):
   - Its left and right children are null (balance = 0).
   - leftBalance = 3 - 1 + 0 + 0 = +2.
   - This means 2 coins must leave this node.
   - Accumulate moves: moves += abs(+2) -> moves = 2.

2. Evaluate Right Child (0):
   - Its left and right children are null (balance = 0).
   - rightBalance = 0 - 1 + 0 + 0 = -1.
   - This means 1 coin must enter this node.
   - Accumulate moves: moves += abs(-1) -> moves = 2 + 1 = 3.

3. Evaluate Root Node (0):
   - leftBalance = +2, rightBalance = -1.
   - rootBalance = 0 - 1 + 2 + (-1) = 0.
   - Entire tree is now balanced.

Final Answer: 3
```

---

# Recursive Solution

## Algorithm

1. Initialize a global or instance variable `moves = 0`.
2. Implement a post-order helper function that returns the net coin balance of a subtree.
3. **Base Case:** If a node is `null`, return `0` (it requires no coins and has no excess).
4. Recursively calculate the balances of the left and right subtrees.
5. Add the absolute values of the left and right balances to the global `moves` counter.
6. Return the current subtree's balance using the formula: `node.val - 1 + leftBalance + rightBalance`.

---

## Java Implementation

```java
// Definition for a binary tree node.
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    
    TreeNode() {}
    
    TreeNode(int val) { 
        this.val = val; 
    }
}

class Solution {
    private int moves = 0;

    public int distributeCoins(TreeNode root) {
        moves = 0; // Reset moves count for each invocation
        getBalance(root);
        return moves;
    }

    private int getBalance(TreeNode root) {
        // Base case: An empty subtree has a balance of 0
        if (root == null) {
            return 0;
        }

        // Post-order traversal: collect balance from subtrees first
        int leftBalance = getBalance(root.left);
        int rightBalance = getBalance(root.right);

        // The absolute balance represents the number of coin transits 
        // crossing the edges between the children and the current parent node
        moves += Math.abs(leftBalance) + Math.abs(rightBalance);

        // Return the net balance of the current subtree up to its parent
        return root.val - 1 + leftBalance + rightBalance;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total number of nodes in the binary tree. We perform a single post-order traversal, visiting each node exactly once.
* **Space Complexity:** O(H) where H is the height of the tree, representing the memory allocation on the system recursion stack. This scales to O(log N) for balanced trees and O(N) for completely skewed trees.
