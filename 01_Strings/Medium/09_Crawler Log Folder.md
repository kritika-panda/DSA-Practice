# Crawler Log Folder

## Problem Statement

The LeetCode file system keeps a log of all operations performed by a user. The operations are represented as a string array `logs` where `logs[i]` is the operation performed at the `i`-th step.

The file system starts in the **main folder** (root directory). The path options are defined as follows:
* `"../"` : Move to the **parent folder** of the current folder. (If you are already in the main folder, **remain in the same folder**).
* `"./"` : Move to the **same folder** you are currently in.
* `"[dir]/"` : Move to a **child folder** named `[dir]`. (This folder is guaranteed to exist).

Return *the **minimum number of operations** needed to go back to the main folder after all the log operations.*

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the total number of operations inside the logs array)*

---

## Examples

### Example 1

**Input**

```java
logs = ["d1/", "d2/", "../", "d21/", "./"]
```

**Output**

```java
2
```

**Explanation**

1. `"d1/"` \(\implies\) Move to child folder `d1`. Depth = 1.
2. `"d2/"` \(\implies\) Move to child folder `d2`. Depth = 2.
3. `"../"` \(\implies\) Move back to parent folder `d1`. Depth = 1.
4. `"d21/"` \(\implies\) Move to child folder `d21`. Depth = 2.
5. `"./"` \(\implies\) Stay in the same folder `d21`. Depth = 2.
To return to the main root folder, we need 2 step-back operations (`"../"` twice).

---

### Example 2

**Input**

```java
logs = ["d1/", "d2/", "./", "d3/", "../", "d31/"]
```

**Output**

```java
3
```

---

### Example 3

**Input**

```java
logs = ["d1/", "../", "../", "../"]
```

**Output**

```java
0
```

**Explanation**

Moving back from the root folder leaves you safely at the root level (Depth = 0).

---

## Brute Force Approach

Simulate the file directory directory tree stack trace by using a full string stack collection, pushing directory names onto the stack and popping them off on backtracks.

### Steps

1. Initialize a `Stack<String>` to hold directory hierarchy paths.
2. Iterate through each log token in the `logs` array.
3. Apply structural checks:
   - If log equals `"../"`, check if the stack is non-empty, and pop the top element.
   - Else if log equals `"./"`, do nothing.
   - Else, push the directory name string directly onto the stack.
4. The remaining size of the stack at the end represents the minimum depth level. Return `stack.size()`.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // Allocates memory frames for string frames on the stack heap
```

While linear asymptotically, pushing and popping dynamic string objects onto a heap collection adds unneeded memory management overhead. Since we only care about the final scalar depth from the root, we can optimize this down to a **zero-allocation footprint** using an integer counter.

---

# Optimal Approach: Single-Pass Depth Simulation Counter

## Key Idea

Instead of tracking the structural folder titles using string objects, we only need to maintain a single integer variable `depth` representing our current distance away from the root directory.

1. Initialize an integer variable `depth = 0`.
2. Iterate forward through the `logs` array in a single pass.
3. For each log command string:
   - **Parent Navigation Check (`"../"`):** If the command matches `"../"`, we attempt to decrement our depth by 1. We must clamp the calculation to prevent dropping into negative levels: `depth = Math.max(0, depth - 1)`.
   - **Current Directory Check (`"./"`):** If the command matches `"./"`, we stay exactly where we are. Ignore it.
   - **Child Directory Check:** If it matches neither, it represents a step deeper into a child folder directory. Increment `depth` by 1.
4. When the loop runs out of tokens, return the final value of `depth`.

---

## Visual Understanding

Suppose `logs = ["d1/", "d2/", "../", "d21/", "./"]`:

- **Initialization:** `depth = 0`.
- **Log 0 (`"d1/"`):** Normal path token. Increment `depth += 1` \(\implies\) `1`.
- **Log 1 (`"d2/"`):** Normal path token. Increment `depth += 1` \(\implies\) `2`.
- **Log 2 (`"../"`):** Backtrack command. `Math.max(0, 2 - 1) = 1` \(\implies\) `depth = 1`.
- **Log 3 (`"d21/"`):** Normal path token. Increment `depth += 1` \(\implies\) `2`.
- **Log 4 (`"./"`):** Anchor command. Skip alteration steps.

The loop finishes. `depth = 2`, indicating exactly 2 operations are required to clear the tree stack layer.

---

## Partition Variables

Let:

```java
int depth = 0;
```

---

### Border Elements

The backtracking assignment expression clips operations at the zero boundary line to shield the tracker from dropping into negative directory ranges:

```java
if (log.equals("../")) {
    depth = Math.max(0, depth - 1);
}
```

---

## Correct Partition Condition

The log evaluation checks are evaluated within a flat linear loop sweep:

```java
for (String log : logs) {
    if (log.equals("../")) {
        depth = Math.max(0, depth - 1);
    } else if (!log.equals("./")) {
        depth++;
    }
}
return depth;
```

---

## Java Solution

```java
class Solution {

    public int minOperations(String[] logs) {
        if (logs == null || logs.length == 0) {
            return 0;
        }

        int depth = 0;

        // Step 1: Scan each file log operation sequentially
        for (String log : logs) {
            
            // Handle Parent Directory step back
            if (log.equals("../")) {
                depth = Math.max(0, depth - 1); // Clamp floor value safely at 0
            } 
            // Ignore Anchor Directory path updates; increment only on new child directories
            else if (!log.equals("./")) {
                depth++;
            }
        }

        return depth;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Depth Simulation Scan** | **O(n)** | **O(1)** | Traverses the string array exactly once. The execution runs entirely in-place within a single local integer register with absolute zero extra memory allocation. |

---

## Key Insight

Substituting structural stack collections with a simple integer tracking counter filters out redundant layout history, checking state distance boundaries in optimal linear time.

```text
Time  : O(n) single pass forward execution
Space : O(1) absolute constant auxiliary tracking memory
```

---

## Similar Problems

1. Backspace String Compare (844)
2. Valid Parentheses (20)
3. Simplify Path (71)
4. Final Value of Variable After Performing Operations (2011)
5. Defanging an IP Address (1108)
