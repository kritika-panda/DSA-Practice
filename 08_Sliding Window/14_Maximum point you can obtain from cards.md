# Maximum Points You Can Obtain from Cards

## Problem Statement

Given `N` cards arranged in a row, each card has an associated score denoted by the `cardScore` array. You need to choose exactly `k` cards. 

In each step, you can choose a card either from the **beginning** or from the **end** of the row. Your total score is the sum of the scores of the chosen cards. Return the **maximum score** you can achieve.

---

## Examples

### Example 1

```text
Input: cardScore =, k = 3
Output: 15
```

### Explanation

```text
Choosing the three rightmost cards will maximize your total score. 
The optimal cards chosen are 4, 5, and 6.
Total score = 4 + 5 + 6 = 15.
```

---

### Example 2

```text
Input: cardScore =, k = 3
Output: 12
```

### Explanation

```text
1. In the first step, choose the card from the beginning (score = 5).
2. In the second step, choose the card from the beginning again (score = 4).
3. In the third step, choose the card from the end (score = 3).
Total score = 5 + 4 + 3 = 12.
```

---

# Key Concept: Sliding Window Shift

Instead of simulating combinations recursively, we can think of picking `k` cards as starting with a window containing **all `k` cards from the left side**. 

We then sequentially substitute elements out from the tail of the left side window and bring elements in from the tail of the right side window. This allows us to inspect all valid edge pick combinations in a single pass.

---

# Intuition

The algorithm proceeds in two main phase shifts:

### Phase 1: Establish Left Sum Baseline
We greedily pick the first `k` elements from the left side of the array and find their total sum (`leftSum`). We set our initial `maxScore` to this value.

---

### Phase 2: Shift Elements to the Right
We run a loop `k` times to gradually replace left-side cards with right-side cards:
1. Subtract the rightmost element of the remaining left-side window from `leftSum`.
2. Add the corresponding element from the end of the array to `rightSum`.
3. Calculate the new combined score (`leftSum + rightSum`) and update our running `maxScore`.

This systematically checks all possible splitting ratios (e.g., `k` left cards + `0` right cards, `k-1` left cards + `1` right card, up to `0` left cards + `k` right cards).

---

# Visualization

Inspecting combinations on `cardScore = [5, 4, 1, 8, 7, 1, 3]` with `k = 3`:

```text
1. Initial Left Baseline (3 Left, 0 Right):
   - Chosen: [5, 4, 1] 8, 7, 1, 3
   - leftSum = 5 + 4 + 1 = 10, rightSum = 0. maxScore = 10.

2. First Shift (2 Left, 1 Right):
   - Eject 1 from left, bring in 3 from right.
   - Chosen: [5, 4] 1, 8, 7, 1, [3]
   - leftSum = 10 - 1 = 9, rightSum = 0 + 3 = 3. 
   - New Score = 9 + 3 = 12. maxScore = max(10, 12) = 12.

3. Second Shift (1 Left, 2 Right):
   - Eject 4 from left, bring in 1 from right.
   - Chosen: [5] 4, 1, 8, 7, [1, 3]
   - leftSum = 9 - 4 = 5, rightSum = 3 + 1 = 4. 
   - New Score = 5 + 4 = 9. maxScore = max(12, 9) = 12.

4. Third Shift (0 Left, 3 Right):
   - Eject 5 from left, bring in 7 from right.
   - Chosen: 5, 4, 1, 8, [7, 1, 3]
   - leftSum = 5 - 5 = 0, rightSum = 4 + 7 = 11. 
   - New Score = 0 + 11 = 11. maxScore = max(12, 11) = 12.

Final Answer: 12
```

---

# Java Implementation

```java
public class Solution {
    public int maxScore(int[] cardPoints, int k) {
        int n = cardPoints.length;
        int leftSum = 0;

        // Step 1: Calculate the sum of the first k elements from the left
        for (int i = 0; i < k; i++) {
            leftSum += cardPoints[i];
        }
        
        int maxScore = leftSum;
        int rightSum = 0;

        // Step 2: Dynamically swap left elements out and right elements in
        for (int i = 0; i < k; i++) {
            leftSum -= cardPoints[k - 1 - i]; // Remove from left window tail
            rightSum += cardPoints[n - 1 - i]; // Add to right window tail
            maxScore = Math.max(maxScore, leftSum + rightSum);
        }
        
        return maxScore;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(k) where `k` is the number of cards to choose. The algorithm makes one pass of size `k` to initialize the sum, and a second pass of size `k` to swap elements. Because the remaining elements in the array are never touched, the runtime is independent of the total array length `N`.
* **Space Complexity:** O(1) auxiliary space. The calculation preserves an in-place window structure using only a few primitive integer counters.
