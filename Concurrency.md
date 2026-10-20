# Java Concurrency In Depth

## 1. What Concurrency Means

Concurrency is the ability of a program to make progress on multiple tasks during overlapping periods of time. In Java, concurrency is primarily achieved with threads, thread pools, asynchronous callbacks, and concurrent data structures.

It is important to separate three related ideas:

- **Concurrency**: multiple tasks are in progress logically at the same time.
- **Parallelism**: multiple tasks are physically executing at the same time on multiple CPU cores.
- **Asynchrony**: work is started and completed later without blocking the caller.

Java supports all three, but interviewers usually focus on:

- thread safety
- race conditions
- visibility
- synchronization
- task execution
- coordination between threads
- performance and correctness tradeoffs

---

## 2. Process vs Thread

- A **process** has its own memory space.
- A **thread** is a lightweight unit of execution inside a process.
- Threads within the same process share heap memory.
- Each thread has its own stack, program counter, and local variables.

Because threads share memory, they can communicate efficiently, but this also introduces data races and synchronization problems.

---

## 3. Creating Threads in Java

### 3.1 Extending `Thread`

```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Running in thread: " + Thread.currentThread().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start();
    }
}
```

### 3.2 Implementing `Runnable`

```java
class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Task executed by: " + Thread.currentThread().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(new MyTask());
        t.start();
    }
}
```

### 3.3 Using Lambda

```java
public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(() ->
            System.out.println("Lambda thread: " + Thread.currentThread().getName())
        );
        t.start();
    }
}
```

### 3.4 Using `Callable`

`Runnable` does not return a result and cannot throw checked exceptions directly. `Callable<T>` solves both problems.

```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newSingleThreadExecutor();

        Callable<Integer> task = () -> 42;
        Future<Integer> future = executor.submit(task);

        System.out.println(future.get());
        executor.shutdown();
    }
}
```

### Interview Note

In modern Java, you usually do **not** create raw threads directly for business logic. You submit tasks to an `ExecutorService` or use `CompletableFuture`.

---

## 4. Thread Lifecycle

Java thread states from `Thread.State`:

- `NEW`
- `RUNNABLE`
- `BLOCKED`
- `WAITING`
- `TIMED_WAITING`
- `TERMINATED`

Example:

```java
Thread t = new Thread(() -> {
    try {
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});

System.out.println(t.getState()); // NEW
t.start();
System.out.println(t.getState()); // RUNNABLE or TIMED_WAITING
```

### Important States

- **BLOCKED**: waiting to acquire a monitor lock for a synchronized block.
- **WAITING**: waiting indefinitely, for example `wait()` or `join()`.
- **TIMED_WAITING**: waiting for a limited time, for example `sleep()` or `join(timeout)`.

---

## 5. Why Concurrency Is Hard

Concurrency bugs are often non-deterministic. The same program may work correctly 100 times and fail on the 101st run.

The most common issues are:

- **Race condition**: outcome depends on timing between threads.
- **Visibility problem**: one thread updates a variable but another thread does not see the latest value.
- **Atomicity problem**: an operation that seems single-step is actually multiple steps.
- **Deadlock**: threads wait forever for each other.
- **Livelock**: threads keep reacting but make no progress.
- **Starvation**: a thread never gets CPU or resources.

---

## 6. Race Conditions and Atomicity

Consider this counter:

```java
class Counter {
    private int count = 0;

    public void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}
```

At first glance, `count++` looks atomic. It is not. It is effectively:

1. read `count`
2. add `1`
3. write result back

If two threads interleave these steps, one increment can be lost.

### Example

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();

        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 100000; i++) {
                counter.increment();
            }
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 100000; i++) {
                counter.increment();
            }
        });

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println(counter.getCount()); // Often less than 200000
    }
}
```

---

## 7. `synchronized`

The `synchronized` keyword provides:

- **mutual exclusion**: only one thread enters the critical section at a time
- **visibility guarantees**: changes made by one thread become visible to others that synchronize on the same monitor

### 7.1 Synchronized Method

```java
class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}
```

For instance methods, the monitor is `this`.

### 7.2 Synchronized Block

```java
class Counter {
    private int count = 0;
    private final Object lock = new Object();

