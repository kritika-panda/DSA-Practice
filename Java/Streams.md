# Java Streams In Depth

## 1. What Java Streams Are

A Java Stream is a sequence of elements that supports declarative data processing operations such as filtering, mapping, sorting, grouping, and reduction.

Streams were introduced in Java 8 as part of the functional programming additions to the language.

A stream is **not** a data structure. It does not store elements itself. Instead, it provides a pipeline over a source such as:

- a collection
- an array
- a file
- generated values
- numeric ranges

### Simple Idea

Without streams:

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");
List<String> result = new ArrayList<>();

for (String name : names) {
    if (name.length() > 3) {
        result.add(name.toUpperCase());
    }
}

System.out.println(result);
```

With streams:

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");

List<String> result = names.stream()
    .filter(name -> name.length() > 3)
    .map(String::toUpperCase)
    .toList();

System.out.println(result);
```

Streams let you focus on **what** you want instead of **how** to iterate manually.

---

## 2. Why Streams Matter

Streams help with:

- writing concise transformation logic
- expressing intent more clearly
- reducing boilerplate loops
- enabling functional style operations
- making bulk operations easier to parallelize

### Interview Perspective

An interviewer expects you to know:

- what a stream is
- how lazy evaluation works
- the difference between intermediate and terminal operations
- when streams improve readability
- when plain loops are still better
- how collectors work
- when parallel streams are dangerous or useful

---

## 3. Stream vs Collection

Although closely related, a stream is not the same as a collection.

| Collection | Stream |
|---|---|
| stores data | processes data |
| eager by nature | lazy by nature |
| allows repeated traversal | usually single-use |
| focused on data structure | focused on computation pipeline |

### Key Point

A collection contains elements.  
A stream describes operations over elements.

---

## 4. Stream Pipeline

A stream pipeline usually has three parts:

1. **source**
2. **intermediate operations**
3. **terminal operation**

Example:

```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

List<String> result = names.stream()              // source
    .filter(name -> name.length() > 3)            // intermediate
    .map(String::toUpperCase)                     // intermediate
    .toList();                                    // terminal
```

### Important Rule

Intermediate operations do not execute immediately. The pipeline runs only when a terminal operation is invoked.

---

## 5. Characteristics of Streams

Java Streams are:

- **declarative**
- **lazy**
- **composable**
- **single-use**
- **functional in style**

### Single-Use Example

```java
Stream<String> stream = Stream.of("a", "b", "c");
stream.forEach(System.out::println);

// This will throw IllegalStateException
stream.forEach(System.out::println);
```

A stream can be consumed only once.

---

## 6. Creating Streams

There are many ways to create streams.

### 6.1 From a Collection

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
Stream<Integer> stream = numbers.stream();
```

### 6.2 From an Array

```java
String[] arr = {"A", "B", "C"};
Stream<String> stream = Arrays.stream(arr);
```

### 6.3 Using `Stream.of()`

```java
Stream<String> stream = Stream.of("Java", "Python", "Go");
```

### 6.4 Empty Stream

```java
Stream<String> stream = Stream.empty();
```

### 6.5 Numeric Streams

```java
IntStream intStream = IntStream.of(1, 2, 3, 4);
LongStream longStream = LongStream.of(10L, 20L, 30L);
DoubleStream doubleStream = DoubleStream.of(1.5, 2.5, 3.5);
```

### 6.6 Range Streams

```java
IntStream.range(1, 5);      // 1, 2, 3, 4
IntStream.rangeClosed(1, 5); // 1, 2, 3, 4, 5
```

### 6.7 Infinite Streams

```java
Stream<Integer> randoms = Stream.generate(() -> new Random().nextInt(100));

Stream<Integer> evenNumbers = Stream.iterate(0, n -> n + 2);
```

Be careful: infinite streams must usually be bounded with `limit()`.

```java
Stream.iterate(0, n -> n + 2)
    .limit(5)
    .forEach(System.out::println);
