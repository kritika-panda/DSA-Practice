# Sequence Reconstruction

## Problem Statement

You are given a 0-indexed integer array `nums` of length `n` which is a permutation of the integers from `1` to `n`. You are also given a 2D integer array `sequences` where `sequences[i]` is a subsequence of `nums`.

Check if `nums` is the **unique shortest common supersequence** that can be built from `sequences`. 

Specifically, return `true` *if `nums` is the **only** valid sequence that can be uniquely reconstructed from `sequences`*, otherwise return `false`.

A **supersequence** is a sequence that can be derived from another sequence by deleting some or no elements without changing the order of the remaining elements.

The overall runtime complexity must be strictly bounded by:

```text
O(V + E)
```

*(where V represents the unique integer elements up to n, and E represents the total length of all sequences combined)*

---

## Example 1

### Input

```text
nums = [1,2,3]
sequences = [[1,2],[1,3]]
```

### Output

```text
false
```

### Explanation

```text
The sequences specify that 1 must come before 2 and 1 must come before 3.
The possible valid supersequences are:
1. [1, 2, 3]
2. [1, 3, 2]
Since there are multiple valid sequences that can be built, nums is not uniquely reconstructed. 
Therefore, return false.
```

---

## Example 2

### Input

```text
nums = [1,2,3]
sequences = [[1,2]]
```

### Output

```text
false
```

### Explanation

```text
The shortest common supersequence is, which does not even match the target length of nums.
Therefore, return false.
```

---

## Example 3

### Input

```text
nums = [1,2,3]
sequences = [[1,2],[2,3]]
```

### Output

```text
true
```

### Explanation

```text
The sequences specify: 1 → 2 and 2 → 3.
There is exactly one unique shortest sequence that can be built, which is.
Since it uniquely matches nums, return true.
```

---

# Intuition

This problem can be structurally rephrased as a directed dependency graph challenge: **Does the graph formed by `sequences` possess a unique Topological Sort?**
* **Nodes:** The integers from `1` to `n`.
* **Directed Edges:** For any individual sequence array `[s1, s2, s3, ...]`, there exists an ordering requirement. This creates directed edges: `s1 → s2`, `s2 → s3`, and so on.

We can solve this problem optimally using **Kahn's Algorithm (BFS-based In-Degree Pruning)**. 

To prove that a topological sort is **completely unique**, there is a strict operational rule: **At every stage of the queue processing loop, there must be exactly ONE vertex inside our queue containing an in-degree of 0.**
* If the queue ever contains **more than one node** at any given moment, it indicates a branching point where multiple nodes have all their prerequisites resolved simultaneously. You could process them in arbitrary order, immediately proving that the topological sort sequence is *not* unique.
* If the queue ever becomes **empty** before processing all `n` nodes, a cycle deadlock exists, meaning no valid reconstruction can be formed.

---

# State Definition

Let:

```text
List<Integer>[] adj = new ArrayList[n + 1]; // Adjacency list graph structure
int[] inDegree = new int[n + 1];            // Array tracking active dependency counts per node
Queue<Integer> queue = new LinkedList<>();  // Buffer holding nodes with 0 remaining prerequisites
```

---

# Recurrence / Pointer Transition Condition

```text
At each step of the BFS loop:
    If queue.size() > 1 -> Reconstructed path is not unique (Return false)
    For each neighbor of curr popped from the queue:
        inDegree[neighbor]--
        If inDegree[neighbor] == 0:
            queue.offer(neighbor)
```

---

# Optimal Structural Graph Traversal Solution

## Idea

By translating sequence elements into adjacency pairings and driving an in-degree contraction sweep using a FIFO queue, we track the size of the unblocked dependency frontier. If the queue size stays locked at exactly 1 throughout the loop, uniqueness is verified in **O(V + E)** time.

---

## Java Code (Kahn's Algorithm Uniqueness Variant)

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

class Solution {

