# Daily Temperatures (LeetCode 739)

## Problem Statement

Given an array of integers `temperatures` represents the daily temperatures, return *an array `answer` such that `answer[i]` is the number of days you have to wait after the `i`-th day to get a warmer temperature*. 

If there is no future day for which this is possible, keep `answer[i] == 0` instead.

The overall runtime complexity must be strictly bounded by:

```text
O(N)
```

*(where N is the length of the temperatures array)*

---

## Examples

### Example 1

```java
Input: temperatures =
Output: 
```

### Explanation

```text
Evaluating elements from left to right:
- Day 0 (73): Wait 1 day until Day 1 (74).
- Day 1 (74): Wait 1 day until Day 2 (75).
- Day 2 (75): Wait 4 days until Day 6 (76).
- Day 3 (71): Wait 1 day until Day 4 (69? No, 72 is at Day 4).
- Day 4 (72): Wait 1 day until Day 5 (76).
- Day 5 (76): No future warmer days. Ans = 0.
- Day 6 (73): Wait 1 day until Day 7 (73? No, Day 7 is 73, not warmer. Wait, Day 6 is 73, Day 7 is 73. No future warmer days. Ans = 0).
- Day 7 (73): No future warmer days. Ans = 0.

Wait, looking closer at Example 1 indices:
73, 74, 75, 71, 69, 72, 76, 73
- 73 (index 0) -> 74 (index 1). Wait = 1 - 0 = 1.
- 74 (index 1) -> 75 (index 2). Wait = 2 - 1 = 1.
- 75 (index 2) -> 76 (index 6). Wait = 6 - 2 = 4.
- 71 (index 3) -> 72 (index 5). Wait = 5 - 3 = 2.
- 69 (index 4) -> 72 (index 5). Wait = 5 - 4 = 1.
- 72 (index 5) -> 76 (index 6). Wait = 6 - 5 = 1.
- 76 (index 6) -> None.         Wait = 0.
- 73 (index 7) -> None.         Wait = 0.

Output list: 
```

---

### Example 2

```java
Input: temperatures =
Output: 
```

---

### Example 3

```java
Input: temperatures =
Output: 
```

---

# Intuition

This problem can be structurally rephrased as finding the distance to the **Next Greater Element** for each element in an array.

A naive approach would check each day and scan ahead through all subsequent days using a nested loop, costing an inefficient O(N²) runtime.

To solve this problem in optimal **linear time**, we use a **Monotonic Decreasing Stack** processed from right to left:
- We want the stack to contain future day indices sorted such that their corresponding temperatures form a strictly decreasing sequence from bottom to top.
- As we traverse the array backward, any index at the top of the stack whose temperature is **less than or equal to** our current day's temperature can never be the next warmer day for us or for any days to our left. We greedily **pop** them off the stack.
- After clearing out colder or equal days, if the stack is not empty, the index sitting at the top represents the absolute closest future warmer day. The required waiting time is simply the index offset delta: `stack.peek() - i`.
- We record this delta in our results array and then push our current index `i` onto the stack to act as a candidate for preceding days.

---

# Brute Force Solution

## Idea

For each day, we run an inner loop scanning forward through all subsequent days until we encounter a temperature strictly greater than the current day's temperature. We calculate the difference between the two indices and save it. If the loop completes without finding a warmer day, the slot remains `0`.

---

## Java Code

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        if (temperatures == null || temperatures.length == 0) {
            return new int;
        }

        int n = temperatures.length;
        int[] result = new int[n];

        for (int i = 0; i < n; i++) {
            // Scan forward to find the next strictly warmer day
            for (int j = i + 1; j < n; j++) {
                if (temperatures[j] > temperatures[i]) {
                    result[i] = j - i; // Calculate day offset delta
                    break; // Warmer day found, exit inner loop
                }
            }
        }

        return result;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N²) due to the nested loops searching the remaining suffix space for every single day index.
- **Space Complexity:** O(1) auxiliary space as it updates the output array using basic scalar iteration variables.

---

# State Definition (Optimal Approach)

To optimize our performance to O(N) linear time, we maintain structural lookup context using a single monotonic stack tracking index frames:

```java
int[] result = new int[n];           // Output array storing day offset wait deltas
Stack<Integer> stack = new Stack<>(); // Monotonic stack tracking elements sorted decreasingly by temperature
```

---

# Recurrence / Stack Contract Condition

```java
for (int i = n - 1; i >= 0; i--) {
    while (!stack.isEmpty() && temperatures[stack.peek()] <= temperatures[i]) {
        stack.pop();
    }
    result[i] = stack.isEmpty() ? 0 : stack.peek() - i;
    stack.push(i);
}
```

---

# Optimal Monotonic Stack Solution

## Idea

By processing the days backward and utilizing a monotonic stack to store future indices, colder day candidates that lose all lookahead utility are aggressively pruned. This drops manual range searches, resolving all wait times inside a single linear **O(N)** pass.

---

## Java Code

```java
import java.util.Stack;

class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        if (temperatures == null || temperatures.length == 0) {
            return new int;
        }

        int n = temperatures.length;
        int[] result = new int[n];
        // Monotonic stack to store array indices instead of raw temperature values
        Stack<Integer> stack = new Stack<>();

        // Traversal pass processed backward from right to left
        for (int i = n - 1; i >= 0; i--) {
            int currentTemp = temperatures[i];

            // Maintain the monotonic decreasing property from bottom to top
            while (!stack.isEmpty() && temperatures[stack.peek()] <= currentTemp) {
                stack.pop(); // Pop indices with colder or equal temperatures
            }

            // If the stack is not empty, the top element is the next warmer day index
            if (!stack.isEmpty()) {
                result[i] = stack.peek() - i; // Calculate index offset delta
            } else {
                result[i] = 0; // No future warmer day available
            }

            // Push current day index onto the stack to act as a future candidate
            stack.push(i);
        }

        return result;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The array of length N is traversed exactly once. Each day index is pushed onto the stack exactly once and popped from the stack at most once across the entire backward sweep. The total number of stack operations is bounded by 2N, guaranteeing an optimal linear runtime footprint.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary storage space scales linearly to accommodate indices inside the stack tracking container in the worst-case scenario where elements are sorted in a strictly increasing sequence.

---

# Key Takeaways

✅ Core foundational design pattern mapping geometric lookahead distances onto a monotonic stack frame.

✅ **The Importance of Tracking Indices:** Storing indices instead of raw values inside the stack is a critical architectural rule for proximity problems. Indices provide dual utility: they allow us to query the actual values on-demand (`temperatures[stack.peek()]`) while enabling instant, constant-time distance calculation via basic subtraction (`stack.peek() - i`).

✅ Directly models production setups like **telemetry buffer latency monitoring pipelines**, stock trend technical alert modules, scheduling timeout managers, and climate tracking tools.

✅ Common Architectural Paradigm:

```text
Monotonic Index Pushing for Temporal Proximity Delta Tracking
```

### Similar Problems

- Next Greater Element I (LeetCode 496)
- Next Greater Element II (LeetCode 503)
- Online Stock Span (LeetCode 901)
- Largest Rectangle in Histogram (LeetCode 84)
- Sum of Subarray Minimums (LeetCode 907)
