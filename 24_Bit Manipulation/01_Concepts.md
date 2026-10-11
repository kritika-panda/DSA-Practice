# Introduction to Bit Manipulation

## Problem Statement

**Bit Manipulation** is the act of algorithmically manipulating bits (0s and 1s) directly within a computer's primitive data types. Operating at the bit level bypasses the abstraction layers of higher-level arithmetic operators, enabling highly performant operations that execute in a **single CPU clock cycle**.

Mastering bit manipulation requires a solid understanding of basic bitwise operators, fundamental identities, and common recipes used to solve complex tracking, filtering, and masking problems efficiently.

The standard bitwise operations include:
```text
1. AND (&)
2. OR (|)
3. XOR (^)
4. NOT (~)
5. Left Shift (<<)
6. Right Shift (>>) / Unsigned Right Shift (>>>)
```

The overall runtime complexity for any primitive bitwise operation is:
```text
O(1)
```

---

## Core Bitwise Operators

### 1. Bitwise AND (`&`)
Returns `1` if **both** bits are `1`; otherwise, returns `0`.
- Used heavily for **bitmasking** (checking if a specific bit is set or clearing bits).
- *Identity:* `x & 0 = 0`, `x & x = x`, `x & ~x = 0`

### 2. Bitwise OR (`|`)
Returns `1` if **at least one** bit is `1`; otherwise, returns `0`.
- Used to **set** specific bits to `1`.
- *Identity:* `x | 0 = x`, `x | x = x`, `x | ~x = all 1s`

### 3. Bitwise XOR (`^`)
Returns `1` if the bits are **different**; returns `0` if they are identical.
- Essential for tracking parity, flipping states, or finding missing duplicate items.
- *Identity:* `x ^ 0 = x`, `x ^ x = 0` (Crucial property), `x ^ y = y ^ x`

### 4. Bitwise NOT (`~`)
Inverts all the bits of the operand (`1` becomes `0`, and `0` becomes `1`).
- Operates under two's complement encoding: `~x = -x - 1`.

### 5. Bitwise Shifts (`<<`, `>>`, `>>>`)
- **Left Shift (`<<`):** Shifts bits to the left, padding empty right-side slots with `0`. Left shifting an integer by `k` positions is mathematically equivalent to **multiplying by \(2^k\)**.
- **Sign-Extension Right Shift (`>>`):** Shifts bits to the right, copying the leftmost sign bit (0 for positive, 1 for negative) to preserve the number's sign. This is equivalent to **integer division by \(2^k\)**.
- **Unsigned Right Shift (`>>>`):** Shifts bits to the right, padding the leftmost empty slots with `0` regardless of the original sign.

---

## Essential Bit Manipulation Recipes

Here are the standard recipes that must be memorized to parse bit structures efficiently:

| Recipe Goal | Bit Expression | Explanation / Mechanics |
| :--- | :--- | :--- |
| **Check if Odd / Even** | `(x & 1) == 0` | If the least significant bit (LSB) is 0, the number is even; if 1, it is odd. |
| **Check if i-th bit is set**| `(x & (1 << i)) != 0` | Creates a mask with a 1 at index `i` and verifies if `x` shares that bit. |
| **Set i-th bit to 1** | `x = x \| (1 << i)` | Forces the bit at index `i` to become 1, leaving other bits untouched. |
| **Clear i-th bit to 0** | `x = x & ~(1 << i)` | Flips a single-bit mask to create an all-1s mask except for a 0 at index `i`. |
| **Toggle i-th bit** | `x = x ^ (1 << i)` | Flips the bit at index `i` to its opposite state. |
| **Clear the lowest set bit** | `x = x & (x - 1)` | Strips out the rightmost 1-bit from `x`. Useful for counting set bits. |
| **Isolate the lowest set bit**| `x & (-x)` | Leaves only the rightmost 1-bit active, clearing everything else to 0. |
| **Check if Power of 2** | `x > 0 && (x & (x - 1)) == 0` | A power of 2 has exactly one 1-bit. Clearing it must result in 0. |

---

## Visual Understanding

Suppose we check if `x = 12` is a power of 2 using the recipe `x & (x - 1)`:
- Binary of `12` is `1100`.
- Binary of `11` (x - 1) is `1011`.

Let's evaluate the bitwise AND operation:
```text
  1100  (12)
& 1011  (11)
  ----
  1000  (Result is 8, which is != 0)
```
Since the result `8` is not equal to `0`, `12` is recognized as **not** a power of 2.

Now suppose we clear the lowest set bit of `12` again using the same expression:
- The result of the first step was `1000` (`8`). Notice that the lowest set bit of `12` (at index 2) has been cleared cleanly.

---

## Java Implementation Example (Bit Toolkit)

```java
class BitToolkit {

    // Counts the number of set bits (1s) in an integer (Brian Kernighan's Algorithm)
    public int countSetBits(int x) {
        int count = 0;
        while (x > 0) {
            x = x & (x - 1); // Clears the lowest set bit in each step
            count++;
        }
        return count;
    }

    // Checks if an integer is a power of two
    public boolean isPowerOfTwo(int x) {
        return x > 0 && (x & (x - 1)) == 0;
    }

    // Retrieves the absolute value of an integer without branching paths
    public int getAbsoluteValue(int x) {
        int mask = x >> 31; // All 0s for positive, all 1s (-1) for negative
        return (x + mask) ^ mask;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Bitwise Operations** | **O(1)** | **O(1)** | Maps directly to basic hardware logic gates inside the ALU. |
| **`countSetBits`** | **O(k)** | **O(1)** | Runs exactly `k` times, where `k` is the number of set bits (at most 32). |

---

## Key Insight

Operating directly on the machine's underlying binary representation removes abstraction layers, transforming complex numerical logic, state toggling, and routing steps into constant-time hardware level steps.

```text
Time  : O(1) per primitive operator
Space : O(1) in-place register layout
```

---

## Similar Problems

1. Number of 1 Bits / Hamming Weight (191)
2. Counting Bits (338)
3. Single Number (136)
4. Power of Two (231)
5. Reverse Bits (190)
