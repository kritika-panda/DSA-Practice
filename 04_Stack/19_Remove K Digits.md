# Remove K Digits (LeetCode 402)

## Problem Statement

Given a string `num` representing a non-negative integer `num`, and an integer `k`, return *the smallest possible integer after removing `k` digits from `num`*.

---

## Examples

### Example 1

```java
Input: num = "1432219", k = 3
Output: "1219"
```

### Explanation

```text
Remove the three digits 4, 3, and 2 to form the new integer "1219" which is the smallest.
```

---

### Example 2

```java
Input: num = "10200", k = 1
Output: "200"
```

### Explanation

```text
Remove the leading 1 and the remaining number is "0200". 
After stripping the leading zeroes, the result is "200".
```

---

### Example 3

```java
Input: num = "10", k = 2
Output: "0"
```

### Explanation

```text
Remove all the digits from the number. Leftover is nothing, which represents "0".
```

---

# Intuition

To make a number as small as possible, we should try to minimize its most significant digits (the digits on the far left). For two numbers of the same length, the one with a smaller digit at the first mismatch point is strictly smaller (e.g., `1234` is smaller than `1324` because `2 < 3`).

This observation allows us to use a **Greedy Strategy** reinforced by a **Monotonic Increasing Stack**:
- As we scan the string from left to right, we want to maintain our digit collection in a non-decreasing order from bottom to top.
- If we see an incoming digit that is strictly **smaller** than the digit at the top of the stack, it means the digit at the top is creating a local peak ("peak/valley" inversion). Removing that larger top digit and replacing it with the smaller incoming digit will result in a smaller overall number.
- We greedily **pop** the top of the stack and decrement `k` as long as the top digit is larger than the current digit, the stack is not empty, and we still have removals left (`k > 0`).
- **Edge Case 1:** If the loop finishes and we still have remaining removals left (`k > 0`), it means the elements left in the stack are sorted in a perfectly increasing order. We simply drop the required number of characters from the tail (top of the stack).
- **Edge Case 2:** Strip any leading zeroes from our output buffer before returning. If the buffer is completely empty, return `"0"`.

---

# State Definition

Instead of using a standard utility `Stack` object which requires an extra conversion step to build our output string, we can use a **StringBuilder** or a **Character Array** directly to act as our LIFO stack framework:

```java
StringBuilder sb = new StringBuilder(); // Acts as our high-speed monotonic stack sequence
```

---

# Recurrence / Stack Contract Condition

```java
for (char digit : num.toCharArray()) {
    while (sb.length() > 0 && sb.charAt(sb.length() - 1) > digit && k > 0) {
        sb.deleteCharAt(sb.length() - 1);
        k--;
    }
    sb.append(digit);
}
```

---

# Optimal Monotonic Stack Solution

## Idea

By processing digits sequentially from left to right and aggressively popping out localized peak elements using a monotonic stack pattern, we ensure the number remains globally minimized. Resolving the remaining operations at the tail and cleaning up leading zeroes provides the result in a fast **O(N)** time footprint.

---

## Java Code

```java
class Solution {
    public String removeKdigits(String num, int k) {
        // Guard Condition: If we need to remove all digits, the result is automatically "0"
        if (num.length() == k) {
            return "0";
        }

        StringBuilder stack = new StringBuilder();

        // Step 1: Push digits sequentially, maintaining a monotonic increasing sequence
        for (int i = 0; i < num.length(); i++) {
            char digit = num.charAt(i);

            // Pop elements from the stack if the top is greater than the incoming digit
            while (stack.length() > 0 && stack.charAt(stack.length() - 1) > digit && k > 0) {
                stack.deleteCharAt(stack.length() - 1); // Pop from top
                k--;
            }
            stack.append(digit); // Push onto stack
        }

        // Step 2: Handle edge cases where elements are already sorted increasing (e.g., "1234", k=2)
        while (k > 0) {
            stack.deleteCharAt(stack.length() - 1);
            k--;
        }

        // Step 3: Strip leading zeroes using a pointer scan
        int leadingZeroesIndex = 0;
        while (leadingZeroesIndex < stack.length() && stack.charAt(leadingZeroesIndex) == '0') {
            leadingZeroesIndex++;
        }

        // Step 4: Extract the final trimmed string slice
        String result = stack.substring(leadingZeroesIndex);

        // If the resulting string is empty, return "0"
        return result.isEmpty() ? "0" : result;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The array is scanned exactly once. Each digit character is appended to the `StringBuilder` at most once and deleted from it at most once across the entire execution sweep. Total runtime operations are strictly bounded linearly relative to the string length \(N\).

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary space scales linearly to store the characters inside the `StringBuilder` stack container buffer, which can hold up to \(N\) digits in the worst-case scenario where zero items are popped.

---

# Key Takeaways

✅ Core fundamental design pattern for dynamic lexicographical stream optimization.

✅ **The Value of Peak Pruning:** When a problem requires building the minimum or maximum sequence by removing a fixed set of items, look for the inversion pattern. Removing a larger number that precedes a smaller number pushes down the magnitude of the highest available decimal column index position, yielding an optimal greedy result.

✅ Universally used inside **data streaming compression engines**, formatting processors, and string token cleaners.

✅ Common Architectural Paradigm:

```text
Monotonic Digit Pruning via LIFO String Accumulators
```

### Similar Problems

- Create Maximum Number (LeetCode 321)
- Find Most Competitive Subsequence (LeetCode 1673)
- Smallest Subsequence of Distinct Characters (LeetCode 1081)
- Next Greater Element I (LeetCode 496)
