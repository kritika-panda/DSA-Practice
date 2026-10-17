# Reverse First K Elements of a Queue ([GFG](https://www.geeksforgeeks.org/reversing-first-k-elements-queue/))

## Problem Statement

You are given a **queue** of \(N\) integers and a positive integer \(K\) where \(K \leq N\). Your task is to **reverse the order of the first \(K\) elements** of the queue, leaving the remaining \(N - K\) elements in their **original relative order** at the back.

A queue follows **FIFO** (First In, First Out) ordering — elements are enqueued at the rear and dequeued from the front.

Implement the solution using a **Stack-Assisted Reversal** strategy:

- **`Queue<Integer> reverseFirstK(Queue<Integer> queue, int k)`** — Given an integer queue and an integer \(K\), returns the modified queue with its first \(K\) elements reversed and the remaining tail elements preserved in original order.

---

## Examples

### Example 1

```text
Input:  queue = [1, 2, 3, 4, 5], k = 3
Output: [3, 2, 1, 4, 5]
```

### Example 2

```text
Input:  queue = [10, 20, 30, 40, 50, 60], k = 4
Output: [40, 30, 20, 10, 50, 60]
```

### Explanation

```text
Example 1:
Original queue (front → rear): [1, 2, 3, 4, 5]
Step 1 — Dequeue first k=3 elements into a stack:
  Stack (top → bottom): [3, 2, 1]   Remaining queue: [4, 5]
Step 2 — Pop from stack back into queue:
  Queue after stack drain: [4, 5, 3, 2, 1]
Step 3 — Rotate remaining (N - k) = 2 tail elements to the back:
  Dequeue 4 → enqueue 4: [5, 3, 2, 1, 4]
  Dequeue 5 → enqueue 5: [3, 2, 1, 4, 5]
Final queue: [3, 2, 1, 4, 5] ✅

Example 2:
Original queue (front → rear): [10, 20, 30, 40, 50, 60]
Step 1 — Dequeue first k=4 elements into a stack:
  Stack (top → bottom): [40, 30, 20, 10]   Remaining queue: [50, 60]
Step 2 — Pop from stack back into queue:
  Queue after stack drain: [50, 60, 40, 30, 20, 10]
Step 3 — Rotate remaining (N - k) = 2 tail elements to the back:
  Dequeue 50 → enqueue 50: [60, 40, 30, 20, 10, 50]
  Dequeue 60 → enqueue 60: [40, 30, 20, 10, 50, 60]
Final queue: [40, 30, 20, 10, 50, 60] ✅
```

---

# Intuition

Reversing elements inside a queue using only queue operations (enqueue/dequeue) blindly would cost an impractical \(O(N^2)\) rotation penalty — requiring repeated full-queue sweeps to bubble each element to its target position. To optimize this down to an efficient **linear \(O(N)\) three-phase pipeline**, we can leverage a **Stack-Assisted Reversal**:

1. **The LIFO Inversion Rule:** A stack's Last-In-First-Out (LIFO) discipline is the natural inverse of a queue's FIFO discipline. By dequeuing the first \(K\) elements from the queue front and pushing them sequentially onto a stack, we load the stack so that its **top holds the \(K\)-th element** — which should become the new front after reversal. Popping the stack back into the queue automatically reinserts these \(K\) elements in reversed order.

2. **Three-Phase Pipeline:** The algorithm proceeds in three structurally independent phases:
   - **Phase 1 — Stack Load:** Dequeue the first \(K\) elements from the queue and push each onto the auxiliary stack. After \(K\) dequeue operations, the stack holds the reversed prefix and the queue holds only the intact \((N - K)\) tail.
   - **Phase 2 — Stack Drain:** Pop all \(K\) elements from the stack and enqueue each back into the queue rear. The reversed prefix is now reattached — but sitting at the back, behind the \((N - K)\) tail elements still waiting at the front.
   - **Phase 3 — Tail Rotation:** Rotate the \((N - K)\) tail elements from the front to the back by performing \((N - K)\) dequeue-then-enqueue cycles. This repositions the intact tail behind the reversed prefix, restoring the correct final layout.

3. **Correctness Guarantee:** Each phase operates in a single linear sweep with no element revisited. The three phases together perform exactly \(K + K + (N - K) = N + K\) queue operations and \(K\) stack operations, all constant-time, yielding an overall \(O(N)\) runtime.

By using a standard stack (`Deque<Integer>` acting as a LIFO stack) alongside the original queue structure, every push, pop, enqueue, and dequeue evaluates in \(O(1)\) amortized constant time, keeping the full three-phase pipeline efficiently linear.

---

# State Definition

The three-phase reversal pipeline tracks element positions on the heap using a stack auxiliary structure and direct in-place queue manipulation:

```java
Queue<Integer> queue;   // The original FIFO queue being transformed in-place
Deque<Integer> stack;   // Auxiliary LIFO stack used to invert the first K elements
int n;                  // Total number of elements currently in the queue
int k;                  // Number of front elements to reverse
```

---

# Recurrence / Structural Matching Condition

