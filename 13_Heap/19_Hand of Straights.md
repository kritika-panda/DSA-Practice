# Hand of Straights

## Problem Statement

Alice has some number of cards and she wants to rearrange the cards into groups so that each group is of size `groupSize`, and consists of `groupSize` consecutive cards.

Given an integer array `hand` where `hand[i]` is the value written on the `i`-th card and an integer `groupSize`, return `true` *if she can rearrange the cards, or `false` otherwise*.

The overall runtime complexity must be:

```text
O(n * log(n))
```

*(where n is the total number of cards in the hand)*

---

## Examples

### Example 1

**Input**

```java
hand = [1, 2, 3, 6, 2, 3, 4, 7, 8]
groupSize = 3
```

**Output**

```java
true
```

**Explanation**

Alice's hand can be rearranged as `[1, 2, 3]`, `[2, 3, 4]`, and `[6, 7, 8]`.

---

### Example 2

**Input**

```java
hand = [1, 2, 3, 4, 5]
groupSize = 4
```

**Output**

```java
false
```

**Explanation**

Alice's hand cannot be rearranged into groups of 4 consecutive cards.

---

## Brute Force Approach

Sort the array and repeatedly search from left to right to construct consecutive groups. When an item is selected for a group, mark it as visited or delete it from the collection.

### Steps

1. Fast-fail if `hand.length % groupSize != 0`.
2. Sort the `hand` array in ascending order.
3. Create a boolean array `visited` of size `n`.
4. Run an outer loop to find the first unvisited card to serve as the start of a new group.
5. Run an inner loop to find the next `groupSize - 1` consecutive integers (`start + 1`, `start + 2`, etc.), scanning forward and marking them as visited.
6. If any consecutive card cannot be found among the unvisited entries, return `false`.

### Complexity

```text
Time Complexity: O(n^2) // due to repeatedly scanning forward for consecutive values
Space Complexity: O(n) to track visited cards
```

Repeatedly scanning an array linearly for consecutive elements results in a quadratic bottleneck. We can optimize this process down to **log-linear time** by tracking card counts dynamically with a sorted tree map.

---

# Optimal Approach: TreeMap Frequency Counting

## Key Idea

To split the cards into valid consecutive groups, we must always start each group with the **absolute smallest card available** in our hand. 

By tracking the frequencies of cards using a **TreeMap**, elements are automatically organized in ascending sorted order by their numeric values. This allows us to find the next starting anchor and verify straight flushes in log-linear time:
1. **Divisibility Check Guard:** If `hand.length % groupSize != 0`, it is mathematically impossible to divide the cards evenly. Return `false` instantly.
2. **Frequency Mapping:** Populate a `TreeMap<Integer, Integer> cardCounts` with the frequency of each card in the hand.
3. **Greedy Group Assembly:** While the map is not empty:
   - Extract the absolute smallest card currently available using `cardCounts.firstKey()`. Let this be our `firstCard`.
   - Loop from `0` up to `groupSize - 1` to construct a straight of consecutive numbers starting from `firstCard`: `currentCard = firstCard + i`.
   - **The Mismatch Check:** If `currentCard` is missing from our map, a consecutive straight cannot be formed. Return `false` immediately.
   - **Count Reduction:** Decrement the frequency of `currentCard` in the map. If its count drops to `0`, remove the key from the map entirely so the next smallest element bubbles to the top.
4. If the map is successfully emptied with zero matching structural breaks, return `true`.

---

## Visual Understanding

Suppose `hand = [1, 2, 3, 6, 2, 3, 4, 7, 8]` and `groupSize = 3`:

- **Step 1 (Validate & Map):** `9 % 3 == 0` (Valid). 
  - TreeMap stores: `{1:1, 2:2, 3:2, 4:1, 6:1, 7:1, 8:1}`.
- **Step 2 (Form Group 1):**
  - Smallest key is `1`. We must form a straight of length 3: `[1, 2, 3]`.
  - Decrement counts: `1` drops to 0 (remove key), `2` drops to 1, `3` drops to 1.
  - Map state: `{2:1, 3:1, 4:1, 6:1, 7:1, 8:1}`.
- **Step 3 (Form Group 2):**
  - Smallest key is now `2`. Form a straight of length 3: `[2, 3, 4]`.
  - Decrement counts: `2` drops to 0 (remove), `3` drops to 0 (remove), `4` drops to 0 (remove).
  - Map state: `{6:1, 7:1, 8:1}`.
- **Step 4 (Form Group 3):**
  - Smallest key is `6`. Form a straight of length 3: `[6, 7, 8]`. All drop to 0 and get cleared.

The map is empty! Return `true`.

---

## Partition Variables

Let:

```java
TreeMap<Integer, Integer> cardCounts = new TreeMap<>();
```

---

### Border Elements

The preliminary guard clause filters out invalid size distributions instantly before allocating object maps on the heap:

```java
if (hand.length % groupSize != 0) {
    return false;
}
```

---

## Correct Partition Condition

The consecutive iteration check decrements maps and cleans up exhausted keys dynamically in-place:

```java
for (int i = 0; i < groupSize; i++) {
    int currentCard = firstCard + i;
    if (!cardCounts.containsKey(currentCard)) return false;
    
    int count = cardCounts.get(currentCard);
    if (count == 1) {
        cardCounts.remove(currentCard);
    } else {
        cardCounts.put(currentCard, count - 1);
    }
}
```

---

## Java Solution

```java
import java.util.TreeMap;

class Solution {

    public boolean isNStraightHand(int[] hand, int groupSize) {
        // Fast-fail: If the total number of cards cannot be divided evenly, return false
        if (hand == null || hand.length % groupSize != 0) {
            return false;
        }

        // Initialize a TreeMap to keep track of card frequencies in sorted order
        TreeMap<Integer, Integer> cardCounts = new TreeMap<>();
        for (int card : hand) {
            cardCounts.put(card, cardCounts.getOrDefault(card, 0) + 1);
        }

        // Step 1: Iteratively process cards starting from the smallest available value
        while (!cardCounts.isEmpty()) {
            int firstCard = cardCounts.firstKey();

            // Step 2: Attempt to form a consecutive group of size groupSize
            for (int i = 0; i < groupSize; i++) {
                int currentCard = firstCard + i;

                // If a consecutive card is missing, the straight cannot be formed
                if (!cardCounts.containsKey(currentCard)) {
                    return false;
                }

                int count = cardCounts.get(currentCard);
                
                // Step 3: Decrement the frequency count or remove the key if exhausted
                if (count == 1) {
                    cardCounts.remove(currentCard);
                } else {
                    cardCounts.put(currentCard, count - 1);
                }
            }
        }

        return true;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **TreeMap Greedy Assembly** | **O(n * log(n))** | **O(n)** | Inserting `n` elements into a `TreeMap` costs (O(n log n)). In each while loop iteration, fetching the first key and updating or removing elements from the tree costs (O(log n)). The map stores up to `n` unique elements. |

---

## Key Insight

Using a sorted frequency map to greedily anchor consecutive card checks on the absolute smallest available element ensures structural straight violations are intercepted at the earliest possible threshold in stable log-linear time.

```text
Time  : O(n * log(n)) optimal log-linear tracking pass
Space : O(n) auxiliary map layout storage allocation
```

---

## Similar Problems

1. Divide Array in Sets of K Consecutive Numbers (1296)
2. Split Array into Consecutive Subsequences (659)
3. TreeMap Internals & Red-Black Tree Architecture
4. Group Anagrams (49)
5. Sort an Array (912)
