# Relative Ranks

## Problem Statement

Given an integer array `score` where `score[i]` is the score of the `i-th` athlete, return an array `answer` of size `n` where:

- `answer[i]` is the rank of the `i-th` athlete.
- The highest score gets `"Gold Medal"`.
- The second highest score gets `"Silver Medal"`.
- The third highest score gets `"Bronze Medal"`.
- The remaining athletes get their ranking number as a string.

### Example 1

**Input:**
```java
score = [5,4,3,2,1]
```

**Output:**
```java
["Gold Medal","Silver Medal","Bronze Medal","4","5"]
```

### Example 2

**Input:**
```java
score = [10,3,8,9,4]
```

**Output:**
```java
["Gold Medal","5","Bronze Medal","Silver Medal","4"]
```

---

## Intuition

Ranks depend on the ordering of scores.

To determine the rank of each athlete while preserving their original positions:

1. Store each score along with its original index.
2. Sort scores in descending order.
3. Assign medals to the top three athletes.
4. Assign rank numbers to the remaining athletes.
5. Place the ranks back at their original indices.

---

## Approach (Sorting + Original Indices)

1. Create pairs of `(score, index)`.
2. Sort pairs in decreasing order of score.
3. Traverse the sorted array:
   - Rank 1 → `"Gold Medal"`
   - Rank 2 → `"Silver Medal"`
   - Rank 3 → `"Bronze Medal"`
   - Rank > 3 → Rank number as string.
4. Store results at the original indices.
5. Return the answer array.

---

## Dry Run

### Input

```java
score = [10,3,8,9,4]
```

### After Storing (Score, Index)

```java
[(10,0), (3,1), (8,2), (9,3), (4,4)]
```

### After Sorting Descending

```java
[(10,0), (9,3), (8,2), (4,4), (3,1)]
```

### Assign Ranks

| Rank | Score | Original Index | Assigned |
|--------|--------|--------|--------|
| 1 | 10 | 0 | Gold Medal |
| 2 | 9 | 3 | Silver Medal |
| 3 | 8 | 2 | Bronze Medal |
| 4 | 4 | 4 | 4 |
| 5 | 3 | 1 | 5 |

### Result

```java
["Gold Medal","5","Bronze Medal","Silver Medal","4"]
```

---

## Java Solution (Optimal)

```java
import java.util.*;

class Solution {
    public String[] findRelativeRanks(int[] score) {

        int n = score.length;

        int[][] athletes = new int[n][2];

        for (int i = 0; i < n; i++) {
            athletes[i][0] = score[i];
            athletes[i][1] = i;
        }

        Arrays.sort(athletes, (a, b) -> b[0] - a[0]);

        String[] answer = new String[n];

        for (int rank = 0; rank < n; rank++) {

            int originalIndex = athletes[rank][1];

            if (rank == 0) {
                answer[originalIndex] = "Gold Medal";
            } else if (rank == 1) {
                answer[originalIndex] = "Silver Medal";
            } else if (rank == 2) {
                answer[originalIndex] = "Bronze Medal";
            } else {
                answer[originalIndex] = String.valueOf(rank + 1);
            }
        }

        return answer;
    }
}
```

---

## Alternative Approach (Max Heap)

### Idea

- Insert `(score, index)` into a max heap.
- Extract elements one by one in descending order.
- Assign ranks while maintaining original indices.

### Java Code

```java
import java.util.*;

class Solution {
    public String[] findRelativeRanks(int[] score) {

        int n = score.length;

        String[] answer = new String[n];

        PriorityQueue<int[]> maxHeap =
            new PriorityQueue<>((a, b) -> b[0] - a[0]);

        for (int i = 0; i < n; i++) {
            maxHeap.offer(new int[]{score[i], i});
        }

        int rank = 1;

        while (!maxHeap.isEmpty()) {

            int[] athlete = maxHeap.poll();
            int index = athlete[1];

            if (rank == 1) {
                answer[index] = "Gold Medal";
            } else if (rank == 2) {
                answer[index] = "Silver Medal";
            } else if (rank == 3) {
                answer[index] = "Bronze Medal";
            } else {
                answer[index] = String.valueOf(rank);
            }

            rank++;
        }

        return answer;
    }
}
```

---

## Complexity Analysis

### Sorting Approach

- **Time Complexity:** `O(n log n)`
- **Space Complexity:** `O(n)`

### Max Heap Approach

- **Time Complexity:** `O(n log n)`
- **Space Complexity:** `O(n)`

---

## Why Sorting is Preferred?

- Simpler implementation.
- Easy rank assignment after sorting.
- No additional heap operations.
- Most commonly expected solution in interviews.

---

## Key Takeaways

- Preserve original indices before sorting.
- Sort scores in descending order.
- Top 3 athletes receive medals instead of numeric ranks.
- Remaining athletes receive rank numbers as strings.
- Optimal solution runs in `O(n log n)` time.
- Classic problem combining sorting and index mapping.
