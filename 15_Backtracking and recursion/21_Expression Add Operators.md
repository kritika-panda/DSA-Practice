# Expression Add Operators ([LeetCode 282](https://leetcode.com))

## Problem Statement

Given a string `num` that contains only digits and an integer `target`, return *all possibilities to insert the binary operators `+`, `-`, and/or `*` between the digits of `num` so that the resultant expression evaluates to the `target` value*.

Note that operands in the returned expressions **must not** contain leading zeros.

---

## Examples

### Example 1

```text
Input: num = "123", target = 6
Output: ["1+2+3", "1*2*3"]
```

### Explanation

```text
Both "1+2+3" and "1*2*3" evaluate exactly to 6.
```

---

### Example 2

```text
Input: num = "232", target = 8
Output: ["2*3+2", "2+3*2"]
```

---

### Example 3

```text
Input: num = "105", target = 5
Output: ["1*0+5", "10-5"]
```

### Explanation

```text
Note that "1*05" is invalid because "05" contains a forbidden leading zero.
```

---

# Intuition

This problem represents a complex **Combinatorial Search** problem over a variable character sequence. We have to decide not only which operator (`+`, `-`, `*`) to place between characters but also how many digits to combine to form an individual multi-digit operand (e.g., parsing `"123"` can treat `"12"` as a single number).

The core difficulty lies in preserving mathematical operator precedence—specifically handling how multiplication (`*`) overrides sequential addition and subtraction. For example, if our current evaluated expression is `2 + 3` (Value = `5`), and the next term to append is `* 4`, the standard linear evaluation would compute `(2 + 3) * 4 = 20`, which is mathematically incorrect. It should resolve to `2 + (3 * 4) = 14`.

To solve this in a single forward backtracking pass, we track a **`prevValue`** parameter representing the numeric value of the *immediate last operand* processed in the expression chain:
- **For `+` addition:** The new evaluation is `currentValue + currentNum`. The new `prevValue` becomes `currentNum`.
- **For `-` subtraction:** The new evaluation is `currentValue - currentNum`. The new `prevValue` becomes `-currentNum`.
- **For `*` multiplication:** To prioritize the multiplication, we must **roll back** the effect of the previous operand. We calculate the new evaluation as: `(currentValue - prevValue) + (prevValue * currentNum)`. The new intermediate `prevValue` becomes `prevValue * currentNum`.

---

# State Definition (Optimal Backtracking Approach)

To trace our expression path configurations systematically inside a deep recursive state-space search tree, we manage states via five parameters:

```text
backtrack(index, currentValue, prevValue, currentPath):
- 'index' tracks our active position pointer inside the source num string.
- 'currentValue' tracks the running mathematical evaluation result of the path.
- 'prevValue' caches the numeric payload value of the absolute last operand added.
- 'currentPath' acts as a shared mutable StringBuilder accumulating expression tokens.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Case:
If index == num.length():
    If currentValue == target -> result.add(currentPath.toString())
    Return

For i from index to num.length() - 1:
    If i > index AND num.charAt(index) == '0' -> Break (Leading Zero Guard)
    
    long currentNum = Long.parseLong(num.substring(index, i + 1))
    
    If index == 0 (First operand, no preceding operator):
        backtrack(i + 1, currentNum, currentNum, currentPath + currentNum)
    Else:
        // Try '+'
        backtrack(i + 1, currentValue + currentNum, currentNum, currentPath + "+" + currentNum)
        // Try '-'
        backtrack(i + 1, currentValue - currentNum, -currentNum, currentPath + "-" + currentNum)
        // Try '*' (Rollback Multiplication)
        backtrack(i + 1, (currentValue - prevValue) + (prevValue * currentNum), prevValue * currentNum, currentPath + "*" + currentNum)
```

---

# Optimal Backtracking Solution

## Idea

