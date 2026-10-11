# Beautiful Array

## Problem Statement

An array `nums` of length `n` is **beautiful** if it is a permutation of the integers from `1` to `n` such that for every $i < j$, there is **no** index $k$ with $i < k < j$ that satisfies:

```text
2 * nums[k] == nums[i] + nums[j]
```

Given an integer `n`, return **any** beautiful array `nums` of length `n`. The history guarantees that an answer always exists.

The overall run time complexity should be:

```text
O(n)
```

*(or better, up to O(n log n) via divide and conquer)*

---

## Examples

### Example 1

**Input**

```java
n = 4
```

**Output**

```java
```

**Explanation**

Let's test the condition for different subsets:
- For `nums[i] = 2` and `nums[j] = 1`, the average is `1.5`, which is not an integer, so no index $k$ can equal it.
- For `nums[i] = 1` and `nums[j] = 3`, the average is `2`. Node `2` sits at index 0, which is *before* index 1 and index 3 ($k < i < j$), so it does not violate the condition.
The output array is beautiful. Another valid answer is ``.

---

### Example 2

**Input**

```java
n = 5
```

**Output**

```java
```

---

## Brute Force Approach

Generate all possible permutations of numbers from `1` to `n` and check each permutation against the condition.

### Steps

1. Generate all permutations of length `n` using backtracking.
2. For each permutation, use three nested loops to test every combination of $(i, k, j)$ where $i < k < j$.
3. Check if $2 \cdot \text{nums}[k] == \text{nums}[i] + \text{nums}[j]$.
4. Return the first permutation that passes the check for all indices.

### Complexity

```text
Time Complexity: O(n! * n^3)
Space Complexity: O(n) for the recursion stack
```

This factorial scaling causes immediate Time Limit Exceeded (TLE) errors for $n > 10$. The problem requires a more clever property-driven decomposition.

---

# Optimal Approach: Divide & Conquer (Odd/Even Separation)

## Key Idea

The core mathematical rule of a beautiful array is to **prevent an arithmetic progression** ($nums[i], nums[k], nums[j]$) where $nums[k]$ is exactly the average of $nums[i]$ and $nums[j]$.

Notice that $2 \cdot nums[k]$ is always an **even number**. Therefore, if we can guarantee that $nums[i] + nums[j]$ is **odd**, then it can never equal $2 \cdot nums[k]$. An addition is odd only when we add an **odd number and an even number**.

This allows us to partition our elements into two groups:
- **Left side:** All odd numbers.
- **Right side:** All even numbers.

By doing this, any pair choosing an $i$ from the left side (odd) and a $j$ from the right side (even) will have an odd sum, making a violation impossible for any intermediate $k$. 

To handle pairs chosen *entirely* within the odd side or *entirely* within the even side, we apply **Divide and Conquer**. A beautiful array preserves its beauty under linear transformations ($A \cdot x + B$). We can recursively construct a beautiful array of size $n$ by scaling down a smaller beautiful array:
- The **odd part** is mapped using the transformation: $2 \cdot x - 1$.
- The **even part** is mapped using the transformation: $2 \cdot x$.

---

## Visual Understanding

Suppose we want to construct a beautiful array for `n = 4`:

```text
Base state: 
```

To build `n = 4`, we split into two halves of size 2. A beautiful array of size 2 is simply ``.

1. **Transform to Odds ($2x - 1$) using ``:**
   - $2(1) - 1 = 1$
   - $2(2) - 1 = 3$
   - Odd part = ``

2. **Transform to Evens ($2x$) using ``:**
   - $2(1) = 2$
   - $2(2) = 4$
   - Even part = ``

Combine them together: `Odds + Evens` = ``.

---

## Partition Variables

Let:

```java
List<Integer> beautifulList = new ArrayList<>();
beautifulList.add(1); // Base case for n = 1
```

---

### Border Elements

During the expansion phase, elements are filtered to remain within the target range up to `n`:

```java
if (2 * x - 1 <= n) nextList.add(2 * x - 1);
if (2 * x <= n) nextList.add(2 * x);
```

---

## Correct Partition Condition

The list size grows continuously from 1. We stop and return the result once the list size matches `n`:

```while (beautifulList.size() < n)```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range search with a bottom-up divide-and-conquer map transformation framework to arrange elements in linear time).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    public int[] beautifulArray(int n) {
        
        List<Integer> resultList = new ArrayList<>();
        resultList.add(1); // Base case: an array of length 1 is always beautiful

        // Bottom-up divide and conquer construction
        while (resultList.size() < n) {
            List<Integer> nextList = new ArrayList<>();

            // Step 1: Generate the odd elements using the linear map (2x - 1)
            for (int x : resultList) {
                if (2 * x - 1 <= n) {
                    nextList.add(2 * x - 1);
                }
            }

            // Step 2: Generate the even elements using the linear map (2x)
            for (int x : resultList) {
                if (2 * x <= n) {
                    nextList.add(2 * x);
                }
            }

            resultList = nextList; // Advance state to the expanded combination list
        }

        // Convert the final list collection back into a flat primitive array
        int[] beautiful = new int[n];
        for (int i = 0; i < n; i++) {
            beautiful[i] = resultList.get(i);
        }

        return beautiful;
    }
}
```

---

## Dry Run

### Input

```java
n = 4
```

---

### Step Execution Traversal

- **Initialization:** `resultList = `. Size is $1 < 4$.
- **Iteration 1:**
  - Odds loop ($x = 1$): $2(1) - 1 = 1$. Added.
  - Evens loop ($x = 1$): $2(1) = 2$. Added.
  - `resultList` becomes ``. Size is $2 < 4$.
- **Iteration 2:**
  - Odds loop:
    - $x = 1 \implies 2(1) - 1 = 1$
    - $x = 2 \implies 2(2) - 1 = 3$
  - Evens loop:
    - $x = 1 \implies 2(1) = 2$
    - $x = 2 \implies 2(2) = 4$
  - `resultList` becomes ``. Size is $4 == 4$. Loop terminates.

---

### Answer

```java
```

---

## Why Is the Time Complexity Linear-O(n)?

The algorithm constructs the array by doubling the size of our beautiful array at each layer step ($\log n$ scaling iterations). In each iteration, we perform linear operations proportional to the size of the elements generated, keeping the cumulative operations bounded to a linear scale.

This geometric expansion yields:

```text
O(n)
```

which satisfies the strict optimization constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

The size of the result list grows exponentially ($1 \to 2 \to 4 \to 8 \dots$). The total number of elements processed across all iterations forms a geometric series: $1 + 2 + 4 + \dots + n \approx 2n$, resulting in an overall time complexity of $O(n)$.

---

### Space Complexity

```text
O(n)
```

Additional lists are allocated at runtime to hold the generated permutations during the transformation step.

---

## Key Insight

Isolating odd and even properties ensures that cross-boundary pairs can never satisfy an arithmetic progression, allowing us to solve a complex global array ordering problem through simple linear transformations.

```text
Time  : O(n) runtime timeline
Space : O(n) temporary space
```

---
