# Largest Rectangle in Histogram (LeetCode 84)

## Problem Statement

Given an array of integers `heights` representing the histogram's bar height where the width of each bar is `1`, return *the area of the largest rectangle in the histogram*.

---

## Examples

### Example 1

```java
Input: heights = [2,1,5,6,2,3]
Output: 10
```

### Explanation

```text
The heights are plotted as a histogram.
The largest rectangle is formed by the bars 5 and 6.
Both bars have a shared height constraint of 5 and span a width of 2.
Area = 5 * 2 = 10.
```

---

### Example 2

```java
Input: heights = [2,4]
Output: 4
```

---

## Visualization

### Example Input

```text
heights = [2,1,5,6,2,3]
```

---

### Step 1: Draw the Histogram

```text
6 |         █
5 |       █ █
4 |       █ █
3 |       █ █     █
2 | █     █ █ █   █
1 | █ █   █ █ █   █
0 + -----------------
    2 1 5 6 2 3
```

---

### Brute Force Intuition

For every bar:

```text
Assume current bar is the height
Expand left until a smaller bar appears
Expand right until a smaller bar appears
```

Area:

```text
height × width
```

---

### Focus on Height = 5

```text
6 |         █
5 |       █ █
4 |       █ █
3 |       █ █
2 |       █ █
1 |       █ █
0 + -----------------
    2 1 5 6 2 3
        ↑
```

---

### Expand Left

```text
2,1,(5)

Stop at 1 because

1 < 5
```

Left Boundary:

```text
Index = 2
```

---

### Expand Right

```text
5,6,(2)

Stop at 2 because

2 < 5
```

Right Boundary:

```text
Index = 3
```

Width:

```text
3 - 2 + 1

= 2
```

Area:

```text
5 × 2

= 10
```

---

### Rectangle Formed

```text
6 |         █
5 |       █ █
4 |       ▓ ▓
3 |       ▓ ▓
2 |       ▓ ▓
1 |       ▓ ▓
0 + -----------------
    2 1 5 6 2 3
```

```text
▓ = Rectangle using height 5
```

Area:

```text
10
```

---

## Nearest Smaller to Left (NSL)

For:

```text
[2,1,5,6,2,3]
```

Nearest Smaller Left Index:

```text
[-1,-1,1,2,1,4]
```

Visualization:

```text
Index : 0 1 2 3 4 5
Value : 2 1 5 6 2 3

NSL   : - - 1 2 1 4
```

Meaning:

```text
For height 5

Nearest smaller on left
is at index 1
(value = 1)
```

---

## Nearest Smaller to Right (NSR)

Nearest Smaller Right Index:

```text
[1,6,4,4,6,6]
```

Visualization:

```text
Index : 0 1 2 3 4 5
Value : 2 1 5 6 2 3

NSR   : 1 6 4 4 6 6
```

Meaning:

```text
For height 5

Nearest smaller on right

Index = 4
(value = 2)
```

---

## Width Formula

Once we know:

```text
Left Smaller Index

and

Right Smaller Index
```

Width becomes:

```java
width =
rightSmaller
-
leftSmaller
-
1
```

---

### Example

For bar:

```text
Height = 5
Index  = 2
```

```text
NSL = 1

NSR = 4
```

Width:

```text
4 - 1 - 1

= 2
```

Area:

```text
5 × 2

= 10
```

---

## Complete Area Calculation

| Index | Height | NSL | NSR | Width | Area |
|---------|---------|---------|---------|---------|---------|
| 0 | 2 | -1 | 1 | 1 | 2 |
| 1 | 1 | -1 | 6 | 6 | 6 |
| 2 | 5 | 1 | 4 | 2 | 10 |
| 3 | 6 | 2 | 4 | 1 | 6 |
| 4 | 2 | 1 | 6 | 4 | 8 |
| 5 | 3 | 4 | 6 | 1 | 3 |

Maximum:

```text
10
```

---

## Monotonic Stack Visualization

### Processing Height = 2

```text
Stack:

[0]
```

