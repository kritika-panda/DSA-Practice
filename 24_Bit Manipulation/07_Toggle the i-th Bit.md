# Toggle the i-th Bit

## Problem Statement

Given a positive integer `n` and a 0-based bit position index `i`, **toggle** the `i`-th bit of `n`. Toggling means if the bit is `1`, change it to `0`, and if the bit is `0`, change it to `1`.

The overall runtime complexity must be strictly bounded by:

```text
O(1)
```

---

## Examples

### Example 1

**Input**

```java
n = 12 // Binary: 1100
i = 2
```

**Output**

```java
8 // Binary: 1000
```

**Explanation**

The binary representation of `12` is `1100`. 
- The bit at position 2 is `1`.
- Toggling it changes it to `0`, transforming the binary string into `1000`, which equals `8`.

---

### Example 2

**Input**

```java
n = 12 // Binary: 1100
i = 1
```

**Output**

```java
14 // Binary: 1110
```

**Explanation**

The bit at position 1 in `1100` is `0`. Toggling it to `1` yields `1110` (`14`).

---

### Example 3

**Input**

```java
n = 0 // Binary: 0000
i = 0
```

**Output**

```java
1 // Binary: 0001
```

---

## Brute Force Approach

Convert the integer into its corresponding mutable binary character array format, manually flip the targeted bit index position from the right side, and parse it back to an integer.

### Steps

1. Convert the integer `n` into a dynamic binary string or character array.
2. Index from the right side of the character array to match the 0-based bit index. Pad with leading zeros if the string length is shorter than `i + 1`.
3. If the character is `'1'`, overwrite it with `'0'`. If `'0'`, overwrite it with `'1'`.
4. Convert the modified binary character representation back into a base-10 integer.

### Complexity

```text
Time Complexity: O(log(n)) // proportional to the number of bits
Space Complexity: O(log(n)) to store the string buffer
```

Converting a number to structural text blocks triggers extensive hardware arithmetic cycles. We can optimize this process down to a single clock cycle using a bitwise mask.

---

# Optimal Approach: Bitmasking with XOR

## Key Idea

We can toggle any target bit in true constant **O(1) time** by combining a bitwise **XOR (`^`)** operator with a shifted bitmask.

The XOR operator follows three core hardware properties:
1. `x ^ 0 = x` (An element XORed with 0 preserves its original value)
2. `x ^ 1 = ~x` (An element XORed with 1 always flips to its opposite state)
3. `x ^ x = 0`

Using these rules, we construct a custom **mask** that contains a `1` exactly at index `i` and `0` everywhere else. We achieve this by left-shifting the number `1` by `i` positions: `1 << i`.

When we apply a bitwise XOR between `n` and this mask (`n ^ (1 << i)`):
- All bits of `n` aligned with `0` inside the mask remain completely untouched.
- The single bit of `n` aligned with the `1` inside the mask is guaranteed to flip to its opposite state, fulfilling the toggle criteria perfectly.

---

## Visual Understanding

Suppose `n = 12` (`1100` in binary) and we want to toggle `i = 2`:

1. **Generate the Mask:** `1 << 2` shifts `0001` left by 2 positions \(\implies\) `0100`.
2. **Apply Bitwise XOR:**
   ```text
     1100  (n = 12)
   ^ 0100  (mask = 4)
     ----
     1000  (Result is 8)
   ```

The bit at position 2 is flipped from `1` to `0` cleanly in a single cycle.

---

## Partition Variables

Let:

```java
int mask = 1 << i;
```

---

### Border Elements

The expression securely handles boundary cases. In standard 32-bit environments, shifting beyond index 31 wraps around using an implicit modulo-32 mask on the shift count, protecting registers from undefined errors.

---

## Correct Partition Condition

The variable state is transformed and evaluated across a single hardware logic expression:

```java
return n ^ (1 << i);
```

---

## Java Solution

```java
class Solution {

    public int toggleKthBit(int n, int i) {
        // Create a mask with a 1 at index i, then bitwise XOR it with n
        // This forces the i-th bit to flip while leaving all other bits unchanged
        return n ^ (1 << i);
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Bitwise XOR Mask** | **O(1)** | **O(1)** | Maps straight to native low-level logic gates inside the CPU's arithmetic logic unit (ALU). |

---

## Key Insight

Leveraging the natural state-flipping property of an XOR gate with a localized single-bit mask isolates and toggles any targeted bit instantly, avoiding sequential bit scans or conditional branching completely.

```text
Time  : O(1) single CPU clock cycle
Space : O(1) in-place register calculation
```

---

## Similar Problems

1. Check if the i-th Bit is Set or Not
2. Set the i-th Bit
3. Clear the i-th Bit
4. Swap Two Numbers
5. Single Number (136)
