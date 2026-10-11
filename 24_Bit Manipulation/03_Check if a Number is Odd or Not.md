# Check if a Number is Odd or Not

## Problem Statement

Given an integer `n`, check whether the number is **odd** or **even**. 

The overall runtime complexity must be strictly bounded by:

```text
O(1)
```

---

## Examples

### Example 1

**Input**

```java
n = 5
```

**Output**

```java
true
```

**Explanation**

`5` divided by `2` leaves a remainder of `1`, which means it is an odd number.

---

### Example 2

**Input**

```java
n = 12
```

**Output**

```java
false
```

**Explanation**

`12` divided by `2` leaves a remainder of `0`, meaning it is an even number.

---

### Example 3

**Input**

```java
n = -3
```

**Output**

```java
true
```

**Explanation**

Negative integers follow the same parity logic; `-3` is an odd number.

---

## Brute Force Approach

Use the standard modulo arithmetic operator (`%`) to find the remainder when the integer is divided by `2`.

### Steps

1. Evaluate `n % 2`.
2. If the result is not `0`, return `true` (the number is odd).
3. Otherwise, return `false`.

### Complexity

```text
Time Complexity: O(1)
Space Complexity: O(1)
```

While the modulo operator (`%`) is asymptotically constant, integer division at the hardware level takes multiple CPU clock cycles. Furthermore, caution must be taken with negative numbers, as `-3 % 2` evaluates to `-1` in many languages (like Java), meaning a naive check like `n % 2 == 1` will fail for negative inputs. We can optimize this and make it completely bulletproof using bitwise operators.

---

# Optimal Approach: Bitwise AND with Least Significant Bit

## Key Idea

In computer architecture, integers are represented inside memory registers as binary strings of `0`s and `1`s. 

Every bit position in a binary number represents a power of 2 (\(2^0, 2^1, 2^2, 2^3 \dots\)), all of which are **even values** except for the absolute first bit—the **Least Significant Bit (LSB)** at index 0—which represents \(2^0 = 1\).

```text
Binary Weights:  ...  8   4   2   1
Bit Position  :  ...  3   2   1   0
```

Because every other bit position contributes a multiple of 2, the parity of the entire integer is dictated **strictly by the LSB**:
- If the LSB is `1`, the number is guaranteed to be **odd**.
- If the LSB is `0`, the number is guaranteed to be **even**.

We can isolate this LSB in a single CPU clock cycle by performing a bitwise `AND` (`&`) between `n` and `1`. This mask zeroes out all higher powers of 2, leaving only the LSB state. This approach works natively for both positive and negative integers because negative numbers stored under Two's Complement encoding preserve this exact LSB parity rule.

---

## Visual Understanding

Suppose `n = 5` (`0101` in binary):

Apply a bitwise AND mask with `1` (`0001` in binary):

```text
  0101  (n = 5)
& 0001  (mask = 1)
  ----
  0001  (Result is 1, which is != 0)
```

Since the result is `1`, the number is confirmed as odd (`true`).

---

## Partition Variables

Let:

```java
int lsb = n & 1;
```

---

### Border Elements

The bitwise mask handles the minimum and maximum boundary limits of 32-bit and 64-bit signed integers flawlessly without any type overflow risks.

---

## Correct Partition Condition

The number is recognized as odd if the bitwise operation captures a non-zero trailing footprint:

```java
return (n & 1) != 0;
```

---

## Java Solution

```java
class Solution {

    public boolean isOdd(int n) {
        // A bitwise AND with 1 isolates the least significant bit (LSB).
        // If LSB is 1, the number is odd. This works for negative numbers too.
        return (n & 1) != 0;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Bitwise LSB Mask** | **O(1)** | **O(1)** | Executes as a basic low-level logic gate directly within the ALU hardware. |

---

## Key Insight

Inspecting the trailing binary digit directly bypasses the latency of division circuits, evaluating parity in a single hardware cycle while maintaining consistent accuracy across negative numerical bounds.

```text
Time  : O(1) single clock cycle step
Space : O(1) in-place register evaluation
```

---

## Similar Problems

1. Check if the i-th Bit is Set or Not
2. Number of 1 Bits / Hamming Weight (191)
3. Power of Two (231)
4. Single Number (136)
5. Set the i-th Bit
