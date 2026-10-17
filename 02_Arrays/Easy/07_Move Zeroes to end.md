# Move Zeroes

## Problem Statement

Given an integer array `nums`, move all `0`s to the end of the array while maintaining the relative order of the non-zero elements.

You must do this in-place without making a copy of the array.

### Example 1

**Input:**
```java
nums = [0,1,0,3,12]
```

**Output:**
```java
[1,3,12,0,0]
```

### Example 2

**Input:**
```java
nums = [0]
```

**Output:**
```java
[0]
```

---

## Intuition

We want to keep all non-zero elements at the beginning of the array while pushing zeros toward the end.

Use a pointer `j` to track the position where the next non-zero element should be placed.

As we iterate through the array:

- If the current element is non-zero, swap it with the element at index `j`.
- Increment `j`.

This ensures:

- All non-zero elements remain in their original relative order.
- All zeros automatically move to the end.

---

## Approach (Two Pointers)

1. Initialize `j = 0`.
2. Traverse the array using index `i`.
3. Whenever a non-zero element is found:
   - Swap `nums[i]` and `nums[j]`.
   - Increment `j`.
4. After traversal, all non-zero elements occupy the first part of the array and zeros are shifted to the end.

---

## Dry Run

### Input

```java
nums = [0,1,0,3,12]
```

| i | nums[i] | j | Action | Array |
|---|----------|---|---------|--------|
| 0 | 0 | 0 | Skip | [0,1,0,3,12] |
| 1 | 1 | 0 | Swap(1,0) | [1,0,0,3,12] |
| 2 | 0 | 1 | Skip | [1,0,0,3,12] |
| 3 | 3 | 1 | Swap(3,1) | [1,3,0,0,12] |
| 4 | 12 | 2 | Swap(4,2) | [1,3,12,0,0] |

### Final Output

```java
[1,3,12,0,0]
```

---

## Java Solution

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int j = 0;

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                int temp = nums[i];
                nums[i] = nums[j];
                nums[j] = temp;

                j++;
            }
        }
    }
}
```

---

## Alternative Approach

First copy all non-zero elements to the front of the array.

Then fill the remaining positions with zeros.

### Java Code

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int index = 0;

        for (int num : nums) {
            if (num != 0) {
                nums[index++] = num;
            }
        }

        while (index < nums.length) {
            nums[index++] = 0;
        }
    }
}
```

---

## Complexity Analysis

### Two Pointer Swap Approach

- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(1)`

### Copy Non-Zero Then Fill Zero Approach

- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(1)`

---

## Key Takeaways

- Use a pointer to track the position of the next non-zero element.
- Maintain the relative order of non-zero elements.
- Perform the operation in-place.
- Achieve optimal `O(n)` time and `O(1)` space complexity.