```

### 6.8 From Files

```java
try (Stream<String> lines = Files.lines(Path.of("data.txt"))) {
    lines.forEach(System.out::println);
}
```

This must be used carefully because file-backed streams hold I/O resources.

---

## 7. Intermediate vs Terminal Operations

### Intermediate Operations

Intermediate operations return another stream. They are lazy.

Examples:

- `filter`
- `map`
- `flatMap`
- `distinct`
- `sorted`
- `peek`
- `limit`
- `skip`

### Terminal Operations

Terminal operations trigger execution and produce a result or side effect.

Examples:

- `forEach`
- `collect`
- `reduce`
- `count`
- `findFirst`
- `findAny`
- `anyMatch`
- `allMatch`
- `noneMatch`
- `min`
- `max`
- `toArray`

---

## 8. Lazy Evaluation

One of the most important concepts in streams is laziness.

Consider:

```java
Stream<String> stream = Stream.of("Alice", "Bob", "Charlie")
    .filter(name -> {
        System.out.println("Filtering: " + name);
        return name.length() > 3;
    });

System.out.println("Pipeline created");
```

At this point, nothing is filtered yet because no terminal operation has been invoked.

Now:

```java
stream.forEach(System.out::println);
```

Only then does the pipeline execute.

### Why Laziness Matters

- avoids unnecessary work
- enables short-circuiting
- improves composability

---

## 9. `filter`

`filter` keeps only elements matching a predicate.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

List<Integer> evens = numbers.stream()
    .filter(n -> n % 2 == 0)
    .toList();

System.out.println(evens);
```

### Common Use Cases

- selecting active users
- keeping valid records
- excluding null or blank values
- applying business rules

---

## 10. `map`

`map` transforms each element into another value.

```java
List<String> names = Arrays.asList("alice", "bob", "charlie");

List<String> upper = names.stream()
    .map(String::toUpperCase)
    .toList();
```

### Another Example

```java
List<Person> people = List.of(
    new Person("Alice", 30),
    new Person("Bob", 25)
);

List<String> personNames = people.stream()
    .map(Person::getName)
    .toList();
```

`map` is one of the most frequently used stream operations.

---

## 11. `flatMap`

`flatMap` is used when each element produces multiple values and you want to flatten them into one stream.

### Example: List of Lists

```java
List<List<Integer>> nested = List.of(
    List.of(1, 2),
    List.of(3, 4),
    List.of(5, 6)
);

List<Integer> flat = nested.stream()
    .flatMap(List::stream)
    .toList();

System.out.println(flat);
```

### Example: Words from Sentences

```java
List<String> sentences = List.of("java streams", "are powerful");

List<String> words = sentences.stream()
    .flatMap(sentence -> Arrays.stream(sentence.split(" ")))
    .toList();
```

### Mental Model

- `map`: one input -> one output
- `flatMap`: one input -> many outputs, then flatten

---

## 12. `distinct`

`distinct` removes duplicate elements based on `equals()` and `hashCode()`.

```java
List<Integer> numbers = List.of(1, 2, 2, 3, 3, 3, 4);

List<Integer> unique = numbers.stream()
    .distinct()
    .toList();
```

### Important

For custom objects, `distinct()` depends on correct `equals()` and `hashCode()` implementation.

---

## 13. `sorted`

### Natural Ordering

```java
List<Integer> numbers = List.of(5, 2, 8, 1, 3);

List<Integer> sorted = numbers.stream()
    .sorted()
    .toList();
```

### Custom Comparator

```java
List<String> names = List.of("Alice", "Bob", "Charlie");

List<String> sortedByLength = names.stream()
    .sorted(Comparator.comparingInt(String::length))
    .toList();
```

### Reverse Order

```java
List<Integer> descending = numbers.stream()
    .sorted(Comparator.reverseOrder())
    .toList();
```

Sorting is a stateful intermediate operation and may be costly on large streams.

---

## 14. `limit` and `skip`

### `limit`

```java
List<Integer> firstThree = Stream.of(10, 20, 30, 40, 50)
    .limit(3)
    .toList();
```

### `skip`