```text
Heights:

2
```

---

### Processing Height = 1

```text
1 < 2
```

Pop:

```text
2
```

Area:

```text
2 × 1

= 2
```

Stack:

```text
[]
```

Push:

```text
1
```

---

### Processing Height = 5

```text
5 > 1
```

Push.

```text
Stack:

[1,5]
```

---

### Processing Height = 6

```text
6 > 5
```

Push.

```text
Stack:

[1,5,6]
```

---

### Processing Height = 2

Current:

```text
2
```

Stack:

```text
[1,5,6]
```

Since:

```text
2 < 6
```

Pop:

```text
6
```

Area:

```text
6 × 1

= 6
```

---

Again:

```text
2 < 5
```

Pop:

```text
5
```

Width:

```text
2
```

Area:

```text
5 × 2

= 10
```

Maximum becomes:

```text
10
```

---

### Rectangle Producing Maximum Area

```text
6 |         █
5 |       █ █
4 |       ▓ ▓
3 |       ▓ ▓
2 |       ▓ ▓
1 |       ▓ ▓
0 + -----------------
    2 1 5 6 2 3
```

Height:

```text
5
```

Width:

```text
2
```

Area:

```text
5 × 2

= 10
```

---

## Final Answer

```text
Largest Rectangle Area

= 10
```

---

## Key Insight

For every bar:

```text
Current Height
×
Maximum Width
where every bar
is at least that height
```

To compute width efficiently:

```text
Nearest Smaller To Left
+
Nearest Smaller To Right
```

or

```text
Single Monotonic Increasing Stack
```

---

### Monotonic Stack Pattern

```text
Stack Pattern
│
├── Next Greater Element
├── Next Smaller Element
├── Daily Temperatures
├── Sum of Subarray Minimums
├── Trapping Rain Water
├── Largest Rectangle in Histogram ✅
└── Maximal Rectangle
```

### Interview Trick

Whenever you hear:

```text
Nearest Smaller
Previous Smaller
Next Smaller
Range Expansion
Histogram
```

Think:

```text
Monotonic Stack
```

because it usually reduces:

```text
O(N²)

to

O(N)
```
time.

# Intuition

To find the largest rectangle, we can inspect each bar individually and ask: **What is the maximum area of a rectangle that uses `heights[i]` as its absolute height constraint?**

If a rectangle is limited by the height of bar `i`, it can extend horizontally to the left and right as long as it encounters bars that are **greater than or equal to** `heights[i]`. The moment it hits a bar strictly smaller than `heights[i]`, the boundary closes.
- **Previous Smaller Element (PSE):** The first bar to the left that is strictly smaller than `heights[i]`.
- **Next Smaller Element (NSE):** The first bar to the right that is strictly smaller than `heights[i]`.

We can solve this problem by manually scanning the left and right boundaries for every single bar (**Brute Force**) or optimizing it with a **Monotonic Increasing Stack** to resolve the width boundaries in a single, super-fast **O(N) linear time** pass.

---

# Brute Force Solution

## Idea

For each bar, we run an inner loop looking left to find the first bar smaller than it (`leftBound`), and another inner loop looking right to find the first bar smaller than it (`rightBound`). This determines the maximal width width limits for the current bar's height. We compute the area for each bar and track the global maximum.

---

## Java Code

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        if (heights == null || heights.length == 0) {
            return 0;
        }

        int n = heights.length;
        int maxArea = 0;

        for (int i = 0; i < n; i++) {
            int leftBound = -1;
            int rightBound = n;

            // Scan left to find the Previous Smaller Element index
            for (int j = i - 1; j >= 0; j--) {
                if (heights[j] < heights[i]) {
                    leftBound = j;
                    break;
                }
            }

            // Scan right to find the Next Smaller Element index
            for (int j = i + 1; j < n; j++) {
                if (heights[j] < heights[i]) {
                    rightBound = j;
                    break;
                }
            }

            // Width calculation eliminates the leftBound and rightBound index bars themselves
            int width = rightBound - leftBound - 1;
            int currentArea = heights[i] * width;
            maxArea = Math.max(maxArea, currentArea);
        }

        return maxArea;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N²) due to the nested loops traversing the left and right neighborhoods for every single bar position.
