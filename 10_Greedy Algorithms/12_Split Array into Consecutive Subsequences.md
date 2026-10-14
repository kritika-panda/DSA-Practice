# Split Array into Consecutive Subsequences (LeetCode 659)

## Problem Statement

You are given an integer array `nums` that is **sorted in non-decreasing order**.

Determine if it is possible to split `nums` into one or more subsequences such that both of the following conditions are true:
1. Each subsequence is a **strictly consecutive increasing sequence** (i.e., each integer is exactly `1` more than the previous integer).
2. All subsequences have a length of **`3` or more**.

Return `true` *if you can split `nums` according to the above conditions, or `false` otherwise*.

---

## Examples

### Example 1

```java
Input: nums = [1,2,3,3,4,5]
Output: true
```

### Explanation

```text
We can split nums into two valid subsequences:
- Subsequence 1: [1, 2, 3] (Length 3, consecutive)
- Subsequence 2: [3, 4, 5] (Length 3, consecutive)

Both conditions are met. Return true.
```

---

### Example 2

```java
Input: nums = [1,2,3,3,4,4,5,5]
Output: true
```

### Explanation

```text
We can split nums into two valid subsequences:
- Subsequence 1: [1, 2, 3, 4, 5] (Length 5, consecutive)
- Subsequence 2: [3, 4, 5]       (Length 3, consecutive)

Both conditions are met. Return true.
```

---

### Example 3

```java
Input: nums = [1,2,3,4,4,5]
Output: false
```

### Explanation

```text
If we try to split, we can form, leaving an isolated.
Alternatively, we can form [1, 2, 3] and, but [4, 5] only has a length of 2.
It is impossible to satisfy the constraints. Return false.
```

---

# Intuition

To maximize our chances of placing every element into a valid consecutive subsequence of length $\ge 3$:

- When processing a number `x`, we have two choices:
  1. **Append it** to an already existing valid consecutive subsequence that ends at `x - 1`.
  2. **Start a new** independent subsequence beginning at `x`. For this new sequence to be valid, we must guarantee that `x + 1` and `x + 2` are also available in our array right now.
- **The Greedy Rule:** Always prioritize **Choice 1** over **Choice 2**. 
  - *Why?* An existing valid sequence already satisfies the length $\ge 3$ rule. Adding `x` to it keeps it valid and avoids the immediate liability of needing to find two more elements (`x + 1` and `x + 2`) to clear the length threshold.

---

# Greedy Observation

We can maintain two hash maps to simulate our choices in real time:
1. `countMap`: Keeps track of the frequencies of remaining numbers in the array.
2. `hypotheticalSubsequences`: Keeps track of the number of active subsequences ending at any specific value.

```text
Number processed: x
```

```text
Choice A: Check hypotheticalSubsequences.get(x - 1) > 0.
          If true, append x here. (hypotheticalSubsequences of x-1 decreases, x increases).

Choice B: If Choice A fails, check if countMap has both x + 1 and x + 2.
          If true, seed a new sequence. (Decrement counts for x, x+1, x+2; hypotheticalSubsequences of x+2 increases).
```

Prioritizing Choice A keeps our strategy clean and globally optimal.

---

# Approach: Frequency Maps + Greedy Choice Selection

## Algorithm

1. Populate a frequency map `countMap` with all numbers in `nums`.
2. Initialize a frequency map `hypotheticalSubsequences` to track the ending numbers of valid sequences.
3. Iterate through each `x` in `nums`:
   - If `countMap.get(x) == 0`, it means `x` has already been fully consumed by a previous sequence creation step. Skip it.
   - **Choice 1 (Append):** If `hypotheticalSubsequences.getOrDefault(x - 1, 0) > 0`:
     - Decrement `hypotheticalSubsequences` for `x - 1`.
     - Increment `hypotheticalSubsequences` for `x`.
     - Decrement `countMap` for `x`.
   - **Choice 2 (Create New):** If Choice 1 fails, check if `x + 1` and `x + 2` are available in `countMap`:
     - If either `countMap.getOrDefault(x + 1, 0) == 0` or `countMap.getOrDefault(x + 2, 0) == 0`, return `false`.
     - Otherwise, consume them: decrement `countMap` for `x`, `x + 1`, and `x + 2`.
     - Register the new sequence: increment `hypotheticalSubsequences` for `x + 2`.
4. If the loop completes successfully, it means every element was correctly categorized into valid blocks. Return `true`.

---

# Dry Run

```java
nums = [1,2,3,3,4,5]
```

### Initial State

```text
countMap = {1:1, 2:1, 3:2, 4:1, 5:1}
hypotheticalSubsequences = {}
```

---

### Step 1 (x = 1)

