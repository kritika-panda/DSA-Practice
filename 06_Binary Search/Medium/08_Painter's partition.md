# Painter's Partition Problem

## Problem Statement

You are given:

- An array `boards[]` where `boards[i]` represents the length of the `i-th` board.
- An integer `k` representing the number of painters.

Each painter can paint only **contiguous boards**.

All painters work at the same rate, and a painter can only paint whole boards (a board cannot be split among multiple painters).

Find the **minimum time required to paint all boards**.

---

## Examples

### Example 1

**Input**

```java
boards = [10, 20, 30, 40]
k = 2
```

**Output**

```java
60
```

**Explanation**

Optimal partition:

```java
Painter 1 -> [10, 20, 30] = 60
Painter 2 -> [40] = 40
```

Time required:

```text
max(60, 40) = 60
```

---

### Example 2

**Input**

```java
boards = [10, 10, 10, 10]
k = 2
```

**Output**

```java
20
```

**Explanation**

```java
Painter 1 -> [10, 10]
Painter 2 -> [10, 10]
```

Time:

```text
20
```

---

### Example 3

**Input**

```java
boards = [5, 10, 30, 20, 15]
k = 3
```

**Output**

```java
35
```

**Explanation**

One optimal assignment:

```java
Painter 1 -> [5, 10]
Painter 2 -> [30]
Painter 3 -> [20, 15]
```

Maximum workload:

```text
35
```

---

# Intuition

We need to minimize:

```text
Maximum boards assigned to any painter
```

This is a classic:

```text
Minimize the Maximum
```

problem.

Whenever you see:

```text
Minimize Maximum
Maximize Minimum
```

think:

```text
Binary Search on Answer
```

---

## Search Space

### Minimum Possible Answer

A painter must paint the largest board.

```java
low = max(boards)
```

---

### Maximum Possible Answer

One painter paints everything.

```java
high = sum(boards)
```

---

## Key Observation

Suppose we guess:

```text
mid = maximum time allowed per painter
```

Can all boards be painted using at most `k` painters?

If yes:

```text
Try smaller answer
```

If not:

```text
Need larger answer
```

This monotonic behavior makes Binary Search possible.

---

## Feasibility Function

For a given limit:

```java
mid
```

Count how many painters are required.

### Rule

Keep assigning contiguous boards to the current painter.

If adding a board exceeds:

```java
mid
```

assign a new painter.

---

## Example

```java
boards = [10,20,30,40]
k = 2
mid = 60
```

### Painter 1

```java
10 + 20 + 30 = 60
```

### Painter 2

```java
40
```

Painters required:

```java
2
```

Valid.

---

## Binary Search Solution

```java
class Solution {

    public int minTime(int[] boards, int k) {

        int low = 0;
        int high = 0;

        for (int board : boards) {
            low = Math.max(low, board);
            high += board;
        }

        int ans = high;

        while (low <= high) {

            int mid = low + (high - low) / 2;

            if (canPaint(boards, k, mid)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }

        return ans;
    }

    private boolean canPaint(int[] boards, int k, int maxTime) {

        int painters = 1;
        int sum = 0;

        for (int board : boards) {

            if (sum + board <= maxTime) {
                sum += board;
            } else {
                painters++;
                sum = board;
            }
        }

        return painters <= k;
    }
}
```

---

## Dry Run

### Input

```java
boards = [10,20,30,40]
k = 2
```

---

### Search Space

```java
low = 40
high = 100
```

---

### mid = 70

```text
Painter 1 -> 10+20+30 = 60
Painter 2 -> 40
```

Painters:

```text
2
```

Valid.

```java
ans = 70
high = 69
```

---

### mid = 54

```text
10+20 = 30
```

Next board:

```text
30
```

Needs new painter.

Next board:

```text
40
```

Needs another painter.

Painters:

```text
3
```

Invalid.

```java
low = 55
```

---

### mid = 60

```text
Painter 1 -> 10+20+30 = 60
Painter 2 -> 40
```

Painters:

```text
2
```

Valid.

```java
ans = 60
```

---

### Final Answer

```java
60
```

---

# Why Binary Search Works

If a time limit:

```text
60
```

works,

then

```text
61, 62, 63...
```

will also work.

This forms a monotonic search space:

```text
Invalid Invalid Invalid Valid Valid Valid
```

which is ideal for Binary Search.

---

# Complexity Analysis

Let:

```java
n = boards.length
```

and

```java
S = sum of all boards
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

Total:

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

Whenever the question asks:

```text
Allocate books
Painter partition
Split array largest sum
Ship packages within D days
Capacity to ship packages
```

the pattern is:

```text
Binary Search on Answer
+
Greedy Feasibility Check
```

---

# Similar Problems

1. Allocate Minimum Number of Pages
2. Split Array Largest Sum (LC 410)
3. Capacity To Ship Packages Within D Days (LC 1011)
4. Minimum Limit of Balls in a Bag
5. Aggressive Cows
6. Painter's Partition Problem

---

## Key Insight

```text
Answer = Minimum Possible Maximum Workload
```

Search range:

```java
[max(board), sum(board)]
```

Use Binary Search to find the smallest workload that allows all boards to be painted using at most `k` painters.

```text
Time  : O(n log(sum))
Space : O(1)
```
