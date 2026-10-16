# Reverse a Stack using Recursion ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given a stack `st`, your task is to **reverse the stack using recursion**. 

- You are **not allowed to use any loop constructs** (such as `while` or `for`).
- You are **not allowed to use any auxiliary data structures** (such as arrays, queues, or another stack) to hold copies of the data elements.

Modifications must be executed completely in-place utilizing the operational call stack layers.

---

## Examples

### Example 1

```text
Input: st = [1, 2, 3, 4, 5] (where 5 is the top of the stack)
Output: [5, 4, 3, 2, 1] (where 1 is the new top of the stack)
```

### Explanation

```text
The initial arrangement pops 5 first. After reversal mutations, 
the original bottom element 1 bubbles up to become the absolute top element.
```

---

### Example 2

```text
Input: st = [10, 20]
Output: [20, 10]
```

---

# Intuition

Reversing an array or a linked list iteratively is straightforward. However, reversing a stack **exclusively using recursion without loops or extra storage structures** requires a smart two-stage recursive process:

1. **The Deconstruction Pass (`reverse`):** We use a recursive function to break down the stack. In each frame, we pop the top element and hold it implicitly inside the local function context frame memory. We repeat this until the stack becomes completely empty.
2. **The Reconstruction Pass (`insertAtBottom`):** Once the stack is empty, we must push the held elements back into the stack. However, a standard `.push()` operation would just rebuild the original stack layout. To reverse it, we need a **secondary helper recursive function** that bypasses the top elements and drives deep down to insert our target item at the absolute **bottom** of the stack.

By nesting these two recursive routines, the elements are peeled back and re-layered in the opposite order using the operating system's execution call stack as a natural implicit buffer.

---

# State Definition

The solution coordinates two separate recursive state configurations across the runtime call execution stack:
*   `reverse(stack)`: Deconstructs the active stack layer by layer until it hits an empty boundary.
*   `insertAtBottom(stack, element)`: Drives through existing stack layers to insert a target item at the base.

---

# Recurrence / Structural State Invariants

```text
Function reverse(stack):
    If stack is empty -> Return
    int topElement = stack.pop()
    reverse(stack)
    insertAtBottom(stack, topElement)

Function insertAtBottom(stack, element):
    If stack is empty -> stack.push(element); return
    int topElement = stack.pop()
    insertAtBottom(stack, element)
    stack.push(topElement)
```

---

# Optimal Two-Stage Recursive Solution

## Idea

The implementation splits the work between two recursive methods. The master method pops all elements to reach an empty state base case, and then triggers the secondary helper method to push each element to the bottom of the stack as the call stack unwinds.

---

## Java Code

```java
import java.util.Stack;

class Solution {
    // Master function to reverse the stack using call stack deconstruction
    public void reverseStack(Stack<Integer> st) {
        // Base Case: If the stack is empty, the deconstruction path ends
        if (st.isEmpty()) {
            return;
        }

        // Step 1: Pop and hold the active top element inside the local stack frame context
        int topElement = st.pop();

        // Step 2: Recursively break down the remaining elements of the stack
        reverseStack(st);

        // Step 3: As the call stack unwinds backward, insert the held element at the bottom
        insertAtBottom(st, topElement);
    }

    // Secondary helper recursive function to push a target item to the absolute base
    private void insertAtBottom(Stack<Integer> st, int element) {
        // Base Case: If the stack is empty, push the item as the new base anchor
        if (st.isEmpty()) {
            st.push(element);
            return;
        }

        // Step A: Pop and hold the top element to clear the path to the bottom
        int topElement = st.pop();

        // Step B: Recurse deeper to continue driving toward the bottom boundary
        insertAtBottom(st, element);

        // Step C: Repush the original top element back onto the stack above our inserted item
        st.push(topElement);
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N²)
```
*Explanation:* The master function `reverseStack` pops elements exactly N times. For each element popped, it calls `insertAtBottom`, which performs up to N recursive steps to reach the base of the stack. This nested recursive layout results in \(1 + 2 + 3 + \dots + N\) operations, bounding execution time to quadratic O(N²) limits.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Although we do not allocate any explicit auxiliary arrays, hash sets, or secondary stacks, the nested recursive execution context utilizes the system execution call stack. Reversing a stack of size N generates up to N activation records, yielding a linear space footprint.

---

# Key Takeaways

✅ Advanced functional paradigm demonstrating how to manipulate a stack using call stack frames.

✅ **The Double-Unwinding Invariant:** This problem highlights how call stack records can act as temporary memory blocks. By popping an element, recursing, and processing it during the stack unwinding phase, we can completely re-engineer data structures without allocating any new heap objects.

✅ Directly models execution workflows like **compiler expression evaluation engines**, matching undo/redo history rollbacks, low-level execution stack frame parsing, and recursion inversion tools.

✅ Common Architectural Paradigm:

```text
Call-Stack Layering Inversions via Nested Decoupled Subproblem Reductions
```

### Similar Problems

- Sort a Stack using Recursion ([GeeksforGeeks](https://geeksforgeeks.com))
- Reverse a Linked List ([LeetCode 206](https://leetcode.com))
- Valid Parentheses ([LeetCode 20](https://leetcode.com))
- Implement Queue using Stacks ([LeetCode 232](https://leetcode.com))
