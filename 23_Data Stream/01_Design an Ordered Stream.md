# Design an Ordered Stream

## Problem Statement

Implement an ordered stream class that takes a stream of `(idKey, value)` pairs in **arbitrary order**, and returns the values in **increasing order of their IDs** as soon as they become contiguous.

Specifically, the stream should initialize with an internal pointer set to ID `1`. When a new pair is inserted:
* If the incoming ID matches our current pointer, we return a list containing this value along with any **subsequent contiguous values** that were inserted previously but couldn't be output yet because of a missing gap.
* If the incoming ID is greater than the current pointer, it means there is a gap in the stream. We store the value internally and return an **empty list**.

Implement the `OrderedStream` class:
* `OrderedStream(int n)` Initializes the stream to accept `n` values.
* `List<String> insert(int idKey, String value)` Inserts the pair `(idKey, value)` into the stream, updates the internal pointer if applicable, and returns the maximum possible list of contiguous values in increasing order.

The overall runtime complexity per insertion must be:

```text
O(1) amortized
```

---

## Example

### Input

```java
OrderedStream os = new OrderedStream(5);
os.insert(3, "ccccc"); // Returns []
os.insert(1, "aaaaa"); // Returns ["aaaaa"]
os.insert(2, "bbbbb"); // Returns ["bbbbb", "ccccc"]
os.insert(5, "eeeee"); // Returns []
os.insert(4, "ddddd"); // Returns ["ddddd", "eeeee"]
```

### Explanation

```text
Internal Pointer starts at ptr = 1.
1. os.insert(3, "ccccc") -> Stored at index 3. ptr is 1, so 3 > ptr. Gap exists. Returns [].
2. os.insert(1, "aaaaa") -> Stored at index 1. 1 == ptr. Collect "aaaaa". ptr moves to 2. Index 2 is empty. Returns ["aaaaa"].
3. os.insert(2, "bbbbb") -> Stored at index 2. 2 == ptr. Collect "bbbbb". ptr moves to 3. Index 3 has "ccccc", collect it. ptr moves to 4. Index 4 is empty. Returns ["bbbbb", "ccccc"].
4. os.insert(5, "eeeee") -> Stored at index 5. 5 > ptr (4). Gap exists. Returns [].
5. os.insert(4, "ddddd") -> Stored at index 4. 4 == ptr. Collect "ddddd", ptr moves to 5. Index 5 has "eeeee", collect it. ptr moves to 6. Returns ["ddddd", "eeeee"].
```

---

# Intuition

The system behaves exactly like a **streaming network buffer** (such as TCP packet reassembly) where out-of-order chunks must be cached in memory until missing sequence blocks arrive to complete a contiguous data span.

We can model this efficiently using a flat **1D Array** or **String Table lookup array** along with a single **incremental pointer `ptr`**:
1. When a value is inserted at `idKey`, we write it directly into our array at `arr[idKey]`.
2. We then evaluate if `idKey == ptr`.
   - **If False:** A gap exists ahead of us. We return an empty list immediately.
   - **If True:** This chunk fills the active missing gap slot! We initialize an extraction list. We then run a lookahead loop, advancing `ptr` forward step-by-step, collecting elements into our list as long as `arr[ptr] != null`.
3. The moment the lookahead loop hits an unpopulated `null` cell, it means another gap has been struck. The loop breaks, and we return the accumulated list.

---

# State Definition

Let:

```text
String[] streamTable = new String[n + 1]; // 1-indexed string data buffer array
int ptr = 1;                              // Running pointer tracking the next expected ID chunk
```

---

# Recurrence / Pointer Transition Condition

```text
While streamTable[ptr] is not null:
    Add streamTable[ptr] to output chunk list
    Increment ptr by 1
```

---

# Optimal Structural Tabulation Solution

## Idea

