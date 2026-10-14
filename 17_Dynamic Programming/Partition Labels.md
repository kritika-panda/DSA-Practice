# Partition Labels (LeetCode 763)

## Problem Statement

You are given a string `s`. We want to partition the string into as many parts as possible so that each letter appears in at most one part.

Note that the partition is done so that after concatenating all the parts in order, the resultant string should be `s`.

Return *a list of integers representing the size of these parts*.

---

## Examples

### Example 1

```java
Input: s = "ababcbacadefegdehijhklij"
Output: [9,7,8]
```

### Explanation

```text
The partition is "ababcbaca", "defegde", "hijhklij".
This is a valid partition because each letter appears in at most one part.
A partition like "ababcbacadefegde", "hijhklij" is incorrect because it uses fewer parts.
```

---

### Example 2

```java
Input: s = "eccbbbbdec"
Output: [10]
```

---

# Intuition

To split the string into the maximum number of valid parts:

- If a letter appears multiple times in the string, all occurrences of that letter must be grouped inside the **same partition**.
- This means the moment we include a character in a partition, the partition's boundary must expand to include at least its **absolute last occurrence** in the string.
- We can track this boundary greedily by pre-recording the final index position of every character. As we scan the string linearly, we update our active partition window's horizon to the maximum last-index of any character seen inside it. When our pointer catches up to that horizon boundary, a self-contained partition is closed.

---

# Greedy Observation

Consider the string:

```text
s = "abacb"
```

Let's record the last seen index for each unique character:

```text
'a' -> index 2
'b' -> index 4
'c' -> index 3
```

Now, track the active window horizon linearly:

```text
i = 0 ('a') → Horizon must expand to at least index 2. Horizon = 2
i = 1 ('b') → Horizon must expand to at least index 4. Horizon = max(2, 4) = 4
i = 2 ('a') → Last position is 2. Horizon stays 4
i = 3 ('c') → Last position is 3. Horizon stays 4
i = 4 ('b') → Pointer catches up to Horizon (4 == 4) 
              Partition closed! Length = 4 - (-1) = 5
```

By expanding our lookahead limit dynamically, the first clean closure point is always globally optimal.

---

# Approach: Last-Occurrence Map + Sliding Horizon

## Algorithm

1. Initialize an integer array `lastIndex[26]` to store the final occurrence index of each character from `'a'` to `'z'`.
2. Loop through the string `s` from `0` to `n - 1` to fill the `lastIndex` lookup cache.
3. Initialize:
   - `start = 0` → The starting index boundary of the current active partition.
   - `end = 0` → The sliding horizon limit of the current partition.
   - `result` → A list to store the lengths of the completed partitions.
4. Iterate through the string using a pointer `i` from `0` to `n - 1`:
   - Update our active horizon: `end = Math.max(end, lastIndex[s.charAt(i) - 'a'])`.
   - If `i == end`:
     - The current character is the last remaining instance of all elements inside the window.
     - Add the partition size to our results: `result.add(end - start + 1)`.
     - Shift the next partition's anchor forward: `start = i + 1`.
5. Return `result`.

---

# Dry Run

```java
s = "eccbbbbdec"
```

### Initial Last-Occurrence Cache

```text
'e' -> 7, 'c' -> 9, 'b' -> 6, 'd' -> 8
```

---

### Execution Trace

```text
i = 0 ('e') → end = max(0, 7) = 7.          i == end (0 == 7) ❌
i = 1 ('c') → end = max(7, 9) = 9.          i == end (1 == 9) ❌
i = 2 = 6) = 9.          i == end (2 == 9) ❌
...
i = 7 ('e') → end = max(9, 7) = 9.          i == end (7 == 9) ❌
i = 8 ('d') → end = max(9, 8) = 9.          i == end (8 == 9) ❌
i = 9 ('c') → end = max(9, 9) = 9.          i == end (9 == 9)  (Horizon Hit!)

Partition Closed! Length = 9 - 0 + 1 = 10. start resets to 10.
```

Loop ends. Answer: `[10]`

---

# Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<Integer> partitionLabels(String s) {
        List<Integer> result = new ArrayList<>();
        if (s == null || s.length() == 0) {
            return result;
        }

        int[] lastIndex = new int[26];

        // Step 1: Pre-calculate the absolute final occurrence index of each character
        for (int i = 0; i < s.length(); i++) {
            lastIndex[s.charAt(i) - 'a'] = i;
        }

        int start = 0;
        int end = 0;

        // Step 2: Linearly track and close partition windows greedily
        for (int i = 0; i < s.length(); i++) {
            // Expand the current partition horizon to capture the last occurrence of character s[i]
            end = Math.max(end, lastIndex[s.charAt(i) - 'a']);

            // If the active pointer catches up to our window horizon, a clean cut is closed
            if (i == end) {
                result.add(end - start + 1);
                start = i + 1; // Shift the start anchor forward for the next partition block
            }
        }

        return result;
    }
}
```

---

# Optimized Understanding

This problem is a variation of **Interval Merging (LeetCode 56)**. 

Each unique letter defines a lifetime interval spanning from its first appearance index to its last appearance index. By finding the maximum last index on the fly, the algorithm implicitly merges all overlapping character intervals into distinct, non-overlapping blocks using an optimal \(O(N)\) time and \(O(1)\) auxiliary alphabet space model.

---

# Correctness Proof

### Greedy Choice

We always close a partition at the absolute earliest index where `i == end`.

Suppose we chose to skip closing the window at `end` and instead extended the partition further to a later index `K`. Since `i == end` was already a valid cut point, all characters inside our partition `[start...end]` have zero remaining instances past `end`. 

Extending the window to `K` would group independent trailing character sets into our current block unnecessarily. This reduces the total number of partitions formed, proving that closing the window at the earliest valid horizon is always optimal.

---

### Optimal Substructure

Once a partition boundary is closed at index `end`, all characters inside `s[start...end]` are fully contained and isolated. The problem of partitioning the remaining suffix `s[end+1...n-1]` reduces to an identical, independent subproblem over a smaller range.

---

# Complexity Analysis

Let:

```text
N = s.length()
```

### Time Complexity

Preprocessing loop:

```text
O(N)
```

Greedy horizon traversal pass:

```text
O(N)
```

Overall:

```text
O(N)
```

---

### Space Complexity

```text
O(1)
```

The auxiliary storage is bounded strictly by the fixed 26-slot alphabet `lastIndex` array, making it completely independent of string length.

---

# Key Pattern

This problem belongs to:

```text
Greedy + Sliding Window Horizon + Implicit Interval Merging
```

Common indicators:
- Partitioning arrays or strings into the maximum possible number of continuous groups
- Sub-components must completely insulate all instances of their constituent keys
- Tracking lookahead final occurrences using localized lookup caches

---

# Similar Problems

1. Merge Intervals (LeetCode 56)
2. Jump Game II (LeetCode 45)
3. Optimal Partition of String (LeetCode 2405)
4. Non-overlapping Intervals (LeetCode 435)
5. Split Array into Consecutive Subsequences (LeetCode 659)

---

# Takeaway

```text
Pre-map the final index position of every unique character.

Track the active window horizon dynamically by checking lookahead limits.

Close the partition the exact moment your index pointer catches up 
to the maximum horizon limit found.
```

**Pattern:** Greedy + Sliding Window Horizon + Implicit Interval Merging

<FollowUp>
We can now look at another advanced greedy window or interval problem from your scope. If you'd like, let me know if we should explore **Merge Intervals (LeetCode 56)** or check out the slot scheduling rules of **Task Scheduler (LeetCode 621)**!
</FollowUp>
