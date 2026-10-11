# Check if the i-th Bit is Set or Not

## Problem Statement

Given a positive integer `n` and a 0-based bit position index `i`, check whether the `i`-th bit of `n` is **set** (`1`) or **not set** (`0`).

The overall runtime complexity must be:

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
true
```

**Explanation**

The binary representation of `12` is `1100`. 
- Bit 0: `0`
- Bit 1: `0`
- Bit 2: `1` (Set)
- Bit 3: `1`
Since the bit at position 2 is `1`, the answer is `true`.

---

### Example 2

**Input**

```java
n = 12 // Binary: 1100
i = 1
```

**Output**

```java
false
```

**Explanation**

The bit at position 1 in `1100` is `0`, so the answer is `false`.

---

### Example 3

**Input**

```java
n = 1 // Binary: 0001
i = 0
```

**Output**

```java
true
```

---

## Brute Force Approach

Convert the integer into its absolute binary string representation and index straight into it from the right side.

### Steps

1. Convert the integer `n` into a binary string using language utility functions (like `Integer.toBinaryString(n)`).
2. Calculate the target index from the end of the string to map the 0-based bit position right-to-left.
3. If the calculated index is out of bounds (due to leading zeros being omitted), it means the bit is `0`.
4. Otherwise, check if the character at that position is `'1'`.

### Complexity

```text
Time Complexity: O(log(n)) // proportional to the number of bits
Space Complexity: O(log(n)) to store the string copy
```

Converting a number to a string forces the CPU to run multiple arithmetic division steps. We can optimize this to a single clock cycle using bitwise operators.

---

# Optimal Approach: Bitmasking (Left Shift / Right Shift)

## Key Idea

We can check the status of any bit in constant **O(1) time** using bitwise operators. There are two simple variations to achieve this:

### Method 1: Left Shift Masking (`&`)
We create a **mask** that contains a `1` exactly at index `i` and `0` everywhere else. We achieve this by left-shifting the number `1` by `i` positions: `1 << i`. 
We then perform a bitwise `AND` between `n` and this mask. If the `i`-th bit of `n` is `1`, the result will be non-zero (`1 << i`). If it is `0`, the result will be exactly `0`.

### Method 2: Right Shift Isolation (`>>`)
Instead of shifting the mask, we shift the number `n` itself to the right by `i` positions: `n >> i`. This brings the target `i`-th bit straight down to the least significant bit (LSB) position at index 0. 
We then mask it with `1` (`(n >> i) & 1`). If the LSB is `1`, the result is `1` (`true`); if `0`, the result is `0` (`false`).

---

## Visual Understanding

Suppose `n = 12` (`1100` in binary) and we want to check `i = 2` using **Method 1 (Left Shift)**:

1. **Generate the Mask:** `1 << 2` shifts `0001` left by 2 spots \(\implies\) `0100`.
2. **Apply Bitwise AND:**
   ```text
     1100  (n = 12)
   & 0100  (mask = 4)
     ----
     0100  (Result is 4, which is != 0)
   ```
Since `4 != 0`, the bit is set (`true`).

---

## Partition Variables

Let:

```java
int mask = 1 << i;
```

---

### Border Elements

The operation securely handles bounds since shifting a 32-bit integer beyond index 31 behaves predictably according to language bitwise rules (it wraps around using a modulo 32 mask on the shift count).

---

## Correct Partition Condition

Using the Left Shift variation, the bit is confirmed as set if the bitwise operation isolates a non-zero evaluation footprint:

```java
return (n & (1 << i)) != 0;
```

Using the Right Shift variation, the expression checks the trailing bit directly:

```java
return ((n >> i) & 1) == 1;
```

---

## Java Solution

```java
class Solution {

    // Method 1: Left Shift Masking
    public boolean checkKthBitLeftShift(int n, int i) {
        // Create a mask with a 1 at index i, then bitwise AND it with n
        return (n & (1 << i)) != 0;
    }

    // Method 2: Right Shift Isolation
    public boolean checkKthBitRightShift(int n, int i) {
        // Shift the target bit down to index 0, then extract it with & 1
        return ((n >> i) & 1) == 1;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Bitwise Invariant** | **O(1)** | **O(1)** | Maps straight to basic hardware logic gates inside the CPU's ALU. |

---

## Key Insight

Isolating an individual bit using a shifted mask or moving it straight to the lowest slot avoids string or mathematical scanning paths completely, executing in a single hardware level check.

```text
Time  : O(1) constant step
Space : O(1) in-place register evaluation
```

---

## Similar Problems

1. Set the i-th Bit (Set Bit)
2. Clear the i-th Bit
3. Toggle the i-th Bit
4. Number of 1 Bits / Hamming Weight (191)
5. Power of Two (231)