    public void increment() {
        synchronized (lock) {
            count++;
        }
    }

    public int getCount() {
        synchronized (lock) {
            return count;
        }
    }
}
```

### Why Prefer a Private Lock Object

- avoids external code locking on `this`
- gives better encapsulation
- makes lock ownership clearer

### 7.3 Static Synchronized Method

```java
class Example {
    public static synchronized void doWork() {
        System.out.println("Class-level lock");
    }
}
```

This locks on `Example.class`, not on an instance.

---

## 8. Java Memory Model and Visibility

The Java Memory Model defines how threads interact through memory and what values are visible across threads.

Without proper synchronization:

- one thread may cache a variable locally
- another thread may update it
- the first thread may keep reading the stale value

### Example Problem

```java
class FlagExample {
    private boolean running = true;

    public void stop() {
        running = false;
    }

    public void work() {
        while (running) {
            // do work
        }
    }
}
```

This may loop forever because `running` is not guaranteed to be re-read from shared memory.

---

## 9. `volatile`

The `volatile` keyword guarantees:

- **visibility** of writes across threads
- ordering constraints around reads and writes

It does **not** guarantee atomicity for compound operations like `count++`.

### Example

```java
class FlagExample {
    private volatile boolean running = true;

    public void stop() {
        running = false;
    }

    public void work() {
        while (running) {
            // do work
        }
    }
}
```

### Good Use Cases for `volatile`

- shutdown flags
- status flags
- one writer, many readers
- configuration values updated atomically

### Bad Use Cases for `volatile`

- counters with `++`
- compound check-then-act logic
- any multi-step invariant

---

## 10. `AtomicInteger` and Atomic Classes

Java provides atomic classes in `java.util.concurrent.atomic`.

Common ones:

- `AtomicInteger`
- `AtomicLong`
- `AtomicBoolean`
- `AtomicReference`

### Example

```java
import java.util.concurrent.atomic.AtomicInteger;

class Counter {
    private final AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        count.incrementAndGet();
    }

    public int getCount() {
        return count.get();
    }
}
```

### Why It Works

Atomic classes use low-level compare-and-swap style operations to update values without traditional locking in many cases.

### Common Methods

```java
AtomicInteger value = new AtomicInteger(10);

value.incrementAndGet();
value.decrementAndGet();
value.addAndGet(5);
value.compareAndSet(15, 20);
```

### Interview Rule

- use `AtomicInteger` for independent atomic updates
- use `synchronized` or locks when multiple variables must change together

---

## 11. Intrinsic Locks vs Explicit Locks

Java supports both:

- intrinsic locks via `synchronized`
- explicit locks via `Lock` and `ReentrantLock`

### 11.1 `ReentrantLock`

```java
import java.util.concurrent.locks.ReentrantLock;

class Counter {
    private int count = 0;
    private final ReentrantLock lock = new ReentrantLock();

    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }

    public int getCount() {
        lock.lock();
        try {
            return count;
        } finally {
            lock.unlock();
        }
    }
}
```

### Advantages of `ReentrantLock`

- `tryLock()`
- interruptible lock acquisition
- fairness option
- multiple condition variables

### 11.2 `tryLock()`

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

public class Main {
    private final ReentrantLock lock = new ReentrantLock();

    public void doWork() throws InterruptedException {
        if (lock.tryLock(1, TimeUnit.SECONDS)) {
            try {
                System.out.println("Acquired lock");
            } finally {
                lock.unlock();
            }
        } else {
            System.out.println("Could not acquire lock");
        }
    }
}
```

---

## 12. Reentrancy

A lock is **reentrant** if the same thread can acquire it multiple times.

Both `synchronized` and `ReentrantLock` are reentrant.

```java
class Example {
    public synchronized void outer() {
        inner();
    }

    public synchronized void inner() {
        System.out.println("Same thread can re-enter");
    }
}
```

If Java locks were not reentrant, this pattern would deadlock.