```java
List<Integer> afterTwo = Stream.of(10, 20, 30, 40, 50)
    .skip(2)
    .toList();
```

### Combined Example

```java
List<Integer> page = Stream.iterate(1, n -> n + 1)
    .skip(10)
    .limit(5)
    .toList();
```

Useful for paging-style logic, though real database pagination should happen at the database layer, not in memory.

---

## 15. `peek`

`peek` is mainly for debugging and inspection.

```java
List<String> result = Stream.of("a", "bb", "ccc")
    .peek(s -> System.out.println("Before filter: " + s))
    .filter(s -> s.length() > 1)
    .peek(s -> System.out.println("After filter: " + s))
    .map(String::toUpperCase)
    .toList();
```

### Important Warning

Do not use `peek` for important business side effects. It is intended for debugging or lightweight observation.

---

## 16. Terminal Operations Overview

Common terminal operations include:

- `collect`
- `reduce`
- `forEach`
- `count`
- `findFirst`
- `findAny`
- `anyMatch`
- `allMatch`
- `noneMatch`
- `min`
- `max`
- `toArray`

Each ends the stream pipeline.

---

## 17. `collect`

`collect` is one of the most powerful terminal operations. It gathers stream elements into a desired result form.

### Collect to List

```java
List<String> names = Stream.of("Alice", "Bob", "Charlie")
    .toList();
```

Or:

```java
List<String> names = Stream.of("Alice", "Bob", "Charlie")
    .collect(Collectors.toList());
```

### Collect to Set

```java
Set<String> set = Stream.of("A", "B", "A")
    .collect(Collectors.toSet());
```

### Collect to Map

```java
Map<Integer, String> map = Stream.of("Alice", "Bob", "Charlie")
    .collect(Collectors.toMap(
        String::length,
        name -> name,
        (existing, replacement) -> existing
    ));
```

The merge function is important if duplicate keys are possible.

---

## 18. Collectors In Depth

The `Collectors` utility class provides predefined collectors.

### 18.1 `toList`, `toSet`, `toCollection`

```java
List<String> list = Stream.of("A", "B", "C")
    .collect(Collectors.toList());

Set<String> set = Stream.of("A", "B", "C")
    .collect(Collectors.toSet());

LinkedList<String> linked = Stream.of("A", "B", "C")
    .collect(Collectors.toCollection(LinkedList::new));
```

### 18.2 `joining`

```java
String joined = Stream.of("Java", "Streams", "Rock")
    .collect(Collectors.joining(" "));

System.out.println(joined);
```

With prefix and suffix:

```java
String result = Stream.of("A", "B", "C")
    .collect(Collectors.joining(", ", "[", "]"));
```

### 18.3 `counting`

```java
long count = Stream.of("A", "B", "C")
    .collect(Collectors.counting());
```

### 18.4 `summingInt`, `averagingInt`, `summarizingInt`

```java
List<Person> people = List.of(
    new Person("Alice", 30),
    new Person("Bob", 25),
    new Person("Charlie", 35)
);

int totalAge = people.stream()
    .collect(Collectors.summingInt(Person::getAge));

double averageAge = people.stream()
    .collect(Collectors.averagingInt(Person::getAge));

IntSummaryStatistics stats = people.stream()
    .collect(Collectors.summarizingInt(Person::getAge));
```

### 18.5 `groupingBy`

```java
Map<Integer, List<String>> groupedByLength = Stream.of("a", "bb", "cc", "ddd")
    .collect(Collectors.groupingBy(String::length));
```

### 18.6 `partitioningBy`

`partitioningBy` is like grouping into exactly two groups based on a boolean predicate.

```java
Map<Boolean, List<Integer>> partitioned = Stream.of(1, 2, 3, 4, 5, 6)
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));
```

### 18.7 `mapping`

```java
Map<Integer, List<String>> groupedUpper = Stream.of("a", "bb", "cc", "ddd")
    .collect(Collectors.groupingBy(
        String::length,
        Collectors.mapping(String::toUpperCase, Collectors.toList())
    ));
```

