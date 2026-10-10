# Minimum Recolors to Get K Consecutive Black Blocks

## Problem Statement

You are given a `0-indexed` string `blocks` of length `n`, where:

- `blocks[i] = 'W'` represents a white block.
- `blocks[i] = 'B'` represents a black block.

You are also given an integer `k`, representing the desired number of consecutive black blocks.

In one operation, you can recolor a white block (`W`) into a black block (`B`).

Return the **minimum number of operations** required so that there exists at least one occurrence of `k` consecutive black blocks.

---

## Examples

### Example 1

**Input**

```java
blocks = "WBBWWBBWBW"
k = 7
```

**Output**

```java
3
```

**Explanation**

One way to achieve `7` consecutive black blocks is to recolor the `0th`, `3rd`, and `4th` blocks:

```text
WBBWWBBWBW
↓  ↓↓
BBBBBBBWBW
```

It can be shown that no solution requires fewer than `3` operations.

---

### Example 2

**Input**

```java
blocks = "WBWBBBW"
k = 2
```

**Output**

```java
0
```

**Explanation**

There already exists a substring with `2` consecutive black blocks:

```text
WBWBBBW
   ^^
```

No recoloring is needed.

---

## Approach: Sliding Window

To make a window of size `k` completely black:

- Every white block (`W`) inside the window must be recolored.
- Therefore, the number of recolors required for a window is simply the count of white blocks in that window.

We slide a window of size `k` across the string and track the number of white blocks.

The answer is the minimum white-block count among all windows of length `k`.

---

## Algorithm

1. Count white blocks in the first window of size `k`.
2. Store this count as the current minimum.
3. Slide the window one position at a time:
   - Add the incoming character.
   - Remove the outgoing character.
   - Update the white count.
   - Update the minimum recolors required.
4. Return the minimum count found.

---

## Java Solution

```java
class Solution {

    public int minimumRecolors(String blocks, int k) {

        int n = blocks.length();
        int whiteCount = 0;

        // Count whites in first window
        for (int i = 0; i < k; i++) {
            if (blocks.charAt(i) == 'W') {
                whiteCount++;
            }
        }

        int minRecolors = whiteCount;

        // Slide the window
        for (int i = k; i < n; i++) {

            if (blocks.charAt(i) == 'W') {
                whiteCount++;
            }

            if (blocks.charAt(i - k) == 'W') {
                whiteCount--;
            }

            minRecolors = Math.min(minRecolors, whiteCount);
        }

        return minRecolors;
    }
}
```

---

## Dry Run

### Input

```java
blocks = "WBBWWBBWBW"
k = 7
```

### First Window

```text
W B B W W B B
```

White blocks:

```text
3
```

```text
minRecolors = 3
```

---

### Slide Window

#### Window 2

```text
B B W W B B W
```

White blocks:

```text
3
```

```text
minRecolors = 3
```

---

#### Window 3

```text
B W W B B W B
```

White blocks:

```text
3
```

```text
minRecolors = 3
```

---

#### Window 4

```text
W W B B W B W
```

White blocks:

```text
4
```

```text
minRecolors = 3
```

---

### Final Answer

```text
3
```

Thus, at least `3` white blocks must be recolored to obtain `7` consecutive black blocks.

---

## Why This Works

For every window of length `k`:

```text
Recolors Needed
=
Number of White Blocks in that Window
```

Since every white block must be converted to black, finding the minimum white count across all windows directly gives the minimum number of recolors.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

- Initial window calculation takes `O(k)`.
- Sliding window traverses the remaining characters once.
- Each character is processed at most once.

Overall:

```text
O(n)
```

where `n` is the length of `blocks`.

---

### Space Complexity

```text
O(1)
```

Only a few integer variables are used:

- `whiteCount`
- `minRecolors`
- `n`

No extra data structures are required.

---

## Key Insight

For every window of size `k`:

```text
Operations Needed
=
Count of 'W' in the Window
```

Therefore:

```text
Answer
=
Minimum White Count Among All Windows of Size k
```

A fixed-size sliding window allows us to compute this efficiently in:

```text
Time  : O(n)
Space : O(1)
```