---

## 13. `wait()`, `notify()`, `notifyAll()`

These methods are defined on `Object` and must be used only when holding that object's monitor.

### Basic Rules

- call them inside a `synchronized` block
- always use a `while` loop, not `if`
- prefer higher-level concurrency utilities when possible

### Producer Consumer Using `wait()` and `notifyAll()`

```java
import java.util.LinkedList;
import java.util.Queue;

class MessageQueue {
    private final Queue<Integer> queue = new LinkedList<>();
    private final int capacity = 5;

    public synchronized void put(int value) throws InterruptedException {
        while (queue.size() == capacity) {
            wait();
        }
        queue.offer(value);
        notifyAll();
    }

    public synchronized int take() throws InterruptedException {
        while (queue.isEmpty()) {
            wait();
        }
        int value = queue.poll();
        notifyAll();
        return value;
    }
}
```

### Why `while`, Not `if`

- protects against spurious wakeups
- rechecks the condition after waking
- handles competing consumers and producers correctly

---

## 14. Higher-Level Synchronizers

The `java.util.concurrent` package provides safer and clearer abstractions than low-level monitor methods.

### 14.1 `CountDownLatch`

Used when one or more threads wait until a set of operations finishes.

```java
import java.util.concurrent.CountDownLatch;

public class Main {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch latch = new CountDownLatch(3);

        Runnable worker = () -> {
            try {
                System.out.println(Thread.currentThread().getName() + " working");
            } finally {
                latch.countDown();
            }
        };

        new Thread(worker).start();
        new Thread(worker).start();
        new Thread(worker).start();

        latch.await();
        System.out.println("All workers finished");
    }
}
```

### 14.2 `CyclicBarrier`

Used when multiple threads must all reach a point before any continue.

```java
import java.util.concurrent.CyclicBarrier;

public class Main {
    public static void main(String[] args) {
        CyclicBarrier barrier = new CyclicBarrier(3, () ->
            System.out.println("All parties arrived")
        );

        Runnable task = () -> {
            try {
                System.out.println(Thread.currentThread().getName() + " reached barrier");
                barrier.await();
                System.out.println(Thread.currentThread().getName() + " continues");
            } catch (Exception e) {
                Thread.currentThread().interrupt();
            }
        };

        new Thread(task).start();
        new Thread(task).start();
        new Thread(task).start();
    }
}
```

### 14.3 `Semaphore`

Used to limit access to a resource with a fixed number of permits.

```java
import java.util.concurrent.Semaphore;

class ConnectionPool {
    private final Semaphore semaphore = new Semaphore(3);

    public void useConnection() throws InterruptedException {
        semaphore.acquire();
        try {
            System.out.println("Using connection by " + Thread.currentThread().getName());
            Thread.sleep(500);
        } finally {
            semaphore.release();
        }
    }
}
```

### 14.4 `Phaser`

Used for multi-phase coordination where participants can register dynamically.

```java
import java.util.concurrent.Phaser;

public class Main {
    public static void main(String[] args) {
        Phaser phaser = new Phaser(1);

        for (int i = 0; i < 3; i++) {
            phaser.register();
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + " phase 1");
                phaser.arriveAndAwaitAdvance();

                System.out.println(Thread.currentThread().getName() + " phase 2");
                phaser.arriveAndDeregister();
            }).start();
        }

        phaser.arriveAndDeregister();
    }
}
```

---

## 15. `ExecutorService`

Creating too many raw threads is expensive and hard to manage. Thread pools solve this.

### Benefits

- reuse worker threads
- control resource usage
- queue tasks
- separate task submission from execution

### Example

```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newFixedThreadPool(4);

        for (int i = 0; i < 10; i++) {
            int taskId = i;
            executor.submit(() ->
                System.out.println("Task " + taskId + " executed by " + Thread.currentThread().getName())
            );
        }

        executor.shutdown();
        executor.awaitTermination(5, TimeUnit.SECONDS);
    }
}
```

### Common Factory Methods