### 18.8 `counting` Inside `groupingBy`

```java
Map<Integer, Long> counts = Stream.of("a", "bb", "cc", "ddd")
    .collect(Collectors.groupingBy(
        String::length,
        Collectors.counting()
    ));
```

### 18.9 `collectingAndThen`

```java
List<String> unmodifiable = Stream.of("a", "b", "c")
    .collect(Collectors.collectingAndThen(
        Collectors.toList(),
        Collections::unmodifiableList
    ));
```

---

## 19. `reduce`

`reduce` combines stream elements into a single result.

### Sum Example

```java
int sum = Stream.of(1, 2, 3, 4, 5)
    .reduce(0, Integer::sum);
```

### Without Identity

```java
Optional<Integer> sum = Stream.of(1, 2, 3, 4, 5)
    .reduce(Integer::sum);
```

### Product Example

```java
int product = Stream.of(1, 2, 3, 4)
    .reduce(1, (a, b) -> a * b);
```

### When To Use `reduce`

Use `reduce` when you truly want to combine elements into one result.  
Do not use it where `collect` is clearer.

---

## 20. `forEach`

`forEach` consumes the stream and performs an action.

```java
Stream.of("Java", "Streams", "API")
    .forEach(System.out::println);
```

### Important Note

`forEach` is fine for printing or side effects, but if your goal is transformation into a result structure, prefer `collect`.

### Ordered Variant

For parallel streams, there is also:

```java
stream.forEachOrdered(System.out::println);
```

This preserves encounter order but may reduce parallel performance.

---

## 21. Matching Operations

### `anyMatch`

```java
boolean hasEven = Stream.of(1, 3, 5, 6)
    .anyMatch(n -> n % 2 == 0);
```

### `allMatch`

```java
boolean allPositive = Stream.of(1, 2, 3, 4)
    .allMatch(n -> n > 0);
```

### `noneMatch`

```java
boolean noneNegative = Stream.of(1, 2, 3)
    .noneMatch(n -> n < 0);
```

These operations short-circuit when possible.

---

## 22. Finding Operations

### `findFirst`

```java
Optional<String> first = Stream.of("Alice", "Bob", "Charlie")
    .filter(name -> name.length() > 3)
    .findFirst();
```

### `findAny`

```java
Optional<String> any = Stream.of("Alice", "Bob", "Charlie")
    .parallel()
    .filter(name -> name.length() > 3)
    .findAny();
```

### Difference

- `findFirst` respects encounter order
- `findAny` may return any matching element, especially useful in parallel streams

---

## 23. `min` and `max`

```java
Optional<Integer> min = Stream.of(5, 2, 8, 1)
    .min(Integer::compareTo);

Optional<Integer> max = Stream.of(5, 2, 8, 1)
    .max(Integer::compareTo);
```

With custom objects:

```java
Optional<Person> oldest = people.stream()
    .max(Comparator.comparingInt(Person::getAge));
```

---

## 24. Primitive Streams

Java provides specialized streams for primitives:

- `IntStream`
- `LongStream`
- `DoubleStream`

These avoid boxing overhead.

### Example

```java
int sum = IntStream.rangeClosed(1, 5).sum();
double avg = IntStream.rangeClosed(1, 5).average().orElse(0);
```

### Why Primitive Streams Matter

Using `Stream<Integer>` involves boxing and unboxing. Primitive streams are more efficient for numeric workloads.

### Conversion Examples

```java
List<String> values = List.of("1", "2", "3");

int sum = values.stream()
    .mapToInt(Integer::parseInt)
    .sum();
```

```java
List<Integer> numbers = IntStream.range(1, 5)
    .boxed()
    .toList();
```

---

## 25. Optional and Streams

Streams often produce `Optional` as a safe wrapper around possibly missing values.

### Example

```java
Optional<String> longest = Stream.of("A", "BB", "CCC")
    .max(Comparator.comparingInt(String::length));
```

### Handling Optional

```java
String result = longest.orElse("default");
```

```java
longest.ifPresent(System.out::println);
```

