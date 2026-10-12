# Excel Sheet Column Title

## Problem Statement

Given an integer `columnNumber`, return *its corresponding column title as it appears in an Excel sheet*.

For example:
- `1` -> `"A"`
- `2` -> `"B"`
- `3` -> `"C"`
- `26` -> `"Z"`
- `27` -> `"AA"`
- `28` -> `"AB"`

The overall runtime complexity must be:

```text
O(log_26(columnNumber))
```

*(which is proportional to the number of characters in the final output title string)*

---

## Examples

### Example 1

**Input**

```java
columnNumber = 1
```

**Output**

```java
"A"
```

---

### Example 2

**Input**

```java
columnNumber = 28
```

**Output**

```java
"AB"
```

**Explanation**

`26` maps to `"Z"`. The next title sequence wraps over to two digits: `27 -> "AA"` and `28 -> "AB"`.

---

### Example 3

**Input**

```java
columnNumber = 701
```

**Output**

```java
"ZY"
```

---

## Brute Force Approach

Iterate or recursively generate string layouts, dividing values while mapping remainders directly to base-26 alphabet structures.

### Steps

1. Create a loop checking if `columnNumber > 0`.
2. Extract the remainder of the number modulo 26: `remainder = columnNumber % 26`.
3. Map this remainder value to a character (e.g., `1 -> 'A'`, `2 -> 'B'`).
4. Update the number: `columnNumber = columnNumber / 26`.
5. Prepend the character to a result string and repeat.

### Complexity

```text
Time Complexity: O(log_26(columnNumber))
Space Complexity: O(log_26(columnNumber)) to hold string buffers
```

While this logic resembles standard base conversion (like decimal to binary), a naive base-26 modulo conversion **breaks on multiples of 26**. For example, if `columnNumber = 26`, `26 % 26` equals `0`, which does not exist in Excel's 1-indexed `"A"` to `"Z"` sequence layout. We must adjust the alignment shift before every division.

---

# Optimal Approach: Adjusted Base-26 Radical Shift

## Key Idea

The Excel column mapping functions like a **base-26 number system**, but with a vital difference: it has **no zero element**. It uses a 1-indexed mapping from `1` (`"A"`) to `26` (`"Z"`).

To make it behave exactly like a standard 0-indexed base-26 positional math framework, we must **subtract 1 from `columnNumber` at the start of each loop iteration**.

This adjustment shifts the boundaries perfectly into a standard `0` to `25` modulo domain:
1. Initialize a mutable `StringBuilder`.
2. While `columnNumber > 0`:
   - **Shift Alignment:** Decrement `columnNumber` by 1. This converts a `1`-to-`26` window down to a safe `0`-to-`25` coordinate spectrum (e.g., `26` becomes `25`).
   - **Isolate Digit:** Extract the current character character index using modulo: `remainder = columnNumber % 26`.
   - **Map to Character:** Convert this remainder into a character relative to the base character `'A'`: `(char) ('A' + remainder)`. Append it to our builder.
   - **Divide Block:** Divide the number by 26 to move to the next higher digit column: `columnNumber = columnNumber / 26`.
3. Since characters are extracted from right to left, reverse the compiled `StringBuilder` before returning the final text.

---

## Visual Understanding

Suppose `columnNumber = 26`:

- **Iteration 1:**
  - Shift alignment: `columnNumber = 26 - 1 = 25`.
  - Isolate digit: `remainder = 25 % 26 = 25`.
  - Map to character: `'A' + 25` evaluates to `'Z'`. Append to builder \(\implies\) `sb = "Z"`.
  - Divide block: `columnNumber = 25 / 26 = 0`.
- **Termination:** `columnNumber` drops to 0. Loop ends. Reverse `sb` \(\implies\) `"Z"`.

Suppose `columnNumber = 27`:

- **Iteration 1:**
  - Shift alignment: `columnNumber = 27 - 1 = 26`.
  - Isolate digit: `remainder = 26 % 26 = 0`.
  - Map to character: `'A' + 0` evaluates to `'A'`. Append to builder \(\implies\) `sb = "A"`.
  - Divide block: `columnNumber = 26 / 26 = 1`.
- **Iteration 2:**
  - Shift alignment: `columnNumber = 1 - 1 = 0`.
  - Isolate digit: `remainder = 0 % 26 = 0`.
  - Map to character: `'A' + 0` evaluates to `'A'`. Append to builder \(\implies\) `sb = "AA"`.
  - Divide block: `columnNumber = 0 / 26 = 0`.
- **Termination:** Reverse `sb` \(\implies\) `"AA"`.

The 1-offset boundary gaps resolve perfectly across all multiples of 26.

---

## Partition Variables

Let:

```java
StringBuilder sb = new StringBuilder();
```

---

### Border Elements

The numerical loop handles large 32-bit integer boundaries up to `Integer.MAX_VALUE` flawlessly, operating inside a strict division track:

```java
while (columnNumber > 0)
```

---

## Correct Partition Condition

The 1-based alignment offset subtraction must precede the modulo extraction in each cycle:

```java
columnNumber--; // Shifting the index down from 1-indexed to 0-indexed boundaries
int remainder = columnNumber % 26;
sb.append((char) ('A' + remainder));
columnNumber /= 26;
```

---

## Java Solution

```java
class Solution {

    public String convertToTitle(int columnNumber) {
        StringBuilder sb = new StringBuilder();

        // Step 1: Process the number by dividing it down progressively
        while (columnNumber > 0) {
            
            // Shift the number down to map 1-26 into a 0-25 base-26 spectrum
            columnNumber--; 
            
            int remainder = columnNumber % 26;
            
            // Convert the remainder into its corresponding uppercase character
            sb.append((char) ('A' + remainder));
            
            // Move to the next higher digit column
            columnNumber /= 26;
        }

        // Step 2: Reverse the builder since characters were extracted from right to left
        return sb.reverse().toString();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Base-26 Reduction** | **O(log_26(n))** | **O(log_26(n))** | Where `n` is the input column number. The variable value drops by a factor of 26 in each loop cycle. The string builder allocates memory matching the size of the final title text. |

---

## Key Insight

Subtracting 1 at each step converts a 1-indexed column structure into a standard 0-indexed base-26 mathematical framework, handling edge cases cleanly in a single pass.

```text
Time  : O(log_26(n)) logarithmic execution path
Space : O(log_26(n)) dynamic string output builder allocation
```

---

## Similar Problems

1. Excel Sheet Column Number (171)
2. Base 7 (504)
3. Integer to Roman (12)
4. Integer to English Words (273)
5. Add Binary (67)