```text
countMap[1] = 1.
Can append to 0? ❌ (hypotheticalSubsequences[0] is 0)
Can form new sequence?
- countMap[2] > 0 ? ✅
- countMap[3] > 0 ? ✅

Consume elements: countMap[1]--, countMap[2]--, countMap[3]--
Register sequence end: hypotheticalSubsequences[3]++

State:
countMap = {1:0, 2:0, 3:1, 4:1, 5:1}
hypotheticalSubsequences = {3:1}
```

---

### Step 2 (x = 2)

```text
countMap[2] == 0. Already consumed. Skip!
```

---

### Step 3 (x = 3)

```text
countMap[3] == 1.
Can append to 2? ❌ (hypotheticalSubsequences[2] is 0)
Can form new sequence?
- countMap[4] > 0 ? ✅
- countMap[5] > 0 ? ✅

Consume elements: countMap[3]--, countMap[4]--, countMap[5]--
Register sequence end: hypotheticalSubsequences[5]++

State:
countMap = {3:0, 4:0, 5:0}
hypotheticalSubsequences = {3:1, 5:1}
```

Remaining items are already consumed. Loop ends.

Answer: `true`

---

# Java Solution

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public boolean isPossible(int[] nums) {
        Map<Integer, Integer> countMap = new HashMap<>();
        Map<Integer, Integer> hypotheticalSubsequences = new HashMap<>();

        // Step 1: Populate total frequencies of elements
        for (int num : nums) {
            countMap.put(num, countMap.getOrDefault(num, 0) + 1);
        }

        // Step 2: Greedily evaluate positions for each element
        for (int x : nums) {
            // Element has already been consumed by a lookahead hook
            if (countMap.get(x) == 0) {
                continue;
            }

            // Choice 1: Prioritize appending to an existing valid sequence
            if (hypotheticalSubsequences.getOrDefault(x - 1, 0) > 0) {
                countMap.put(x, countMap.get(x) - 1);
                hypotheticalSubsequences.put(x - 1, hypotheticalSubsequences.get(x - 1) - 1);
                hypotheticalSubsequences.put(x, hypotheticalSubsequences.getOrDefault(x, 0) + 1);
            } 
            // Choice 2: Fall back to forming a brand new sequence of length 3
            else if (countMap.getOrDefault(x + 1, 0) > 0 && countMap.getOrDefault(x + 2, 0) > 0) {
                countMap.put(x, countMap.get(x) - 1);
                countMap.put(x + 1, countMap.get(x + 1) - 1);
                countMap.put(x + 2, countMap.get(x + 2) - 1);
                
                hypotheticalSubsequences.put(x + 2, hypotheticalSubsequences.getOrDefault(x + 2, 0) + 1);
            } 
            // Neither choice can accommodate the element; split fails
            else {
                return false;
            }
        }

        return true;
    }
}
```

---

# Optimized Understanding

Whenever `hypotheticalSubsequences.get(x - 1) > 0`, expanding the sequence length from $L \rightarrow L+1$ carries **zero risk** because the subsequence has already met the length limit of 3. 

If we choose to form a new sequence instead, we force ourselves to find `x + 1` and `x + 2`. This leaves fewer remaining resources for previous sequences that might still be incomplete (under length 3). This greedy ordering ensures maximum flexibility.

---

# Correctness Proof

### Greedy Choice

We always prioritize expanding an existing sequence over initiating a new one.

Suppose we instead initiated a new sequence starting at `x` while a sequence ending at `x - 1` lay open. 

```text
Sequence 1 ends at: x - 1
Sequence 2 starts at: x, x+1, x+2
```

If we run out of elements immediately after this, Sequence 1 is left valid. However, if we had instead chosen to append `x` to Sequence 1, the elements `x + 1` and `x + 2` would remain completely free. These elements can then be used by other parts of the array, which creates equal or better global outcomes.

---

### Optimal Substructure

After assigning an element `x` to a sequence optimally:

```text
Remaining frequency configurations inside countMap
Remaining sequence tracking parameters
```

form a reduced subproblem of the exact same nature. Solving this subproblem optimally builds the global solution.

---

# Complexity Analysis

Let:

```text
n = nums.length
```

### Time Complexity

Frequency pre-population:

```text
O(n)
```

Greedy loop processing pass:

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
O(n)
```

In the worst-case scenario where all elements are unique, the hash maps store keys proportional to $n$.

---

# Key Pattern

This problem belongs to:

```text
Greedy + Dual Hashing Counter Simulation
```

Common indicators:

• Partitioning an array into continuous chunks meeting length limits
• Real-time choice tracking with lookahead elements
• Elements must be consumed sequentially without backtracking
