# Minimum Add to Make Parentheses Valid (LeetCode 921)

## Problem Statement

A parentheses string is valid if and only if:
1. It is the empty string,
2. It can be written as `AB` (A concatenated with B), where A and B are valid strings, or
3. It can be written as `(A)`, where A is a valid string.

You are given a parentheses string `s`. In one move, you can insert a parenthesis at any position of the string.
- For example, if `s = "())"`, you can insert an opening parenthesis to be `"(())"` or a closing parenthesis to be `"()))"`.

Return *the **minimum number of moves** required to make `s` valid*.

---

## Examples

### Example 1

```java
Input: s = "())"
Output: 1
```

### Explanation

```text
Insert an opening parenthesis at the beginning to get "(())", which is valid.
Total moves = 1.
```

---

### Example 2

```java
Input: s = "((("
Output: 3
```

### Explanation

```text
Insert three closing parentheses at the end to get "((()))", which is valid.
Total moves = 3.
```

---

# Intuition

To find the minimum insertions needed to balance a parentheses string:

- A closing parenthesis `)` can only balance out an opening parenthesis `(` that appeared **before** it.
- If we track the balance of unclosed opening parentheses dynamically from left to right, we can detect exactly when the sequence breaks:
  1. An opening parenthesis `(` increases our need for a closing parenthesis.
  2. A closing parenthesis `)` decreases our need for a closing parenthesis *if* there is an unmatched open parenthesis available.
  3. If a closing parenthesis `)` arrives and there are **no unmatched open parentheses left**, it is permanently stranded. It can never be closed by future characters. We must immediately add an opening parenthesis to fix it, increasing our insertion count.

---

# Greedy Observation

Consider the string:

```text
s = " ) ) ( "
```

Let's track unmatched brackets linearly:

```text
i = 0 (')') → No open brackets to match. This close bracket is stranded!
              We need 1 insertion immediately. strandedCloseCount = 1
i = 1 (')') → Still no open brackets. This close bracket is also stranded!
              strandedCloseCount = 2
i = 2 ('(') → An open bracket arrives. openCount = 1
```

At the end of the string, any remaining open brackets (`openCount = 1`) will also need closing insertions. 
Total required additions = `2 (stranded close) + 1 (leftover open) = 3`.

---

# Approach: Single-Pass Balance Tracking

## Algorithm

1. Initialize two counters:
   - `openCount = 0` → Tracks currently active, unmatched opening parentheses `(`.
   - `insertions = 0` → Tracks the total number of forced structural insertions.
2. Iterate through each character `c` in the string `s`:
   - If `c == '('`:
     - Increment `openCount`.
   - Otherwise (`c == ')'`):
     - If `openCount > 0`, this closing bracket successfully pairs with a preceding open bracket. Decrement `openCount`.
     - If `openCount == 0`, this closing bracket has no partner. It is permanently broken. Increment `insertions`.
3. After the loop, any leftover `openCount` represents open brackets that never found a closing partner. They also require insertions.
4. Total structural additions = `insertions + openCount`.

---

# Dry Run

```java
s = "()))(("
```

### Initial State

```text
openCount = 0
insertions = 0
```

---

### Step-by-Step Execution

```text
i = 0 ('(') → openCount = 1, insertions = 0
i = 1 (')') → openCount > 0? ✅ (1) -> openCount = 0, insertions = 0
i = 2 (')') → openCount > 0? ❌ (0) -> openCount = 0, insertions = 1 (Stranded close!)
i = 3 (')') → openCount > 0? ❌ (0) -> openCount = 0, insertions = 2 (Stranded close!)
i = 4 ('(') → openCount = 1, insertions = 2
i = 5 ('(') → openCount = 2, insertions = 2
```

Loop ends.

Final Calculation: `insertions (2) + openCount (2) = 4`.

Answer:

```text
4
```

---

# Java Solution

```java
class Solution {
    public int minAddToMakeValid(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        int openCount = 0;
        int insertions = 0;

        // Traverse the string linearly to monitor balance thresholds
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);

            if (c == '(') {
                // An opening bracket creates a demand for a future closing bracket
                openCount++;
            } else {
                // A closing bracket arrives
                if (openCount > 0) {
                    // Valid match: pair it up with the most recent open bracket
                    openCount--;
                } else {
                    // Invalid/Stranded match: we must insert an opening bracket here
                    insertions++;
                }
            }
        }

        // Add remaining unclosed opening brackets to the total insertion cost
        return insertions + openCount;
    }
}
```

---

# Complexity Analysis

Let:

```text
n = s.length()
```

### Time Complexity

Single-pass loop traversal:

```text
O(n)
```

Overall:

```text
O(n)
```

---

### Space Complexity

```text
O(1)
```

Highly efficient. It requires only two scalar primitive integer registers, avoiding the heap allocation overhead of an explicit `Stack` structure.

---

# Key Pattern

This problem belongs to:

```text
Greedy + Linear Balance Tracking + Stack Elimination
```

Common indicators:

- Finding the minimum additions, removals, or edits to balance matching bracket sets
- Stream processing of paired tokens where lookback can be simplified to a scalar counter
- Strict chronological pairing dependencies

---

# Similar Problems

1. Valid Parentheses (LeetCode 20)
2. Longest Valid Parentheses (LeetCode 32)
3. Minimum Remove to Make Valid Parentheses (LeetCode 1249)
4. Check if a Parentheses String Can Be Valid (LeetCode 2116)
5. Minimum Insertions to Balance a Parentheses String (LeetCode 1541)

---

# Takeaway

```text
Track bracket balances using space-efficient O(1) integer counters.

Treat isolated closing brackets as permanent failures that require immediate insertion updates.

Combine left-over open counts with current failures at the terminal boundary for the total score.
```

**Pattern:** Greedy + Linear Balance Tracking + Stack Elimination

<FollowUp>
We can scale this balance pattern to handle more complex bracket constraints. If you'd like, let me know if we should generate the raw markdown code for **Minimum Remove to Make Valid Parentheses (LeetCode 1249)** or explore the double-balance rules of **Minimum Insertions to Balance a Parentheses String (LeetCode 1541)**!
</FollowUp>