- **Space Complexity:** O(1) as it maps variables only onto primitive tracking registers.

---

# State Definition (Optimal Approach)

To optimize the execution to O(N) linear time, we can track indices using a **Monotonic Increasing Stack**. When an incoming bar is strictly smaller than the bar at the top of the stack, it acts as the right boundary (NSE) for that top bar. The element *below* the top bar in our monotonic stack acts as its left boundary (PSE). 

```java
Stack<Integer> stack = new Stack<>(); // Monotonic stack sorting indices increasingly by height
int maxArea = 0;                     // Global variable tracking the largest area found
```

---

# Recurrence / Structural State Condition

```java
while (!stack.isEmpty() && heights[stack.peek()] > currentHeight) {
    int height = heights[stack.pop()];
    int leftBound = stack.isEmpty() ? -1 : stack.peek();
    int rightBound = i;
    maxArea = Math.max(maxArea, height * (rightBound - leftBound - 1));
}
```

---

# Optimal Monotonic Stack Solution

## Idea

We can implement this cleanly in a **single linear pass** by calculating areas the exact moment elements are popped from the stack. To ensure that any remaining elements are flushed out of the stack when the loop reaches the end of the array, we run the loop up to index `n` and treat index `n` as a virtual sentinel bar with a height of `0`.

---

## Java Code

```java
import java.util.Stack;

class Solution {
    public int largestRectangleArea(int[] heights) {
        if (heights == null || heights.length == 0) {
            return 0;
        }

        int n = heights.length;
        Stack<Integer> stack = new Stack<>();
        int maxArea = 0;

        // Loop up to n; index n acts as a virtual 0-height sentinel
        // to force-flush any remaining elements out of the stack at the end
        for (int i = 0; i <= n; i++) {
            // Virtual terminal bar has a height of 0 to clear out all open indices
            int currentHeight = (i == n) ? 0 : heights[i];

            // Maintain the monotonic increasing property
            while (!stack.isEmpty() && heights[stack.peek()] > currentHeight) {
                // The element being popped is our active height bottleneck
                int height = heights[stack.pop()];
                
                // The new top of the stack is the left boundary (PSE)
                int leftBound = stack.isEmpty() ? -1 : stack.peek();
                // The current index 'i' acts as the right boundary (NSE)
                int rightBound = i;

                int width = rightBound - leftBound - 1;
                maxArea = Math.max(maxArea, height * width);
            }
            
            // Push the current bar index onto the stack
            stack.push(i);
        }

        return maxArea;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* Every single bar index is pushed onto the stack exactly once and popped from the stack exactly once across the entire loop pass. The total number of stack transactions is bounded by 2N, guaranteeing an optimal linear runtime.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary space scales linearly to accommodate indices inside the stack tracking buffer in the worst-case scenario where elements are sorted in a strictly increasing sequence.

---

# Key Takeaways

✅ High-tier fundamental design pattern combining boundary lookup tracks with geometric calculations.

✅ **The Virtual Sentinel Trick:** Appending a virtual index step at `i == n` with a height of `0` ensures that when the main array ends, the loop triggers a cascade of pop operations. This flattens and processes any lingering open indices remaining on the stack without needing a separate cleanup block.

✅ Directly models execution layers like **maximal area clustering engines**, image segmentation processors, UI text box wrap boundaries layout calculations, and 2D matrix max-subgrid solvers.

✅ Common Architectural Paradigm:

```text
Monotonic Index Popping for Dynamic Width Boundary Formations
```

### Similar Problems

- Maximal Rectangle (2D Matrix extension of this problem - LeetCode 85)
- Trapping Rain Water (LeetCode 42)
- Sum of Subarray Minimums (LeetCode 907)
- Next Greater Element I (LeetCode 496)
- Daily Temperatures (LeetCode 739)
