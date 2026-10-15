# Sum of Subarray Ranges (LeetCode 2104)

## Problem Statement

You are given an integer array `nums`. The **range** of a subarray of `nums` is the difference between the largest and smallest element in the subarray.

Return *the **sum of all subarray ranges** of `nums`*.

A subarray is a contiguous **non-empty** sequence of elements within an array.

---

## Examples

### Example 1

```java
Input: nums =
Output: 4
```

### Explanation

```text
The contiguous subarrays and their ranges are:
- Length 1: range(1)=0, range(2)=0, range(3)=0
- Length 2: range(1,2)=2-1=1, range(2,3)=3-2=1
- Length 3: range(1,2,3)=3-1=2

Sum of all ranges = 0 + 0 + 0 + 1 + 1 + 2 = 4.
```

---

### Example 2

```java
Input: nums =
Output: 4
```

### Explanation

```text
The contiguous subarrays and their ranges are:
- range(1)=0, range(4)=0, range(1)=0
- range(1,4)=3, range(4,1)=3
- range(1,4,1)=3

Sum of all ranges = 0 + 0 + 0 + 3 + 3 + 3 = 9.
```

---

### Example 3

```java
Input: nums =
Output: 59
```

---

# Intuition

A naive solution would check every single subarray to compute its range, costing an inefficient $O(N^2)$ runtime.

To solve this problem in optimal **linear time**, we can leverage the mathematical linearity of summation. The sum of all ranges is equivalent to subtracting the sum of all subarray minimums from the sum of all subarray maximums:
$$\sum \text{range}(b) = \sum (\max(b) - \text{min}(b)) = \sum \max(b) - \sum \text{min}(b)$$

This allows us to break the task down into two separate subproblems, both solvable via a **Monotonic Stack**:
1. **Sum of Subarray Minimums:** For each element `nums[i]`, we find the number of subarrays where it acts as the minimum by locating its Previous Smaller Element (PSE) and Next Smaller Element (NSE).
2. **Sum of Subarray Maximums:** For each element `nums[i]`, we find the number of subarrays where it acts as the maximum by locating its Previous Greater Element (PGE) and Next Greater Element (NGE).

The total number of subarrays where an element dominates is determined by the product of its valid left and right expansion counts:
$$\text{Total Subarrays} = \text{leftCount} \times \text{rightCount}$$

*(Note: To prevent double-counting duplicate values across overlapping subarrays, we enforce strict inequalities on one side of our boundary sweeps and non-strict inequalities on the other).*

---

# State Definition

The structural state tracking uses a single unified method layout wrapping two distinct monotonic stack passes:

```java
import java.util.Stack;

class Solution {
    public long subArrayRanges(int[] nums) {
        // Splitting the global summation: Sum(Max) - Sum(Min)
        return sumSubarrayMaxs(nums) - sumSubarrayMins(nums);
    }
    
    private long sumSubarrayMins(int[] nums) { ... }
    private long sumSubarrayMaxs(int[] nums) { ... }
}
```

---

# Recurrence / Collision Resolution Condition

```java
import java.util.Stack;

class Solution {
    public long subArrayRanges(int[] nums) {
        return sumSubarrayMaxs(nums) - sumSubarrayMins(nums);
    }

    private long sumSubarrayMins(int[] nums) {
        int n = nums.length;
        int[] leftBound = new int[n];
        int[] rightBound = new int[n];
        Stack<Integer> stack = new Stack<>();

        // PSE Pass
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && nums[stack.peek()] >= nums[i]) {
                stack.pop();
            }
            leftBound[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(i);
        }

        stack.clear();

        // NSE Pass
        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && nums[stack.peek()] > nums[i]) {
                stack.pop();
            }
            rightBound[i] = stack.isEmpty() ? n : stack.peek();
            stack.push(i);
        }

        long sum = 0;
        for (int i = 0; i < n; i++) {
            sum += (long) (i - leftBound[i]) * (rightBound[i] - i) * nums[i];
        }
        return sum;
    }

    private long sumSubarrayMaxs(int[] nums) {
        int n = nums.length;
        int[] leftBound = new int[n];
        int[] rightBound = new int[n];
        Stack<Integer> stack = new Stack<>();

        // PGE Pass
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && nums[stack.peek()] <= nums[i]) {
                stack.pop();
            }
            leftBound[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(i);
        }

        stack.clear();

        // NGE Pass
        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && nums[stack.peek()] < nums[i]) {
                stack.pop();
            }
            rightBound[i] = stack.isEmpty() ? n : stack.peek();
            stack.push(i);
        }

        long sum = 0;
        for (int i = 0; i < n; i++) {
            sum += (long) (i - leftBound[i]) * (rightBound[i] - i) * nums[i];
        }
        return sum;
    }
}
```

