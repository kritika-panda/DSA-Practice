# Reverse String

## Problem Statement

Write a function that reverses a string. The input string is given as an array of characters `s`.

You must do this by modifying the input array **in-place** with **O(1) extra memory**.

The overall runtime complexity must be:

```text
O(n)
```

*(where n is the length of the character array s)*

---

## Examples

### Example 1

**Input**

```java
s = ['h', 'e', 'l', 'l', 'o']
```

**Output**

```java
['o', 'l', 'l', 'e', 'h']
```

---

### Example 2

**Input**

```java
s = ['H', 'a', 'n', 'n', 'a', 'h']
```

**Output**

```java
['h', 'a', 'n', 'n', 'a', 'H']
```

---

## Brute Force Approach

Create a new character array of the same size, copy elements from the original array backward from the end to the beginning, and overwrite the source array.

### Steps

1. Instantiate a new auxiliary character array `result` of size `n`.
2. Loop through the source array from `0` to `n - 1`.
3. In each step, assign `result[n - 1 - i] = s[i]`.
4. Copy all elements from `result` back into `s`.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // Fails the O(1) in-place constraint
```

Allocating an extra array breaks the strict constant space restriction. We can easily achieve a **zero-allocation footprint** by mirroring elements from both ends simultaneously.

---

# Optimal Approach: Two-Pointer Linear Inversion

## Key Idea

We can reverse the array completely in-place in **O(n) time and O(1) space** by using a **Two-Pointer technique**.

1. Place a `left` pointer at the absolute beginning of the array (index `0`) and a `right` pointer at the absolute end of the array (index `s.length - 1`).
2. Run a loop that continues as long as `left < right`.
3. In each step, swap the characters sitting at the `left` and `right` indices.
4. Advance the pointers toward the center: increment `left` by 1 and decrement `right` by 1.

Since we stop when the pointers meet or cross at the exact midpoint, every element is updated within its own original memory cell.

---

## Visual Understanding

Suppose `s = ['h', 'e', 'l', 'l', 'o']`:

- **Initialization:** `left = 0`, `right = 4`.
- **Step 1 (`0 < 4`):** Swap `s[0]` ('h') and `s[4]` ('o'). 
  - Array becomes: `['o', 'e', 'l', 'l', 'h']`.
  - Advance pointers: `left = 1`, `right = 3`.
- **Step 2 (`1 < 3`):** Swap `s[1]` ('e') and `s[3]` ('l').
  - Array becomes: `['o', 'l', 'l', 'e', 'h']`.
  - Advance pointers: `left = 2`, `right = 2`.
- **Termination:** `left < right` (`2 < 2`) evaluates to false. Loop exits.

The characters are inverted cleanly with zero extra memory overhead.

---

## Partition Variables

Let:

```java
int left = 0;
int right = s.length - 1;
```

---

### Border Elements

The swapping operations naturally handle odd-length and even-length arrays without throwing any out-of-bounds pointer exceptions.

---

## Correct Partition Condition

The linear loop tracks elements symmetrically until the boundary pointers meet or cross at the middle axis:

```java
while (left < right) {
    char temp = s[left];
    s[left] = s[right];
    s[right] = temp;
    left++;
    right--;
}
```

---

## Java Solution

```java
class Solution {

    public void reverseString(char[] s) {
        if (s == null || s.length <= 1) {
            return;
        }

        int left = 0;
        int right = s.length - 1;

        // Step 1: Slide pointers toward the middle, swapping elements in-place
        while (left < right) {
            char temp = s[left];
            s[left] = s[right];
            s[right] = temp;

            // Step 2: Step inward to process the next inner pair
            left++;
            right--;
        }
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Two-Pointer Swap** | **O(n)** | **O(1)** | Processes exactly `n / 2` swap steps in a single forward/backward sweep using constant primitive variable registers. |

---

## Key Insight

Symmetrically swapping characters from both ends inward using two pointers avoids external allocations completely, inverting array bounds in optimal linear time.

```text
Time  : O(n) single pass execution
Space : O(1) absolute constant extra memory
```

---

## Similar Problems

1. Reverse Vowels of a String (345)
2. Reverse Words in a String (151)
3. Reverse String II (541)
4. Valid Palindrome (125)
5. Swap Two Numbers