### Anti-Pattern

```java
String result = longest.get();
```

Avoid calling `get()` blindly because it throws if empty.

---

## 26. Method References

Method references make stream code more concise when a lambda simply calls an existing method.

### Types of Method References

- `ClassName::staticMethod`
- `objectRef::instanceMethod`
- `ClassName::instanceMethod`
- `ClassName::new`

### Examples

```java
List<String> upper = names.stream()
    .map(String::toUpperCase)
    .toList();
```

```java
List<Person> people = names.stream()
    .map(Person::new)
    .toList();
```

Method references improve readability when they remain obvious.

---

## 27. Common Stream Patterns

### 27.1 Filter Then Map

```java
List<String> emails = users.stream()
    .filter(User::isActive)
    .map(User::getEmail)
    .toList();
```

### 27.2 Group and Count

```java
Map<String, Long> roleCounts = users.stream()
    .collect(Collectors.groupingBy(User::getRole, Collectors.counting()));
```

### 27.3 Flatten Nested Structures

```java
List<OrderItem> items = orders.stream()
    .flatMap(order -> order.getItems().stream())
    .toList();
```

### 27.4 Find Best Match

```java
Optional<Product> cheapest = products.stream()
    .min(Comparator.comparingDouble(Product::getPrice));
```

### 27.5 Build a Map

```java
Map<Long, User> usersById = users.stream()
    .collect(Collectors.toMap(User::getId, user -> user));
```

---

## 28. Stateful vs Stateless Operations

### Stateless Intermediate Operations

Each element can be processed independently.

Examples:

- `filter`
- `map`
- `flatMap`
- `peek`

### Stateful Intermediate Operations

These may need to see more than one element to proceed.

Examples:

- `sorted`
- `distinct`
- `limit`
- `skip`

Stateful operations may affect performance, especially in parallel streams.

---

## 29. Stream Ordering

Some streams have an encounter order, such as lists. Others may not, such as some set-based sources.

### Why It Matters

Operations like `findFirst`, `forEachOrdered`, and `sorted` can behave differently depending on order.

### Example

```java
List<String> values = List.of("A", "B", "C");

values.stream().forEach(System.out::println);
values.parallelStream().forEach(System.out::println);
values.parallelStream().forEachOrdered(System.out::println);
```

The second line may not print in order.

---

## 30. Parallel Streams

Java supports parallel streams for potential parallel execution.

```java
List<Integer> numbers = IntStream.rangeClosed(1, 100)
    .boxed()
    .toList();

int sum = numbers.parallelStream()
    .mapToInt(Integer::intValue)
    .sum();
```

### When Parallel Streams May Help

- large datasets
- CPU-bound work
- operations that are independent
- low shared state

### When Parallel Streams May Hurt

- small collections
- blocking I/O
- shared mutable state
- ordering-sensitive logic
- expensive coordination

### Interview Rule

Do not assume parallel streams are automatically faster. Explain tradeoffs.

---

## 31. Dangers of Shared Mutable State

This is one of the most common stream mistakes.

### Wrong

```java
List<Integer> result = new ArrayList<>();

Stream.of(1, 2, 3, 4, 5)
    .parallel()
    .filter(n -> n % 2 == 0)
    .forEach(result::add);
```

This is unsafe because `ArrayList` is not thread-safe.

### Better

```java
List<Integer> result = Stream.of(1, 2, 3, 4, 5)
    .parallel()
    .filter(n -> n % 2 == 0)
    .toList();
```

### Main Principle

Prefer stateless transformations and immutable results.

---

## 32. Side Effects in Streams

Streams work best when operations are pure and side-effect free.

### Bad Practice

```java
int[] sum = {0};

Stream.of(1, 2, 3, 4)
    .forEach(n -> sum[0] += n);
```

### Better

```java
int sum = Stream.of(1, 2, 3, 4)
    .mapToInt(Integer::intValue)
    .sum();
```

### Why Side Effects Are Bad

- harder to reason about
- unsafe in parallel streams
- less readable
- violates functional style benefits