- `newFixedThreadPool(n)`
- `newCachedThreadPool()`
- `newSingleThreadExecutor()`
- `newScheduledThreadPool(n)`

### Interview Note

The factory methods are convenient, but in production, interviewers like hearing that you may prefer `ThreadPoolExecutor` directly for more explicit control.

---

## 16. `ThreadPoolExecutor`

This is the underlying implementation behind many executors.

Key parameters:

- `corePoolSize`
- `maximumPoolSize`
- `keepAliveTime`
- `workQueue`
- `threadFactory`
- `rejectedExecutionHandler`

### Example

```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2,
            4,
            60,
            TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(10),
            Executors.defaultThreadFactory(),
            new ThreadPoolExecutor.CallerRunsPolicy()
        );

        for (int i = 0; i < 20; i++) {
            int taskId = i;
            executor.execute(() -> {
                System.out.println("Task " + taskId + " by " + Thread.currentThread().getName());
            });
        }

        executor.shutdown();
    }
}
```

### Rejection Policies

- `AbortPolicy`
- `CallerRunsPolicy`
- `DiscardPolicy`
- `DiscardOldestPolicy`

Understanding rejection strategy is a strong interview signal.

---

## 17. `Future`

`Future` represents the result of an asynchronous computation.

```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newFixedThreadPool(2);

        Future<String> future = executor.submit(() -> {
            Thread.sleep(1000);
            return "done";
        });

        System.out.println("Doing other work");
        System.out.println(future.get());

        executor.shutdown();
    }
}
```

### Limitations of `Future`

- difficult to chain
- blocking `get()`
- weak error composition

This is why `CompletableFuture` is preferred for richer async flows.

---

## 18. `CompletableFuture`

`CompletableFuture` enables asynchronous pipelines and non-blocking composition.

### 18.1 Basic Example

```java
import java.util.concurrent.CompletableFuture;

public class Main {
    public static void main(String[] args) {
        CompletableFuture<String> future = CompletableFuture
            .supplyAsync(() -> "hello")
            .thenApply(String::toUpperCase)
            .thenApply(s -> s + " WORLD");

        System.out.println(future.join());
    }
}
```

### 18.2 Combining Futures

```java
import java.util.concurrent.CompletableFuture;

public class Main {
    public static void main(String[] args) {
        CompletableFuture<Integer> a = CompletableFuture.supplyAsync(() -> 10);
        CompletableFuture<Integer> b = CompletableFuture.supplyAsync(() -> 20);

        CompletableFuture<Integer> result = a.thenCombine(b, Integer::sum);
        System.out.println(result.join());
    }
}
```

### 18.3 Exception Handling

```java
import java.util.concurrent.CompletableFuture;

public class Main {
    public static void main(String[] args) {
        CompletableFuture<Integer> future = CompletableFuture
            .supplyAsync(() -> 10 / 0)
            .exceptionally(ex -> {
                System.out.println("Recovered from: " + ex.getMessage());
                return -1;
            });

        System.out.println(future.join());
    }
}
```

### Important Methods

- `supplyAsync()`
- `runAsync()`
- `thenApply()`
- `thenAccept()`
- `thenRun()`
- `thenCompose()`
- `thenCombine()`
- `allOf()`
- `anyOf()`
- `exceptionally()`
- `handle()`

### Interview Tip

Use `CompletableFuture` when describing async service orchestration, fan-out/fan-in calls, and non-blocking pipelines.

---

## 19. Concurrent Collections

The standard collections like `ArrayList` and `HashMap` are not thread-safe.

Java provides concurrent alternatives.

### 19.1 `ConcurrentHashMap`

```java
import java.util.concurrent.ConcurrentHashMap;

public class Main {
    public static void main(String[] args) {
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

        map.put("a", 1);
        map.compute("a", (k, v) -> v == null ? 1 : v + 1);

        System.out.println(map.get("a"));
    }
}
```

#### Why Not Just `Collections.synchronizedMap(...)`

`ConcurrentHashMap` offers better scalability for many concurrent workloads.

### 19.2 `CopyOnWriteArrayList`