By utilizing a direct-address array table, write operations execute in instant O(1) time. Although the lookahead `while` loop inside the `insert` method can scan multiple elements at once when a gap is filled, **each index in the array is visited by the pointer exactly once** across the entire lifecycle of the stream. Thus, the time complexity per insertion scales to an optimized **amortized O(1)** performance boundary.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class OrderedStream {

    // 1-indexed data array buffer to store out-of-order data streams
    private final String[] streamTable;
    // Running pointer marking the absolute next contiguous element expected
    private int ptr;

    public OrderedStream(int n) {
        // Size n + 1 accommodates 1-indexed inputs safely
        this.streamTable = new String[n + 1];
        this.ptr = 1;
    }
    
    public List<String> insert(int idKey, String value) {
        List<String> contiguousChunk = new ArrayList<>();

        // Step 1: Write the incoming value directly into its target direct-address cell
        streamTable[idKey] = value;

        // Step 2: If the inserted ID matches our current pointer, flush out the contiguous segment
        if (idKey == ptr) {
            // Scan forward as long as subsequent elements are already pre-cached in memory
            while (ptr < streamTable.length && streamTable[ptr] != null) {
                contiguousChunk.add(streamTable[ptr]);
                ptr++; // Advance pointer to the next slot
            }
        }

        // Step 3: Return the accumulated segment (will be empty if a gap was struck)
        return contiguousChunk;
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(1) Amortized -> The pointer 'ptr' only moves forward and visits each cell at most once over N calls.
Space Complexity: O(N) -> Utilizes a string table array of size N + 1 to cache streamed payloads.
```

---

# Dry Run

## Input

```java
OrderedStream os = new OrderedStream(5);
```

---

### Initial State

```text
streamTable = [null, null, null, null, null, null]
ptr = 1
```

---

### os.insert(3, "ccccc")

```text
streamTable[3] = "ccccc" -> [null, null, null, "ccccc", null, null]
idKey (3) != ptr (1) -> Loop is skipped.
Returns: []
```

---

### os.insert(1, "aaaaa")

```text
streamTable[1] = "aaaaa" -> [null, "aaaaa", null, "ccccc", null, null]
idKey (1) == ptr (1) -> Enter Flush Loop:
  - Add streamTable[1] ("aaaaa"), ptr becomes 2.
  - streamTable[2] is null -> Loop breaks.
Returns: ["aaaaa"]
```

---

### os.insert(2, "bbbbb")

```text
streamTable[2] = "bbbbb" -> [null, "aaaaa", "bbbbb", "ccccc", null, null]
idKey (2) == ptr (2) -> Enter Flush Loop:
  - Add streamTable[2] ("bbbbb"), ptr becomes 3.
  - streamTable[3] is non-null! Add "ccccc", ptr becomes 4.
  - streamTable[4] is null -> Loop breaks.
Returns: ["bbbbb", "ccccc"]
```

---

# Visualization

## Stream Processing Table

```text
[ptr]
  │
  ▼
┌───┬───┬───┬───┬───┐
│ 1 │ 2 │ 3 │ 4 │ 5 │  <-- ID Tracks
└───┴───┴───┴───┴───┘
  ▲       ▲
  │       │
"aaaaa"  "ccccc" arriving out-of-order get cached until gap is closed
```

---

# Design Pattern

## Identify State

```text
A direct-address lookup sequence combined with a monotonic lookahead pointer.
```

---

## Choices

```text
Write incoming elements to their exact index. Advance pointer to harvest lookahead chains.
```

---

## Key Takeaways

✅ Standard direct-address array system engineering pattern.

✅ Simulates real-world low-level systems architectures like **TCP packet reassembly** buffers or sliding window streaming network frames.

✅ Amortized Analysis ensures that while single calls might trigger a multi-element flush loop, the global runtime profile remains locked in high-speed linear O(N) execution space.

✅ Common Systems Design Pattern:

```text
Contiguous Data Sequencing via Direct-Address Array Buffering
```

### Similar Problems

- Design an Underground System (LeetCode 1396)
- Design A Leaderboard (LeetCode 1244)
- LRU Cache (LeetCode 146)
- Longest Consecutive Sequence (LeetCode 128)
