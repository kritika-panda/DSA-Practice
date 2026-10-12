# Zigzag Conversion

## Problem Statement

The string `"PAYPALISHIRING"` is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

```text
P   A   H   N
A P L S I I G
Y   I   R
```

And then read line by line: `"PAHNAPLSIIGYIR"`.

Write the code that will take a string `s` and make this conversion given a number of rows:

```java
String convert(String s, int numRows);
```

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "PAYPALISHIRING"
numRows = 3
```

**Output**

```java
"PAHNAPLSIIGYIR"
```

---

### Example 2

**Input**

```java
s = "PAYPALISHIRING"
numRows = 4
```

**Output**

```java
"PINALSIGYAHRPI"
```

**Explanation**

```text
P     I    N
A   L S  I G
Y A   H R
P     I
```

---

### Example 3

**Input**

```java
s = "A"
numRows = 1
```

**Output**

```java
"A"
```

---

## Brute Force Approach

Create a full 2D character matrix array tracking both row and column positions explicitly, write out characters one by one following a zigzag path simulation, and scan the entire grid row by row to assemble the result.

### Steps

1. Create a 2D grid matrix of characters with size `numRows` by `s.length()`.
2. Maintain row and column pointers along with a direction flag variable.
3. Traverse the string characters from left to right:
   - If moving vertically down, increment row pointer.
   - If moving diagonally up, decrement row pointer and increment column pointer.
4. Scan through the entire grid row by row, ignoring empty padded placeholders, to combine the output string.

### Complexity

```text
Time Complexity: O(numRows * n)
Space Complexity: O(numRows * n) // allocates a sparse matrix grid with heavy empty cell blocks
```

Allocating a massive sparse matrix wastes both time and memory space on empty blocks. We can optimize this process down to pure linear performance by simulating only the rows using an array of `StringBuilder` buckets.

---

# Optimal Approach: Row-by-Row Simulation via Direction Toggling

## Key Idea

Instead of mapping out explicit column grids across a full 2D grid, we only care about which **row** a character lands on. We can model this efficiently by creating an array of `StringBuilder` buckets—one for each row.

We then simulate the zigzag trajectory by bouncing an index pointer up and down across the rows sequentially:
1. Initialize an array of `StringBuilder` blocks matching `numRows`.
2. Track our active row position using a `currRow` index pointer initialized to 0, and track our vertical movement using a boolean direction indicator `goingDown`.
3. Loop through the characters of string `s` from left to right:
   - Append the current character directly to the `StringBuilder` at `rowBuilders[currRow]`.
   - **The Direction Toggle Rule:** If `currRow == 0`, we have hit the top roof, so we force `goingDown = true`. If `currRow == numRows - 1`, we have hit the bottom floor, so we force `goingDown = false`.
   - Update our row coordinate: If `goingDown` is true, increment `currRow` by 1. Otherwise, decrement `currRow` by 1.
4. Finally, merge all rows sequentially into a single unified `StringBuilder` and return the result.

---

## Visual Understanding

Suppose `s = "PAYPALISHIRING"` and `numRows = 3`:

- **Initialization:** `currRow = 0`, `goingDown = false`, 3 empty string builders.
- **Character 0 ('P'):** Append to Row 0. `currRow == 0` \(\implies\) toggle `goingDown = true`. Move to `currRow = 1`.
- **Character 1 ('A'):** Append to Row 1. Move down to `currRow = 2`.
- **Character 2 ('Y'):** Append to Row 2. `currRow == 2` (numRows - 1) \(\implies\) toggle `goingDown = false`. Move up to `currRow = 1`.
- **Character 3 ('P'):** Append to Row 1. Move up to `currRow = 0`.
- **Character 4 ('A'):** Append to Row 0. `currRow == 0` \(\implies\) toggle `goingDown = true`. Move down to `currRow = 1`.

The character stream bounces smoothly across row tracks.

---

## Partition Variables

Let:

```java
StringBuilder[] rows = new StringBuilder[numRows];
int currRow = 0;
boolean goingDown = false;
```

---

### Border Elements

If `numRows == 1` or the string's length is shorter than the row count, no zigzagging can physically occur. Return the original string immediately to prevent loop errors:

```java
if (numRows == 1 || s.length() <= numRows) {
    return s;
}
```

---

## Correct Partition Condition

The bounce direction changes state precisely when the row index strikes the upper or lower boundary limits:

```java
if (currRow == 0 || currRow == numRows - 1) {
    goingDown = !goingDown; // Reverse direction state
}
currRow += goingDown ? 1 : -1;
```

---

## Java Solution

```java
class Solution {

    public String convert(String s, int numRows) {
        // Fast-fail: No conversion is needed if there's only 1 row or string is too short
        if (numRows == 1 || s.length() <= numRows) {
            return s;
        }

        // Create an array of StringBuilders, one for each row track
        StringBuilder[] rowBuilders = new StringBuilder[numRows];
        for (int i = 0; i < numRows; i++) {
            rowBuilders[i] = new StringBuilder();
        }

        int currRow = 0;
        boolean goingDown = false;

        // Step 1: Distribute characters across row buckets
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            rowBuilders[currRow].append(c);

            // Step 2: Toggle direction at the boundary limits (top row or bottom row)
            if (currRow == 0 || currRow == numRows - 1) {
                goingDown = !goingDown;
            }

            // Step 3: Move the index pointer up or down accordingly
            currRow += goingDown ? 1 : -1;
        }

        // Step 4: Merge all row buckets into a single string result
        StringBuilder result = new StringBuilder();
        for (StringBuilder row : rowBuilders) {
            result.append(row);
        }

        return result.toString();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Row-Bouncing Scan** | **O(n)** | **O(n)** | Traverses the string exactly once. The combined row string builders allocate memory cells matching the final output string size. |

---

## Key Insight

Isolating text segments into row-specific buckets instead of tracking horizontal matrix column shifts removes empty cell placeholders entirely, securing linear time execution inside compact boundaries.

```text
Time  : O(n) single pass forward execution
Space : O(n) dynamic string output builder allocations
```

---

## Similar Problems

1. Reverse String (344)
2. String to Integer / atoi (8)
3. Text Justification (68)
4. Longest Common Prefix (14)
5. Diagonal Traverse (498)