---

# Optimal Linear Simulation Solution

## Idea

By treating the sum of maximums and the sum of minimums as independent linear properties and isolating their expansion intervals via monotonic stacks, we skip all intermediate subarray comparisons. This simplifies the range aggregation down to constant-time multiplication logic per index, running smoothly in **O(N)** time.

---

## Java Code

```java
import java.util.Stack;

class Solution {
    public long subArrayRanges(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        // Total Range Sum = Total Max Contributions - Total Min Contributions
        return sumSubarrayMaxs(nums) - sumSubarrayMins(nums);
    }

    // Helper method to compute the sum of all subarray minimums
    private long sumSubarrayMins(int[] nums) {
        int n = nums.length;
        int[] leftBound = new int[n];
        int[] rightBound = new int[n];
        Stack<Integer> stack = new Stack<>();

        // Discover Previous Smaller Element (PSE) indices
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && nums[stack.peek()] >= nums[i]) {
                stack.pop();
            }
            leftBound[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(i);
        }

        stack.clear();

        // Discover Next Smaller Element (NSE) indices
        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && nums[stack.peek()] > nums[i]) {
                stack.pop();
            }
            rightBound[i] = stack.isEmpty() ? n : stack.peek();
            stack.push(i);
        }

        long totalMins = 0;
        for (int i = 0; i < n; i++) {
            long leftCount = i - leftBound[i];
            long rightCount = rightBound[i] - i;
            totalMins += leftCount * rightCount * nums[i];
        }
        return totalMins;
    }

    // Helper method to compute the sum of all subarray maximums
    private long sumSubarrayMaxs(int[] nums) {
        int n = nums.length;
        int[] leftBound = new int[n];
        int[] rightBound = new int[n];
        Stack<Integer> stack = new Stack<>();

        // Discover Previous Greater Element (PGE) indices
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && nums[stack.peek()] <= nums[i]) {
                stack.pop();
            }
            leftBound[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(i);
        }

        stack.clear();

        // Discover Next Greater Element (NGE) indices
        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && nums[stack.peek()] < nums[i]) {
                stack.pop();
            }
            rightBound[i] = stack.isEmpty() ? n : stack.peek();
            stack.push(i);
        }

        long totalMaxs = 0;
        for (int i = 0; i < n; i++) {
            long leftCount = i - leftBound[i];
            long rightCount = rightBound[i] - i;
            totalMaxs += leftCount * rightCount * nums[i];
        }
        return totalMaxs;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* Both helper tracking functions process the array of length N exactly twice to determine boundary limits. Since each index item is pushed and popped from the stack at most once per pass, total execution runtime scales cleanly inside flat linear bounds.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary allocation memory is required to store the directional boundary offset tracking arrays (`leftBound`, `rightBound`) along with element tracking index frames inside the stack wrapper.

---

# Key Takeaways

✅ Advanced culmination of monotonic boundary aggregation logic using distributive subtraction frameworks.

✅ **The Linearity of Summation Trick:** Deconstructing a complex compound property ($\max - \min$) into individual standalone metrics avoids tracking multidimensional state layers. Solving for extremes separately and merging them via subtraction simplifies the runtime code significantly.

✅ Direct production engine used inside **financial market risk telemetry systems** (e.g., calculating cumulative variance spikes or trading volume drawdowns across sliding calendar windows), analytics aggregators, and anomaly pattern trackers.

✅ Common Architectural Paradigm:

```text
Symmetric Monotonic Dominance Mapping via Distributive Structural Subtraction
```
