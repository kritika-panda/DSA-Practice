# Generate Binary Strings Without Consecutive 1s ([LeetCode 3211 Variant / GeeksforGeeks](https://leetcode.com))

## Problem Statement

Given a positive integer `N`, generate all possible **binary strings** of length `N` that do not contain any **consecutive 1s** (i.e., no two `1`s appear right next to each other).

Return *the list of valid binary strings in **any order***.

---

## Examples

### Example 1

```text
Input: N = 3
Output: ["000", "001", "010", "100", "101"]
```

### Explanation

```text
The total possible binary strings of length 3 is 2³ = 8.
Strings like "011", "110", and "111" are removed because they contain consecutive 1s.
```

---

### Example 2

```text
Input: N = 1
Output: ["0", "1"]
```

---

# Intuition

This problem can be visually represented as a guided path exploration through a **State-Space Decision Tree**. For every character index slot from `0` to `N - 1`, we try to choose between appending a `'0'` or a `'1'`.

To avoid generating invalid sequences and filtering them afterward, we can enforce a **Lookback Validation Rule** on our branching logic:
1.  **Rule for '0':** We can **always** append a `'0'` at any position, regardless of what character came before it.
2.  **Rule for '1':** We can **only** append a `'1'` if the string is currently empty (index 0) **OR** if the immediate preceding character was a `'0'`. If the previous character was a `'1'`, choosing to append another `'1'` would violate our consecutive limit, so we prune that branch entirely.

By checking the last character before appending, our algorithm only explores paths that are guaranteed to be valid, reaching the base case leaf node with zero wasted operations.

---

# Brute Force Solution

## Idea

We use a simple recursive permutation framework to generate **all** \(2^N\) possible binary strings of length `N`. Once a string reaches full length, we run a linear scan pass over it to check for any adjacent `"11"` patterns, adding it to the results list only if it passes the filter.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> generateStrings(int N) {
        List<String> result = new ArrayList<>();
        generateAll(new char[N], 0, result);
        return result;
    }

    private void generateAll(char[] current, int pos, List<String> result) {
        if (pos == current.length) {
            if (isValid(current)) {
                result.add(new String(current));
            }
            return;
        }
        
        current[pos] = '0';
        generateAll(current, pos + 1, result);
        current[pos] = '1';
        generateAll(current, pos + 1, result);
    }

    private boolean isValid(char[] current) {
        for (int i = 0; i < current.length - 1; i++) {
            if (current[i] == '1' && current[i + 1] == '1') {
                return false; // Consecutive 1s found
            }
        }
        return true;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** \(O(N \times 2^N)\) because \(2^N\) strings are generated, and validating each string takes O(N) linear time.
- **Space Complexity:** O(N) auxiliary recursive stack depth allocation.

---

# State Definition (Optimal Backtracking Approach)

To formalise our combinatorial paths inside a clean, controlled recursive backtracking framework, we track state parameters via three contextual values:

```text
backtrack(pos, currentPath):
- 'pos' tracks our current position pointer in the character array string.
- 'currentPath' acts as a shared mutable array or string builder tracking the active state configuration.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Case:
If pos == N -> result.add(new String(currentPath)); return

Recursive Controlled Branching:
1. Always try branch '0':
   currentPath[pos] = '0'
   backtrack(pos + 1, currentPath)

2. Conditionally try branch '1':
   If pos == 0 OR currentPath[pos - 1] == '0':
       currentPath[pos] = '1'
       backtrack(pos + 1, currentPath)
```

---

# Optimal Controlled Backtracking Solution

## Idea

We allocate a single primitive `char[N]` buffer workspace to compile our strings. We start our recursive depth pass from position 0. By inspecting `currentPath[pos - 1]` before entering a `'1'` branching track, we prune invalid sequences early, ensuring that every leaf node reached holds a valid binary string combination.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> generateStrings(int N) {
        List<String> result = new ArrayList<>();
        if (N <= 0) {
            return result;
        }
        
        // Shared character array buffer workspace to prevent heap allocation garbage
        char[] currentPath = new char[N];
        
        // Launch backtracking pass from the 0-th position index
        backtrack(0, N, currentPath, result);
        return result;
    }

    private void backtrack(int pos, int N, char[] currentPath, List<String> result) {
        // Base Case: If the position cursor reaches N, a valid string layout is finalized
        if (pos == N) {
            result.add(new String(currentPath));
            return;
        }

        // Branch Choice 1: Append a '0' (Always valid at any index position)
        currentPath[pos] = '0';
        backtrack(pos + 1, N, currentPath, result); // Recurse deeper

        // Branch Choice 2: Append a '1' (Valid only if it's the first slot OR trails a '0')
        if (pos == 0 || currentPath[pos - 1] == '0') {
            currentPath[pos] = '1';
            backtrack(pos + 1, N, currentPath, result); // Recurse deeper
        }
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(Fibonacci(N + 2)) asymptotically matching O(1.618^N)
```
*Explanation:* The total number of valid binary strings of length `N` without consecutive 1s is equal to the (N+2)-th **Fibonacci Number**. Since our structural pruning rule ensures that we only visit valid branches, the number of recursive operations scales strictly with the count of valid outputs, keeping runtime highly optimal.

---

### Space Complexity

```text
Space Complexity: O(N) auxiliary space
```
*Explanation:* Excluding the structural memory blocks required to store and return the final string result collections, auxiliary storage space scales linearly at O(N) to accommodate the recursive call stack depth and the size of the shared `char[]` path buffer.

---

# Key Takeaways

# Key Takeaways

✅ Core fundamental paradigm for processing stateful sequence parsing and string generation tasks.

✅ **The Fibonacci Sequence Connection:** The total count of valid binary strings without consecutive 1s matches the **Fibonacci Sequence** progression (N=1 → 2, N=2 → 3, N=3 → 5, N=4 → 8). If a problem only asks for the *count* of such strings instead of printing them, it can be optimized down to an ultra-fast O(N) or \(O(\log N)\) Fibonacci DP table computation.

✅ Directly drives real-world microarchitectures like **asymmetric data communication encoders** (e.g., standard RLL code blocks that limit consecutive matching high-voltage signals to avoid clock drift), data compression modules, cryptographic key filters, and bit-stream protocol parsers.

✅ Common Architectural Paradigm:

```text
State-Lookahead Invariant Branching with Inline Workspace Buffer Reuses
```

### Similar Problems

- House Robber ([LeetCode 198](https://leetcode.com))
- Combination Sum ([LeetCode 39](https://leetcode.com))
- Letter Combinations of a Phone Number ([LeetCode 17](https://leetcode.com))
- Subsets / Power Set ([LeetCode 78](https://leetcode.com))
