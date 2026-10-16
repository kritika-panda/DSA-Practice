# Online Stock Span ([LeetCode 901](https://leetcode.com))

## Problem Statement

Design an algorithm that collects daily price quotes for some stock and returns the **span** of that stock's price for the current day.

The **span** of the stock's price in one day is the maximum number of **consecutive days** (starting from today and going backward) for which the stock price was **less than or equal to** today's price.

- For example, if the prices of the stock in the last four days are `[10, 4, 3, 6]` and the price of the stock today is `8`, then the span of today is `4` because starting from today, the price was less than or equal to 8 for `4` consecutive days.
- If the prices of the stock in the last four days are `[10, 4, 3, 6]` and the price of the stock today is `6`, then the span of today is `1`.

Implement the `StockSpanner` class:
1. `StockSpanner()` Initializes the object of the class.
2. `int next(int price)` Returns the **span** of the stock's price given that today's price is `price`.

The overall runtime complexity per data quote stream addition must be strictly bounded by:

```text
O(1) amortized
```

---

## Example Execution Trace

```text
StockSpanner stockSpanner = new StockSpanner();
1. stockSpanner.next(100); → Price history:,          Returns 1
2. stockSpanner.next(80);  → Price history:,      Returns 1
3. stockSpanner.next(60);  → Price history:,  Returns 1
4. stockSpanner.next(70);  → Price history:, 
                             70 is > 60. Span updates: 1 (for 70) + 1 (for 60) = 2. Returns 2
5. stockSpanner.next(60);  → Price history:, Returns 1
6. stockSpanner.next(75);  → Collapses 60 and 70 tracking weights. Returns 4
7. stockSpanner.next(85);  → Collapses context back to the 100 anchor. Returns 6
```

---

# Intuition

A naive approach would append each price to a list and scan backward during every `next()` call to count the consecutive days, costing an inefficient O(N) time per query (O(N²) overall).

To achieve an optimal **O(1) amortized runtime**, we process the live data stream using a **Monotonic Decreasing Stack**. Instead of storing raw data entries independently, we store pairs containing:
\[\text{Pair} = \{\text{Price}, \text{Span Weight}\}\]

When a new `price` arrives:
- We want to find the nearest previous day that had a strictly higher price. This day acts as a natural ceiling block.
- Any historical day on the stack that has a price **less than or equal to** the incoming `price` has its tracking span completely consumed by the new entry. Since the new larger entry will protect these smaller entries from any future lookback sweeps on the left, their separate identity on the stack becomes redundant.
- We greedily **pop** these smaller entries from the stack and add their compiled `Span Weight` metrics straight into our current day's running counter.
- This dynamic flattening of smaller elements keeps the stack elements sorted strictly decreasingly.

---

# Brute Force Solution

## Idea

We allocate an expanding dynamic list container to act as our baseline price log history tracking book. Each time `next(price)` triggers, we save the new entry onto the tail of our list. Then, we use a manual loop pointer to step backward from the tail toward the front, incrementing a count until we hit a price strictly greater than the active input.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class StockSpanner {
    private final List<Integer> prices;

    public StockSpanner() {
        this.prices = new ArrayList<>();
    }
    
    public int next(int price) {
        // Step 1: Record the incoming price quote onto history logs
        prices.add(price);
        
        int span = 0;
        int index = prices.size() - 1;
        
        // Step 2: Manually loop backwards to count consecutive days <= price
        while (index >= 0 && prices.get(index) <= price) {
            span++;
            index--;
        }
        
        return span;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) worst-case per individual `next()` call if prices are continuously sorted in an increasing track (e.g., `[10, 20, 30, 40]`). Total execution for N queries hits quadratic O(N²) bounds.
- **Space Complexity:** O(N) to maintain the full historical price log sequence array list on heap memory.

---

# State Definition (Optimal Approach)

To minimize operational costs, we maintain structural history context using an array or node stack holding matching pairs:

```java
Stack<int[]> stack = new Stack<>(); // Stack storing primitives layout blocks: [price, span]
```

---

# Recurrence / Stack Contract Condition

```java
int currentSpan = 1;
while (!stack.isEmpty() && stack.peek()[0] <= price) {
    currentSpan += stack.pop()[1];
}
stack.push(new int[]{price, currentSpan});
return currentSpan;
```

---

# Optimal Monotonic Stack Solution

## Idea

We store a 2-element primitive array node `[price, span]` inside our monotonic stack. When a new quote arrives, we accumulate the span values of all smaller or equal entries from the top of the stack while popping them off. Once a larger price bottleneck is found, we push our current compiled entry back onto the stack and return the sum.

---

## Java Code

```java
import java.util.Stack;

class StockSpanner {
    // Monotonic stack storing tracking blocks: [price, calculated_span_weight]
    private final Stack<int[]> stack;

    public StockSpanner() {
        this.stack = new Stack<>();
    }
    
    public int next(int price) {
        // Base state: a single day naturally includes itself, matching a base span of 1
        int currentSpan = 1;

        // Maintain monotonic decreasing property from bottom to top
        // Pop elements whose price quotes are smaller than or equal to the incoming quote
        while (!stack.isEmpty() && stack.peek()[0] <= price) {
            // Absorb the pre-calculated span tracking weights of the popped days
            currentSpan += stack.pop()[1];
        }

        // Push the consolidated entry onto the stack to act as an anchor for future queries
        stack.push(new int[]{price, currentSpan});

        return currentSpan;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(1) amortized per call
```
*Explanation:* Although a single `next()` invocation can trigger an O(N) linear loop pass if it pops a large block of items at once, each price quote stream entry is pushed onto the stack exactly once and popped from the stack at most once across the entire application runtime timeline. The total number of stack processing mutations is bounded by 2N over a string sequence of N queries, keeping the amortized operational complexity locked at an optimized O(1) constant time.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Auxiliary space scales linearly to store tracking elements inside our stack, bounded by the maximum number of daily queries N in the worst-case scenario where incoming stock prices decrease continuously.

---

# Key Takeaways

✅ High-tier fundamental design pattern for processing real-time streaming data using monotonic invariants.

✅ **The Value of Weight Consolidation:** Instead of storing repetitive chronological points, bundling elements into abstract compound pairs (`[value, aggregated_weight]`) flattens the internal history log. This reduces a multi-step backtracking problem into an efficient sequence of single-step reductions.

✅ Directly drives real-world production configurations like **financial chart tickers pricing analytics modules**, real-world sensor telemetry threshold monitoring monitors, server traffic network spike detection logs, and algorithmic asset risk systems.

✅ Common Architectural Paradigm:

```text
Historical Context Compression via Stateful Monotonic Stack Aggregations
```

### Similar Problems

- Daily Temperatures ([LeetCode 739](https://leetcode.com))
- Next Greater Element I ([LeetCode 496](https://leetcode.com))
- Next Smaller Element
- Sum of Subarray Minimums ([LeetCode 907](https://leetcode.com))
- Largest Rectangle in Histogram ([LeetCode 84](https://leetcode.com))