Great for many readers and few writers.

```java
import java.util.concurrent.CopyOnWriteArrayList;

public class Main {
    public static void main(String[] args) {
        CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
        list.add("A");
        list.add("B");

        for (String item : list) {
            System.out.println(item);
            list.add("X");
        }
    }
}
```

This works because iteration happens on a snapshot.

### 19.3 `BlockingQueue`

Useful for producer-consumer patterns.

```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) {
        BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(5);

        Thread producer = new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    queue.put(i);
                    System.out.println("Produced " + i);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        Thread consumer = new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    int value = queue.take();
                    System.out.println("Consumed " + value);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        producer.start();
        consumer.start();
    }
}
```

### Common `BlockingQueue` Implementations

- `ArrayBlockingQueue`
- `LinkedBlockingQueue`
- `PriorityBlockingQueue`
- `DelayQueue`
- `SynchronousQueue`

---

## 20. Read Write Locks

If reads are frequent and writes are rare, `ReadWriteLock` may help.

```java
import java.util.concurrent.locks.*;

class Cache {
    private int value = 0;
    private final ReadWriteLock rwLock = new ReentrantReadWriteLock();

    public int read() {
        rwLock.readLock().lock();
        try {
            return value;
        } finally {
            rwLock.readLock().unlock();
        }
    }

    public void write(int newValue) {
        rwLock.writeLock().lock();
        try {
            value = newValue;
        } finally {
            rwLock.writeLock().unlock();
        }
    }
}
```

### Tradeoff

Read-write locks are not always faster. They help only when:

- reads dominate
- read sections are not tiny
- contention is meaningful

---

## 21. `StampedLock`

`StampedLock` can support optimistic reads.

```java
import java.util.concurrent.locks.StampedLock;

class Point {
    private double x;
    private double y;
    private final StampedLock lock = new StampedLock();

    public void move(double deltaX, double deltaY) {
        long stamp = lock.writeLock();
        try {
            x += deltaX;
            y += deltaY;
        } finally {
            lock.unlockWrite(stamp);
        }
    }

    public double distanceFromOrigin() {
        long stamp = lock.tryOptimisticRead();
        double currentX = x;
        double currentY = y;

        if (!lock.validate(stamp)) {
            stamp = lock.readLock();
            try {
                currentX = x;
                currentY = y;
            } finally {
                lock.unlockRead(stamp);
            }
        }

        return Math.sqrt(currentX * currentX + currentY * currentY);
    }
}
```

This is an advanced topic and useful to mention in senior interviews, but not always necessary.

---

## 22. Thread Interruption

Interruption is Java's cooperative cancellation mechanism.

### Example

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread worker = new Thread(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try {
                    Thread.sleep(100);
                    System.out.println("Working...");
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
            System.out.println("Stopped");
        });

        worker.start();
        Thread.sleep(500);
        worker.interrupt();
    }
}
```

### Best Practices

- do not swallow `InterruptedException`
- either propagate it or restore interrupt status
- interruption is a request, not forced termination

### Wrong

```java
catch (InterruptedException e) {
    e.printStackTrace();
}
```

### Better

```java
catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}
```

---

## 23. `ThreadLocal`

`ThreadLocal` gives each thread its own independent copy of a variable.

```java
public class Main {
    private static final ThreadLocal<Integer> localValue =
        ThreadLocal.withInitial(() -> 0);