We use a high-speed recursive depth pass starting from position 0. By utilizing a single mutable `StringBuilder` and recording its length before mutations, we can easily strip away appended operators and digits during the backtrack phase. We use `long` registers to protect against integer overflows.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<String> addOperators(String num, int target) {
        List<String> result = new ArrayList<>();
        // Guard Condition: Empty strings contain no operands
        if (num == null || num.length() == 0) {
            return result;
        }

        // Shared mutable path buffer workspace to prevent memory thrashing on the heap
        StringBuilder currentPath = new StringBuilder();

        // Launch recursive backtracking search from index 0
        backtrack(0, 0, 0, target, num, currentPath, result);
        return result;
    }

    private void backtrack(int index, long currentValue, long prevValue, int target, String num, StringBuilder currentPath, List<String> result) {
        // Base Case: If the position cursor reaches string length, check if target is met
        if (index == num.length()) {
            if (currentValue == target) {
                result.add(currentPath.toString());
            }
            return;
        }

        int originalLength = currentPath.length();

        // Extend the current operand by pulling additional digits forward one by one
        for (int i = index; i < num.length(); i++) {
            // Leading Zero Guard: An operand cannot start with '0' if it spans multiple digits
            if (i > index && num.charAt(index) == '0') {
                break;
            }

            // Extract the multi-digit operand fragment string and parse to 64-bit long
            long currentNum = Long.parseLong(num.substring(index, i + 1));

            if (index == 0) {
                // First Number Case: No leading operator is placed at index 0
                currentPath.append(currentNum);
                backtrack(i + 1, currentNum, currentNum, target, num, currentPath, result);
                currentPath.setLength(originalLength); // Backtrack step (Restore original length)
            } else {
                // Choice Branch A: Try inserting the '+' operator
                currentPath.append('+').append(currentNum);
                backtrack(i + 1, currentValue + currentNum, currentNum, target, num, currentPath, result);
                currentPath.setLength(originalLength); // Backtrack step

                // Choice Branch B: Try inserting the '-' operator
                currentPath.append('-').append(currentNum);
                backtrack(i + 1, currentValue - currentNum, -currentNum, target, num, currentPath, result);
                currentPath.setLength(originalLength); // Backtrack step

                // Choice Branch C: Try inserting the '*' operator (Execute Rollback Math)
                currentPath.append('*').append(currentNum);
                long rolledBackValue = (currentValue - prevValue) + (prevValue * currentNum);
                backtrack(i + 1, rolledBackValue, prevValue * currentNum, target, num, currentPath, result);
                currentPath.setLength(originalLength); // Backtrack step
            }
        }
    }
}
```

---

## Complexity Analysis

Let N be the absolute length of the input digits string `num`.

### Time Complexity

```text
Time Complexity: O(4^N) worst case
```
*Explanation:* At each step between the characters of the string, we choose between 4 possible structural transitions: append a `+`, `-`, `*`, or no operator at all (joining digits to extend the operand length). This creates an expression tree whose states scale exponentially up to an asymptotic upper bound of \(O(4^N)\).

---

### Space Complexity

```text
Space Complexity: O(N) auxiliary space
```
*Explanation:* Excluding the structural memory blocks required to store and return the final expression strings, auxiliary storage space scales linearly at O(N) to accommodate the maximum depth height of the recursive call stack and the size of the shared `StringBuilder` buffer container.

---

# Key Takeaways

✅ High-tier fundamental backtracking challenge representing the peak of algebraic sequence exploration templates.

✅ **The Rollback Multiplication Rule:** Tracking `prevValue` lets you rewrite algebra on the fly without running multiple passes or using expensive postfix evaluation stack tools. Doing `(currentValue - prevValue) + (prevValue * currentNum)` dynamically overrides standard left-to-right evaluation bounds, enforcing strict operator precedence in-place.

✅ Directly drives real-world microarchitectures like **compilers mathematical expression evaluation passes**, algorithmic trade calculation routers, template evaluation engines, and automated macro calculation parsers.

✅ Common Architectural Paradigm:

```text
Algebraic Precedence Backtracking with Stateful Value Rollback Invariants
```

### Similar Problems

- Target Sum ([LeetCode 494](https://leetcode.com))
- Evaluate Reverse Polish Notation ([LeetCode 150](https://leetcode.com))
- Basic Calculator ([LeetCode 224](https://leetcode.com))
- Unique Binary Search Trees II ([LeetCode 95](https://leetcode.com))
