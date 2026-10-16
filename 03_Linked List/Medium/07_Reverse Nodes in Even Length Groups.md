# Reverse Nodes in Even Length Groups

## Problem Statement

You are given the head of a linked list.

The nodes in the linked list are sequentially assigned to non-empty groups whose lengths are:

- 1st group → 1 node
- 2nd group → 2 nodes
- 3rd group → 3 nodes
- 4th group → 4 nodes
- and so on...

The length of the last group may be less than or equal to the expected size.

Reverse the nodes of every group having an **even length** and return the head of the modified linked list.

**LeetCode:** 2074. Reverse Nodes in Even Length Groups

---

## Example

### Input

```text
1 -> 1 -> 0 -> 6
```

### Grouping

```text
Group 1: [1]       length = 1 (odd)
Group 2: [1, 0]    length = 2 (even) -> reverse
Group 3: [6]       length = 1 (odd)
```

### Output

```text
1 -> 0 -> 1 -> 6
```

---

## Approach

### Key Observation

We process the linked list group by group.

For each group:

1. Determine the actual number of nodes in the group.
2. If the group length is even:
   - Reverse the nodes of that group.
3. Otherwise:
   - Leave it as it is.
4. Connect the previous group to the current processed group.

---

## Algorithm

1. Start from the head.
2. For group sizes `1, 2, 3, 4...`
3. Count actual nodes available.
4. If count is even:
   - Reverse that segment.
5. Reconnect the reversed segment with the remaining list.
6. Move to the next group.
7. Return head.

---

## Java Solution

```java
class Solution {

    public ListNode reverseEvenLengthGroups(ListNode head) {

        ListNode dummy = new ListNode(0);
        dummy.next = head;

        ListNode prevGroupEnd = dummy;
        ListNode curr = head;

        int groupSize = 1;

        while (curr != null) {

            ListNode groupStart = curr;

            int count = 0;

            while (count < groupSize && curr != null) {
                curr = curr.next;
                count++;
            }

            if (count % 2 == 0) {

                ListNode prev = curr;
                ListNode node = groupStart;

                for (int i = 0; i < count; i++) {
                    ListNode next = node.next;
                    node.next = prev;
                    prev = node;
                    node = next;
                }

                prevGroupEnd.next = prev;
                prevGroupEnd = groupStart;

            } else {

                prevGroupEnd = groupStart;

                for (int i = 1; i < count; i++) {
                    prevGroupEnd = prevGroupEnd.next;
                }
            }

            groupSize++;
        }

        return dummy.next;
    }
}
```

---

## Dry Run

### Input

```text
1 -> 2 -> 3 -> 4 -> 5 -> 6
```

### Groups

```text
Group 1 = [1]
Length = 1 (odd)

Result:
1 -> 2 -> 3 -> 4 -> 5 -> 6
```

```text
Group 2 = [2,3]
Length = 2 (even)

Reverse:
[3,2]
```

Current List:

```text
1 -> 3 -> 2 -> 4 -> 5 -> 6
```

```text
Group 3 = [4,5,6]
Length = 3 (odd)
```

Final:

```text
1 -> 3 -> 2 -> 4 -> 5 -> 6
```

---

## Correctness Proof

Let each group be processed independently.

For every group:

- If the length is odd, nodes remain unchanged.
- If the length is even, we reverse exactly the nodes belonging to that group.
- Reconnection preserves the relative ordering of all other groups.
- Every node belongs to exactly one group and is processed exactly once.

Therefore:

- Every even-length group is reversed.
- Every odd-length group remains unchanged.
- Entire linked list remains properly connected.

Hence the algorithm is correct.

---

## Complexity Analysis

### Time Complexity

```text
O(N)
```

Each node is visited a constant number of times.

### Space Complexity

```text
O(1)
```

Only pointers are used.

---

## Alternative Approach (Array Simulation)

Store all node values in an array:

1. Traverse linked list.
2. Form groups using indices.
3. Reverse values of even-length groups.
4. Write values back to linked list.

### Complexity

```text
Time : O(N)
Space: O(N)
```

Less optimal because it uses extra memory.

---

## Key Interview Points

- Linked List + Group Processing
- Variable-sized blocks
- In-place reversal of sublists
- Careful pointer reconnection
- O(1) space solution preferred
