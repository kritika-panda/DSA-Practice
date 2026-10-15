# Baseball Game (LeetCode 682)

## Problem Statement

You are keeping the scores for a baseball game with strange rules. The game consists of several rounds, where the scores of past rounds may affect future rounds' scores.

At the beginning of the game, you start with an empty record. You are given a list of strings `operations`, where `operations[i]` is the `i`-th operation you must apply to the record and is one of the following:

1. **An integer `x`**: Record a new score of `x`.
2. **`"+"`**: Record a new score that is the sum of the previous two scores. It is guaranteed there will always be at least two previous scores.
3. **`"D"`**: Record a new score that is double the previous score. It is guaranteed there will always be at least one previous score.
4. **`"C"`**: Invalidate the previous score, removing it from the record. It is guaranteed there will always be at least one previous score.

Return *the **sum of all the scores** on the record after applying all the operations*.

The overall runtime complexity must be strictly bounded by:

```text
O(N)
```

*(where N is the length of the operations array)*

---

## Examples

### Example 1

```java
Input: operations = ["5","2","C","D","+"]
Output: 30
```

### Explanation

```text
- "5" - Add 5 to the record, record is now.
- "2" - Add 2 to the record, record is now.
- "C" - Invalidate and remove the last score, record is now.
- "D" - Add 2 * 5 = 10 to the record, record is now.
- "+" - Add 5 + 10 = 15 to the record, record is now.
The total sum is 5 + 10 + 15 = 30.
```

---

### Example 2

```java
Input: operations = ["5","-2","4","C","D","9","+","+"]
Output: 27
```

---

### Example 3

```java
Input: operations = ["1","C"]
Output: 0
```

---

# Intuition

This problem can be effectively modeled as a **Last-In, First-Out (LIFO)** simulation problem. 

The operations depend heavily on the most recent scores added to the record:
- An integer `x` simply appends to the top of our record.
- `"C"` invalidates the most recent score, which translates to a standard **pop** operation.
- `"D"` looks at the top element to double it.
- `"+"` requires reading the two top-most elements to compute their sum.

Because we are constantly adding, removing, and reading from the absolute end of our history timeline, a **Stack** or a dynamically resizable **ArrayList/List** acting as a stack is the perfect data structure to manage our operational history cleanly.

---

# Brute Force Solution

## Idea

We allocate an expandable storage array list to act as our score history logger. We iterate through the string elements one by one. If we encounter a `"C"`, we delete the tail item. If it is a `"D"`, we multiply the tail item by 2 and append it. For a `"+"`, we calculate the sum of the two tail items and append it. Otherwise, we parse the raw string into an integer. Finally, we run a separate loop at the end to add up everything inside our list.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public int calPoints(String[] operations) {
        if (operations == null || operations.length == 0) {
            return 0;
        }

        List<Integer> history = new ArrayList<>();

        for (String op : operations) {
            if (op.equals("C")) {
                // Invalidate the last valid score entry
                history.remove(history.size() - 1);
            } else if (op.equals("D")) {
                // Double the last valid score entry
                int lastScore = history.get(history.size() - 1);
                history.add(2 * lastScore);
            } else if (op.equals("+")) {
                // Sum the previous two score entries
                int lastScore = history.get(history.size() - 1);
                int secondLastScore = history.get(history.size() - 2);
                history.add(lastScore + secondLastScore);
            } else {
                // Parse and record a new baseline score
                history.add(Integer.parseInt(op));
            }
        }

        // Aggregate total score sum metrics across history
        int totalSum = 0;
        for (int score : history) {
            totalSum += score;
        }

        return totalSum;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because adding and removing from the end of an `ArrayList` takes O(1) amortized time, and we iterate through the list of length N exactly once.
- **Space Complexity:** O(N) to store the scores inside our auxiliary history collection list.

---

# State Definition (Optimal Approach)

To optimize our performance and compute the total sum on-the-fly inside a single unified pass without needing a secondary summation loop, we can use a primitive **Integer Array Stack** layout:

```java
int[] stack = new int[operations.length]; // Array simulating a high-speed primitive LIFO stack
int top = -1;                            // Pointer index tracking the top of the stack
int totalSum = 0;                        // Rolling variable aggregating the score sum dynamically
```

---

# Recurrence / Structural State Condition

```java
if (op.equals("C")) {
    totalSum -= stack[top];
    top--;
} else if (op.equals("D")) {
    int newScore = 2 * stack[top];
    top++;
    stack[top] = newScore;
    totalSum += newScore;
} else if (op.equals("+")) {
    int newScore = stack[top] + stack[top - 1];
    top++;
    stack[top] = newScore;
    totalSum += newScore;
} else {
    int newScore = Integer.parseInt(op);
    top++;
    stack[top] = newScore;
    totalSum += newScore;
}
```

---

# Optimal Primitive Stack Solution

## Idea

By utilizing a primitive integer array instead of object-wrapped collections and maintaining a rolling accumulator tracking the `totalSum` during the primary iteration loop, we eliminate runtime type boxing overhead and avoid secondary collection loops. This tracks all updates in a high-speed **O(N)** time footprint.

---

## Java Code

```java
class Solution {
    public int calPoints(String[] operations) {
        if (operations == null || operations.length == 0) {
            return 0;
        }

        int n = operations.length;
        // Allocate a flat array stack to bypass wrapper class overhead
        int[] stack = new int[n];
        int top = -1;
        int totalSum = 0;

        for (String op : operations) {
            switch (op) {
                case "C":
                    // Subtract the invalidated score from our running total sum
                    totalSum -= stack[top];
                    top--; // Pop from stack
                    break;
                    
                case "D":
                    // Double the top element and record it
                    int doubledScore = 2 * stack[top];
                    top++;
                    stack[top] = doubledScore;
                    totalSum += doubledScore;
                    break;
                    
                case "+":
                    // Sum the top two elements and record it
                    int sumScore = stack[top] + stack[top - 1];
                    top++;
                    stack[top] = sumScore;
                    totalSum += sumScore;
                    break;
                    
                default:
                    // Parse raw numeric string token entries
                    int numericScore = Integer.parseInt(op);
                    top++;
                    stack[top] = numericScore;
                    totalSum += numericScore;
                    break;
            }
        }

        return totalSum;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The algorithm performs a single pass over the `operations` array of length N. Inside the loop, every operation maps directly to constant-time array writes and basic primitive arithmetic operations, keeping the time complexity strictly linear.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary space scales linearly to allocate the primitive `stack` storage array capable of holding up to N game round scores concurrently.

---

# Key Takeaways

✅ Core fundamental linear tracking simulation matching structural stack state conditions.

✅ **The Value of In-Loop Aggregation:** Instead of building a record and running a separate aggregation loop to count the points at the end, tracking a rolling subtraction/addition counter variable (`totalSum`) inside the primary switch map optimizes our cache usage and speeds up processing time.

✅ Direct production blueprint matching **transaction undo/redo state trackers**, text editors tracking macro text layers history, and telemetry scoring systems.

✅ Common Architectural Paradigm:

```text
Rolling Balance Invalidation via LIFO Memory Offsets
```

### Similar Problems

- Valid Parentheses (LeetCode 20)
- Asteroid Collision (LeetCode 735)
- Backspace String Compare (LeetCode 844)
- Remove All Adjacent Duplicates In String (LeetCode 1047)
