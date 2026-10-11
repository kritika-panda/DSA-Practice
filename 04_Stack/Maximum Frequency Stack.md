# Maximum Frequency Stack

## Problem Statement

Design a stack-like data structure that logs elements and supports retrieving the most frequent element dynamically.

Implement the `FreqStack` class:
- `FreqStack()` Initializes the object with an empty frequency stack profile.
- `void push(int val)` Pushes an integer `val` onto the top of the stack.
- `int pop()` Removes and returns the most frequent element in the stack. If there is a tie for the most frequent element, the element closest to the top of the stack (the most recently pushed one) is removed and returned.

The overall run time complexity for both operations should be:

```text
O(1)
```

---

## Examples

### Example 1

**Input**

```java
FreqStack freqStack = new FreqStack();
freqStack.push(5); // [5]
freqStack.push(7); // [5, 7]
freqStack.push(5); // [5, 7, 5]
freqStack.push(7); // [5, 7, 5, 7]
freqStack.push(4); // [5, 7, 5, 7, 4]
freqStack.push(5); // [5, 7, 5, 7, 4, 5]
freqStack.pop();   // return 5 (most frequent, frequency is 3)
freqStack.pop();   // return 7 (tied for max frequency 2, but 7 is newer)
freqStack.pop();   // return 5 (most frequent, frequency is 2)
freqStack.pop();   // return 4 (tied for max frequency 1, but 4 is newer)
```

---

## Brute Force Approach

Maintain an internal list tracking elements alongside a Hash Map for counting, sorting or scanning the data structures linearly on each query.

### Steps

1. Maintain an internal list of integers `elementsList` representing the layout sequence.
2. `push(val)`: Append the incoming item to the end of `elementsList`.
3. `pop()`: Re-calculate frequencies for all values dynamically using a frequency map. Locate the highest frequency count, track backward from the end of `elementsList` to find the first node matching that maximum count threshold, remove it from the list, and return it.

### Complexity

```text
Time Complexity: O(1) for push, O(n) per pop query
Space Complexity: O(n)
```

Scanning and computing frequencies from scratch on every eviction step triggers linear execution bottlenecks. Alternatively, a Max-Heap Priority Queue paired with secondary counter timestamps optimizes pop updates to `O(log n)`, which still falls short of true constant time.

---

# Optimal Approach: Grouping Stacks by Frequency

## Key Idea

We can complete both operations in true constant **O(1) time** by mapping the frequency values themselves directly to independent stacks.

We maintain two tracking layers:
1. **Global Frequency Map (`freqMap`):** A standard map mapping `val -> current_frequency`.
2. **Frequency Buckets Map (`groupMap`):** A map mapping `frequency -> Stack<Integer>`. Each frequency layer holds its own separate stack of values.

When a value is pushed, we increment its frequency count. We then look up the stack corresponding to this new frequency inside `groupMap` and push the value onto it. We also update a global variable `maxFreq` to track the absolute highest frequency bucket present in our system.

When an eviction (`pop()`) occurs:
- We peek at the stack associated with the highest known frequency bucket (`maxFreq`).
- The top element of this specific stack is our answer because it naturally satisfies both criteria: it sits in the highest frequency bucket, and because it is at the top of a LIFO stack, it is the most recently added element among the tied items.
- We pop the item, decrement its count in `freqMap`, and if its frequency stack becomes completely empty, we decrement `maxFreq`.

---

## Visual Understanding

Suppose we execute the following operations:

```java
freqStack.push(5); 
freqStack.push(7); 
freqStack.push(5);
```

1. **`push(5)`**: `freqMap = {5:1}`. Max frequency becomes `1`.  
   `groupMap` updates: `1 -> [5]`.
2. **`push(7)`**: `freqMap = {5:1, 7:1}`. Max frequency stays `1`.  
   `groupMap` updates: `1 -> [5, 7]`.
3. **`push(5)`**: `freqMap = {5:2, 7:1}`. Max frequency becomes `2`.  
   `groupMap` updates: `1 -> [5, 7]`, `2 -> [5]`.

When `pop()` runs:
- We check `maxFreq = 2`. The stack at `groupMap[2]` is `[5]`. 
- We pop `5`. `groupMap[2]` becomes empty, so we decrement `maxFreq` back to `1`. 

The item `5` is safely returned in O(1) time.

---

## Partition Variables

Let:

```java
Map<Integer, Integer> freqMap = new HashMap<>();
Map<Integer, Stack<Integer>> groupMap = new HashMap<>();
int maxFreq = 0;
```

---

### Border Elements

If a particular frequency stack does not exist inside our bucket map, it is initialized dynamically during insertion:

```java
groupMap.computeIfAbsent(f, x -> new Stack<>()).push(val);
```

---

## Correct Partition Condition

If the stack tracking the highest frequency tier becomes empty after an eviction, the active maximum indicator drops down:

```java
if (groupMap.get(maxFreq).isEmpty()) {
    maxFreq--;
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy drops traditional binary range partition splits in favor of a unique double-hashed multi-stack index pipeline to achieve O(1) runtime constraints).*

---

## Java Solution

```java
import java.util.HashMap;
import java.util.Map;
import java.util.Stack;

class FreqStack {

    private final Map<Integer, Integer> freqMap; // Maps elements to their frequencies
    private final Map<Integer, Stack<Integer>> groupMap; // Maps frequencies to a stack of elements
    private int maxFreq; // Tracks the current maximum frequency in the system

    public FreqStack() {
        this.freqMap = new HashMap<>();
        this.groupMap = new HashMap<>();
        this.maxFreq = 0;
    }
    
    public void push(int val) {
        // Step 1: Update the frequency count of the value
        int f = freqMap.getOrDefault(val, 0) + 1;
        freqMap.put(val, f);

        // Step 2: Track the absolute highest frequency tier seen so far
        if (f > maxFreq) {
            maxFreq = f;
        }

        // Step 3: Insert the value into its corresponding frequency stack bucket
        groupMap.computeIfAbsent(f, x -> new Stack<>()).push(val);
    }
    
    public int pop() {
        // Step 1: Grab the most recently added element from the max frequency tier stack
        int val = groupMap.get(maxFreq).pop();

        // Step 2: Decrement the item's frequency count in our global map
        freqMap.put(val, freqMap.get(val) - 1);

        // Step 3: If the highest tier stack becomes empty, decrement the max frequency indicator
        if (groupMap.get(maxFreq).isEmpty()) {
            maxFreq--;
        }

        return val;
    }
}
```

---

## Dry Run

### Input Operations

```java
FreqStack fs = new FreqStack();
fs.push(5);
fs.push(5);
fs.pop();
```

---

### Step Execution Traversal

- **`push(5)`**: `f = 1`. `maxFreq = 1`. `groupMap` state: `{1=[5]}`.
- **`push(5)`**: `f = 2`. `maxFreq = 2`. `groupMap` state: `{1=[5], 2=[5]}`.
- **`pop()`**:
  - `maxFreq` is `2`. Reads `groupMap.get(2)`. 
  - Pops value `5` from the stack.
  - Updates frequency map: `freqMap.put(5, 1)`.
  - Stack at index 2 becomes empty \(\implies\) `maxFreq` decrements to `1`.
  - Returns `5`.

---

### Answer

```java
5
```

---

## Why Is the Performance Time Complexity Constant?

By mapping frequencies directly to individual stack layers, we eliminate the need to run global linear searches or perform tree-balancing steps when fetching the most frequent element. Pushing and popping from a standard `Stack` and fetching items from a `HashMap` both run in average constant time, locking our operations at an optimal O(1) bound.

This multi-stack layout yields:

```text
O(1) time complexity for both push and pop
```

which satisfies the optimal latency constraint cleanly.

---

## Complexity Analysis

### Time Complexity

| Operation | Time Complexity | Reason |
| :--- | :--- | :--- |
| **`push`** | **O(1)** | Constant time hash-map retrievals and standard stack insertions. |
| **`pop`** | **O(1)** | Direct retrieval and removal from the head of the maximum frequency stack layer. |

---

### Space Complexity

```text
O(n)
```

The data structure allocates memory nodes across maps and stacks proportional to the total number of items stored in the system.

---

## Key Insight

Grouping elements into distinct stack layers indexed by their current frequency automatically handles the LIFO tie-breaking condition, reducing a complex priority tracking task into a simple constant-time stack operation.

```text
Time  : O(1) execution runtime bounds
Space : O(n) continuous multi-bucket tracking footprint
```

---

## Similar Problems

1. LRU Cache (146)
2. LFU Cache (460)
3. Top K Frequent Elements (347)
4. Design Search Autocomplete System (642)
5. Min Stack (155)
