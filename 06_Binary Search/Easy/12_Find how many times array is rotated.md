# Find K Rotation

## Problem Statement

Given a sorted array that has been rotated clockwise `K` times, find the value of `K`.

The rotation count is equal to the index of the smallest element in the array.

---

## Examples

### Example 1

**Input**

```java
arr = [5, 1, 2, 3, 4]
```

**Output**

```java
1
```

**Explanation**

The original sorted array was:

```java
[1, 2, 3, 4, 5]
```

After `1` clockwise rotation:

```java
[5, 1, 2, 3, 4]
```

Therefore:

```java
K = 1
```

---

### Example 2

**Input**

```java
arr = [3, 4, 5, 1, 2]
```

**Output**

```java
3
```

**Explanation**

The smallest element is:

```java
1
```

at index:

```java
3
```

Hence, the array has been rotated `3` times.

---

### Example 3

**Input**

```java
arr = [1, 2, 3, 4, 5]
```

**Output**

```java
0
```

**Explanation**

The array is already sorted and has not been rotated.

---

## Approach: Binary Search

### Key Observation

In a rotated sorted array:

- The smallest element is the pivot point.
- The index of the smallest element equals the number of rotations.

Example:

```java
[4, 5, 6, 1, 2, 3]
```

Smallest element:

```java
1
```

Index:

```java
3
```

Rotation count:

```java
3
```

Instead of scanning the entire array, we can locate the smallest element using Binary Search.

---

## Binary Search Logic

At any step:

```java
mid = (low + high) / 2
```

### Case 1

```java
arr[mid] > arr[high]
```

This means the minimum element lies in the right half.

```java
low = mid + 1;
```

---

### Case 2

```java
arr[mid] <= arr[high]
```

This means the minimum element lies in the left half (including mid).

```java
high = mid;
```

---

Eventually:

```java
low == high
```

and both point to the smallest element.

Its index is the answer.

---

## Java Solution

```java
class Solution {

    public int findKRotation(int arr[]) {

        int low = 0;
        int high = arr.length - 1;

        while (low < high) {

            int mid = (low + high) / 2;

            if (arr[mid] > arr[high]) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }

        // Index of smallest element
        return low;
    }
}
```

---

## Dry Run

### Input

```java
arr = [4, 5, 1, 2, 3]
```

---

### Iteration 1

```java
low = 0
high = 4

mid = 2
arr[mid] = 1
arr[high] = 3
```

Since:

```java
1 <= 3
```

Move left:

```java
high = mid = 2
```

---

### Iteration 2

```java
low = 0
high = 2

mid = 1
arr[mid] = 5
arr[high] = 1
```

Since:

```java
5 > 1
```

Move right:

```java
low = mid + 1 = 2
```

---

### Result

```java
low = high = 2
```

Smallest element:

```java
arr[2] = 1
```

Rotation count:

```java
2
```

---

## Why Does This Work?

When:

```java
arr[mid] > arr[high]
```

the rotation point must be on the right side because the right half contains smaller elements.

When:

```java
arr[mid] <= arr[high]
```

the right half is sorted, meaning the minimum could be at `mid` or to its left.

By repeatedly eliminating half of the search space, we eventually reach the smallest element.

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

## Key Insight

```text
Rotation Count
=
Index of the Smallest Element
```

Using Binary Search:

```java
if (arr[mid] > arr[high])
    low = mid + 1;
else
    high = mid;
```

allows us to find the smallest element and the rotation count efficiently in:

```text
Time
