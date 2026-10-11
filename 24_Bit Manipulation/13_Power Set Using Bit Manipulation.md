# Power Set Using Bit Manipulation

## Problem Statement

Given an integer array `nums` of **unique** elements, return *all possible subsets (the power set)*. The solution set must not contain duplicate subsets. You may return the solution in **any order**.

The overall runtime complexity must be:

```text
O(n * 2^n)
```

*(where n is the total number of elements in the nums array)*

---

## Examples

### Example 1

**Input**

```java
nums = [1, 2, 3]
```

**Output**

```java
[[], [1], [2], [1, 2], [3], [1, 3], [2, 3], [1, 2, 3]]
```

**Explanation**

An array of size 3 yields exactly 2³ = 8 subsets.

---

### Example 2

**Input**

```java
nums = [0]
```

**Output**

```java
[[], [0]]
```

---

## Brute Force Approach

Use structural recursive backtracking to branch out and construct subset sequences explicitly by executing choice nodes (include vs. exclude) across a depth tree.

### Steps

1. Initialize a global list to capture subset combinations.
2. Launch a recursive helper function passing an active index tracker along with a temporary path list.
3. For each element position:
   - Path 1: Include the current element and recurse on the next index.
   - Path 2: Exclude the current element and recurse on the next index.
4. When the index crosses array boundaries, copy the temporary path snapshot to the global list.

### Complexity

```text
Time Complexity: O(n * 2^n)
Space Complexity: O(n) for the recursion stack frame depth
```

While structural backtracking is a highly valid approach, it relies heavily on system call frames. We can convert this task into a clean, **iterative, non-recursive loop** by mapping subset paths straight onto binary integer combinations.

---

# Optimal Approach: Binary Counter Masking

## Key Idea

A power set of an array with size `n` contains exactly **\(2^n\) unique subsets**. 

Notice that we can map each subset directly to a binary integer value ranging from `0` to \(2^n - 1\):
- An integer variable `mask` counts linearly from `0` to \(2^n - 1\).
- Each bit position `i` within that `mask` represents a choice for `nums[i]`.
- If the `i`-th bit is **set (`1`)**, we **include** `nums[i]` in our current subset.
- If the `i`-th bit is **unset (`0`)**, we **exclude** `nums[i]` from our current subset.

By counting up through these \(2^n\) combinations, the bit configurations generate every single mathematical subset permutation automatically without needing any recursion stack layers.

---

## Visual Understanding

Suppose `nums = [1, 2, 3]` with size `n = 3`. Total combinations = 2³ = 8.

We count the `mask` variable from `0` (`000`) up to `7` (`111`):

```text
Mask (Base 10) | Binary Mask [bit 2, bit 1, bit 0] | Subset Formed
------------------------------------------------------------------
0              | 0 0 0                              | []
1              | 0 0 1                              | [1]
2              | 0 1 0                              | [2]
3              | 0 1 1                              | [1, 2]
4              | 1 0 0                              | [3]
5              | 1 0 1                              | [1, 3]
6              | 1 1 0                              | [2, 3]
7              | 1 1 1                              | [1, 2, 3]
```

Every permutation maps smoothly to an isolated integer index state.

---

## Partition Variables

Let:

```java
int n = nums.length;
int totalSubsets = 1 << n; // Computes 2^n using a left shift
List<List<Integer>> powerSet = new ArrayList<>();
```

---

### Border Elements

The primary loop drives our masking configuration continuously from zero to the ultimate binary size boundary:

```java
for (int mask = 0; mask < (1 << n); mask++)
```

---

## Correct Partition Condition

The internal bitmask filter isolates whether a specific element is mapped into the active subset combination slot:

```java
if ((mask & (1 << i)) != 0) {
    currentSubset.add(nums[i]);
}
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> powerSet = new ArrayList<>();
        if (nums == null) {
            return powerSet;
        }

        int n = nums.length;
        // The total number of subsets for a set of size n is 2^n
        int totalSubsets = 1 << n; 

        // Step 1: Run an iterative mask counter from 0 up to 2^n - 1
        for (int mask = 0; mask < totalSubsets; mask++) {
            List<Integer> currentSubset = new ArrayList<>();

            // Step 2: Inspect each of the n bit positions inside the current mask
            for (int i = 0; i < n; i++) {
                // If the i-th bit is set, include nums[i] in the current subset
                if ((mask & (1 << i)) != 0) {
                    currentSubset.add(nums[i]);
                }
            }

            // Step 3: Add the completed subset combo to our power set collection
            powerSet.add(currentSubset);
        }

        return powerSet;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Binary Mask Loop** | **O(n * 2^n)** | **O(1)** | The outer loop executes exactly \(2^n\) times, and the inner loop checks `n` bit positions in-place with zero recursion stack overhead. |

---

## Key Insight

Mapping subset choices straight onto the incremental binary bit states of an integer variable completely removes the state tracking dependencies of recursive trees, translating power set assembly into a clean, flat sequential extraction pass.

```text
Time  : O(n * 2^n) timeline expansion
Space : O(1) auxiliary frame memory allocation
```

---

## Similar Problems

1. Subsets II (90) - *Subsets with duplicate array entries*
2. Combinations (77)
3. Permutations (46)
4. Letter Case Permutation (784)
5. Counting Bits (338)
