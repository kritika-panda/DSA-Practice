# 3829. Design Ride Sharing System

## Problem Statement

A ride sharing system manages ride requests from riders and availability from drivers.

The system should match riders and drivers in the order they arrive (FIFO order).

Implement the `RideSharingSystem` class:

```java
RideSharingSystem()
```

Initializes the system.

```java
void addRider(int riderId)
```

Adds a new rider with the given `riderId`.

```java
void addDriver(int driverId)
```

Adds a new driver with the given `driverId`.

```java
int[] matchDriverWithRider()
```

Matches the earliest available driver with the earliest waiting rider and removes both from the system.

Returns:

```java
[driverId, riderId]
```

if a match is made.

Returns:

```java
[-1, -1]
```

if no match is available.

```java
void cancelRider(int riderId)
```

Cancels the ride request of the rider with the given `riderId` if the rider exists and has not yet been matched.

---

## Example 1

### Input

```text
["RideSharingSystem","addRider","addDriver","addRider",
"matchDriverWithRider","addDriver","cancelRider",
"matchDriverWithRider","matchDriverWithRider"]

[[],[3],[2],[1],[],[5],[3],[],[]]
```

### Output

```text
[null,null,null,null,[2,3],null,null,[5,1],[-1,-1]]
```

### Explanation

```java
RideSharingSystem rideSharingSystem = new RideSharingSystem();

rideSharingSystem.addRider(3);          // rider 3 joins queue
rideSharingSystem.addDriver(2);         // driver 2 available
rideSharingSystem.addRider(1);          // rider 1 joins queue

rideSharingSystem.matchDriverWithRider();
// returns [2, 3]

rideSharingSystem.addDriver(5);

rideSharingSystem.cancelRider(3);
// already matched -> no effect

rideSharingSystem.matchDriverWithRider();
// returns [5, 1]

rideSharingSystem.matchDriverWithRider();
// returns [-1, -1]
```

---

# Intuition

The requirements indicate:

```text
Earliest rider  -> First served
Earliest driver -> First assigned
```

This is a classic FIFO problem.

Therefore:

```text
Riders  -> Queue
Drivers -> Queue
```

For rider cancellation, removing an arbitrary rider from the middle of a queue is expensive.

Instead, use:

```text
HashSet<Integer> cancelled
```

and perform **lazy deletion** during matching.

---

# Data Structures

```java
Queue<Integer> riders;
Queue<Integer> drivers;
Set<Integer> cancelled;
```

### Purpose

```text
riders      -> waiting riders
drivers     -> available drivers
cancelled   -> riders who cancelled request
```

---

# Algorithm

## addRider(riderId)

```text
1. Add rider to rider queue.
```

### Complexity

```text
Time  : O(1)
Space : O(1)
```

---

## addDriver(driverId)

```text
1. Add driver to driver queue.
```

### Complexity

```text
Time  : O(1)
Space : O(1)
```

---

## cancelRider(riderId)

```text
1. Add riderId to cancelled set.
```

### Complexity

```text
Time  : O(1)
Space : O(1)
```

---

## matchDriverWithRider()

```text
1. Remove cancelled riders from front.
2. If either queue is empty:
      return [-1, -1]
3. Remove earliest driver.
4. Remove earliest rider.
5. Return [driverId, riderId].
```

### Complexity

```text
Time  : O(1) Amortized
Space : O(1)
```

---

# Java Solution

```java
import java.util.*;

class RideSharingSystem {

    *rivate Queue<Integer> riders;
    *rivate Queue<Integer> drivers;
   *private Set<Integer> cancelled;

 *  public RideSharingSystem() {
   *    riders = new LinkedList<>();
 *      drivers = new LinkedList<>()*
        cancelled = new HashSet<>*);
    }

    public void addRider*int riderId) {
        riders.offe*(riderId);
    }

    public void *ddDriver(int driverId) {
        d*ivers.offer(driverId);
    }

    *ublic int[] matchDriverWithRider()*{

        while (!riders.isEmpty(* &&
               cancelled.conta*ns(riders.peek())) {
            r*ders.poll();
        }

        if*(drivers.isEmpty() || riders.isEmp*y()) {
            return new int[] { -1, -1 };
        }

        in* driverId = drivers.poll();
      * int riderId = riders.poll();

   *    return new int[] { driverId, r*derId };
    }

    public void ca*celRider(int riderId) {
        ca*celled.add(riderId);
    }
}
```

*--

# Dry Run

## Initial State

`*`text
Drivers Queue : []
Riders Qu*ue  : []
Cancelled     : {}
```

-*-

## addRider(3)

```text
Riders *ueue : [3]
```

---

## addDriver(*)

```text
Drivers Queue : [2]
```*
---

## addRider(1)

```text
Ride*s Queue : [3, 1]
```

---

## matc*DriverWithRider()

Before Match:

*``text
Drivers : [2]
Riders  : [3,*]
```

Match:

```text
Driver 2 ↔ *ider 3
```

After Match:

```text
*rivers : []
Riders  : [1]
```

Ret*rn:

```text
[2,3]
```

---

## ad*Driver(5)

```text
Drivers : [5]
`*`

---

## matchDriverWithRider()
*Before Match:

```text
Drivers : [*]
Riders  : [1]
```

Match:

```te*t
Driver 5 ↔ Rider 1
```

After Ma*ch:

```text
Drivers : []
Riders  * []
```

Return:

```text
[5,1]
``*

---

# Complexity Analysis

| Op*ration | Time Complexity | Space C*mplexity |
|------------|---------*------|------------------|
| addRi*er() | O(1) | O(1) |
| addDriver()*| O(1) | O(1) |
| cancelRider() | *(1) | O(1) |
| matchDriverWithRide*() | O(1) Amortized | O(1) |

---
*# Why O(1) Amortized?

Consider:

*``java
while (!riders.isEmpty() &&*       cancelled.contains(riders.p*ek())) {
    riders.poll();
}
```
*A cancelled rider is:

```text
Add*d once
Removed once
```

Therefore*across all operations:

```text
To*al removals = O(n)
```

Hence:

``*text
Average cost per match operat*on = O(1) amortized
```

---

# Pa*tern Used

```text
Queue + HashSet*+ Lazy Deletion
```

### Queue

``*text
Maintains FIFO matching order*
```

### HashSet

```text
Provide* O(1) cancellation lookup.
```

##* Lazy Deletion

```text
Cancelled riders remain in queue
until they reach the front.
```

This avoids expensive middle-element removals from the queue.
