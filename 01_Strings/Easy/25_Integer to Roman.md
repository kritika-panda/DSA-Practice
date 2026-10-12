# Integer to Roman

## Problem Statement

Given an integer `num`, convert it to a roman numeral string.

Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`, `C`, `D` and `M`.

| Symbol | Value |
| :--- | :--- |
| **I** | 1 |
| **V** | 5 |
| **X** | 10 |
| **L** | 50 |
| **C** | 100 |
| **D** | 500 |
| **M** | 1000 |

There are six instances where subtraction is used to prevent four identical symbols from appearing consecutively:
* `I` can be placed before `V` (5) and `X` (10) to make 4 and 9. 
* `X` can be placed before `L` (50) and `C` (100) to make 40 and 90. 
* `C` can be placed before `D` (500) and `M` (1000) to make 400 and 900.

The overall runtime complexity must be:

```text
O(1)
```

*(Since the input integer is bounded up to 3999, the execution path runs within a small fixed upper bound of operations).*

---

## Examples

### Example 1

**Input**

```java
num = 3
```

**Output**

```java
"III"
```

---

### Example 2

**Input**

```java
num = 58
```

**Output**

```java
"LVIII"
```

**Explanation**

`L` = 50, `V` = 5, `III` = 3. `50 + 5 + 3 = 58`.

---

### Example 3

**Input**

```java
num = 1994
```

**Output**

```java
"MCMXCIV"
```

**Explanation**

`M` = 1000, `CM` = 900, `XC` = 90, `IV` = 4. `1000 + 900 + 90 + 4 = 1994`.

---

## Brute Force Approach

Extract individual numerical digits (thousands, hundreds, tens, ones) from the number using modulo arithmetic, and write custom conditional switch statements for every digit column position to map it to its respective Roman symbol string.

### Steps

1. Isolate digits: `thousands = num / 1000`, `hundreds = (num % 1000) / 100`, etc.
2. Route each column value through explicit `if-else` or `switch` branches (e.g., if hundreds equals 9, append `"CM"`; if it equals 4, append `"CD"`; else if it is greater than 5, append `"D"` followed by loops of `"C"`).
3. Concat all string fragments together to compile the final Roman numeral result.

### Complexity

```text
Time Complexity: O(1)
Space Complexity: O(1)
```

While functional, hardcoding conditional switch branches for each distinct numerical column level results in verbose, error-prone branching code. We can solve this cleanly by flattening both base values and subtractive pairs into parallel static array trackers.

---

# Optimal Approach: Greedy Value Mapping

## Key Idea

We can flatten the standard symbols alongside the six special subtractive pairs into two parallel static arrays sorted in descending numerical value order. This provides an absolute map covering all possible numerical Roman token states:

```text
Values:  [1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1]
Symbols: ["M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"]
```

By traversing this mapping greedily from largest to smallest, we strip out the largest available chunks from `num` at each step:
1. Initialize a mutable `StringBuilder`.
2. Loop through the `values` tracking array starting from index 0.
3. While `num >= values[i]`, we subtract `values[i]` from `num` and append `symbols[i]` directly to our builder.
4. Advance to the next smaller index value and repeat. The process completes natively in constant time.

---

## Visual Understanding

Suppose `num = 1994`:

- **i = 0 (Value 1000, Symbol "M"):** `1994 >= 1000` is true. 
  - `num = 1994 - 1000 = 994`. Append `"M"`. `sb = "M"`.
  - Loop repeats: `994 >= 1000` is false. Advance to index 1.
- **i = 1 (Value 900, Symbol "CM"):** `994 >= 900` is true.
  - `num = 994 - 900 = 94`. Append `"CM"`. `sb = "MCM"`.
  - Loop repeats: `94 >= 900` is false. Advance to index 2.
- Skip indices until matching index...
- **i = 5 (Value 90, Symbol "XC"):** `94 >= 90` is true.
  - `num = 94 - 90 = 4`. Append `"XC"`. `sb = "MCMXC"`.
- **i = 11 (Value 4, Symbol "IV"):** `4 >= 4` is true.
  - `num = 4 - 4 = 0`. Append `"IV"`. `sb = "MCMXCIV"`.

`num` drops to 0. The final string `"MCMXCIV"` is compiled completely in a single pass.

---

## Partition Variables

Let:

```java
int[] values = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
String[] symbols = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
StringBuilder sb = new StringBuilder();
```

---

### Border Elements

The loops check ranges dynamically, terminating instantly as soon as `num` is reduced to 0:

```java
if (num == 0) break;
```

---

## Correct Partition Condition

The greedy subtractive decrement rule executes within a nested conditional tracking pass:

```java
while (num >= values[i]) {
    num -= values[i];
    sb.append(symbols[i]);
}
```

---

## Java Solution

```java
class Solution {

    public String intToRoman(int num) {
        // Parallel static lookup tables mapping Roman states greedily in descending order
        int[] values = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        String[] symbols = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};

        StringBuilder sb = new StringBuilder();

        // Step 1: Greedily match values from largest to smallest
        for (int i = 0; i < values.length; i++) {
            // Subtract the matched chunk repeatedly until num drops below the current value threshold
            while (num >= values[i]) {
                num -= values[i];
                sb.append(symbols[i]);
            }

            // Early exit if the number has been completely converted
            if (num == 0) {
                break;
            }
        }

        return sb.toString();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Greedy Reduction** | **O(1)** | **O(1)** | The maximum possible value for `num` is 3999, locking the total loop iterations to a small, predictable constant footprint. |

---

## Key Insight

Flattening the subtractive combinations into a sorted static dictionary transforms an intricate base-parsing assignment into a fast, linear subtraction check.

```text
Time  : O(1) constant runtime execution path
Space : O(1) absolute constant extra space
```

---

## Similar Problems

1. Roman to Integer (13)
2. Integer to English Words (273)
3. Excel Sheet Column Title (168)
4. Base 7 (504)
5. Valid Parentheses (20)
