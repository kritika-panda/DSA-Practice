# Median of Two Sorted Arrays

## Problem Statement

Given two sorted arrays `nums1` and `nums2` of sizes `m` and `n` respectively, return the median of the two sorted arrays.

The overall run time complexity should be:

```text
O(log(min(m, n)))
```

---

## Examples

### Example 1

**Input**

```java
nums1 = [1, 3]
nums2 = [2]
```

**Output**

```java
2.0
```

**Explanation**

Merged array:

```java
[1, 2, 3]
```

Median:

```java
2
```

---

### Example 2

**Input**

```java
nums1 = [1, 2]
nums2 = [3, 4]
```

**Output**

```java
2.5
```

**Explanation**

Merged array:

```java
[1, 2, 3, 4]
```

Median:

```java
(2 + 3) / 2
= 2.5
```

---

### Example 3

**Input**

```java
nums1 = []
nums2 = [1]
```

**Output**

```java
1.0
```

---

## Brute Force Approach

Merge both arrays and then find the median.

### Steps

1. Merge the two sorted arrays.
2. Find the middle element(s).
3. Return the median.

### Complexity

```text
Time Complexity: O(m + n)
Space Complexity: O(m + n)
```

However, the problem requires a more optimal solution.

---

# Optimal Approach: Binary Search on Partition

## Key Idea

Instead of merging the arrays, we perform Binary Search on the smaller array.

We divide both arrays into:

```text
Left Half
Right Half
```

such that:

```text
Number of elements in left half
=
Number of elements in right half
(or differs by one)
```

and

```text
max(left side)
<=
min(right side)
```

When this condition is satisfied, we have found the correct partition.

---

## Visual Understanding

Suppose:

```java
nums1 = [1, 3, 8]
nums2 = [7, 9, 10, 11]
```

Total elements:

```text
7
```

Required elements on left side:

```text
(7 + 1) / 2 = 4
```

A valid partition:

```text
nums1: [1, 3] | [8]

nums2: [7, 9] | [10, 11]
```

Left side:

```text
1 3 7 9
```

Right side:

```text
8 10 11
```

Check:

```text
max(left) = 9
min(right) = 8
```

Invalid partition.

Move Binary Search accordingly.

---

## Partition Variables

Let:

```java
cut1 = partition in nums1
cut2 = partition in nums2
```

Since:

```text
left side size = (m + n + 1)/2
```

we get:

```java
cut2 = (m + n + 1)/2 - cut1;
```

---

### Border Elements

```java
l1 = nums1[cut1 - 1]
l2 = nums2[cut2 - 1]

r1 = nums1[cut1]
r2 = nums2[cut2]
```

Handle boundaries using:

```java
Integer.MIN_VALUE
Integer.MAX_VALUE
```

---

## Correct Partition Condition

```java
l1 <= r2
&&
l2 <= r1
```

If true, we found the median.

---

## How to Move Binary Search

### Case 1

```java
l1 > r2
```

Partition in `nums1` is too far right.

Move left:

```java
high = cut1 - 1;
```

---

### Case 2

```java
l2 > r1
```

Partition in `nums1` is too far left.

Move right:

```java
low = cut1 + 1;
```

---

## Java Solution

```java
class Solution {

    public double findMedianSortedArrays(int[] nums1, int[] nums2) {

        if (nums1.length > nums2.length) {
            return findMedianSortedArrays(nums2, nums1);
        }

        int m = nums1.length;
        int n = nums2.length;

        int low = 0;
        int high = m;

        while (low <= high) {

            int cut1 = low + (high - low) / 2;
            int cut2 = (m + n + 1) / 2 - cut1;

            int l1 = (cut1 == 0)
                    ? Integer.MIN_VALUE
                    : nums1[cut1 - 1];

            int l2 = (cut2 == 0)
                    ? Integer.MIN_VALUE
                    : nums2[cut2 - 1];

            int r1 = (cut1 == m)
                    ? Integer.MAX_VALUE
                    : nums1[cut1];

            int r2 = (cut2 == n)
                    ? Integer.MAX_VALUE
                    : nums2[cut2];

            if (l1 <= r2 && l2 <= r1) {

                if ((m + n) % 2 == 0) {

                    return (Math.max(l1, l2)
                           + Math.min(r1, r2))
                           / 2.0;
                }

                return Math.max(l1, l2);
            }

            else if (l1 > r2) {
                high = cut1 - 1;
            }

            else {
                low = cut1 + 1;
            }
        }

        return 0.0;
    }
}
```

---

## Dry Run

### Input

```java
nums1 = [1, 3]
nums2 = [2]
```

Since Binary Search should run on the smaller array:

```java
nums1 = [2]
nums2 = [1, 3]
```

---

### Initial Values

```java
m = 1
n = 2

low = 0
high = 1
```

---

### Iteration 1

```java
cut1 = 0
cut2 = 2
```

```java
l1 = -∞
r1 = 2

l2 = 3
r2 = +∞
```

Since:

```java
l2 > r1
```

Move right:

```java
low = cut1 + 1
```

---

### Iteration 2

```java
cut1 = 1
cut2 = 1
```

```java
l1 = 2
r1 = +∞

l2 = 1
r2 = 3
```

Check:

```java
l1 <= r2
2 <= 3

l2 <= r1
1 <= +∞
```

Valid partition.

---

### Median

Total elements:

```text
3 (odd)
```

Median:

```java
max(l1, l2)
=
max(2,1)
=
2
```

Answer:

```java
2.0
```

---

## Why Do We Search on the Smaller Array?

Binary Search complexity depends on the search space.

Searching on the smaller array gives:

```text
O(log(min(m,n)))
```

which satisfies the constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(log(min(m, n)))
```

Binary Search is performed on the smaller array.

---

### Space Complexity

```text
O(1)
```

No extra space is used.

---

## Key Insight

The median is obtained when:

```java
max(left half)
<=
min(right half)
```

Using Binary Search, we find the correct partition without merging the arrays.

```text
Time  : O(log(min(m,n)))
Space : O(1)
```

---

## Similar Problems

1. K-th Element of Two Sorted Arrays
2. Median of a Data Stream
3. Find Peak Element
4. Search in Rotated Sorted Array
5. Split Array Largest Sum
6. K-th Smallest Element in a Sorted Matrix