---

## 33. Common Collector Patterns

### Group People by City

```java
Map<String, List<Person>> byCity = people.stream()
    .collect(Collectors.groupingBy(Person::getCity));
```

### Group and Count

```java
Map<String, Long> byCityCount = people.stream()
    .collect(Collectors.groupingBy(Person::getCity, Collectors.counting()));
```

### Group and Sum

```java
Map<String, Integer> totalSalaryByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.summingInt(Employee::getSalary)
    ));
```

### Group and Map

```java
Map<String, List<String>> namesByCity = people.stream()
    .collect(Collectors.groupingBy(
        Person::getCity,
        Collectors.mapping(Person::getName, Collectors.toList())
    ));
```

These are very common in interviews and production code.

---

## 34. `toMap` Pitfalls

One of the biggest stream interview traps is `Collectors.toMap(...)`.

### Problem Case

```java
Map<Integer, String> map = Stream.of("aa", "bb", "cc")
    .collect(Collectors.toMap(String::length, s -> s));
```

This throws if duplicate keys appear.

### Correct Version

```java
Map<Integer, String> map = Stream.of("aa", "bb", "ccc")
    .collect(Collectors.toMap(
        String::length,
        s -> s,
        (existing, replacement) -> existing
    ));
```

### Interview Tip

Whenever using `toMap`, think about duplicate keys immediately.

---

## 35. `groupingBy` vs `partitioningBy`

### `groupingBy`

Used when keys can be many values.

```java
Map<Integer, List<String>> grouped = Stream.of("a", "bb", "ccc")
    .collect(Collectors.groupingBy(String::length));
```

### `partitioningBy`

Used for true/false split.

```java
Map<Boolean, List<String>> partitioned = Stream.of("a", "bb", "ccc")
    .collect(Collectors.partitioningBy(s -> s.length() > 1));
```

### Rule

- use `partitioningBy` for boolean split
- use `groupingBy` for general classification

---

## 36. `map` vs `flatMap`

This is a common interview question.

### `map`

```java
List<Integer> lengths = Stream.of("a", "bb", "ccc")
    .map(String::length)
    .toList();
```

### `flatMap`

```java
List<String> words = Stream.of("java streams", "are useful")
    .flatMap(s -> Arrays.stream(s.split(" ")))
    .toList();
```

### Summary

- `map` transforms each item into one item
- `flatMap` transforms each item into multiple items and flattens the result

---

## 37. `stream()` vs `parallelStream()`

### `stream()`

- sequential processing
- simpler reasoning
- usually preferred by default

### `parallelStream()`

- attempts parallel execution
- may improve CPU-bound bulk processing
- may hurt readability and predictability

### Interview Position

Use sequential streams by default unless you have a measured reason to parallelize.

---

## 38. Streams and Null Handling

Streams do not handle `null` magically. You still need to be careful.

### Example

```java
List<String> values = Arrays.asList("A", null, "B", null, "C");

List<String> cleaned = values.stream()
    .filter(Objects::nonNull)
    .toList();
```

### Optional With Streams

If a method returns `Optional<T>`, you can convert it into a stream in modern Java.

```java
List<String> results = values.stream()
    .map(MyService::findValue)     // Optional<String>
    .flatMap(Optional::stream)
    .toList();
```

This is elegant and common in advanced codebases.

---

## 39. Custom Objects Example

Let us use a `Person` class in stream examples:

```java
class Person {
    private final String name;
    private final int age;
    private final String city;

    public Person(String name, int age, String city) {
        this.name = name;
        this.age = age;
        this.city = city;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public String getCity() {
        return city;
    }

    @Override
    public String toString() {
        return name + " (" + age + ", " + city + ")";
    }
}
```

### Example Data

```java
List<Person> people = List.of(
    new Person("Alice", 30, "NY"),
    new Person("Bob", 25, "LA"),
    new Person("Charlie", 35, "NY"),
    new Person("David", 28, "LA"),
    new Person("Eve", 40, "SF")
);
```

### Common Operations

#### Names of People Older Than 30

