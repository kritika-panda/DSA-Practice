# Best Time to Buy and Sell Stock (LeetCode 121)

## Problem Statement

You are given an array `prices` where:

```java
prices[i]
```

represents the stock price on day `i`.

You want to maximize your profit by choosing:

- One day to buy the stock
- A different future day to sell the stock

Return the maximum profit possible.

If no profit can be made, return `0`.

---

## Example 1

```text
Input: prices = [7,1,5,3,6,4]

Output: 5

Explanation:

Buy at price = 1
Sell at price = 6

Profit = 6 - 1 = 5
```

---

## Example 2

```text
Input: prices = [7,6,4,3,1]

Output: 0

Explanation:

No profitable transaction exists.
```

---

# Intuition

To maximize profit:

```text
Profit = Sell Price - Buy Price
```

For every day:

```text
Current Profit =
Current Price - Minimum Price Seen So Far
```

So we only need:

1. Minimum buying price till current day.
2. Maximum profit obtained so far.

---

# Brute Force

## Idea

Try every pair:

```text
Buy on day i
Sell on day j (j > i)
```

Calculate all possible profits.

### Code

```java
class Solution {

    public int maxProfit(int[] prices) {

        int maxProfit = 0;

        for (int i = 0; i < prices.length; i++) {

            for (int j = i + 1; j < prices.length; j++) {

                maxProfit = Math.max(
                        maxProfit,
                        prices[j] - prices[i]
                );
            }
        }

        return maxProfit;
    }
}
```

### Complexity

```text
Time  : O(n²)
Space : O(1)
```

---

# Optimal Approach (Greedy + DP Thinking)

## Key Observation

For every day:

```text
Sell Today
```

To maximize profit:

```text
Buy at the minimum price seen before today
```

Maintain:

```java
minPrice
maxProfit
```

---

# Algorithm

For each price:

### Step 1

Update minimum buying price.

```java
minPrice = Math.min(minPrice, price);
```

### Step 2

Calculate profit if sold today.

```java
profit = price - minPrice;
```

### Step 3

Update answer.

```java
maxProfit = Math.max(maxProfit, profit);
```

---

# Optimal Code

```java
class Solution {

    public int maxProfit(int[] prices) {

        int minPrice = Integer.MAX_VALUE;
        int maxProfit = 0;

        for (int price : prices) {

            minPrice = Math.min(minPrice, price);

            int profit = price - minPrice;

            maxProfit = Math.max(maxProfit, profit);
        }

        return maxProfit;
    }
}
```

---

# Dry Run

```text
prices = [7,1,5,3,6,4]
```

### Day 1

```text
Price = 7

minPrice = 7
profit = 0
maxProfit = 0
```

### Day 2

```text
Price = 1

minPrice = 1
profit = 0
maxProfit = 0
```

### Day 3

```text
Price = 5

profit = 5 - 1 = 4

maxProfit = 4
```

### Day 4

```text
Price = 3

profit = 3 - 1 = 2

maxProfit = 4
```

### Day 5

```text
Price = 6

profit = 6 - 1 = 5

maxProfit = 5
```

### Day 6

```text
Price = 4

profit = 4 - 1 = 3

maxProfit = 5
```

Final Answer:

```text
5
```

---

# Visualization

```text
Prices:

Day:    0  1  2  3  4  5
Price:  7  1  5  3  6  4
            ^
         Buy Here

                  ^
               Sell Here

Profit = 6 - 1 = 5
```

---

# Why Does This Work?

At every index:

```text
Current Price = Potential Selling Price
```

The best buying price is always:

```text
Minimum price encountered before it
```

So checking:

Current Price - Min Price So Far

automatically evaluates the best transaction ending at that day.

---

# Pattern Recognition

This problem belongs to:

```text
Array
Greedy
Kadane-Like Optimization
Prefix Minimum
```

Whenever you see:

```text
Maximize Difference

A[j] - A[i]
where j > i
```

Think:

```text
Maintain Minimum Value Seen So Far
```

---

# Complexity Analysis

### Optimal Solution

```text
Time Complexity  : O(n)
Space Complexity : O(1)
```

---

# Interview Follow-Ups

### 1. Best Time to Buy and Sell Stock II

```text
Unlimited Transactions
```

Greedy solution.

---

### 2. Best Time to Buy and Sell Stock III

```text
At Most 2 Transactions
```

DP on Transactions.

---

### 3. Best Time to Buy and Sell Stock IV

```text
At Most K Transactions
```

DP + State Machine.

---

### 4. Best Time to Buy and Sell Stock with Cooldown

```text
After selling,
must wait one day before buying.
```

DP States.

---

### 5. Best Time to Buy and Sell Stock with Transaction Fee

```text
Profit = Sell - Buy - Fee
```

DP State Machine.

---

# One-Line Formula

```text
maxProfit = max(
    maxProfit,
    currentPrice - minPriceSoFar
)
```

where

```text
minPriceSoFar =
min(minPriceSoFar, currentPrice)
```
