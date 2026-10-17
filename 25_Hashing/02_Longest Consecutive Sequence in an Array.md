# Longest Consecutive Sequence in an Array

**LeetCode 128 - Longest Consecutive Sequence**

## Problem Statement

Given an unsorted array of integers `nums`, return the length of the longest consecutive elements sequence.

You must write an algorithm that runs in `O(N)` time.

---

## Example

```java
int[] nums = {100, 4, 200, 1, 3, 2};

Solution sol = new Solution();
sol.longestConsecutive(nums); // Returns 4
```

**Explanation:** The longest consecutive elements sequence is `[1, 2, 3, 4]`. Therefore, its length is 4.

---

# Intuition

A naive approach would be to sort the array and look for consecutive items. However, sorting takes `O(N log N)` time, which violates the strict `O(N)` time constraint.

To achieve linear time complexity, we need a way to look up elements in O(1) constant time. We can achieve this by storing all elements inside a **HashSet**. 

Once the numbers are in the set, we can identify the **start** of any potential sequence. A number `num` is the start of a sequence only if `num - 1` does not exist in our set. Once we identify a starting number, we simply count how many consecutive numbers (`num + 1`, `num + 2`, etc.) follow it.

---

# Approach: HashSet Optimization

## Data Structure

```java
Set<Integer> numSet
```
Used to store all numbers from the array to allow O(1) lookup operations.

---

## Algorithm Steps

1. Insert all elements of `nums` into a `HashSet`.
2. Iterate through each number `num` in the set:
   - Check if `num` is the **beginning** of a sequence by checking if `num - 1` is in the set.
   - If `num - 1` **is** in the set, skip it (this element will be counted when we find its sequence's actual head).
   - If `num - 1` **is not** in the set, this is the start of a sequence. Start a loop to find `num + 1`, `num + 2`, etc., and track the current sequence length.
3. Update the global `maxLength` found across all valid sequences.

---

# Dry Run

### Input
```java
nums = [100, 4, 200, 1, 3, 2]
```

### Execution Trace

- **Step 1:** Build `numSet` = `{100, 4, 200, 1, 3, 2}`

- **Step 2:** Process elements in `numSet`:
  - `num = 100`: Is `99` in the set? No. Start sequence from `100`. Next element `101` is missing. Sequence length = `1`. `maxLength` = `1`.
  - `num = 4`: Is `3` in the set? Yes. Skip (it's not the start).
  - `num = 200`: Is `199` in the set? No. Start sequence from `200`. Next element `201` is missing. Sequence length = `1`. `maxLength` = `1`.
  - `num = 1`: Is `0` in the set? No. Start sequence from `1`.
    - Check `2` (found), Check `3` (found), Check `4` (found), Check `5` (missing).
    - Sequence length = `4` (`[1, 2, 3, 4]`). `maxLength` updates to `4`.
  - `num = 3`: Is `2` in the set? Yes. Skip.
  - `num = 2`: Is `1` in the set? Yes. Skip.

### Result
```java
maxLength = 4
```

---

# Java Solution

```java
import java.util.*;

class Solution {
    public int longestConsecutive(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }

        Set<Integer> numSet = new HashSet<>();
        for (int num : nums) {
            numSet.add(num);
        }

        int maxLength = 0;

        for (int num : numSet) {
            // Check if 'num' is the start of a sequence
            if (!numSet.contains(num - 1)) {
                int currentNum = num;
                int currentLength = 1;

                // Count how far the consecutive sequence extends
                while (numSet.contains(currentNum + 1)) {
                    currentNum += 1;
                    currentLength += 1;
                }

                maxLength = Math.max(maxLength, currentLength);
            }
        }

        return maxLength;
    }
}
```

---

# Complexity Analysis

### Time Complexity

```java
O(N)
```
Although it looks like a nested loop (`for` inside `while`), it runs in linear time. An element is only visited inside the `while` loop if it is part of a sequence whose starting element was found. Across the entire execution, each number is touched at most twice (once in the outer loop, and once within a `while` loop sequence sweep).

### Space Complexity

```java
O(N)
```
To store the elements within the `HashSet`.

---

# Comparison of Approaches

| Approach | Time Complexity | Space Complexity | Pros / Cons |
|---|---|---|---|
| **Sorting** | \(O(N \log N)\) | O(1) or O(N) depending on sort | Simple, but fails the strict O(N) interview requirement. |
| **HashSet (Optimal)** | O(N) | O(N) | Meets optimal bounds; requires auxiliary memory. |

---

# Key Takeaways

- Use a **HashSet** to convert expensive linear lookups into immediate O(1) time checks.
- Prevent duplicate sequence scanning by checking `num - 1`. If `num - 1` exists, skip processing until you land on the true anchor node of the sequence.
- Core concepts:
  - Hashing
  - Array Sequences
  - Optimization via Boundary Filtering
- Expected interview preference: **HashSet Approach**.
