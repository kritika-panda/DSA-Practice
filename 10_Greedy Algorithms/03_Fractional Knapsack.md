# Fractional Knapsack Problem

## Problem Statement

Given:

- `N` items
- Each item has:
  - `value[i]`
  - `weight[i]`
- A knapsack with capacity `W`

Unlike the 0/1 Knapsack Problem, you can take a fraction of an item.

Return the maximum profit that can be obtained.

---

## Example

### Input

```text
Items:

Value  Weight
60     10
100    20
120    30

Capacity = 50
```

### Output

```text
240
```

### Explanation

Calculate value per unit weight:

```text
60/10  = 6
100/20 = 5
120/30 = 4
```

Sort by ratio in descending order:

```text
(60,10)  ratio = 6
(100,20) ratio = 5
(120,30) ratio = 4
```

Take items greedily:

```text
Take entire item 1 -> Profit = 60
Remaining capacity = 40

Take entire item 2 -> Profit = 160
Remaining capacity = 20

Take 20/30 of item 3
Profit += 120 × (20/30) = 80
```

Final profit:

```text
160 + 80 = 240
```

---

# Intuition

To maximize profit:

```text
Profit = Value / Weight
```

We should prioritize items that give the maximum value for each unit of weight.

Therefore:

```text
Sort items by:

value / weight
```

in descending order.

Then:

- Take the whole item if possible.
- Otherwise take only the fraction that fits.

This greedy choice is optimal.

---

# Greedy Observation

Suppose:

```text
Item A:
Value = 100
Weight = 10

Ratio = 10
```

```text
Item B:
Value = 200
Weight = 40

Ratio = 5
```

For every unit of weight:

```text
A gives 10 profit
B gives 5 profit
```

Clearly, choosing A first is always beneficial.

Thus:

```text
Higher ratio = Higher priority
```

---

# Approach

## Step 1

Create items containing:

```text
(value, weight)
```

---

## Step 2

Compute:

```text
ratio = value / weight
```

---

## Step 3

Sort all items in decreasing order of ratio.

---

## Step 4

Traverse sorted items.

### Case 1: Entire item fits

```text
weight <= remainingCapacity
```

Take the whole item.

```text
profit += value
capacity -= weight
```

---

### Case 2: Item does not fit

Take only required fraction.

```text
fraction = remainingCapacity / weight
```

Profit gained:

```text
value × fraction
```

Knapsack becomes full.

---

# Dry Run

```text
Value  = [60,100,120]
Weight = [10,20,30]

Capacity = 50
```

Ratios:

```text
6, 5, 4
```

Sorted:

```text
(60,10)
(100,20)
(120,30)
```

---

### Item 1

```text
Capacity = 50

Take complete item

Profit = 60
Capacity = 40
```

---

### Item 2

```text
Take complete item

Profit = 160
Capacity = 20
```

---

### Item 3

```text
Weight = 30
Capacity = 20
```

Take fraction:

```text
20/30
```

Profit added:

```text
120 × (20/30) = 80
```

Final:

```text
Profit = 240
```

---

# Java Solution

```java
import java.util.*;

class Item {
    int value;
   *int weight;

    Item(int value, i*t weight) {
        this.value = v*lue;
        this.weight = weight;*    }
}

class Solution {

    dou*le fractionalKnapsack(int W, Item[] arr, int n) {

        Arrays.sor*(arr, (a, b) -> {
            doub*e r1 = (double) a.value / a.weight*
            double r2 = (double) *.value / b.weight;

            re*urn Double.compare(r2, r1);
      * });

        double profit = 0.0;*
        for (Item item : arr) {

*           if (item.weight <= W) {*
                profit += item.va*ue;
                W -= item.weig*t;

            } else {

        *       profit += ((double) item.va*ue / item.weight) * W;
                break;
            }
        }

        return profit;
    }
}
```

---

# Correctness Proof

Let:

```text
ratio = value / weight
```

For any remaining capacity:

```text
Choosing the item with the highest ratio
produces the maximum profit per unit weight.
```

If we choose an item with a smaller ratio before a larger ratio:

```text
Profit gained per unit weight decreases.
```

Swapping them always increases or maintains profit.

Therefore the optimal solution must always include the highest ratio item first.

Repeating this argument for every step proves the greedy strategy is optimal.

---

# Complexity Analysis

Let:

```text
N = Number of items
```

### Time Complexity

Sorting:

```text
O(N log N)
```

Traversal:

```text
O(N)
```

Overall:

```text
O(N log N)
```

---

### Space Complexity

```text
O(1)
```

Ignoring sorting space.

---

# Fractional Knapsack vs 0/1 Knapsack

| Feature | Fractional Knapsack | 0/1 Knapsack |
|----------|----------|----------|
| Fraction allowed | Yes | No |
| Technique | Greedy | Dynamic Programming |
| Sort by ratio | Yes | No |
| Time Complexity | O(N log N) | O(N × W) |
| Greedy works? | Yes | No |

---

# Why Greedy Works Here?

Because items can be broken into fractions.

Example:

```text
Value = 120
Weight = 30
```

Need only:

```text
20 weight
```

Take:

```text
20/30 of item
```

Profit:

```text
120 × (20/30)
```

Since partial selection is allowed, choosing the best value-per-weight ratio first is always optimal.

---

# Pattern Recognition

Use **Fractional Knapsack Greedy** when:

```text
✓ Fractional selection is allowed
✓ Need to maximize profit/value
✓ Every item has weight & value
✓ Capacity constraint exists
✓ Value per unit weight matters
```

---

# Similar Problems

1. Job Sequencing Problem
2. Assign Cookies
3. Minimum Platforms
4. Huffman Encoding
5. Connect Ropes with Minimum Cost
6. Maximum Units on a Truck
7. IPO (LeetCode 502)

---

# Takeaway

```text
Whenever partial selection is allowed:

1. Compute Value / Weight ratio
2. Sort in descending order
3. Take highest ratio items first
4. Take fraction of last item if necessary
```

**Pattern:** Greedy + Sorting by Value Density (`value / weight`)