```text
Function reverseFirstK(queue, k):
    n = queue.size()

    // Phase 1: Load the first K elements onto the auxiliary stack (LIFO inversion buffer)
    For i in range(0, k):
        stack.push(queue.dequeue())

    // Phase 2: Drain the stack back into the queue rear (reversed prefix now at back)
    While stack is not empty:
        queue.enqueue(stack.pop())

    // Phase 3: Rotate the remaining (n - k) tail elements from front to back
    // This repositions the original tail behind the now-reversed prefix
    For i in range(0, n - k):
        queue.enqueue(queue.dequeue())

    Return queue
```

---

# Optimal Stack-Assisted Reversal Solution

## Idea

We load the first \(K\) elements into an auxiliary stack to exploit its LIFO inversion property, then drain the stack back into the queue to reinsert them in reversed order at the rear. Finally, we perform \((N - K)\) dequeue-then-enqueue rotations to cycle the untouched tail elements from the front to the back, completing the structural rearrangement in a single clean three-phase linear pass.

---

## Java Code

```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    public Queue<Integer> reverseFirstK(Queue<Integer> queue, int k) {
        // Edge case: k == 0 or k equals full queue size with single element — no work needed
        if (queue.isEmpty() || k == 0) return queue;

        // Record total size upfront before any structural modifications
        int n = queue.size();

        // Auxiliary LIFO stack to exploit inversion discipline on the first K elements
        Deque<Integer> stack = new ArrayDeque<>();

        // ── Phase 1: Stack Load ──────────────────────────────────────────────────────
        // Dequeue the first K elements from the queue front and push onto the stack.
        // After K dequeues, the stack top holds element K (the future new front),
        // and the queue holds only the intact (N - K) tail elements.
        for (int i = 0; i < k; i++) {
            stack.push(queue.poll());
        }

        // ── Phase 2: Stack Drain ─────────────────────────────────────────────────────
        // Pop all K elements from the stack and enqueue each at the queue rear.
        // LIFO pop order reverses the prefix automatically — element K is enqueued first,
        // element 1 is enqueued last, placing the reversed prefix at the back.
        while (!stack.isEmpty()) {
            queue.offer(stack.pop());
        }

        // ── Phase 3: Tail Rotation ───────────────────────────────────────────────────
        // The queue is currently ordered as: [tail (N-K elements)] [reversed prefix (K elements)]
        // Rotate the (N - K) tail elements from front to back to restore correct order:
        // [reversed prefix (K elements)] [tail (N-K elements)]
        for (int i = 0; i < n - k; i++) {
            queue.offer(queue.poll());
        }

        return queue;
    }
}
```

---

## Complexity Analysis

Let \(N\) be the total number of elements currently held inside the queue, and \(K\) be the count of front elements to reverse where \(K \leq N\).

### Time Complexity

- **Phase 1 — Stack Load:** \(O(K)\) linear time. Performs exactly \(K\) dequeue operations from the queue front and \(K\) push operations onto the auxiliary stack, each in \(O(1)\) amortized constant time.
- **Phase 2 — Stack Drain:** \(O(K)\) linear time. Performs exactly \(K\) stack pop operations and \(K\) enqueue operations to the queue rear, each in \(O(1)\) amortized constant time.
- **Phase 3 — Tail Rotation:** \(O(N - K)\) linear time. Performs exactly \((N - K)\) dequeue-then-enqueue cycles, each pair in \(O(1)\) amortized constant time.
- **Overall:** \(O(K + K + (N - K)) = O(N + K) = O(N)\) linear time. Since \(K \leq N\), the total operation count is bounded above by \(2N\), keeping the full pipeline entirely linear in the queue size.

---

### Space Complexity

- **Auxiliary Space Complexity:** \(O(K)\) memory allocations are required on the heap to store the first \(K\) dequeued elements inside the auxiliary stack during Phase 1. The stack reaches its maximum capacity of \(K\) entries at the end of Phase 1 and is fully drained during Phase 2. No additional heap structures are allocated beyond the stack, and the original queue is modified in-place throughout all three phases.

---

# Key Takeaways

✅ Core fundamental paradigm for queue structural manipulation problems, LIFO-FIFO discipline interplay, and linear in-place data structure transformations.

✅ **The LIFO Inversion Trick:** Recognizing that a stack's pop order is the exact reverse of a queue's dequeue order is the critical insight. By routing elements through an auxiliary stack, we sidestep the need for any index-tracking, element-swapping, or multi-pass reversal logic — the inversion emerges naturally from the stack's structural discipline in a single drain pass.

✅ **Three-Phase Pipeline Discipline:** Decomposing the transformation into three structurally independent linear sweeps (load → drain → rotate) keeps the algorithm clean, provably correct, and easy to reason about. Each phase has a single well-defined responsibility, and no phase revisits elements processed by a prior phase.

✅ Directly drives real-world microarchitectures like **network packet reordering buffers** (reversing the transmission priority of the first \(K\) packets in a FIFO pipeline without disrupting the tail sequence), operating system process scheduler undo stacks (inverting the execution order of the most recently submitted \(K\) job requests), and audio signal processing pipelines reversing the first \(K\) samples of a streaming frame buffer.

### Similar Problems

- Reverse a Stack Using Recursion
- Implement Stack Using Queues ([LeetCode 225](https://leetcode.com))
- Implement Queue Using Stacks ([LeetCode 232](https://leetcode.com))
- Design Circular Queue ([LeetCode 622](https://leetcode.com))
- Sliding Window Maximum ([LeetCode 239](https://leetcode.com))
