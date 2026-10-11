# Divide Chocolate

**LeetCode 1231 - Divide Chocolate**

## Problem Statement

You have one chocolate bar that consists of multiple chunks.

The sweetness of the `i-th` chunk is given by:

```java
sweetness[i]
```

You want to share the chocolate with your `k` friends.

Since there are:

```text
k friends + you
```

the chocolate must be divided into:

```text
k + 1 pieces
```

using exactly `k` cuts.

Each piece must consist of contiguous chunks.

You will eat the piece with the **minimum total sweetness**.

Your goal is to maximize the sweetness of the piece you receive.

Return the maximum possible sweetness you can get.

---

## Examples

### Example 1

**Input**

```java
sweetness = [1,2,3,4,5,6,7,8,9]
k = 5
```

**Output**

```java
6
```

**Explanation**

One possible division:

```text
[1,2,3]
[4,5]
[6]
[7]
[8]
[9]
```

Sweetness sums:

```text
6, 9, 6, 7, 8, 9
```

The minimum sweetness among all pieces is:

```text
6
```

which is the maximum possible answer.

---

### Example 2

**Input**

```java
sweetness = [5,6,7,8,9,1,2,3,4]
k = 8
```

**Output**

```java
1
```

**Explanation**

Each person gets exactly one chunk.

You receive the minimum chunk:

```text
1
```

---

### Example 3

**Input**

```java
sweetness = [1,2,2,1,2,2,1,2,2]
k = 2
```

**Output**

```java
5
```

---

# Intuition

We want to:

```text
Maximize the minimum sweetness received.
```

This is a classic:

```text
Maximize Minimum
```

problem.

Whenever you see:

```text
Maximize Minimum
or
Minimize Maximum
```

think:

```text
Binary Search on Answer
```

---

## Key Observation

Suppose we guess:

```java
mid
```

as the minimum sweetness we want to guarantee for every piece.

Can we divide the chocolate into at least:

```text
k + 1 pieces
```

such that every piece has sweetness:

```text
>= mid
```

?

### If Yes

We can probably achieve a larger minimum sweetness.

```java
low = mid + 1
```

---

### If No

The sweetness target is too large.

```java
high = mid - 1
```

---

## Search Space

### Minimum Possible Sweetness

The smallest possible sweetness is:

```java
low = 1
```

---

### Maximum Possible Sweetness

If there were no friends:

```java
high = sum(sweetness)
```

---

# Greedy Feasibility Check

For a given value:

```java
mid
```

Accumulate sweetness.

Whenever the current sweetness becomes:

```java
>= mid
```

create a piece.

If the number of pieces formed is at least:

```java
k + 1
```

then the partition is possible.

---

## Example

```java
sweetness = [1,2,3,4,5,6]
k = 2
```

Suppose:

```java
mid = 6
```

### Build Pieces

```text
1+2+3 = 6
```

Piece 1 formed.

```text
4+5 = 9
```

Piece 2 formed.

```text
6
```

Piece 3 formed.

Total pieces:

```text
3
```

Since:

```text
3 >= k+1
```

the answer is feasible.

---

# Binary Search Solution

```java
class Solution {

    public int maximizeSweetness(int[] sweetness, int k) {

        int low = 1;
        int high = 0;

        for (int s : sweetness) {
            high += s;
        }

        int answer = 0;

        while (low <= high) {

            int mid = low + (high - low) / 2;

            if (canDivide(sweetness, k + 1, mid)) {
                answer = mid;
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }

        return answer;
    }

    private boolean canDivide(int[] sweetness,
                              int requiredPieces,
                              int minSweetness) {

        int current = 0;
        int pieces = 0;

        for (int sweet : sweetness) {

            current += sweet;

            if (current >= minSweetness) {
                pieces++;
                current = 0;
            }
        }

        return pieces >= requiredPieces;
    }
}
```

---

# Dry Run

### Input

```java
sweetness = [1,2,3,4,5,6,7,8,9]
k = 5
```

Need:

```text
6 pieces
```

---

### Search Space

```java
low = 1
high = 45
```

---

### mid = 23

Can we create 6 pieces with sweetness at least 23?

```text
No
```

Move left.

```java
high = 22
```

---

### mid = 11

Pieces formed:

```text
1+2+3+4+5 = 15
6+7 = 13
8+9 = 17
```

Only:

```text
3 pieces
```

Not enough.

Move left.

---

### mid = 6

Pieces formed:

```text
1+2+3 = 6
4+5 = 9
6
7
8
9
```

Total:

```text
6 pieces
```

Valid.

Try larger answer.

---

Eventually Binary Search converges to:

```java
6
```

---

# Why Greedy Works

If we can create a piece as soon as:

```java
currentSum >= target
```

we should immediately cut it.

This leaves maximum remaining sweetness for future pieces.

Thus the greedy strategy generates the maximum number of valid pieces.

---

# Why Binary Search Works

If sweetness:

```text
6
```

is achievable,

then:

```text
1, 2, 3, 4, 5
```

are also achievable.

If sweetness:

```text
10
```

is not achievable,

then:

```text
11, 12, 13...
```

are also not achievable.

This gives a monotonic search space:

```text
Valid Valid Valid Valid Invalid Invalid
```

making Binary Search possible.

---

# Complexity Analysis

Let:

```java
n = sweetness.length
```

and

```java
S = sum of all sweetness values
```

---

### Time Complexity

Binary Search:

```text
O(log S)
```

Feasibility Check:

```text
O(n)
```

Overall:

```text
O(n log S)
```

---

### Space Complexity

```text
O(1)
```

No extra data structures are used.

---

# Pattern Recognition

The Divide Chocolate problem belongs to the family of:

```text
Binary Search on Answer
```

problems involving:

```text
Maximize Minimum
```

---

## Similar Problems

1. Aggressive Cows
2. Magnetic Force Between Two Balls
3. Allocate Minimum Pages
4. Painter's Partition
5. Split Array Largest Sum (LC 410)
6. Capacity To Ship Packages Within D Days
7. Minimize Maximum Distance Between Gas Stations

---

# Key Insight

```text
Answer = Maximum Possible Minimum Sweetness
```

For every guessed sweetness:

```java
mid
```

check whether we can form at least:

```text
k + 1 pieces
```

with each piece having sweetness:

```text
>= mid
```

using a greedy partition strategy.

```text
Time  : O(n log(sum))
Space : O(1)
```
