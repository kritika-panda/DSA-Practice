# Find Missing Number

## Problem Statement

Given an array `nums` containing `n` distinct numbers in the range `[0, n]`, return the only number in the range that is missing from the array.

### Example 1

**Input:**
```java
nums = [3,0,1]
```

**Output:**
```java
2
```

### Example 2

**Input:**
```java
nums = [0,1]
```

**Output:**
```java
2
```

### Example 3

**Input:**
```java
nums = [9,6,4,2,3,5,7,0,1]
```

**Output:**
```java
8
```

---

## Intuition

The array contains numbers from `0` to `n`, but one number is missing.

If we know:

```java
Expected Sum = n * (n + 1) / 2
```

and subtract the actual sum of elements, the difference gives the missing number.

---

## Approach 1: Sum Formula

1. Calculate the expected sum from `0` to `n`.
2. Calculate the actual sum of array elements.
3. Return:

```java
Expected Sum - Actual Sum
```

---

## Dry Run

### Input

```java
nums = [3,0,1]
```

```java
n = 3
Expected Sum = 3 * 4 / 2 = 6
Actual Sum = 3 + 0 + 1 = 4

Missing Number = 6 - 4 = 2
```

### Output

```java
2
```

---

## Java Solution (Sum Formula)

```java
class Solution {
    public int missingNumber(int[] nums) {
        int n = nums.length;

        int expectedSum = n * (n + 1) / 2;
        int actualSum = 0;

        for (int num : nums) {
            actualSum += num;
        }

        return expectedSum - actualSum;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(1)`

---

## Approach 2: XOR (Optimal)

### Intuition

Properties of XOR:

```java
x ^ x = 0
x ^ 0 = x
```

If we XOR:

- All indices from `0` to `n`
- All elements of the array

Every common number cancels out, leaving only the missing number.

---

## Dry Run

### Input

```java
nums = [3,0,1]
```

```java
xor = 0 ^ 1 ^ 2 ^ 3 ^ 3 ^ 0 ^ 1

= 2
```

### Output

```java
2
```

---

## Java Solution (XOR)

```java
class Solution {
    public int missingNumber(int[] nums) {
        int xor = nums.length;

        for (int i = 0; i < nums.length; i++) {
            xor ^= i;
            xor ^= nums[i];
        }

        return xor;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(1)`

---

## Approach 3: Sorting

### Idea

1. Sort the array.
2. Check whether each index matches the value.
3. The first mismatch is the missing number.

### Java Code

```java
import java.util.Arrays;

class Solution {
    public int missingNumber(int[] nums) {
        Arrays.sort(nums);

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != i) {
                return i;
            }
        }

        return nums.length;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** `O(n log n)`
- **Space Complexity:** `O(1)` (ignoring sorting space)

---

## Comparison of Approaches

| Approach | Time | Space |
|-----------|---------|---------|
| Sum Formula | O(n) | O(1) |
| XOR | O(n) | O(1) |
| Sorting | O(n log n) | O(1) |

---

## Why XOR is Preferred in Interviews?

- No risk of integer overflow.
- Uses constant extra space.
- Elegant bit-manipulation solution.
- Frequently asked as a follow-up after the sum formula approach.

---

## Key Takeaways

- Numbers belong to the range `[0, n]`.
- Sum Formula provides a simple `O(n)` solution.
- XOR is the most interview-friendly optimal solution.
- Sorting works but is less efficient due to `O(n log n)` time complexity.
