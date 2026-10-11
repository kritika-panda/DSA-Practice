# Swap Two Numbers

## Problem Statement

Given two variables `a` and `b`, swap their values so that `a` takes the original value of `b` and `b` takes the original value of `a`.

The swap must be performed optimally, and ideally **in-place** without allocating an entire secondary temporary storage variable if memory constraints are tight.

The overall runtime complexity must be strictly bounded by:

```text
O(1)
```

---

## Examples

### Example 1

**Input**

```java
a = 5, b = 10
```

**Output**

```java
a = 10, b = 5
```

---

### Example 2

**Input**

```java
a = -3, b = 7
```

**Output**

```java
a = 7, b = -3
```

---

## Brute Force Approach

The standard, most common way to swap two numbers is by using a third temporary variable to hold one of the values during the transition.

### Steps

1. Create a temporary tracking variable: `int temp = a;`.
2. Copy the value of `b` into `a`: `a = b;`.
3. Copy the stored value from `temp` into `b`: `b = temp;`.

### Complexity

```text
Time Complexity: O(1)
Space Complexity: O(1) // requires 1 word of auxiliary storage
```

While this approach is perfectly fine and heavily readable, it uses extra memory for the third variable. If you are constrained to an absolute **zero-allocation** footprint, you can perform the swap using purely in-place mathematical or bitwise combinations.

---

# Optimal Approach: Bitwise XOR In-Place Swap

## Key Idea

We can swap two variables completely in-place with **zero extra memory allocations** by leveraging the properties of the bitwise **XOR (`^`)** operator.

The XOR operator follows three crucial mathematical identities:
1. `x ^ x = 0` (An element XORed with itself cancels out to 0)
2. `x ^ 0 = x` (An element XORed with 0 stays unchanged)
3. `x ^ y = y ^ x` (The operation is commutative)

By applying these rules, we can swap the variables using exactly three successive XOR operations:

```text
1. a = a ^ b;  // 'a' now stores the combined state of both numbers
2. b = a ^ b;  // b = (original_a ^ original_b) ^ original_b = original_a ^ 0 = original_a
3. a = a ^ b;  // a = (original_a ^ original_b) ^ original_a = original_b ^ 0 = original_b
```

This updates both variables in-place without needing a temporary buffer. 

*(Note: You can also use arithmetic addition and subtraction like `a = a + b; b = a - b; a = a - b;`, but the bitwise XOR method is preferred because it is completely immune to integer overflow errors under standard hardware bounds).*

---

## Visual Understanding

Suppose `a = 5` (`0101` in binary) and `b = 10` (`1010` in binary):

- **Step 1 (`a = a ^ b`):**
  ```text
    0101  (a = 5)
  ^ 1010  (b = 10)
    ----
    1111  (New value of a = 15)
  ```
- **Step 2 (`b = a ^ b`):**
  ```text
    1111  (Current a = 15)
  ^ 1010  (Current b = 10)
    ----
    0101  (New value of b = 5 -> Swapped successfully!)
  ```
- **Step 3 (`a = a ^ b`):**
  ```text
    1111  (Current a = 15)
  ^ 0101  (Current b = 5)
    ----
    1010  (New value of a = 10 -> Swapped successfully!)
  ```

Variables have swapped values entirely within their own memory slots.

---

## Partition Variables

Let:

```java
// No intermediate temporary allocation variables are declared
```

---

### Border Elements

**Important Edge Case:** This in-place XOR trick works perfectly as long as `a` and `b` reference different memory addresses. If you attempt to use this trick on the exact same array element slot (i.e., swapping an element with itself via `swap(nums[i], nums[i])`), `a ^ a` immediately drives the value to `0`, permanently erasing the data. We guard against this by ensuring indices or values differ.

---

## Correct Partition Condition

The sequence of in-place modifications stabilizes the registers instantly across three lines of code:

```java
a = a ^ b;
b = a ^ b;
a = a ^ b;
```

---

## Java Solution

```java
class Solution {

    public void swapValues(int[] nums, int i, int j) {
        // Guard condition: If indices are identical, swapping is redundant.
        // Doing XOR swap on the exact same memory cell will mistakenly zero it out.
        if (i == j) {
            return;
        }

        // Three-step XOR in-place swap
        nums[i] = nums[i] ^ nums[j];
        nums[j] = nums[i] ^ nums[j];
        nums[i] = nums[i] ^ nums[j];
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Bitwise In-Place Swap** | **O(1)** | **O(1)** | Executes instantly as a few primitive CPU clock cycle register changes with zero stack memory updates. |

---

## Key Insight

Utilizing the canceling property of the XOR gate allows two memory locations to exchange data directly through combined states, bypassing auxiliary variable allocation costs completely.

```text
Time  : O(1) constant runtime
Space : O(1) absolute zero-allocation footprint
```

---

## Similar Problems

1. Single Number (136)
2. Missing Number (268)
3. Reverse Bits (190)
4. Check if the i-th Bit is Set or Not
5. Toggle the i-th Bit