    public static void main(String[] args) {
        Runnable task = () -> {
            localValue.set(localValue.get() + 1);
            System.out.println(Thread.currentThread().getName() + ": " + localValue.get());
        };

        new Thread(task).start();
        new Thread(task).start();
    }
}
```

### Common Uses

- request-scoped context
- user/session data
- formatters in old code
- transaction context

### Danger

In thread pools, forgetting to clean `ThreadLocal` can cause leaks or stale data.

```java
try {
    localValue.set(123);
    // use it
} finally {
    localValue.remove();
}
```

---

## 24. Deadlock

Deadlock occurs when threads hold locks in a circular wait.

### Example

```java
public class Main {
    private static final Object lock1 = new Object();
    private static final Object lock2 = new Object();

    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            synchronized (lock1) {
                sleep();
                synchronized (lock2) {
                    System.out.println("T1 acquired both locks");
                }
            }
        });

        Thread t2 = new Thread(() -> {
            synchronized (lock2) {
                sleep();
                synchronized (lock1) {
                    System.out.println("T2 acquired both locks");
                }
            }
        });

        t1.start();
        t2.start();
    }

    private static void sleep() {
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

### How To Avoid Deadlock

- always acquire locks in a consistent global order
- avoid nested locking when possible
- use `tryLock()` with timeout
- reduce shared mutable state

---

## 25. Livelock and Starvation

### Livelock

Threads keep changing state in response to each other but make no progress.

Example idea:

- thread A yields to thread B
- thread B yields to thread A
- both remain active but blocked logically

### Starvation

A thread never gets a chance to proceed because others keep consuming resources.

Potential causes:

- unfair locks
- thread priorities
- overloaded executors

---

## 26. Producer Consumer Pattern

This is one of the most important concurrency patterns.

### Recommended Modern Approach

Use `BlockingQueue`.

```java
import java.util.concurrent.*;

class Producer implements Runnable {
    private final BlockingQueue<String> queue;

    Producer(BlockingQueue<String> queue) {
        this.queue = queue;
    }

    @Override
    public void run() {
        try {
            for (int i = 1; i <= 5; i++) {
                String item = "item-" + i;
                queue.put(item);
                System.out.println("Produced " + item);
            }
            queue.put("END");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

class Consumer implements Runnable {
    private final BlockingQueue<String> queue;

    Consumer(BlockingQueue<String> queue) {
        this.queue = queue;
    }

    @Override
    public void run() {
        try {
            while (true) {
                String item = queue.take();
                if ("END".equals(item)) {
                    break;
                }
                System.out.println("Consumed " + item);
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

### Why It Is Better Than Manual `wait()` and `notify()`

- less error-prone
- better readability
- standard implementation
- built-in backpressure

---

## 27. Fork Join Framework

Useful for divide-and-conquer tasks.

```java
import java.util.concurrent.*;

class SumTask extends RecursiveTask<Long> {
    private final int[] arr;
    private final int start;
    private final int end;

    SumTask(int[] arr, int start, int end) {
        this.arr = arr;
        this.start = start;
        this.end = end;
    }

    @Override
    protected Long compute() {
        if (end - start <= 2) {
            long sum = 0;
            for (int i = start; i < end; i++) {
                sum += arr[i];
            }
            return sum;
        }

        int mid = (start + end) / 2;
        SumTask left = new SumTask(arr, start, mid);
        SumTask right = new SumTask(arr, mid, end);

        left.fork();
        long rightResult = right.compute();
        long leftResult = left.join();
        return leftResult + rightResult;
    }
}

public class Main {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5, 6};
        ForkJoinPool pool = new ForkJoinPool();
        long result = pool.invoke(new SumTask(arr, 0, arr.length));
        System.out.println(result);
    }
}
```

### Good Use Cases

- recursive tasks
- CPU-bound parallel work
- divide-and-conquer algorithms

---

## 28. Scheduled Tasks

Use `ScheduledExecutorService` for delayed or periodic execution.

```java
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) {
        ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);

        scheduler.schedule(() -> System.out.println("Runs once after delay"),
            2, TimeUnit.SECONDS);

        scheduler.scheduleAtFixedRate(() -> System.out.println("Periodic task"),
            0, 1, TimeUnit.SECONDS);
    }
}
```

### Difference

- `scheduleAtFixedRate()`: tries to maintain regular timing
- `scheduleWithFixedDelay()`: waits after one execution completes before scheduling the next

---

## 29. Common Interview Comparisons

### `synchronized` vs `volatile`

| Topic | `synchronized` | `volatile` |
|---|---|---|
| Mutual exclusion | Yes | No |
| Visibility | Yes | Yes |
| Atomic compound operations | Yes | No |
| Blocking | Yes | No |
| Use case | critical sections | simple state flags |

### `synchronized` vs `ReentrantLock`

| Topic | `synchronized` | `ReentrantLock` |
|---|---|---|
| Simplicity | simpler | more flexible |
| `tryLock()` | No | Yes |
| Interruptible acquisition | No | Yes |
| Fairness policy | No | Yes |
| Multiple conditions | No | Yes |

### `Runnable` vs `Callable`

| Topic | `Runnable` | `Callable<T>` |
|---|---|---|
| Returns value | No | Yes |
| Throws checked exception | No | Yes |
| Used with | `Thread`, executor | executor |

### `wait()/notify()` vs `BlockingQueue`

| Topic | `wait()/notify()` | `BlockingQueue` |
|---|---|---|
| Abstraction level | low | high |
| Error-prone | high | lower |
| Preferred in interviews | mention for fundamentals | use for practical design |

---

## 30. Safe Publication

An object must be safely published so that other threads see it in a fully initialized state.

Ways to safely publish:

- initialize in a static initializer
- store in a `volatile` field
- publish through a properly locked field
- use final fields correctly
- publish through thread-safe collections

### Example

```java
class Holder {
    private final int value;

