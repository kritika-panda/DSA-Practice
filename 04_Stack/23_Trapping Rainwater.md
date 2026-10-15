# Trapping Rain Water (LeetCode 42)

## Problem Statement

Given `n` non-negative integers representing an elevation map where the width of each bar is `1`, compute how much water it can trap after raining.

---

## Examples

### Example 1

```java
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```

### Explanation

```text
The elevation map is represented by the array. 
In this case, 6 units of rain water are being trapped.
```

---

### Example 2

```java
Input: height = [4,2,0,3,2,5]
Output: 9
```

---

# Intuition

The amount of water that can be trapped on top of any single bar at index `i` is determined by the bottleneck of its surroundings:
- Find the maximum height to its left (`leftMax`).
- Find the maximum height to its right (`rightMax`).
- The water level above bar `i` is bounded by the smaller of these two heights: `Math.min(leftMax, rightMax)`.
- Subtract the bar's own height to get the trapped water volume: `water = Math.min(leftMax, rightMax) - height[i]`.

We can implement this by scanning left and right for every single bar (**Brute Force**), precomputing these maximum bounds with auxiliary arrays (**Prefix Sum/Precomputation**), or optimizing it into an elegant **O(1) constant space solution** using **Two Pointers**.

---

# Brute Force Solution

## Idea

For every element in the array, we scan the entire left side to find its local peak height, and scan the entire right side to find its right peak height. The water trapped on top of the current bar is calculated by taking the minimum of these two peaks and subtracting the current bar's height.

---

## Java Code

```java
class Solution {
    public int trap(int[] height) {
        if (height == null || height.length <= 2) {
            return 0;
        }

        int n = height.length;
        int totalWater = 0;

        // Iterate through each bar (excluding first and last as they cannot trap water)
        for (int i = 1; i < n - 1; i++) {
            int leftMax = 0;
            int rightMax = 0;

            // Search the maximum height to the left of the current bar
            for (int j = i; j >= 0; j--) {
                leftMax = Math.max(leftMax, height[j]);
            }

            // Search the maximum height to the right of the current bar
            for (int j = i; j < n; j++) {
                rightMax = Math.max(rightMax, height[j]);
            }

            // The trapped water volume is bounded by the smaller of the two peaks
            totalWater += Math.min(leftMax, rightMax) - height[i];
        }

        return totalWater;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N²) due to the nested loops searching the left and right maximum heights for each index bar.
- **Space Complexity:** O(1) as it only utilizes primitive tracking variables.

---

# State Definition (Optimal Approach)

To optimize the O(N²) time layout to an optimal O(N) runtime without paying an O(N) memory tax for prefix arrays, we can maintain four scalar primitives using a **Two-Pointer** configuration:

```java
int left = 0;                  // Left scanning pointer
int right = height.length - 1; // Right scanning pointer
int leftMax = 0;               // Peak height seen from the left side
int rightMax = 0;              // Peak height seen from the right side
```

---

# Recurrence / Structural State Condition

```java
while (left < right) {
    if (height[left] <= height[right]) {
        if (height[left] >= leftMax) {
            leftMax = height[left];
        } else {
            totalWater += leftMax - height[left];
        }
        left++;
    } else {
        if (height[right] >= rightMax) {
            rightMax = height[right];
        } else {
            totalWater += rightMax - height[right];
        }
        right--;
    }
}
```

---

# Optimal Two-Pointer Solution

## Idea

Place a `left` pointer at the beginning and a `right` pointer at the end of the array. Compare `height[left]` and `height[right]`. Whichever side has a lower bar determines the active bottleneck limit. If `height[left] <= height[right]`, we are certain that `leftMax` is the absolute constraint for the `left` position (since there is a larger barrier on the right protecting it). We calculate its water contribution, update `leftMax`, and advance `left++`. We do the same from the right edge if the right side is smaller.

---

## Java Code

```java
class Solution {
    public int trap(int[] height) {
        if (height == null || height.length <= 2) {
            return 0;
        }

        int left = 0;
        int right = height.length - 1;
        int leftMax = 0;
        int rightMax = 0;
        int totalWater = 0;

        // Inward scanning loop until pointers meet
        while (left < right) {
            // The side with the lower height determines the deterministic bottleneck
            if (height[left] <= height[right]) {
                if (height[left] >= leftMax) {
                    // Update peak boundary tracking from the left
                    leftMax = height[left];
                } else {
                    // Accumulate water contribution bounded by the left peak
                    totalWater += leftMax - height[left];
                }
                left++;
            } else {
                if (height[right] >= rightMax) {
                    // Update peak boundary tracking from the right
                    rightMax = height[right];
                } else {
                    // Accumulate water contribution bounded by the right peak
                    totalWater += rightMax - height[right];
                }
                right--;
            }
        }

        return totalWater;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The `left` and `right` pointers slide inward continuously. Every element in the array of length N is processed exactly once, yielding a linear runtime.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. It replaces multi-pass arrays with four primitive tracking registers, eliminating extra heap allocations.

---

# Key Takeaways

✅ Core fundamental paradigm for array volume aggregation and container squeezing.

✅ **The Stack De-escalation Alternative:** This problem can also be solved in O(N) time and space using a **Monotonic Decreasing Stack** by processing elements from left to right. When a larger bar arrives, it pops smaller elements from the stack, filling up basins horizontally layer-by-layer. However, the Two-Pointer method is widely preferred because it accomplishes the same task while dropping the space cost down to a constant tier.

✅ Universally maps to real-world systems like **graphic height map shadows rendering pipelines**, computing topographical drainage basins inside geographic mapping tools, and buffer capacity allocation managers.

✅ Common Architectural Paradigm:

```text
Converging Boundary Squeezing for Sub-Range Volume Accumulations
```

### Similar Problems

- Container With Most Water (LeetCode 11)
- Product of Array Except Self (LeetCode 238)
- Largest Rectangle in Histogram (LeetCode 84)
- Trapping Rain Water II (3D Matrix variant - LeetCode 407)