    public boolean sequenceReconstruction(int[] nums, List<List<Integer>> sequences) {
        int n = nums.length;
        
        // Step 1: Pre-allocate graph adjacency tracking systems
        List<Integer>[] adj = new ArrayList[n + 1];
        for (int i = 1; i <= n; i++) {
            adj[i] = new ArrayList<>();
        }
        
        int[] inDegree = new int[n + 1];
        boolean hasNodes = false;

        // Step 2: Build directed priority edges from adjacent sequence pairings
        for (List<Integer> seq : sequences) {
            hasNodes = true;
            for (int i = 0; i < seq.size() - 1; i++) {
                int u = seq.get(i);
                int v = seq.get(i + 1);
                
                // Out-of-bounds boundary defense check
                if (u < 1 || u > n || v < 1 || v > n) {
                    return false;
                }
                
                adj[u].add(v);
                inDegree[v]++;
            }
        }

        if (!hasNodes) return false;

        // Step 3: Seed our FIFO queue with all nodes containing zero prerequisite constraints
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 1; i <= n; i++) {
            if (inDegree[i] == 0) {
                queue.offer(i);
            }
        }

        int index = 0;

        // Step 4: Run the dependency-pruning uniqueness sweep
        while (!queue.isEmpty()) {
            // CRUCIAL UNIQUENESS CHECK: If there is more than 1 choice available, the sequence is ambiguous
            if (queue.size() > 1) {
                return false;
            }

            int curr = queue.poll();
            
            // Check if the current uniquely unlocked element matches our target nums sequence pattern
            if (index >= n || nums[index] != curr) {
                return false;
            }
            index++;

            // Contract the dependency frontier for adjacent nodes
            for (int neighbor : adj[curr]) {
                inDegree[neighbor]--;
                
                if (inDegree[neighbor] == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        // Step 5: If all vertices were successfully processed, the path matches nums uniquely
        return index == n;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(V + E) -> Where V is the length of nums (n) and E is the total length sum of all sequences. Populating the graph nodes takes O(E) operations, and the subsequent BFS queue pass processes each vertex and edge exactly once.
Space Complexity: O(V + E) -> Footprint allocated to store the adjacency lists, the tracking in-degree status array, and the BFS queue container.
```

---

# Dry Run

## Input

```text
nums =, sequences = [[1, 2], [2, 3]] (Matching Example 3)
```

---

### Execution Trace

```text
1. Compute In-Degrees: inDegree = [0, 0, 1, 1] (Node 1 has 0, 2 has 1, 3 has 1)
2. Seed Queue: queue = [1]
3. Loop 1:
   - Check queue.size() == 1 -> Valid.
   - Pop 1 -> Check nums[0] (1) == 1 -> Valid. index becomes 1.
   - Neighbor 2: inDegree drops from 1 to 0 -> queue = [2]
4. Loop 2:
   - Check queue.size() == 1 -> Valid.
   - Pop 2 -> Check nums[1] (2) == 2 -> Valid. index becomes 2.
   - Neighbor 3: inDegree drops from 1 to 0 -> queue = [3]
5. Loop 3:
   - Check queue.size() == 1 -> Valid.
   - Pop 3 -> Check nums[2] (3) == 3 -> Valid. index becomes 3.
   - Queue empty, loop ends.

Check final state: index (3) == n (3) -> Returns true.
```

---

# Visualization

## Unique Dynamic Dependency Queue Path

```text
    Queue Size: 1                  Queue Size: 1                  Queue Size: 1
      ┌───────┐                      ┌───────┐                      ┌───────┐
      │   1   │                      │   2   │                      │   3   │
      └───────┘                      └───────┘                      └───────┘
     inDegree: 0                    inDegree: 0                    inDegree: 0
    (Next: 2 Unlocked)             (Next: 3 Unlocked)             (No neighbors)
    
  No branching factor ever develops. The dependency chain collapses in exactly 1 unique timeline.
```

---

# Graph Paradigm Pattern

## Identify State

```text
Topological sequence validation with explicit strict lookahead queue-size constraints to guarantee track uniqueness.
```

## Choices

```text
Prune prerequisite constraints systematically. Intercept and fail fast if multiple alternative nodes hit an in-degree of 0 simultaneously.
```

---

## Key Takeaways

✅ Advanced, high-frequency variation of directed graph scheduling and sequence compilation.

✅ The Uniqueness Metric Rule: For a graph to have a completely unique topological sort sequence, its queue size must never exceed 1. A queue size of 2 or more indicates a parallel scheduling path variation is valid, which breaks the uniqueness property.

✅ Perfect structural component used for reconstructing revision histories from disjoint distributed log files (like Git commits merging into a single master lineage), decoding DNA sequences from short fragmented reads, and compiling linear task dependencies.

✅ Common Graph Design Pattern:
text Unique Topological Reconstruction via Single-Choice In-Degree Elimination 
