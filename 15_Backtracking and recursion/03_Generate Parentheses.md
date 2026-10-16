# Generate Parentheses ([LeetCode 22](https://leetcode.com))

## Problem Statement

Given `n` pairs of parentheses, write a function to *generate all combinations of well-formed parentheses*.

---

## Examples

### Example 1

```text
Input: n = 3
Output: ["((()))","(()())","(())()","()(())","()()()"]
```

---

### Example 2

```text
Input: n = 1
Output: ["()"]
```

---

# Intuition

Generating all valid combinations of parentheses is a classical **Combinatorial Search** problem that maps onto a state-space decision tree. At any given point during string construction, we have up to two choices: we can either append an opening parenthesis `'('` or a closing parenthesis `')'`. 

To ensure that we only generate **well-formed (valid)** parenthesis combinations, we can enforce two simple structural counting constraints on our recursive choices:
1. **Opening Rule:** We can always append an opening parenthesis `'('` as long as the count of open parentheses used so far is strictly less than `n` (`openCount < n`).
2. **Closing Rule:** We can only append a closing parenthesis `')'` if the number of closing parentheses used so far is strictly less than the number of opening parentheses currently in the string (`closeCount < openCount`). This prevents invalid states like `")"`.

By tracking these boundary integers, we prune invalid branches early and guarantee that every path reaching a length of `2 * n` is perfectly well-formed.

---

# Brute Force Solution

## Idea

A naive solution generates **all** possible \(2^{2n}\) strings of length `2n` consisting of `'('` and `')'` characters using a simple recursive permutation generator. Once a string reaches full length, we run a linear stack-based validation sweep over it. We only add the string to our results collection if it passes the structural balance check. This approach is highly inefficient for larger values of `n`.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> result = new ArrayList<>();
        generateAll(new char[2 * n], 0, result);
        return result;
    }

    private void generateAll(char[] current, int pos, List<String> result) {
        if (pos == current.length) {
            if (isValid(current)) {
                result.add(new String(current));
            }
            return;
        }
        
        current[pos] = '(';
        generateAll(current, pos + 1, result);
        current[pos] = ')';
        generateAll(current, pos + 1, result);
    }

    private boolean isValid(char[] current) {
        int balance = 0;
        for (char c : current) {
            if (c == '(') balance++;
            else balance--;
            if (balance < 0) return false; // Closing bracket out of order
        }
        return balance == 0;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** \(O(2^{2n} \times n)\) because there are \(2^{2n}\) possible strings generated, and the validation step checks each string in O(n) time.
- **Space Complexity:** O(n) auxiliary call stack memory depth allocation.

---

# State Definition (Optimal Backtracking Approach)

To model our valid combinatorial decision tree explicitly using an optimized recursive backtracking framework, we track state constraints using two tracking counter variables:

```text
backtrack(openCount, closeCount, currentPath):
- 'openCount' tracks the total opening brackets '(' used so far.
- 'closeCount' tracks the total closing brackets ')' used so far.
- 'currentPath' acts as a mutable string builder tracking the active combination.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Case:
If currentPath.length() == 2 * n -> result.add(currentPath.toString()); return

Recursive Controlled Branching:
1. If openCount < n:
   currentPath.append('(')
   backtrack(openCount + 1, closeCount, currentPath)
   currentPath.deleteCharAt(currentPath.length() - 1) // Backtrack step

2. If closeCount < openCount:
   currentPath.append(')')
   backtrack(openCount, closeCount + 1, currentPath)
   currentPath.deleteCharAt(currentPath.length() - 1) // Backtrack step
```

---

# Optimal Backtracking Solution

## Idea

We launch a guided depth-first recursive search starting with our open and close counters set to `0`. By tracking choices using a mutable `StringBuilder` and removing the terminal character right after a recursive track completes, we explore all valid combination spaces without duplicating garbage objects on the heap.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> result = new ArrayList<>();
        // Launch backtracking pass with an empty tracking buffer
        backtrack(0, 0, n, new StringBuilder(), result);
        return result;
    }

    private void backtrack(int openCount, int closeCount, int max, StringBuilder currentPath, List<String> result) {
        // Base Case: If the string length reaches 2 * max, a well-formed combination is finalized
        if (currentPath.length() == 2 * max) {
            result.add(currentPath.toString());
            return;
        }

        // Branch Choice 1: Append an opening parenthesis if we haven't reached the maximum limit
        if (openCount < max) {
            currentPath.append('(');
            backtrack(openCount + 1, closeCount, max, currentPath, result); // Recurse deeper
            
            // Backtrack Step: Delete the trailing character to clean the state for the next branch
            currentPath.deleteCharAt(currentPath.length() - 1);
        }

        // Branch Choice 2: Append a closing parenthesis if it wouldn't violate the balance invariant
        if (closeCount < openCount) {
            currentPath.append(')');
            backtrack(openCount, closeCount + 1, max, currentPath, result); // Recurse deeper
            
            // Backtrack Step: Delete the trailing character to clean the state for the next branch
            currentPath.deleteCharAt(currentPath.length() - 1);
        }
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(4^n / √n)
```
*Explanation:* The total number of valid well-formed parenthesis combinations is bounded exactly by the n-th **Catalan Number**, which scales asymptotically bounded by \(\frac{4^n}{n\sqrt{n}}\). Since each valid string combination of length 2n is generated directly without visiting invalid path states, the time complexity matches the Catalan progression.

---

### Space Complexity

```text
Space Complexity: O(n) auxiliary space
```
*Explanation:* Excluding the structural memory blocks required to store and return the final string collections, auxiliary storage space scales linearly at O(n) to accommodate the deep recursive call stack and the max size of the shared `StringBuilder` path buffer.

---

# Key Takeaways

✅ Core fundamental paradigm for processing structured recursive language syntax and language parsing trees.

✅ **The Value of Backtracking Pruning:** Instead of generating all possible answers and filtering them afterward, embedding validation checks directly into the branching statement (`openCount < max` and `closeCount < openCount`) transforms an exponential problem into an optimized Catalan search.

✅ Directly drives real-world architectures like **compiler syntax token validators**, mathematical text bracket parsers, serialization engines, HTML/XML tag nested block formatting verification tools, and language processors.

✅ Common Architectural Paradigm:

```text
State-Bounded Binary Decision Trees with Trailing Buffer De-allocations
```

### Similar Problems

- Valid Parentheses ([LeetCode 20](https://leetcode.com))
- Letter Combinations of a Phone Number ([LeetCode 17](https://leetcode.com))
- Subsets / Power Set ([LeetCode 78](https://leetcode.com))
- Check if a Parentheses String Can Be Valid ([LeetCode 2116](https://leetcode.com))
