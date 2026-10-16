# Generate All Binary Strings of Length N ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given a positive integer `N`, generate and return **all possible binary strings** of length `N`. 

A binary string of length `N` is a sequence composed strictly of character symbols `'0'` and `'1'`. The final sequence should contain exactly **\(2^N\)** combinations. 

The output collection can return the strings in **any order** (though lexicographical order is standard).

---

## Examples

### Example 1

```text
Input: N = 3
Output: ["000", "001", "010", "011", "100", "101", "110", "111"]
```

### Explanation

```text
For a string slot footprint of length 3, there exist 2³ = 8 distinct 
positional bitmask arrangements.
```

---

### Example 2

```text
Input: N = 1
Output: ["0", "1"]
```

---

# Intuition

Generating all binary strings of length `N` represents a fundamental **State-Space Exhaustive Search** puzzle. For each individual character position from index `0` up to `N - 1`, we face a symmetrical, unconstrained binary choice:
1. Append a `'0'` token to the active path segment.
2. Append a `'1'` token to the active path segment.

```text
                  [Root: Base Empty String]
                        /           \
                 Assign '0'       Assign '1'
                   /    \           /    \
                 '00'   '01'      '10'   '11'
```

Since we branch exactly 2 times at every single one of the N depth levels, the final state-space tree will yield exactly **\(2^N\) leaf nodes**. Because we explicitly need to collect and materialize every independent string track path rather than merely counting them, a **Depth-First Search (DFS) Backtracking** engine is the most optimal approach.

---

# Brute Force / Iterative Cascading Solution

## Idea

We initialize our results collection tracking vector with a single empty string record base `[""]`. We then run an outer loop N times. In each loop pass, we duplicate all strings created so far, appending a `'0'` to the first batch and a `'1'` to the duplicated batch, doubling our total list size on the heap at each step.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> generateBinaryStringsIterative(int N) {
        List<String> result = new ArrayList<>();
        if (N <= 0) return result;
        
        // Seed with the baseline base empty string
        result.add("");

        // Cascade execution N times to shift bits horizontally
        for (int i = 0; i < N; i++) {
            int currentSize = result.size();
            List<String> nextLevel = new ArrayList<>(currentSize * 2);
            
            // Duplicate and split every existing permutation entry tracking slot
            for (int j = 0; j < currentSize; j++) {
                String currentStr = result.get(j);
                nextLevel.add(currentStr + "0");
                nextLevel.add(currentStr + "1");
            }
            result = nextLevel;
        }
        return result;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** \(O(N \times 2^N)\) because the outer framework loop runs N times, doubling string matrix records on each pass, and string copying parameters scale with length metrics.
- **Space Complexity:** \(O(N \times 2^N)\) heap allocation footprint memory penalty required to accumulate all \(2^N\) string permutations simultaneously.

---

# State Definition (Optimal Backtracking Approach)

To trace our combinatorics states systematically within a recursive state-space decision tree using a single reusable inline array buffer workspace:

```text
backtrack(pos, currentPath):
- 'pos' tracks our current position pointer index slot being filled inside the array.
- 'currentPath' acts as a shared mutable char[] buffer altered in-place directly on the heap.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Case:
If pos == N -> result.add(new String(currentPath)); return

Recursive Controlled Branching:
currentPath[pos] = '0'
backtrack(pos + 1, currentPath) // Recurse deeper to process subsequent indexes

currentPath[pos] = '1'
backtrack(pos + 1, currentPath) // Recurse deeper to process subsequent indexes
```

---

# Optimal Single-Pass In-Place Backtracking Solution

## Idea

We allocate a single primitive `char[N]` array workspace buffer to compile our characters. We launch our depth-first search starting from position index 0. Because each position slot is overwritten directly during the next sibling branch loop loop, we do not need an explicit deletion step, keeping auxiliary allocations at an absolute flat constant floor.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> generateBinaryStrings(int N) {
        List<String> result = new ArrayList<>();
        // Guard Condition: Dimensions equal to or below zero yield empty wrappers
        if (N <= 0) {
            return result;
        }

        // Shared primitive char array workspace buffer to eliminate heap memory thrashing
        char[] currentPath = new char[N];

        // Launch our recursive backtracking pass from position index 0
        backtrack(0, N, currentPath, result);
        return result;
    }

    private void backtrack(int pos, int N, char[] currentPath, List<String> result) {
        // Base Case: If the position cursor reaches N, a unique binary string is finalized
        if (pos == N) {
            result.add(new String(currentPath)); // Convert active buffer workspace to immutable string
            return;
        }

        // Choice Step 1: Assign character token '0' to the current position slot
        currentPath[pos] = '0';
        backtrack(pos + 1, N, currentPath, result); // Recurse deeper to fill upcoming slots

        // Choice Step 2: Assign character token '1' to the current position slot
        // Overwrites '0' inline on the heap automatically, executing an implicit backtrack clear
        currentPath[pos] = '1';
        backtrack(pos + 1, N, currentPath, result); // Recurse deeper to fill upcoming slots
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N × 2^N)
```
*Explanation:* There exist exactly \(2^N\) unique binary string variations for a length of N, meaning the base case leaf node statement is reached precisely \(2^N\) times. Instantiating a fresh `String` object from our primitive `char[]` buffer workspace costs O(N) linear character copy time at each leaf, locking total execution runtime complexity securely to \(O(N \times 2^N)\) dimensions.

---

### Space Complexity

```text
Space Complexity: O(N) auxiliary space
```
*Explanation:* Highly optimal auxiliary memory footprint. The character modifications are written completely in-place inside our `char[]` buffer workspace. Auxiliary memory usage scales linearly at O(N) to accommodate the maximum depth height of the recursive call stack (which reaches a maximum height of N).

---

# Key Takeaways

✅ Core baseline foundational template for exploring binary option trees and combinatorics combinations.

✅ **The Implicit Backtrack Invariant:** Unlike stack collections or lists where you must run an explicit `.remove(list.size() - 1)` or `.deleteCharAt(...)` step to undo a choice, a fixed-size primitive workspace array (`char[]`) executes backtracks implicitly. Because Choice 2 (`currentPath[pos] = '1'`) overwrites Choice 1 on the exact same heap memory block index slot, state context remains clean automatically for adjacent sibling paths.

✅ Directly drives real-world microarchitectures like **truth table generator tools**, generating complete bitmask selectors, testing register state permutations, and security brute-force key space scanners.

✅ Common Architectural Paradigm:

```text
Positional Primitive Index Overwrites for Allocation-Free State-Space Branchings
```

### Similar Problems

- Generate Binary Strings Without Consecutive 1s ([LeetCode 3211 Variant](https://leetcode.com))
- Subsets / Power Set ([LeetCode 78](https://leetcode.com))
- Letter Case Permutation ([LeetCode 784](https://leetcode.com))
- Combinations ([LeetCode 77](https://leetcode.com))