```java
List<String> names = people.stream()
    .filter(p -> p.getAge() > 30)
    .map(Person::getName)
    .toList();
```

#### Group by City

```java
Map<String, List<Person>> byCity = people.stream()
    .collect(Collectors.groupingBy(Person::getCity));
```

#### Average Age

```java
double avg = people.stream()
    .collect(Collectors.averagingInt(Person::getAge));
```

#### Oldest Person

```java
Optional<Person> oldest = people.stream()
    .max(Comparator.comparingInt(Person::getAge));
```

---

## 40. Streams and Immutability

Streams encourage immutable-style thinking.

Instead of mutating a shared list:

```java
List<String> result = new ArrayList<>();
for (String name : names) {
    result.add(name.toUpperCase());
}
```

You can derive a new result:

```java
List<String> result = names.stream()
    .map(String::toUpperCase)
    .toList();
```

This leads to:

- fewer bugs
- better readability
- safer reasoning

---

## 41. Stream Reuse Limitation

Streams cannot be reused because they represent a one-time computation pipeline.

### Wrong

```java
Stream<String> stream = Stream.of("A", "B", "C");

long count = stream.count();
List<String> list = stream.toList();
```

### Correct

```java
Supplier<Stream<String>> streamSupplier = () -> Stream.of("A", "B", "C");

long count = streamSupplier.get().count();
List<String> list = streamSupplier.get().toList();
```

---

## 42. Short-Circuiting

Some operations stop early when enough information is known.

Examples:

- `findFirst`
- `findAny`
- `anyMatch`
- `allMatch`
- `noneMatch`
- `limit`

### Example

```java
boolean found = Stream.of(1, 2, 3, 4, 5)
    .peek(System.out::println)
    .anyMatch(n -> n > 3);
```

This does not need to process all elements once a match is found.

---

## 43. Performance Considerations

Streams are expressive, but not always the fastest option.

### Potential Costs

- object creation
- boxing and unboxing
- lambda overhead
- collector overhead
- poor use of parallel streams

### When Streams Are Great

- transformation pipelines
- filtering and grouping logic
- readability-focused business code
- declarative data processing

### When Loops May Be Better

- hot performance-critical paths
- very simple logic where streams reduce clarity
- complex exception-heavy flows
- stateful algorithms

### Interview Answer

A good engineer does not say "streams are always better."  
A good engineer says "use streams where they improve clarity and maintainability, and measure performance when it matters."

---

## 44. Streams vs Loops

### Loop Style

```java
List<String> result = new ArrayList<>();
for (String s : values) {
    if (s.length() > 3) {
        result.add(s.toUpperCase());
    }
}
```

### Stream Style

```java
List<String> result = values.stream()
    .filter(s -> s.length() > 3)
    .map(String::toUpperCase)
    .toList();
```

### Comparison

| Topic | Loop | Stream |
|---|---|---|
| control flow | explicit | declarative |
| mutation | common | minimized |
| readability for pipelines | often lower | often higher |
| debugging | straightforward | sometimes trickier |
| performance tuning | easier to control | can be more abstract |

Both are useful. Choose based on clarity and need.

---

## 45. Common Interview Questions

### Q1. Difference between `map` and `flatMap`?

- `map` gives one result per input
- `flatMap` gives zero or more results per input, flattened into one stream

### Q2. Difference between intermediate and terminal operations?

- intermediate operations return a stream and are lazy
- terminal operations trigger execution and produce a result or side effect

### Q3. Why are streams lazy?

- to avoid unnecessary work
- to support short-circuiting
- to allow pipeline optimization

### Q4. Can a stream be reused?

No. A stream is single-use.

### Q5. Difference between `findFirst` and `findAny`?

- `findFirst` preserves order
- `findAny` may return any matching element, especially in parallel streams

### Q6. Difference between `collect` and `reduce`?

- `reduce` combines elements into one value
- `collect` accumulates into a mutable result container or structured result

### Q7. What is the issue with `toMap`?

Duplicate keys can cause exceptions unless a merge function is provided.