    Holder(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }
}
```

Properly constructed objects with final fields have stronger visibility guarantees.

---

## 31. Double Checked Locking

Used for lazy initialization with reduced locking overhead.

```java
class Singleton {
    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

### Important

`instance` must be `volatile`, otherwise instruction reordering can break correctness.

---

## 32. Immutability and Concurrency

One of the best ways to make code thread-safe is to avoid mutation.

Benefits of immutable objects:

- thread-safe by design
- easier reasoning
- no locking needed
- safer sharing between threads

### Example

```java
final class User {
    private final String name;
    private final int age;

    User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

If you can replace shared mutable state with immutable snapshots, concurrency becomes far simpler.

---

## 33. Performance Considerations

Concurrency is not automatically faster.

Sometimes it makes systems slower due to:

- context switching
- synchronization overhead
- cache contention
- false sharing
- too many threads
- blocking I/O

### Practical Rules

- use CPU core count as a guide for CPU-bound tasks
- use higher thread counts only when tasks spend time waiting on I/O
- reduce shared state
- keep critical sections small
- measure before optimizing

---

## 34. Common Mistakes

### 1. Using `volatile` for compound operations

Wrong:

```java
volatile int count = 0;
count++;
```

### 2. Forgetting `finally` when unlocking

Wrong:

```java
lock.lock();
count++;
lock.unlock();
```

Correct:

```java
lock.lock();
try {
    count++;
} finally {
    lock.unlock();
}
```

### 3. Swallowing interruption

### 4. Using non-thread-safe collections from multiple threads

### 5. Over-synchronizing everything

### 6. Holding locks during slow I/O calls

### 7. Forgetting to shut down executors

```java
executor.shutdown();
executor.awaitTermination(5, TimeUnit.SECONDS);
```

---

## 35. Interview-Oriented Example: Thread-Safe Counter Variants

### 35.1 With `synchronized`

```java
class SynchronizedCounter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int get() {
        return count;
    }
}
```

### 35.2 With `AtomicInteger`

```java
import java.util.concurrent.atomic.AtomicInteger;

class AtomicCounter {
    private final AtomicInteger count = new AtomicInteger();

    public void increment() {
        count.incrementAndGet();
    }

    public int get() {
        return count.get();
    }
}
```

### 35.3 With `LongAdder`

`LongAdder` performs better under very high contention for counters that are mostly incremented and occasionally read.

```java
import java.util.concurrent.atomic.LongAdder;

class HighThroughputCounter {
    private final LongAdder adder = new LongAdder();

    public void increment() {
        adder.increment();
    }

    public long get() {
        return adder.sum();
    }
}
```

### Interview Summary

- `synchronized`: simple and correct
- `AtomicInteger`: lock-free atomic operations
- `LongAdder`: better throughput under heavy contention

---

## 36. Interview-Oriented Example: Thread-Safe Cache

```java
import java.util.concurrent.*;

