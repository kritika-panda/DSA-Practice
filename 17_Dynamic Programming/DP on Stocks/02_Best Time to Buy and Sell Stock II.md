# Best Time to Buy and Sell Stock II (LeetCode 122)

## Problem Statement

You are given an integer array `prices` where `prices[i]` is the price of a given stock on the `i`-th day.

On each day, you may decide to buy and/or sell the stock. You can only hold **at most one** share of the stock at any time. However, you can buy it and then sell it on the **same day**.

Find and return *the **maximum profit** you can achieve*.

---

## Examples

### Example 1

```java
Input: prices = [7,1,5,3,6,4]
Output: 7
```

### Explanation

```text
- Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5 - 1 = 4.
- Buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6 - 3 = 3.
Total profit = 4 + 3 = 7.
```

---

### Example 2

```java
Input: prices = [1,2,3,4,5]
Output: 4
```

### Explanation

```text
- Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5 - 1 = 4.
Total profit = 4.
```

---

### Example 3

```java
Input: prices = [7,6,4,3,1]
Output: 0
```

### Explanation

```text
The stock price decreases every day, so no profit can be made. Return 0.
```

---

# Intuition

To maximize our profit when allowed to make **unlimited transactions**:

- We want to capture **every single upward price movement** across our timeline.
- If the price of the stock tomorrow is strictly greater than the price today, we can conceptually buy the stock today and sell it tomorrow to secure that localized gain.
- Even if a stock grows continuously over several days (e.g., `1 → 2 → 5`), the total profit `5 - 1 = 4` is mathematically identical to collecting the individual consecutive up-steps: `(2 - 1) + (5 - 2) = 1 + 3 = 4`.
- This observation allows us to discard complex tracking engines or state-machines. We simply scan the array linearly and greedily sum up every positive adjacent delta (`prices[i] - prices[i-1]`).

---

# Greedy Observation

Consider the array:

```text
prices = [7, 1, 5, 3, 6, 4]
```

Let's look at all adjacent daily price deltas:

```text
Day 1 to Day 2: 1 - 7 = -6 ❌ (Ignore price drops)
Day 2 to Day 3: 5 - 1 = +4  (Add to profit)
Day 3 to Day 4: 3 - 5 = -2 ❌ (Ignore price drops)
Day 4 to Day 5: 6 - 3 = +3  (Add to profit)
Day 5 to Day 6: 4 - 6 = -2 ❌ (Ignore price drops)

Total Greedy Profit = 4 + 3 = 7.
```

By adding up every incremental gain on the spot, the total cumulative sum converges on the absolute maximum profit.

---

# Approach: Single-Pass Greedy Step Summation

## Algorithm

1. Initialize `maxProfit = 0`.
2. Loop through the array from index `i = 1` up to `prices.length - 1`:
   - Check if the current day's price is strictly greater than the previous day's price: `prices[i] > prices[i - 1]`.
   - If true, add the localized positive delta directly to our accumulator:
     ```text
     maxProfit += prices[i] - prices[i - 1]
     ```
3. Return `maxProfit`.

---

# Dry Run

```java
prices = [7, 1, 5, 3, 6, 4]
```

### Initial State

```text
maxProfit = 0
```

---

### i = 1 (price = 1, prev = 7)

```text
1 > 7 ❌ (Price dropped, do nothing)
```

---

### i = 2 (price = 5, prev = 1)

```text
5 > 1 ✅

maxProfit += (5 - 1) → 4
```

---

### i = 3 (price = 3, prev = 5)

```text
3 > 5 ❌ (Price dropped, do nothing)
```

---

### i = 4 (price = 6, prev = 3)

```text
6 > 3 ✅

maxProfit += (6 - 3) → 4 + 3 = 7
```

---

### i = 5 (price = 4, prev = 6)

```text
4 > 6 ❌ (Price dropped, do nothing)
```

Loop ends.

Answer:

```text
7
```

---

# Java Solution

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length == 0) {
            return 0;
        }

        int maxProfit = 0;

        // Linearly traverse the prices array from the second day onwards
        for (int i = 1; i < prices.length; i++) {
            // Greedy Core: If today's price is higher than yesterday's, capture the gain
            if (prices[i] > prices[i - 1]) {
                maxProfit += prices[i] - prices[i - 1];
            }
        }

        return maxProfit;
    }
}
```

---

# Optimized Understanding

While this problem can be structured as an explicit multi-state dynamic programming problem tracking holding vs. un-holding balances, the unlimited transaction rule reduces the graph search space down to a flat line. 

Accumulating all localized monotonic up-slopes effectively flattens any complex holding period into continuous segments of growth, yielding an optimal $O(1)$ space runtime execution.

---

# Correctness Proof

### Greedy Choice

We capture a profit step whenever `prices[i] > prices[i-1]`. 

Suppose there is a multi-day increasing run: $A \le B \le C$. Our greedy strategy collects $(B - A) + (C - B)$, which telescopes perfectly into $C - A$. 

Now suppose there is a drop: $A \le B > C \le D$. Buying at $A$ and holding until $D$ earns $D - A$. However, by selling greedily at the peak $B$ and buying back at the low $C$, we earn $(B - A) + (D - C)$. Since $B > C$, it is a mathematical guarantee that:
$$(B - A) + (D - C) > D - A$$

Thus, picking every incremental increase is always a safe and globally optimal choice.

---

### Optimal Substructure

After collecting the daily delta `prices[i] - prices[i-1]` at a valid step, the problem of maximizing profit across the remaining suffix sub-segment `prices[i...n-1]` remains completely independent, preserving an identical subproblem pattern.

---

# Complexity Analysis

Let:

```text
n = prices.length
```

### Time Complexity

Single-pass loop:

```text
O(n)
```

Overall:

```text
O(n)
```

---

### Space Complexity

```text
O(1)
```

Requires only one primitive scalar integer tracking register, completely avoiding extra collection list allocations.

---

# Key Pattern

This problem belongs to:

```text
Greedy + Adjacent Delta Summation + Slope Tallying
```

Common indicators:

- Making unlimited transaction choices over a sequential timeline
- Maximizing cumulative metrics by summing up local increasing changes
- O(1) space constraints with strict single-pass limitations

---

# Similar Problems

1. Best Time to Buy and Sell Stock (Single transaction - LeetCode 121)
2. Best Time to Buy and Sell Stock III (At most 2 transactions - LeetCode 123)
3. Best Time to Buy and Sell Stock IV (At most k transactions - LeetCode 188)
4. Best Time to Buy and Sell Stock with Cooldown (LeetCode 309)
5. Best Time to Buy and Sell Stock with Transaction Fee (LeetCode 714)

---

# Takeaway

```text
Scan the prices linearly from left to right.

Compare each day's price with the immediate previous day's price.

Greedily add every positive price difference to your running total 
to maximize total profit.
```

**Pattern:** Greedy + Adjacent Delta Summation + Slope Tallying

<FollowUp>
We can now review how introducing constraints impacts your choice strategy. Would you like to generate the raw markdown code for **Best Time to Buy and Sell Stock with Transaction Fee (LeetCode 714)** or explore the multi-state DP layout of **Best Time to Buy and Sell Stock III (At most 2 transactions)**? Let me know!
</FollowUp>