### Q8. Are parallel streams always faster?

No. They help only in the right workload and can easily hurt performance or correctness.

---

## 46. Common Mistakes

### 1. Reusing a stream

### 2. Forgetting a terminal operation

```java
Stream.of(1, 2, 3).filter(n -> n > 1);
```

Nothing happens here.

### 3. Using side effects inside stream operations

### 4. Using `peek` for business logic

### 5. Ignoring duplicate keys in `toMap`

### 6. Using parallel streams with shared mutable state

### 7. Overusing streams for logic that becomes unreadable

### 8. Calling `Optional.get()` unsafely after `findFirst` or `max`

---

## 47. Advanced Collector Example

Suppose we want:

- group employees by department
- extract names only
- sort names

```java
Map<String, List<String>> result = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.mapping(
            Employee::getName,
            Collectors.collectingAndThen(
                Collectors.toList(),
                list -> list.stream().sorted().toList()
            )
        )
    ));
```

This shows how collectors can be nested powerfully.

---

## 48. `Stream.concat`

You can concatenate two streams.

```java
Stream<String> combined = Stream.concat(
    Stream.of("A", "B"),
    Stream.of("C", "D")
);

List<String> result = combined.toList();
```

Useful, but if many streams must be merged, other patterns may be cleaner.

---

## 49. `iterate`, `generate`, and Infinite Streams

### `iterate`

```java
List<Integer> firstFive = Stream.iterate(1, n -> n + 1)
    .limit(5)
    .toList();
```

### `generate`

```java
List<Double> randoms = Stream.generate(Math::random)
    .limit(3)
    .toList();
```

### Important

These can create infinite streams, so you often need `limit()`.

---

## 50. Real-World Examples

### Example 1: Active User Emails

```java
List<String> emails = users.stream()
    .filter(User::isActive)
    .map(User::getEmail)
    .filter(Objects::nonNull)
    .toList();
```

### Example 2: Top 3 Expensive Products

```java
List<Product> top3 = products.stream()
    .sorted(Comparator.comparingDouble(Product::getPrice).reversed())
    .limit(3)
    .toList();
```

### Example 3: Orders Total by Customer

```java
Map<Long, Double> totalByCustomer = orders.stream()
    .collect(Collectors.groupingBy(
        Order::getCustomerId,
        Collectors.summingDouble(Order::getAmount)
    ));
```

### Example 4: Unique Tags Across Articles

```java
Set<String> tags = articles.stream()
    .flatMap(article -> article.getTags().stream())
    .collect(Collectors.toSet());
```

---

## 51. Best Practices

1. Prefer readability over cleverness.
2. Keep stream pipelines short and meaningful.
3. Avoid side effects.
4. Use method references when they improve clarity.
5. Use primitive streams for numeric performance-sensitive work.
6. Handle `Optional` safely.
7. Be careful with `toMap`.
8. Use parallel streams only with evidence and appropriate workloads.
9. Prefer `collect` for structured accumulation.
10. Switch back to loops when stream code becomes harder to understand.

---

## 52. Revision Sheet

- Streams process data declaratively.
- A stream pipeline has source, intermediate operations, and a terminal operation.
- Intermediate operations are lazy.
- Terminal operations trigger execution.
- `filter` selects.
- `map` transforms.
- `flatMap` transforms and flattens.
- `collect` accumulates.
- `reduce` combines into one result.
- `groupingBy` is for classification.
- `partitioningBy` is for boolean split.
- `toMap` needs duplicate key thinking.
- streams are single-use.
- avoid side effects and shared mutable state.
- parallel streams are not automatically faster.

---

## 53. One Strong Summary

Java Streams provide a declarative and composable way to process data. They are powerful for filtering, mapping, grouping, aggregation, and transformation pipelines. The most important ideas are:

- lazy evaluation
- intermediate vs terminal operations
- collectors
- avoidance of side effects
- understanding when streams improve clarity and when loops are better

In interviews, good answers emphasize correctness, readability, and tradeoffs rather than just syntax.
