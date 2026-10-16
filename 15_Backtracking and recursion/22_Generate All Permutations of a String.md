# Generate All Permutations of a String ([LeetCode 46 Variant / GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given a string `s`, your task is to generate and return **all possible permutations** of its characters. 

A permutation is an arrangement of all characters of the string into different positional configurations. If the input string contains duplicate characters, the final solution set **must contain only unique permutations** (no duplicates).

The output collection can return the permutations in **any order**.

---

## Examples

### Example 1

```text
Input: s = "ABC"
Output: ["ABC", "ACB", "BAC", "BCA", "CAB", "CBA"]
```

### Explanation

```text
A string of 3 unique characters yields exactly 3! = 6 unique positional permutations.
```

---

### Example 2

```text
Input: s = "AAB"
Output: ["AAB", "ABA", "BAA"]
```

### Explanation

```text
Even though the string has a length of 3, the duplicate 'A' characters restrict the total number of unique positional permutations to exactly 3.
```

---

# Intuition

Generating all permutations is a classic **Combinatorial Search** problem that can be modeled as a traversal over a state-space choice tree. Unlike *subsequences* where the relative chronological order is locked, permutations focus entirely on **reordering positional elements**.

We can solve this efficiently using a **Backtracking (Depth-First Search)** approach with an in-place element swapping pattern:
1. **Positional Swapping:** We use a pointer `index` to track the current position we are filling in the string.
2. **Multi-Way Choice Loop:** For the current `index`, we try swapping the character at `s[index]` with every single character from `index` up to `N - 1`. This places a new candidate character into the active position slot. We then recursively advance to the next index (`index + 1`).
3. **The Backtrack Swap-Back:** Once the downstream recursive path finishes exploring, we perform the **exact same swap a second time**. This undoes the state modification, restoring the string array back to its original layout so the loop can safely try the next sibling character.
4. **Duplicate Pruning Invariant:** If the input string has duplicate characters, swapping identical items horizontally within the same level loop will generate identical search sub-trees, leading to duplicate outputs. To prevent this in O(1) constant auxiliary space, we use a small boolean flag array or a local **lookup set** inside the function scope to ensure a unique character value is swapped into the `index` position at most once per recursion level.

---

# State Definition (Optimal Backtracking Approach)

To trace our combinatorics states systematically within a recursive state-space decision tree using inline array swaps:

```text
backtrack(index, charArray):
- 'index' tracks our active position pointer slot being filled in the sequence.
- 'charArray' acts as our mutable char[] character pool workspace modified in-place.
```

---

# Recurrence / Structural Backtracking Invariant

```text
Base Case:
If index == charArray.length -> result.add(new String(charArray)); return

Recursive Controlled Branching:
Set<Character> swappedInThisLevel = new HashSet()

For i from index to charArray.length - 1:
    If swappedInThisLevel.contains(charArray[i]) -> Continue (Duplicate Guard)
    swappedInThisLevel.add(charArray[i])
    
    swap(charArray, index, i) // Place character i into position 'index'
    backtrack(index + 1, charArray) // Recurse deeper
    swap(charArray, index, i) // Backtrack step (Undo the swap mutation)
```

---

# Optimal Backtracking Solution with Duplicate Pruning

## Idea

We convert the string into a mutable `char[]` array to serve as our central in-place workspace. We launch a depth-first search starting from position index 0. By utilizing a localized tracking directory within the function scope, duplicate characters are blocked from being swapped into the active position slot twice, ensuring all leaf nodes reached contain strictly unique string sequences.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

class Solution {
    public List<String> findPermutations(String s) {
        List<String> result = new ArrayList<>();
        if (s == null || s.length() == 0) {
            return result;
        }

        // Convert to a char array to serve as an inline, mutable workspace pool
        char[] charArray = s.toCharArray();

        // Launch our recursive backtracking pass from index position 0
        backtrack(0, charArray, result);
        return result;
    }

    private void backtrack(int index, char[] charArray, List<String> result) {
        // Base Case: If the position cursor reaches the end, a unique permutation is finalized
        if (index == charArray.length) {
            result.add(new String(charArray));
            return;
        }

        // Localized lookup set to prevent swapping duplicate character values 
        // into the current 'index' position slot during this level tier pass
        Set<Character> swappedInThisLevel = new HashSet<>();

        // Iterate through all candidate characters from 'index' to the end of the array
        for (int i = index; i < charArray.length; i++) {
            // Pruning Invariant: If this character value was already swapped into the 
            // current 'index' slot during this level pass, skip it to prevent duplicates!
            if (swappedInThisLevel.contains(charArray[i])) {
                continue;
            }
            
            // Record the character value as processed for this loop tier position
            swappedInThisLevel.add(charArray[i]);

            // Choice Step: Swap character at index 'i' into our active target position 'index'
            swap(charArray, index, i);

            // Recurse deeper by advancing our tracking pointer to the subsequent slot position
            backtrack(index + 1, charArray, result);

            // Backtrack Step: Swap elements back to restore original state layout context
            swap(charArray, index, i);
        }
    }

    // High-speed array reference element swapper
    private void swap(char[] arr, int i, int j) {
        char temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

---

## Complexity Analysis

Let N be the total character length of the input string `s`.

### Time Complexity

```text
Time Complexity: O(N × N!) worst case
```
*Explanation:* For a string of length N containing entirely unique characters, there are exactly \(N!\) unique permutations, meaning the base case leaf node is reached exactly \(N!\) times. Generating a new string object from the `char[]` array workspace takes O(N) linear time at each leaf, locking total execution runtime complexity to an asymptotic bound of \(O(N \times N!)\).

---

### Space Complexity

```text
Space Complexity: O(N) auxiliary space
```
*Explanation:* Highly optimal auxiliary memory footprint. The array shifts are executed completely in-place directly inside the `char[]` workspace pool. Auxiliary memory usage scales linearly at O(N) to accommodate the maximum depth height of the recursive call stack and the localized lookup tracking sets allocated at each level layer.

---

# Key Takeaways

✅ Core fundamental paradigm for processing sequence rearrangements and combinatorial permutations.

✅ **The Double Swap Invariant:** Executing an inline element swap right before a recursive call (`swap(arr, index, i)`) and running the exact same swap statement immediately after the call returns is a vital backtracking pattern. This maps out choices smoothly while keeping space costs securely clamped at an optimal O(1) floor by avoiding heap array allocations.

✅ Directly drives real-world microarchitectures like **cryptographic brute-force key decoders**, TSP (Traveling Salesperson) routing simulators exploring path permutations, puzzle layout solvers, and dictionary text anagram classifiers.

✅ Common Architectural Paradigm:

```text
Positional Reference Swapping with Localized Hash Pruning for Uniqueness Invariants
```

### Similar Problems

- Permutations ([LeetCode 46](https://leetcode.com))
- Permutations II (Handling duplicate element arrays - [LeetCode 47](https://leetcode.com))
- Subsets / Power Set ([LeetCode 78](https://leetcode.com))
- Next Permutation ([LeetCode 31](https://leetcode.com))