class Cache<K, V> {
    private final ConcurrentHashMap<K, V> map = new ConcurrentHashMap<>();

    public V get(K key, java.util.function.Function<K, V> loader) {
        return map.computeIfAbsent(key, loader);
    }
}
```

### Why This Is Good

- avoids explicit locking
- atomic load-if-absent
- scalable for concurrent access

### But Be Careful

The mapping function should be:

- side-effect free if possible
- not too slow
- not recursively modify the same map in unsafe ways

---

## 37. Testing Concurrent Code

Concurrent code is hard to test because timing changes behavior.

### Tips

- run tests repeatedly
- reduce sleep-based assertions
- use latches/barriers for deterministic coordination
- use stress tests for race conditions
- isolate shared state

### Example Using `CountDownLatch`

```java
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.atomic.AtomicInteger;

public class Main {
    public static void main(String[] args) throws InterruptedException {
        int threadCount = 10;
        CountDownLatch ready = new CountDownLatch(threadCount);
        CountDownLatch start = new CountDownLatch(1);
        CountDownLatch done = new CountDownLatch(threadCount);
        AtomicInteger counter = new AtomicInteger();

        for (int i = 0; i < threadCount; i++) {
            new Thread(() -> {
                ready.countDown();
                try {
                    start.await();
                    counter.incrementAndGet();
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                } finally {
                    done.countDown();
                }
            }).start();
        }

        ready.await();
        start.countDown();
        done.await();

        System.out.println(counter.get());
    }
}
```

This pattern helps all threads start at roughly the same time.

---

## 38. Practical Design Guidance

When designing concurrent Java systems:

1. Prefer immutable data first.
2. Minimize shared mutable state.
3. Use library concurrency utilities before low-level primitives.
4. Choose the right abstraction:
   - `Atomic*` for single-variable updates
   - `ConcurrentHashMap` for concurrent maps
   - `BlockingQueue` for producer-consumer
   - `ExecutorService` for task execution
   - `CompletableFuture` for async pipelines
5. Keep critical sections small.
6. Be explicit about cancellation and timeouts.
7. Avoid blocking inside shared locks.

---

## 39. Recommended Learning Order

If you are studying Java concurrency deeply, this sequence works well:

1. threads and lifecycle
2. race conditions
3. `synchronized`
4. visibility and `volatile`
5. atomic classes
6. locks and read-write locks
7. `wait()` and `notify()`
8. `BlockingQueue`
9. executors and thread pools
10. `Future` and `CompletableFuture`
11. concurrent collections
12. deadlock and performance tuning

---

## 40. Quick Revision Sheet

- `count++` is not atomic.
- `volatile` gives visibility, not compound atomicity.
- `synchronized` gives mutual exclusion and visibility.
- `AtomicInteger` is great for independent counters.
- `ReentrantLock` offers `tryLock()`, fairness, and conditions.
- `BlockingQueue` is ideal for producer-consumer.
- `ExecutorService` is preferred over manual thread creation.
- `CompletableFuture` is preferred for async composition.
- `ConcurrentHashMap` is better than synchronizing a regular `HashMap`.
- always handle interruption correctly.
- avoid deadlock by consistent lock ordering.
- prefer immutability whenever possible.

---

## 41. Final Interview Answer Pattern

If an interviewer asks, "How do you make code thread-safe in Java?", a strong structured answer is:

1. Identify shared mutable state.
2. Decide whether it can be removed or made immutable.
3. If not, choose synchronization strategy:
   - `synchronized`
   - `Lock`
   - atomic classes
   - concurrent collections
4. Ensure visibility and safe publication.
5. Consider contention, scalability, and cancellation.
6. Test under concurrent execution.

---

## 42. One Strong Summary

Java concurrency is about writing code that remains correct when multiple threads operate at the same time. The core ideas are:

- understand shared mutable state
- control access correctly
- guarantee visibility
- choose the right high-level abstraction
- avoid low-level bugs like deadlocks and races

In interviews, correctness comes first, then clarity, then performance tradeoffs.
