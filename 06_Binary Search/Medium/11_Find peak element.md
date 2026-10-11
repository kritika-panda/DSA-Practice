# Find Peak Element

## Problem Statement

A peak element is an element that is strictly greater than its adjacent elements.

Given an array `nums`, find the index of any peak element and return it.

You may assume:

```text
nums[-1] = -∞
nums[n] = -∞
```

which means the elements outside the array boundaries are considered negative infinity.

If the array contains multiple peaks, return the index of any one of them.

The solution must run in:

```text
O(log n)
```

time complexity.

---

## Examples

### Example 1

**Input**

```java
nums = [1, 2, 3, 1]
```

**Output**

```java
2
```

**Explanation**

```java
nums[2] = 3
```

is greater than both neighbors:

```java
3 > 2
3 > 1
```

Therefore, index `2` is a peak.

---

### Example 2

**Input**

```java
nums = [1, 2, 1, 3, 5, 6, 4]
```

**Output**

```java
5
```

**Explanation**

```java
nums[5] = 6
```

is greater than both neighbors:

```java
6 > 5
6 > 4
```

So index `5` is a valid peak.

Another valid answer is:

```java
1
```

since:

```java
nums[1] = 2
```

is also a peak.

---

### Example 3

**Input**

```java
nums = [1]
```

**Output**

```java
0
```

**Explanation**

With only one element, it is automatically a peak.

---

## Approach: Binary Search

### Key Observation

Instead of checking every element, we can use Binary Search.

Suppose:

```java
mid
```

is the current element.

### Case 1: Increasing Slope

```java
nums[mid] < nums[mid + 1]
```

Example:

```text
1 3 5 8 10
      ^
     mid
```

The peak must exist on the right side because the sequence is increasing.

Move:

```java
low = mid + 1;
```

---

### Case 2: Decreasing Slope

```java
nums[mid] > nums[mid + 1]
```

Example:

```text
10 8 6 4 2
   ^
  mid
```

A peak exists on the left side (including `mid`).

Move:

```java
high = mid;
```

---

### Why Does This Work?

At every step:

- If the slope goes up → move right.
- If the slope goes down → move left.

Eventually:

```java
low == high
```

and both point to a peak element.

---

## Java Solution

```java
class Solution {

    public int findPeakElement(int[] nums) {

        int low = 0;
        int high = nums.length - 1;

        while (low < high) {

            int mid = low + (high - low) / 2;

            if (nums[mid] < nums[mid + 1]) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }

        return low;
    }
}
```

---

## Dry Run

### Input

```java
nums = [1, 2, 3, 1]
```

---

### Iteration 1

```java
low = 0
high = 3

mid = 1
```

```java
nums[mid] = 2
nums[mid + 1] = 3
```

Since:

```java
2 < 3
```

Move right:

```java
low = mid + 1 = 2
```

---

### Iteration 2

```java
low = 2
high = 3

mid = 2
```

```java
nums[mid] = 3
nums[mid + 1] = 1
```

Since:

```java
3 > 1
```

Move left:

```java
high = mid = 2
```

---

### Result

```java
low = high = 2
```

Peak element:

```java
nums[2] = 3
```

Return:

```java
2
```

---

## Visual Intuition

### Increasing Slope

```text
1 2 3 4 5
      ↑
     mid
```

Go Right

```java
low = mid + 1
```

---

### Decreasing Slope

```text
5 4 3 2 1
    ↑
   mid
```

Go Left

```java
high = mid
```

---

### Mountain Shape

```text
1 3 7 9 6 4 2
        ^
      Peak
```

Binary Search gradually converges to the peak.

---

## Why Is a Peak Guaranteed?

Because:

```text
nums[-1] = -∞
nums[n] = -∞
```

If we keep moving uphill:

```text
1 2 3 4 5
```

the last element becomes a peak.

If we move downhill:

```text
5 4 3 2 1
```

the first element becomes a peak.

Therefore at least one peak always exists.

---

## Complexity Analysis

### Time Complexity

```text
O(log n)
```

Binary Search halves the search space in every iteration.

---

### Space Complexity

```text
O(1)
```

Only a few variables are used:

```java
low
high
mid
```

No extra space is required.

---

## Edge Cases

### Single Element

```java
nums = [5]
```

Peak Index:

```java
0
```

---

### Strictly Increasing

```java
nums = [1,2,3,4,5]
```

Peak:

```java
5
```

Index:

```java
4
```

---

### Strictly Decreasing

```java
nums = [5,4,3,2,1]
```

Peak:

```java
5
```

Index:

```java
0
```

---

## Key Insight

Whenever:

```java
nums[mid] < nums[mid + 1]
```

the peak lies on the right side.

Whenever:

```java
nums[mid] > nums[mid + 1]
```

the peak lies on the left side (including `mid`).

Thus:

```java
if (nums[mid] < nums[mid + 1])
    low = mid + 1;
else
    high = mid;
```

allows us to find a peak element efficiently in:

```text
Time  : O(log n)
Space : O(1)
```

---

## Similar Problems

1. Peak Index in a Mountain Array (LC 852)
2. Find Peak Element (LC 162)
3. Search in Rotated Sorted Array
4. Find Minimum in Rotated Sorted Array
5. Single Element in a Sorted Array
6. Mountain Array Search
