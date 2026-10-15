# Sum of Subarray Minimums (LeetCode 907)

## Problem Statement

Given an array of integers `arr`, find the sum of `min(b)`, where `b` ranges over every (contiguous) subarray of `arr`. Since the answer may be large, return the answer **modulo 10⁹ + 7**.

---

## Examples

### Example 1

```java
Input: arr = [3,1,2,4]
Output: 17
```

### Explanation

```text
The contiguous subarrays and their minimum values are:
- Length 1: [3] (min=3), [1] (min=1), [2] (min=2), [4] (min=4)
- Length 2: [3,1] (min=1), [1,2] (min=1), [2,4] (min=2)
- Length 3: [3,1,2] (min=1), [1,2,4] (min=1)
- Length 4: [3,1,2,4] (min=1)

Sum of all minimums = 3 + 1 + 2 + 4 + 1 + 1 + 2 + 1 + 1 + 1 = 17.
```

---

### Example 2

```java
Input: arr = [11,81,94,43,3]
Output: 444
```

---

# Intuition

A naive nested-loop solution would check every single subarray to compute its minimum, costing an inefficient O(N²) runtime.

To solve this in optimal **linear time**, we change our perspective: **Instead of finding the minimum for each subarray, find how many subarrays have a specific element `arr[i]` as their minimum.**

If `arr[i]` is the minimum for a subarray, that subarray can expand to the left and right as long as it does not encounter an element strictly smaller than `arr[i]`. 
- Let `leftCount` be the number of consecutive elements to the left of `arr[i]` that are strictly greater than `arr[i]`.
- Let `rightCount` be the number of consecutive elements to the right of `arr[i]` that are greater than or equal to `arr[i]`.
*(Note: Using strict inequality on one side and a non-strict inequality on the other avoids double-counting duplicate minimum values across overlapping subarrays).*

The total number of subarrays where `arr[i]` acts as the minimum is exactly:
\[\text{Total Subarrays} = \text{leftCount} \times \text{rightCount}\]

The total contribution of `arr[i]` to our final sum is:
\[\text{Contribution} = \text{arr}[i] \times \text{leftCount} \times \text{rightCount}\]

We can calculate `leftCount` and `rightCount` for every element efficiently using a **Monotonic Increasing Stack**.

---

# State Definition

The structural calculation tracks directional boundaries using two allocation structures:
```text
int[] leftBound = new int[n];  // Stores index of Previous Smaller Element (PSE)
int[] rightBound = new int[n]; // Stores index of Next Smaller Element (NSE)
Stack<Integer> stack = new Stack<>(); // Monotonic stack holding indices
```

---

# Recurrence / Collision Resolution Condition

```java
import java.util.Stack;

class Solution {
    public int sumSubarrayMins(int[] arr) {
        int n = arr.length;
        int[] leftBound = new int[n];
        int[] rightBound = new int[n];
        Stack<Integer> stack = new Stack<>();
        long totalSum = 0;
        int MOD = 1000000007;

        // Step 1: Calculate Previous Smaller Element (PSE) index
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && arr[stack.peek()] >= arr[i]) {
                stack.pop();
            }
            leftBound[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(i);
        }

        stack.clear();

        // Step 2: Calculate Next Smaller Element (NSE) index
        // Use strict inequality to cleanly deduplicate matching items
        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && arr[stack.peek()] > arr[i]) {
                stack.pop();
            }
            rightBound[i] = stack.isEmpty() ? n : stack.peek();
            stack.push(i);
        }

        // Step 3: Compute totals using combinations boundary math
        for (int i = 0; i < n; i++) {
            long leftCount = i - leftBound[i];
            long rightCount = rightBound[i] - i;
            long contribution = (leftCount * rightCount) % MOD;
            contribution = (contribution * arr[i]) % MOD;
            totalSum = (totalSum + contribution) % MOD;
        }

        return (int) totalSum;
    }
}
```

---

# Optimal Linear Simulation Solution

## Idea

By utilizing a monotonic increasing stack to capture both the left and right boundaries of an element's structural dominance domain in two distinct linear passes, index matching reduces down to elementary combination math. This completely avoids nested window scans, running inside a predictable **O(N)** time parameter.

---

## Java Code

```java
import java.util.Stack;

class Solution {
    public int sumSubarrayMins(int[] arr) {
        if (arr == null || arr.length == 0) {
            return 0;
        }

        int n = arr.length;
        int[] leftBound = new int[n];
        int[] rightBound = new int[n];
        Stack<Integer> stack = new Stack<>();
        
        int MOD = 1000000007;

        // Step 1: Discover the Previous Smaller Element (PSE) for each element
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && arr[stack.peek()] >= arr[i]) {
                stack.pop();
            }
            // If empty, boundary drops all the way left to -1
            leftBound[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(i);
        }

        stack.clear(); // Clear the stack buffer safely for the second sweep

        // Step 2: Discover the Next Smaller Element (NSE) for each element
        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && arr[stack.peek()] > arr[i]) {
                stack.pop();
            }
            // If empty, boundary overshoots all the way right to index n
            rightBound[i] = stack.isEmpty() ? n : stack.peek();
            stack.push(i);
        }

        long totalSum = 0;

        // Step 3: Apply structural combinatorial multiplication to compute sums
        for (int i = 0; i < n; i++) {
            long leftCount = i - leftBound[i];
            long rightCount = rightBound[i] - i;
            
            // Calculate total unique subarray permutations centered around index i
            long contribution = (leftCount * rightCount) % MOD;
            contribution = (contribution * arr[i]) % MOD;
            
            totalSum = (totalSum + contribution) % MOD;
        }

        return (int) totalSum;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The array is processed twice to precompute boundaries, and once more to aggregate total metrics. Within the boundary loops, every index element is pushed onto the stack exactly once and popped from the stack at most once, locking execution time to flat linear dimensions.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary storage space scales linearly to hold the primitive boundary pointer mapping arrays (`leftBound`, `rightBound`) alongside indices tracking within the stack container structure.

---

# Key Takeaways

✅ Advanced integration of monotonic architectures with combinatorics array counting.

✅ **The Boundary Deduplication Rule:** When an array contains duplicate elements (e.g., `[2, 1, 2, 1]`), failing to vary inequality signs across boundary sweeps can cause the algorithm to multi-count identical subarray minimum allocations. Enforcing `>=` on the left and strict `>` on the right forces exactly one instance of a duplicate value to claim responsibility for the overlapping chunk.

✅ Universally applied inside real-world systems like **database structural lookup query aggregations**, telemetry index tracking engines, and computing algorithmic pricing metrics.

✅ Common Architectural Paradigm:

```text
Dual-Directional Monotonic Boundary Mapping for Combinatorial Range Reductions
```

### Similar Problems

- Next Greater Element I (LeetCode 496)
- Daily Temperatures (LeetCode 739)
- Sum of Subarray Ranges (LeetCode 2104)
- Online Stock Span (LeetCode 901)
- Largest Rectangle in Histogram (LeetCode 84)
