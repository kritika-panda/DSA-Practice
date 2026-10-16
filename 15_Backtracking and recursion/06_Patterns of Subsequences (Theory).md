# Patterns of Subsequences (Theory)

## 1. Core Paradigm: What is a Subsequence?

A **subsequence** is a sequence derived from an initial array or string by deleting zero or more elements without changing the relative structural order of the remaining elements. 

Formally, given a sequence \(S = [s_1, s_2, \dots, s_n]\), a subsequence \(Sub = [s_{i_1}, s_{i_2}, \dots, s_{i_k}]\) must satisfy the strict chronological constraint:
\[1 \le i_1 < i_2 < \dots < i_k \le n\]

### Key Distinctions

| Structure | Contiguous? | Preserves Relative Order? | Total Count for Size N | Example for `[A, B, C]` |
| :--- | :--- | :--- | :--- | :--- |
| **Subsequence** | **No** (Elements can be skipped) | **Yes** | \(2^N\) | `[A, C]` |
| **Substring / Subarray** | **Yes** (Must be a continuous block) | **Yes** | \(\frac{N(N+1)}{2}\) | `[A, B]` |
| **Permutation** | **No** (Rearranges elements) | **No** | N! | `[C, B, A]` |

---

## 2. Mathematical State Space: The Combinatorial \(2^N\) Tree

Every subsequence generation problem can be modeled as a sequence of **binary choices**. For each element i in a sequence of size N, the algorithm encounters a fundamental invariant decision:

```text
               [Root: Decision Array]
                    /          \
         Include Element i    Exclude Element i
               /   \              /   \
           [In]   [Out]        [In]   [Out]
```

This branching logic maps directly to a state space size of:
\[\prod_{i=1}^{N} 2 = 2 \times 2 \times \dots \times 2 = 2^N\]

Including the **empty subsequence** `[]` (where every element is excluded), this collection forms the complete mathematical **Power Set** of the data stream.

---

## 3. Structural Patterns & Solution Blueprints

Subsequence problems typically fall into three major behavioral patterns based on size and operational requirements.

### Pattern A: Combinatorial Generation (Backtracking)
- **Objective:** Explicitly compile and output every single individual subsequence combination path.
- **Trigger Bounds:** N ≤ 20 (since 2²⁰ ≈ 10⁶ operations, which fits cleanly inside standard 1-second timeout windows).
- **Strategy:** Depth-First Search (DFS) state-space exploration with a mutable path buffer.

```java
// Standard Backtracking Blueprint
void backtrack(int index, List<Integer> currentPath) {
    if (index == nums.length) {
        result.add(new ArrayList<>(currentPath)); // Deep copy at leaf node
        return;
    }
    // Decision 1: Include nums[index]
    currentPath.add(nums[index]);
    backtrack(index + 1, currentPath);
    currentPath.remove(currentPath.size() - 1); // Undo state change (Backtrack)

    // Decision 2: Exclude nums[index]
    backtrack(index + 1, currentPath);
}
```

### Pattern B: Combinatorial Optimization (Dynamic Programming)
- **Objective:** Find a global scalar value (e.g., maximum length, minimum sum, count of valid paths) without printing the subsets.
- **Trigger Bounds:** N ≤ 10³ or 10⁴.
- **Strategy:** Break down overlapping subproblems by caching states inside a table to drop runtime costs from exponential \(O(2^N)\) down to polynomial O(N²) or \(O(N \cdot \text{Target})\).

```text
State Definition Example (Longest Increasing Subsequence):
dp[i] = Maximum length of an increasing subsequence that ends strictly at index i.

Mathematical Transition:
dp[i] = 1 + max(dp[j]) for all j < i where nums[j] < nums[i]
```

### Pattern C: Greedy Sequence Verification (Dual Pointers)
- **Objective:** Determine if string A is a subsequence of string B.
- **Trigger Bounds:** N, M ≤ 10⁶.
- **Strategy:** Use two linear tracking cursors to find elements greedily in a single forward pass, completely bypassing structural tree lookups.

```java
// Subsequence Check Invariant
boolean isSubsequence(String s, String t) {
    int i = 0, j = 0;
    while (i < s.length() && j < t.length()) {
        if (s.charAt(i) == t.charAt(j)) {
            i++; // Greedily match and advance target token
        }
        j++; // Always advance source stream to find next match
    }
    return i == s.length();
}
```

---

## 4. Advanced Invariants: Sorting & Duplication

### The Sorting Invariant
When solving subsequence problems, a common question is: *Can I sort the input array?*
- **Rule:** If the problem rules depend on **index positions or original ordering** (e.g., *Longest Increasing Subsequence* or *Subarray/Subsequence transformations*), sorting **destroys** the structural context and is strictly forbidden.
- **Exception:** If the problem evaluates **combinations where order is irrelevant** (e.g., *Subset Sum*, *Find all subsets that sum to Target*, or *Minimum Pair Removals*), sorting is highly strategic. It groups identical items together and allows you to short-circuit loops early.

### The Duplicate Pruning Rule
If the source collection contains duplicate elements (e.g., `[1, 2, 2]`), a standard backtracking branch will generate identical subsets, violating uniqueness constraints.

To prune duplicate branches in O(1) constant time without using an expensive `HashSet`:
1. **Sort the array up front** to force all duplicate elements into a contiguous block.
2. Inside the recursive transition loop, skip any element that matches its immediate predecessor if that predecessor was already skipped in the current frame.

```text
State Transition Guard:
For i from start to N-1:
    If i > start AND nums[i] == nums[i - 1] -> Continue (Prune branch)
```

---

## 5. Architectural Paradigms

```text
        Subsequence Problems Classification
                     │
    ┌────────────────┴────────────────┐
    ▼                                 ▼
Scalar Metrics (DP/Greedy)       Explicit Sets (Backtracking)
  ├── O(N log N) / O(N²)           └── O(N * 2^N)
  └── Space: O(N) / O(1)           └── Space: O(N) Stack
```

### Canonical Problems Matrix

| Problem Template | Classification | Structural Invariant Requirement | Time Complexity | Space Complexity |
| :--- | :--- | :--- | :--- | :--- |
| **Subsets I ([LeetCode 78](https://leetcode.com))** | Backtracking | Unconstrained Binary Choices | $O(N \cdot 2^N)$ | $O(N)$ |
| **Subsets II ([LeetCode 90](https://leetcode.com))** | Backtracking | Contiguous Duplicate Pruning | $O(N \cdot 2^N)$ | $O(N)$ |
| **Is Subsequence ([LeetCode 392](https://leetcode.com))** | Two-Pointer | Greedy Linear Matching Pass | $O(N)$ | $O(1)$ |
| **Longest Increasing Subsequence ([LeetCode 300](https://leetcode.com))** | Dynamic Programming | Predecessor Pivot Scans | $O(N^2)$ or $O(N \log N)$ | $O(N)$ |
| **Partition Equal Subset Sum ([LeetCode 416](https://leetcode.com))** | Dynamic Programming | Knapsack Capacity Target Reductions | $O(N \cdot \text{Sum})$ | $O(\text{Sum})$ |
