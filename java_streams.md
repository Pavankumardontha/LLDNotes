# Java Streams — Master From Scratch

## Table of Contents

### Prerequisite
- [Functional Interfaces & Lambda Expressions](#prerequisite-functional-interfaces--lambda-expressions)
  - [What is an Anonymous Class?](#what-is-an-anonymous-class)
  - [What is a Functional Interface?](#what-is-a-functional-interface)
  - [What is a Lambda Expression?](#what-is-a-lambda-expression)
  - [Lambda Syntax — All the Forms](#lambda-syntax--all-the-forms)
  - [Java's Built-in Functional Interfaces](#javas-built-in-functional-interfaces-javautilfunction)
  - [Method References — Even Shorter Than Lambdas](#method-references--even-shorter-than-lambdas)

### Core Concepts
- [Topic 1: What are Streams and How to Create Them](#topic-1-what-are-streams-and-how-to-create-them)
  - [What is a Stream?](#what-is-a-stream)
  - [How to Create a Stream](#how-to-create-a-stream)
  - [The Stream Pipeline](#the-stream-pipeline)
- [Topic 2: Intermediate Operations](#topic-2-intermediate-operations)
  - [filter()](#1-filterpredicate--keep-elements-that-pass-a-test)
  - [map()](#2-mapfunction--transform-each-element-into-something-else)
  - [flatMap()](#3-flatmapfunction--flatten-nested-structures)
  - [sorted()](#4-sorted--sort-elements)
  - [distinct()](#5-distinct--remove-duplicates)
  - [limit()](#6-limitn--take-only-the-first-n-elements)
  - [skip()](#7-skipn--skip-the-first-n-elements)
  - [peek()](#8-peekconsumer--look-at-elements-without-changing-them)
- [Topic 3: Terminal Operations](#topic-3-terminal-operations)
  - [collect()](#1-collectcollector--gather-results-into-a-collection)
  - [forEach()](#2-foreachconsumer--do-something-with-each-element)
  - [count()](#3-count--count-the-elements)
  - [reduce()](#4-reduce--combine-all-elements-into-one-value)
  - [findFirst()](#5-findfirst--get-the-first-element)
  - [findAny()](#6-findany--get-any-matching-element)
  - [anyMatch(), allMatch(), noneMatch()](#7-anymatch-allmatch-nonematch--test-conditions)
  - [min() and max()](#8-min-and-max--find-smallestlargest)
  - [toArray()](#9-toarray--convert-to-an-array)
- [Topic 4: Collectors](#topic-4-collectors)
  - [toList() and toSet()](#1-tolist-and-toset)
  - [toMap()](#2-tomap--collect-into-a-map)
  - [groupingBy()](#3-groupingby--group-elements-by-a-classifier)
  - [partitioningBy()](#4-partitioningby--split-into-two-groups-truefalse)
  - [joining()](#5-joining--concatenate-strings)
  - [counting()](#6-counting--count-elements-as-a-downstream-collector)
  - [summarizingInt()](#7-summarizingint--summarizingdouble--get-statistics)
- [Topic 5: Optional](#topic-5-optional)
  - [Creating an Optional](#creating-an-optional)
  - [ifPresent()](#ifpresentconsumer--do-something-if-value-exists)
  - [orElse() / orElseGet() / orElseThrow()](#orelse--provide-a-default-value)
  - [map() and flatMap()](#map--transform-the-value-inside-optional)
  - [filter()](#filter--keep-value-only-if-it-matches-a-condition)
  - [Optional in Spring Boot](#optional-in-spring-boot--why-it-matters)
- [Topic 6: Primitive Streams](#topic-6-primitive-streams--intstream-longstream-doublestream)
- [Topic 7: Parallel Streams](#topic-7-parallel-streams)
- [Topic 8: Real-World Patterns in Spring Boot](#topic-8-real-world-patterns-in-spring-boot)

---

## Prerequisite: Functional Interfaces & Lambda Expressions

### The Problem — Too Much Code for Simple Things

Imagine you want to sort a list of names by their length. Before Java 8, you had to do this:

```java
List<String> names = Arrays.asList("Pavan", "Jo", "Alexander", "Sam");

Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return Integer.compare(a.length(), b.length());
    }
});

System.out.println(names); // [Jo, Sam, Pavan, Alexander]
```

Look at lines 3-7. You created an **entire anonymous class** just to say "compare by length." That's 5 lines for 1 line of logic. This is what Java developers called **boilerplate** — code you *have* to write but adds no real meaning.

What if you could just write this instead?

```java
Collections.sort(names, (a, b) -> Integer.compare(a.length(), b.length()));
```

One line. Same result. That `(a, b) -> Integer.compare(a.length(), b.length())` is a **lambda expression**. But before understanding lambdas, you need to understand **anonymous classes** and **what kind of interface allows this**.

---

### What is an Anonymous Class?

An **anonymous class** lets you implement an interface **right where you need it**, without creating a separate named class.

#### Start With the Normal Approach

Suppose you have an interface:

```java
interface Greeting {
    void sayHello(String name);
}
```

The normal way — create a separate class that implements it:

```java
class FriendlyGreeting implements Greeting {
    @Override
    public void sayHello(String name) {
        System.out.println("Hello " + name + "!");
    }
}

// Use it
Greeting g = new FriendlyGreeting();
g.sayHello("Pavan"); // Hello Pavan!
```

But what if you need this class **only once**, in this one place? Creating a whole separate class feels wasteful.

#### Anonymous Class — Define and Create in One Place

```java
Greeting g = new Greeting() {            // no class name — created right here
    @Override
    public void sayHello(String name) {
        System.out.println("Hello " + name + "!");
    }
};

g.sayHello("Pavan"); // Hello Pavan!
```

Breaking down the syntax:

```
Greeting g = new Greeting() {
                  ↑              ↑
           the interface     opening brace of
           you're            the class body
           implementing      (provide method implementations here)
};
 ↑
 semicolon — because this whole thing is an assignment statement
```

**Why "anonymous"?** Because the class has **no name**:

```java
// Named class
class FriendlyGreeting implements Greeting { ... }

// Anonymous class — no name, just new Greeting() { ... }
Greeting g = new Greeting() { ... };
```

#### When is This Useful?

Whenever a method asks you to **pass an interface as an argument**, and you only need the implementation once. For example, with `Runnable` (for threads):

```java
// Without anonymous class — need a separate class
class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Task running!");
    }
}
Thread t = new Thread(new MyTask());
t.start();

// With anonymous class — define it inline
Thread t = new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("Task running!");
    }
});
t.start();
```

#### Decoding the Comparator Example

```java
Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return Integer.compare(a.length(), b.length());
    }
});
```

**What does `Collections.sort()` expect?**

```java
public static <T> void sort(List<T> list, Comparator<? super T> comparator)
```

It takes: (1) a list, and (2) a `Comparator` that tells it **how** to compare two elements.

**What is `Comparator<String>`?** An interface with one key method:

```java
public interface Comparator<T> {
    int compare(T o1, T o2);  // return negative, 0, or positive
}
```

The return value tells the sort algorithm how to order elements:

| Return Value | Meaning |
|-------------|---------|
| Negative (e.g. -1) | `a` should come **before** `b` |
| 0 | `a` and `b` are **equal** in order |
| Positive (e.g. 1) | `a` should come **after** `b` |

**Without** anonymous class:

```java
class LengthComparator implements Comparator<String> {
    @Override
    public int compare(String a, String b) {
        return Integer.compare(a.length(), b.length());
    }
}
Collections.sort(names, new LengthComparator());
```

**With** anonymous class (same thing, inline):

```java
Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return Integer.compare(a.length(), b.length());
    }
});
```

**What does `Integer.compare(a.length(), b.length())` do?**

```
"Pavan".length() = 5,  "Jo".length() = 2
Integer.compare(5, 2) → positive → "Pavan" goes AFTER "Jo"

"Jo".length() = 2,  "Sam".length() = 3
Integer.compare(2, 3) → negative → "Jo" goes BEFORE "Sam"

Result: [Jo, Sam, Pavan, Alexander] — sorted shortest to longest
```

#### The Evolution: Named Class → Anonymous Class → Lambda

```java
List<String> names = Arrays.asList("Pavan", "Jo", "Alexander", "Sam");

// 1. Named class (most verbose)
class LengthComparator implements Comparator<String> {
    @Override
    public int compare(String a, String b) {
        return Integer.compare(a.length(), b.length());
    }
}
Collections.sort(names, new LengthComparator());

// 2. Anonymous class (no class name needed)
Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return Integer.compare(a.length(), b.length());
    }
});

// 3. Lambda (shortest — only the logic)
Collections.sort(names, (a, b) -> Integer.compare(a.length(), b.length()));

// 4. Method reference using Comparator helper
Collections.sort(names, Comparator.comparingInt(String::length));

// All four produce: [Jo, Sam, Pavan, Alexander]
```

Lambdas are possible because `Comparator` is a **functional interface** (one abstract method). Java sees the lambda and knows it's the implementation of `compare`. Everything else — `new Comparator<String>()`, `@Override`, `public int compare` — is boilerplate that Java infers on its own.

#### Multi-line Lambdas

When your logic has more than one statement, use **curly braces `{}`** — just like a method body:

```java
// Anonymous class — multi-line compare: sort by length, then alphabetically if same length
Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        if (a.length() != b.length()) {
            return Integer.compare(a.length(), b.length());
        }
        return a.compareTo(b);
    }
});

// Lambda — multi-line (use curly braces + explicit return)
Collections.sort(names, (a, b) -> {
    if (a.length() != b.length()) {
        return Integer.compare(a.length(), b.length());
    }
    return a.compareTo(b);
});
```

**Rule:**
- **Single expression** → no `{}`, no `return` → `(a, b) -> a + b`
- **Multiple statements** → must use `{}`, must write `return` (if it returns something) → `(a, b) -> { ... return ...; }`

---

### What is a Functional Interface?

A **Functional Interface** is an interface that has **exactly one abstract method**. That's it. That's the only rule.

```java
// This IS a functional interface — exactly 1 abstract method
interface Greeting {
    void sayHello(String name);
}
```

```java
// This is NOT a functional interface — 2 abstract methods
interface TwoMethods {
    void methodA();
    void methodB();
}
```

**Why does "exactly one" matter?** Because when an interface has only one method, Java can **figure out which method you're implementing** without you writing the method name. This is what makes lambdas possible.

#### The `@FunctionalInterface` Annotation

Java provides an optional annotation to mark functional interfaces:

```java
@FunctionalInterface
interface Greeting {
    void sayHello(String name);
}
```

This annotation doesn't change behavior — it just tells the **compiler** to verify that this interface truly has only one abstract method. If you accidentally add a second one, the compiler will throw an error. Think of it as a safety net.

#### Can a Functional Interface Have Other Methods?

Yes! It can have:
- **default methods** (methods with a body) — as many as you want
- **static methods** — as many as you want
- **Methods from Object class** (like `toString()`, `equals()`) — these don't count

Only **abstract methods** count toward the "exactly one" rule.

```java
@FunctionalInterface
interface Greeting {
    void sayHello(String name);          // THE one abstract method

    default void sayBye(String name) {   // default method — doesn't count
        System.out.println("Bye " + name);
    }

    static void wave() {                 // static method — doesn't count
        System.out.println("Waving!");
    }
}
```

This is still a valid functional interface because there's only **one abstract method**: `sayHello`.

---

### What is a Lambda Expression?

A lambda expression is a **short way to implement a functional interface** — without writing a full class.

#### The Old Way (Anonymous Class):

```java
Greeting g = new Greeting() {
    @Override
    public void sayHello(String name) {
        System.out.println("Hello " + name);
    }
};
g.sayHello("Pavan"); // Hello Pavan
```

#### The Lambda Way:

```java
Greeting g = (name) -> System.out.println("Hello " + name);
g.sayHello("Pavan"); // Hello Pavan
```

Both do the **exact same thing**. The lambda is just a shorter syntax.

#### How Does Java Know Which Method You're Implementing?

Because `Greeting` is a **functional interface** — it has only ONE abstract method (`sayHello`). So when you write a lambda, Java says: "There's only one method to implement, so this lambda must be the implementation of `sayHello`."

This is why lambdas ONLY work with functional interfaces. If there were 2 abstract methods, Java wouldn't know which one you're implementing.

---

### Lambda Syntax — All the Forms

The general syntax is:

```
(parameters) -> { body }
```

Here are all the variations:

```java
// 1. Full form — multiple statements in body
Greeting g1 = (String name) -> {
    String msg = "Hello " + name;
    System.out.println(msg);
};

// 2. Type inference — Java knows `name` is String from the interface
Greeting g2 = (name) -> {
    String msg = "Hello " + name;
    System.out.println(msg);
};

// 3. Single parameter — parentheses are optional
Greeting g3 = name -> {
    System.out.println("Hello " + name);
};

// 4. Single statement — curly braces are optional
Greeting g4 = name -> System.out.println("Hello " + name);
```

For lambdas that **return** a value:

```java
@FunctionalInterface
interface Calculator {
    int compute(int a, int b);
}

// Full form
Calculator add1 = (a, b) -> { return a + b; };

// Short form — when body is a single expression, `return` and `{}` are dropped
Calculator add2 = (a, b) -> a + b;
```

**Rule:** If you use `{}`, you MUST write `return`. If you skip `{}`, you MUST NOT write `return`.

For lambdas with **no parameters**:

```java
@FunctionalInterface
interface Task {
    void run();
}

Task t = () -> System.out.println("Running!");
t.run(); // Running!
```

Empty parentheses `()` are required when there are no parameters.

---

### Java's Built-in Functional Interfaces (`java.util.function`)

You don't need to create your own functional interfaces most of the time. Java provides ready-made ones in the `java.util.function` package. These are the ones you'll use with Streams **constantly**:

| Interface | Abstract Method | Takes | Returns | Use Case |
|-----------|---------------|-------|---------|----------|
| `Predicate<T>` | `test(T t)` | T | boolean | Filtering — "is this even?", "is age > 18?" |
| `Function<T, R>` | `apply(T t)` | T | R | Transforming — convert String to Integer |
| `Consumer<T>` | `accept(T t)` | T | void | Consuming — print it, save to DB |
| `Supplier<T>` | `get()` | nothing | T | Supplying — create a new object, generate a value |
| `BiFunction<T, U, R>` | `apply(T t, U u)` | T, U | R | Takes 2 inputs, returns 1 output |
| `BiPredicate<T, U>` | `test(T t, U u)` | T, U | boolean | Test condition on 2 inputs |
| `UnaryOperator<T>` | `apply(T t)` | T | T | Special Function where input & output types are same |
| `BinaryOperator<T>` | `apply(T t1, T t2)` | T, T | T | Special BiFunction where all types are same |

#### Understanding the `<T>` in These Interfaces

All these interfaces use **Generics** — the `<T>` is a type placeholder. You fill it in when you use the interface. Take `Predicate` as an example — here's how it's defined inside Java:

```java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
}
```

When you write `Predicate<Integer>`, you replace `T` with `Integer`:

```
Predicate<T>             →    Predicate<Integer>
boolean test(T t)        →    boolean test(Integer t)
             ↑                                ↑
        placeholder                    now it's Integer
```

So `Predicate<Integer>` means: "a predicate that **tests an Integer** and returns true/false." You can put **any type** in the angle brackets:

```java
Predicate<String>  isLong    = s -> s.length() > 5;      // tests a String
Predicate<Double>  isPositive = d -> d > 0;               // tests a Double
Predicate<Student> hasPassed = st -> st.marks >= 40;      // tests a Student
```

The same applies to all the interfaces in the table:

```java
Function<String, Integer>  →  Integer apply(String s)     // T=String, R=Integer
Consumer<String>           →  void accept(String s)       // T=String
Supplier<Double>           →  Double get()                // T=Double
```

**Why `Integer` and not `int`?** Generics only work with **reference types** (class types), not primitives. `int` is a primitive, `Integer` is its wrapper class. Java **auto-boxes** between them:

```java
Predicate<Integer> isEven = n -> n % 2 == 0;
//                           ↑
//         n is Integer, but Java auto-unboxes to int for the % operation

isEven.test(4);
//          ↑
//     4 is int, Java auto-boxes it to Integer to match Predicate<Integer>
```

#### 1. Predicate — "Does this pass the test?"

```java
import java.util.function.Predicate;

Predicate<Integer> isEven = n -> n % 2 == 0;

System.out.println(isEven.test(4));   // true
System.out.println(isEven.test(7));   // false
```

Used in Streams: `.filter(n -> n % 2 == 0)` — filter takes a `Predicate`.

#### 2. Function — "Transform this into something else"

```java
import java.util.function.Function;

Function<String, Integer> toLength = s -> s.length();

System.out.println(toLength.apply("Pavan"));  // 5
System.out.println(toLength.apply("Hi"));     // 2
```

Used in Streams: `.map(s -> s.length())` — map takes a `Function`.

#### 3. Consumer — "Do something with this, return nothing"

```java
import java.util.function.Consumer;

Consumer<String> printer = s -> System.out.println("Value: " + s);

printer.accept("Hello");  // Value: Hello
printer.accept("World");  // Value: World
```

Used in Streams: `.forEach(s -> System.out.println(s))` — forEach takes a `Consumer`.

#### 4. Supplier — "Give me something, I give you nothing"

```java
import java.util.function.Supplier;

Supplier<Double> randomValue = () -> Math.random();

System.out.println(randomValue.get());  // 0.7234... (some random number)
System.out.println(randomValue.get());  // 0.1456... (different each time)
```

Used in Streams and `Optional`: `optional.orElseGet(() -> "default")` — orElseGet takes a `Supplier`.

---

### Method References — Even Shorter Than Lambdas

#### The Core Idea

Look at this lambda:

```java
Consumer<String> printer = s -> System.out.println(s);
```

This lambda receives `s` and **immediately passes it to an existing method** `System.out.println()`. The lambda is just a middleman — it receives the value and hands it off, doing nothing else.

When a lambda **only calls an existing method and passes its parameters directly**, you can replace it with a **method reference** using `::`:

```java
Consumer<String> printer = System.out::println;
```

**When can you use it?** Only when the lambda does **nothing except call one method** with the same parameters:

```java
// CAN replace — lambda just passes s directly to println
s -> System.out.println(s)              ✅  →  System.out::println

// CANNOT replace — lambda does extra work (adds "Hello ")
s -> System.out.println("Hello " + s)   ❌  →  must stay as lambda
```

There are 4 types of method references:

#### Type 1: Static Method Reference — `ClassName::staticMethod`

A **static method** belongs to the class itself. You call it as `ClassName.method()`.

```java
// The static method: Integer.parseInt(String s) — takes a String, returns an int

// Lambda:
Function<String, Integer> parser1 = s -> Integer.parseInt(s);

// Method reference:
Function<String, Integer> parser2 = Integer::parseInt;

System.out.println(parser2.apply("42"));  // 42
```

How does Java match this?

```
Function<String, Integer> has method:  Integer apply(String s)
Integer.parseInt has signature:        int parseInt(String s)

Both take String, return int/Integer. Match!
So: s -> Integer.parseInt(s)  becomes  Integer::parseInt
```

More examples:

```java
// Math.abs(int n) — takes int, returns int
Function<Integer, Integer> abs = Math::abs;
System.out.println(abs.apply(-5));  // 5

// String.valueOf(int n) — takes int, returns String
Function<Integer, String> toString = String::valueOf;
System.out.println(toString.apply(100));  // "100"
```

#### Type 2: Instance Method on a Specific Object — `object::method`

Here, you have a **specific object already created**, and you reference its method.

```java
String greeting = "Hello World";

// Lambda:
Supplier<String> upper1 = () -> greeting.toUpperCase();

// Method reference — on this specific object:
Supplier<String> upper2 = greeting::toUpperCase;

System.out.println(upper2.get());  // HELLO WORLD
```

How does Java match this?

```
Supplier<String> has method:  String get()         — takes nothing, returns String
greeting.toUpperCase():       String toUpperCase()  — takes nothing, returns String

Both take nothing, return String. Match!
So: () -> greeting.toUpperCase()  becomes  greeting::toUpperCase
```

The most common example — `System.out` is a specific object of type `PrintStream`:

```java
// System.out is an object. println is its instance method.
// Lambda:
Consumer<String> printer1 = s -> System.out.println(s);

// Method reference on the specific object System.out:
Consumer<String> printer2 = System.out::println;

printer2.accept("Hello");  // Hello
```

Another example:

```java
List<String> names = new ArrayList<>();

// names is a specific object. add is its instance method.
// Lambda:
Consumer<String> adder1 = name -> names.add(name);

// Method reference:
Consumer<String> adder2 = names::add;

adder2.accept("Pavan");
adder2.accept("Sam");
System.out.println(names);  // [Pavan, Sam]
```

#### Type 3: Instance Method on the Parameter — `ClassName::instanceMethod`

This is the **trickiest** one. The method is called **on the parameter itself**, not on a specific object you already have.

```java
// Lambda:
Function<String, String> upper1 = s -> s.toUpperCase();

// Method reference:
Function<String, String> upper2 = String::toUpperCase;

System.out.println(upper2.apply("hello"));  // HELLO
```

`String::toUpperCase` looks like a static method reference (Type 1) — it uses `ClassName::method`. But `toUpperCase()` is NOT static — it's an instance method. So what's happening?

**The rule:** When you write `ClassName::instanceMethod`, Java interprets it as: "call this method **on the first parameter**."

```
Function<String, String> has method:  String apply(String s)

Java sees String::toUpperCase and thinks:
  "toUpperCase is an instance method of String"
  "So call it on the parameter: s.toUpperCase()"

So: s -> s.toUpperCase()  becomes  String::toUpperCase
```

**How is this different from Type 2?**

```java
// Type 2: method called on a SPECIFIC OBJECT you already have
String greeting = "hello";
Supplier<String> t2 = greeting::toUpperCase;
// means: () -> greeting.toUpperCase()
// greeting is fixed — always "hello"

// Type 3: method called on WHATEVER PARAMETER comes in
Function<String, String> t3 = String::toUpperCase;
// means: s -> s.toUpperCase()
// works on any String passed to it
```

**With two parameters** — the first parameter becomes the object, the rest become arguments:

```java
// String.compareTo(String other) — instance method that takes one argument
// Lambda:
BiFunction<String, String, Integer> comp1 = (a, b) -> a.compareTo(b);

// Method reference — first param (a) is the object, second param (b) is the argument:
BiFunction<String, String, Integer> comp2 = String::compareTo;

System.out.println(comp2.apply("Pavan", "Sam"));  // negative (P comes before S)
```

```
BiFunction<String, String, Integer> has method:  Integer apply(String a, String b)

Java sees String::compareTo and thinks:
  "compareTo is an instance method of String"
  "First parameter (a) is the object, second parameter (b) is the argument"
  "So: a.compareTo(b)"
```

More examples:

```java
// String.startsWith(String prefix) — instance method
BiPredicate<String, String> starts1 = (s, prefix) -> s.startsWith(prefix);
BiPredicate<String, String> starts2 = String::startsWith;

System.out.println(starts2.test("Hello", "He"));  // true
```

#### Type 4: Constructor Reference — `ClassName::new`

Instead of calling a constructor inside a lambda, reference it with `::new`.

```java
// Lambda:
Supplier<ArrayList<String>> maker1 = () -> new ArrayList<>();

// Constructor reference:
Supplier<ArrayList<String>> maker2 = ArrayList::new;

List<String> list = maker2.get();  // creates a new empty ArrayList
```

**With parameters** — Java matches the constructor that fits:

```java
// StringBuilder has a constructor: StringBuilder(String s)
// Lambda:
Function<String, StringBuilder> builder1 = s -> new StringBuilder(s);

// Constructor reference — Java finds the constructor that takes String:
Function<String, StringBuilder> builder2 = StringBuilder::new;

System.out.println(builder2.apply("Hello"));  // Hello
```

A practical example — converting a list of names into StringBuilder objects:

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Jo");

// Lambda:
List<StringBuilder> builders1 = names.stream()
    .map(name -> new StringBuilder(name))
    .collect(Collectors.toList());

// Constructor reference:
List<StringBuilder> builders2 = names.stream()
    .map(StringBuilder::new)
    .collect(Collectors.toList());
```

#### Quick Reference — All 4 Types in a Stream

```java
List<String> names = Arrays.asList("pavan", "sam", "jo");

// TYPE 1: Static method — ClassName::staticMethod
names.stream().map(s -> String.valueOf(s))              // lambda
names.stream().map(String::valueOf)                      // method reference

// TYPE 2: Instance method on specific object — object::method
names.stream().forEach(s -> System.out.println(s))       // lambda
names.stream().forEach(System.out::println)              // method reference

// TYPE 3: Instance method on the parameter — ClassName::instanceMethod
names.stream().map(s -> s.toUpperCase())                 // lambda
names.stream().map(String::toUpperCase)                  // method reference

// TYPE 4: Constructor — ClassName::new
names.stream().map(s -> new StringBuilder(s))            // lambda
names.stream().map(StringBuilder::new)                   // method reference
```

You'll see method references everywhere in Spring Boot — `UserService::findAll`, `User::getName`, `UserDTO::new`, etc.

---

## Topic 1: What are Streams and How to Create Them

### What is a Stream?

A **Stream** is a sequence of elements from a source that supports **pipeline operations** to process data. Introduced in **Java 8** (same version as lambdas — they go hand in hand).

A Stream is **NOT** a data structure. It does not store data. It takes data from a source (like a List, array, or file), processes it, and gives you a result.

| Aspect | Collection (List, Set) | Stream |
|--------|----------------------|--------|
| Purpose | **Store** data | **Process** data |
| Stores elements? | Yes, in memory | **No** — just passes them through |
| Reusable? | Yes, use many times | **No** — a stream can be consumed only ONCE |
| Iteration | You control it (for-each loop) | Stream controls it internally |
| Lazy? | No — all elements exist | **Yes** — computes only when needed |
| Can be infinite? | No — memory limit | **Yes** — e.g. stream of random numbers |

---

### How to Create a Stream

#### 1. From a Collection (most common — used 90% of the time in Spring Boot)

Every Collection (List, Set, Queue) has a `.stream()` method:

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Jo");
Stream<String> nameStream = names.stream();
```

#### 2. From an Array

```java
String[] arr = {"Pavan", "Sam", "Jo"};

// Option 1: Arrays.stream()
Stream<String> s1 = Arrays.stream(arr);

// Option 2: Stream.of()
Stream<String> s2 = Stream.of("Pavan", "Sam", "Jo");
```

#### 3. From individual values using `Stream.of()`

```java
Stream<Integer> numbers = Stream.of(1, 2, 3, 4, 5);
```

#### 4. Empty Stream

```java
Stream<String> empty = Stream.empty();
```

Useful when a method needs to return a stream but has no data.

#### 5. Infinite Streams — `Stream.generate()` and `Stream.iterate()`

```java
// generate() — calls the Supplier repeatedly
Stream<Double> randoms = Stream.generate(Math::random);
// produces: 0.72, 0.34, 0.91, 0.15, ... (never ends!)

// iterate() — starts with a seed, applies a function to get the next value
Stream<Integer> evens = Stream.iterate(0, n -> n + 2);
// produces: 0, 2, 4, 6, 8, 10, ... (never ends!)
```

These are **infinite** — they never stop. You **must** use `.limit()` to cap them:

```java
Stream.iterate(0, n -> n + 2)
      .limit(5)
      .forEach(System.out::println);
// Output: 0, 2, 4, 6, 8
```

#### 6. From a String's characters

```java
"hello".chars()                        // IntStream of char codes
       .mapToObj(c -> (char) c)        // convert to Character stream
       .forEach(System.out::println);  // h, e, l, l, o
```

---

### The Stream Pipeline

Every stream operation follows this structure:

```
Source  →  Intermediate Operations  →  Terminal Operation
  ↑              ↑                           ↑
 where         what to do               when to do it
 data          (filter, map, sort)       (collect, print, count)
 comes from    (can chain many)          (exactly ONE, triggers execution)
```

A concrete example:

```java
List<String> names = Arrays.asList("Pavan", "Jo", "Alexander", "Sam", "Al");

List<String> result = names.stream()          // 1. SOURCE
    .filter(name -> name.length() > 2)        // 2. INTERMEDIATE — keep names longer than 2
    .map(String::toUpperCase)                 // 3. INTERMEDIATE — convert to uppercase
    .sorted()                                 // 4. INTERMEDIATE — sort alphabetically
    .collect(Collectors.toList());            // 5. TERMINAL — collect into a new list

System.out.println(result); // [ALEXANDER, PAVAN, SAM]
```

**Three rules about the pipeline:**

**Rule 1: Intermediate operations are LAZY**

They do **nothing** by themselves. They just set up the pipeline. No data flows until a terminal operation is called.

```java
Stream<String> stream = names.stream()
    .filter(name -> {
        System.out.println("Filtering: " + name);
        return name.length() > 2;
    });

// At this point, NOTHING has been printed!
// The filter hasn't executed yet. It's just a plan.

stream.collect(Collectors.toList());
// NOW it executes — you'll see the print statements
```

**Rule 2: Terminal operation triggers execution**

The terminal operation is the "go" button. Without it, the stream does nothing.

```java
// This does NOTHING — no terminal operation
names.stream()
     .filter(name -> name.length() > 2)
     .map(String::toUpperCase);
// Data never flows. No result produced. Nothing happens.
```

**Rule 3: A stream can be consumed only ONCE**

```java
Stream<String> stream = names.stream();
stream.forEach(System.out::println);  // works fine

stream.forEach(System.out::println);  // CRASH! IllegalStateException
// "stream has already been operated upon or closed"
```

If you need to process the data again, create a new stream from the source: `names.stream()`.

---

## Topic 2: Intermediate Operations

Intermediate operations are the **middle steps** of the pipeline. They transform the stream and return a **new stream**, so you can chain them. They are **lazy** — nothing happens until a terminal operation is called.

---

### 1. `filter(Predicate)` — Keep elements that pass a test

Takes a `Predicate` (returns true/false). Only elements where the predicate returns `true` survive.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

List<Integer> evens = numbers.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());

System.out.println(evens);  // [2, 4, 6, 8, 10]
```

Think of it like a **gatekeeper** — checks each element and either lets it pass or blocks it:

```
1 → filter(n % 2 == 0) → ❌ blocked
2 → filter(n % 2 == 0) → ✅ passes through
3 → filter(n % 2 == 0) → ❌ blocked
4 → filter(n % 2 == 0) → ✅ passes through
```

You can chain multiple filters:

```java
// Even AND greater than 4
List<Integer> result = numbers.stream()
    .filter(n -> n % 2 == 0)
    .filter(n -> n > 4)
    .collect(Collectors.toList());

System.out.println(result);  // [6, 8, 10]

// Same thing with a single filter using &&
List<Integer> result2 = numbers.stream()
    .filter(n -> n % 2 == 0 && n > 4)
    .collect(Collectors.toList());
```

---

### 2. `map(Function)` — Transform each element into something else

Takes a `Function` — receives one thing, returns another. The stream changes from a stream of X to a stream of Y.

```java
List<String> names = Arrays.asList("pavan", "sam", "jo");

List<String> upper = names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());

System.out.println(upper);  // [PAVAN, SAM, JO]
```

```
"pavan" → map(toUpperCase) → "PAVAN"
"sam"   → map(toUpperCase) → "SAM"
"jo"    → map(toUpperCase) → "JO"
```

The input and output types **don't have to be the same**:

```java
// String → Integer (name to its length)
List<Integer> lengths = names.stream()
    .map(String::length)
    .collect(Collectors.toList());

System.out.println(lengths);  // [5, 3, 2]
```

With a custom object:

```java
List<String> studentNames = students.stream()
    .map(Student::getName)
    .collect(Collectors.toList());
```

---

### 3. `flatMap(Function)` — Flatten nested structures

This is `map` + `flatten`. Used when each element maps to **multiple elements** (a collection or stream), and you want them all in a single flat stream.

**The problem `flatMap` solves:**

```java
List<List<Integer>> nested = Arrays.asList(
    Arrays.asList(1, 2, 3),
    Arrays.asList(4, 5),
    Arrays.asList(6, 7, 8, 9)
);

// map would give: Stream<List<Integer>> — still nested, not what we want!

// flatMap flattens it into a single Stream<Integer>
List<Integer> flat = nested.stream()
    .flatMap(list -> list.stream())
    .collect(Collectors.toList());

System.out.println(flat);  // [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

Visualizing the difference:

```
map:     [1,2,3]   → [1,2,3]         result: [[1,2,3], [4,5], [6,7,8,9]]
         [4,5]     → [4,5]                    (still nested)
         [6,7,8,9] → [6,7,8,9]

flatMap: [1,2,3]   → 1, 2, 3         result: [1, 2, 3, 4, 5, 6, 7, 8, 9]
         [4,5]     → 4, 5                     (flattened into one stream)
         [6,7,8,9] → 6, 7, 8, 9
```

A practical example — splitting sentences into words:

```java
List<String> sentences = Arrays.asList("Hello World", "Java Streams", "Are Fun");

List<String> words = sentences.stream()
    .flatMap(sentence -> Arrays.stream(sentence.split(" ")))
    .collect(Collectors.toList());

System.out.println(words);  // [Hello, World, Java, Streams, Are, Fun]
```

```
"Hello World"  → split → ["Hello", "World"]  → flatMap → Hello, World
"Java Streams" → split → ["Java", "Streams"] → flatMap → Java, Streams
"Are Fun"      → split → ["Are", "Fun"]      → flatMap → Are, Fun

Final: [Hello, World, Java, Streams, Are, Fun]
```

**Rule of thumb:** Use `map` when each element becomes **one** element. Use `flatMap` when each element becomes **multiple** elements.

---

### 4. `sorted()` — Sort elements

Without arguments — sorts by natural order (alphabetical for Strings, ascending for numbers):

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Jo", "Alex");

List<String> sorted = names.stream()
    .sorted()
    .collect(Collectors.toList());

System.out.println(sorted);  // [Alex, Jo, Pavan, Sam]
```

With a `Comparator` — custom sort order:

```java
// Sort by string length
List<String> sortedByLength = names.stream()
    .sorted(Comparator.comparingInt(String::length))
    .collect(Collectors.toList());

System.out.println(sortedByLength);  // [Jo, Sam, Alex, Pavan]

// Reverse order
List<String> reversed = names.stream()
    .sorted(Comparator.reverseOrder())
    .collect(Collectors.toList());

System.out.println(reversed);  // [Sam, Pavan, Jo, Alex]
```

---

### 5. `distinct()` — Remove duplicates

Removes duplicate elements (uses `.equals()` to check):

```java
List<Integer> numbers = Arrays.asList(1, 2, 2, 3, 3, 3, 4, 4, 5);

List<Integer> unique = numbers.stream()
    .distinct()
    .collect(Collectors.toList());

System.out.println(unique);  // [1, 2, 3, 4, 5]
```

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Pavan", "Jo", "Sam");

List<String> unique = names.stream()
    .distinct()
    .collect(Collectors.toList());

System.out.println(unique);  // [Pavan, Sam, Jo]
```

---

### 6. `limit(n)` — Take only the first n elements

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

List<Integer> firstThree = numbers.stream()
    .limit(3)
    .collect(Collectors.toList());

System.out.println(firstThree);  // [1, 2, 3]
```

Essential for infinite streams:

```java
List<Integer> fiveEvens = Stream.iterate(0, n -> n + 2)
    .limit(5)
    .collect(Collectors.toList());

System.out.println(fiveEvens);  // [0, 2, 4, 6, 8]
```

---

### 7. `skip(n)` — Skip the first n elements

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

List<Integer> afterSkip = numbers.stream()
    .skip(3)
    .collect(Collectors.toList());

System.out.println(afterSkip);  // [4, 5, 6, 7, 8, 9, 10]
```

`skip` + `limit` together for **pagination**:

```java
int pageSize = 3;
int pageNumber = 2;  // 0-indexed

// Page 0: [1, 2, 3]
// Page 1: [4, 5, 6]
// Page 2: [7, 8, 9]  ← we want this
List<Integer> page = numbers.stream()
    .skip(pageNumber * pageSize)
    .limit(pageSize)
    .collect(Collectors.toList());

System.out.println(page);  // [7, 8, 9]
```

---

### 8. `peek(Consumer)` — Look at elements without changing them

Takes a `Consumer` — does something with each element (like printing) but passes the element through unchanged. Mainly used for **debugging**.

```java
List<String> result = Arrays.asList("pavan", "sam", "jo").stream()
    .peek(name -> System.out.println("Before filter: " + name))
    .filter(name -> name.length() > 2)
    .peek(name -> System.out.println("After filter: " + name))
    .map(String::toUpperCase)
    .peek(name -> System.out.println("After map: " + name))
    .collect(Collectors.toList());
```

Output:

```
Before filter: pavan
After filter: pavan
After map: PAVAN
Before filter: sam
After filter: sam
After map: SAM
Before filter: jo
```

Notice `"jo"` never reaches "After filter" — it was blocked. Also notice elements flow **one at a time through all stages**, not one stage at a time for all elements.

---

### Chaining — The Real Power

```java
List<String> names = Arrays.asList(
    "Pavan", "Sam", "Jo", "Alexander", "Sam", "Al", "Pavan"
);

List<String> result = names.stream()
    .filter(name -> name.length() > 2)    // remove short names
    .distinct()                            // remove duplicates
    .map(String::toUpperCase)             // convert to uppercase
    .sorted()                              // sort alphabetically
    .limit(3)                              // take first 3
    .collect(Collectors.toList());

System.out.println(result);  // [ALEXANDER, PAVAN, SAM]
```

Step by step:

```
Source:    [Pavan, Sam, Jo, Alexander, Sam, Al, Pavan]
filter:   [Pavan, Sam, Alexander, Sam, Pavan]          — Jo, Al removed (length ≤ 2)
distinct: [Pavan, Sam, Alexander]                       — duplicate Sam, Pavan removed
map:      [PAVAN, SAM, ALEXANDER]                       — uppercased
sorted:   [ALEXANDER, PAVAN, SAM]                       — alphabetical
limit(3): [ALEXANDER, PAVAN, SAM]                       — take first 3
```

---

## Topic 3: Terminal Operations

Terminal operations are the **final step** of the pipeline. They trigger execution of the entire pipeline and produce a **result** (a value, a collection, or a side effect). After a terminal operation, the stream is **consumed** and cannot be used again.

---

### 1. `collect(Collector)` — Gather results into a collection

The most common terminal operation. Collects stream elements into a List, Set, Map, or other structure.

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Jo");

// Into a List
List<String> list = names.stream()
    .filter(n -> n.length() > 2)
    .collect(Collectors.toList());
// [Pavan, Sam]

// Into a Set (no duplicates)
Set<String> set = names.stream()
    .collect(Collectors.toSet());
// [Pavan, Sam, Jo]  (order may vary)

// Into a String (joining)
String joined = names.stream()
    .collect(Collectors.joining(", "));
// "Pavan, Sam, Jo"

String joinedWithBrackets = names.stream()
    .collect(Collectors.joining(", ", "[", "]"));
// "[Pavan, Sam, Jo]"
```

`Collectors` has many more methods — covered in detail in the next topic.

---

### 2. `forEach(Consumer)` — Do something with each element

Takes a `Consumer` — performs an action on each element, returns nothing.

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Jo");

names.stream()
    .forEach(name -> System.out.println("Hello " + name));
// Hello Pavan
// Hello Sam
// Hello Jo

// With method reference:
names.stream().forEach(System.out::println);
```

**`forEach` vs `map`** — a common confusion:
- `map` **transforms** and returns a new stream (intermediate)
- `forEach` **does something** and returns void (terminal)

```java
// WRONG — forEach returns void, can't chain or collect after it
names.stream()
    .forEach(String::toUpperCase);  // return value is lost, does nothing useful

// RIGHT — use map to transform, then collect
names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

---

### 3. `count()` — Count the elements

Returns a `long` — the number of elements in the stream.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

long count = numbers.stream()
    .filter(n -> n % 2 == 0)
    .count();

System.out.println(count);  // 5  (five even numbers)
```

---

### 4. `reduce()` — Combine all elements into one value

Takes all elements and **reduces** them to a single value by repeatedly applying an operation.

**With identity (starting value):**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

int sum = numbers.stream()
    .reduce(0, (a, b) -> a + b);

System.out.println(sum);  // 15
```

How it works step by step:

```
Start:  a = 0 (identity)
Step 1: a = 0 + 1 = 1
Step 2: a = 1 + 2 = 3
Step 3: a = 3 + 3 = 6
Step 4: a = 6 + 4 = 10
Step 5: a = 10 + 5 = 15
Result: 15
```

More examples:

```java
// Product: start with 1, keep multiplying
int product = numbers.stream()
    .reduce(1, (a, b) -> a * b);
System.out.println(product);  // 120  (1*2*3*4*5)

// Find max manually
int max = numbers.stream()
    .reduce(Integer.MIN_VALUE, (a, b) -> a > b ? a : b);
System.out.println(max);  // 5

// Concatenate strings
List<String> words = Arrays.asList("Java", "Streams", "Are", "Cool");
String sentence = words.stream()
    .reduce("", (a, b) -> a + " " + b)
    .trim();
System.out.println(sentence);  // "Java Streams Are Cool"
```

**Without identity — returns `Optional`** (because what do you return if the list is empty?):

```java
Optional<Integer> sum = numbers.stream()
    .reduce((a, b) -> a + b);

sum.ifPresent(System.out::println);  // 15
```

---

### 5. `findFirst()` — Get the first element

Returns an `Optional` containing the first element (or empty if stream is empty).

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

Optional<Integer> first = numbers.stream()
    .filter(n -> n > 3)
    .findFirst();

System.out.println(first.get());       // 4
System.out.println(first.isPresent()); // true

// Safe way — provide a default if nothing found
int result = numbers.stream()
    .filter(n -> n > 100)
    .findFirst()
    .orElse(-1);

System.out.println(result);  // -1  (nothing matched)
```

---

### 6. `findAny()` — Get any matching element

Similar to `findFirst()`, but doesn't guarantee which element is returned. Useful with **parallel streams** for better performance.

```java
Optional<Integer> any = numbers.stream()
    .filter(n -> n > 3)
    .findAny();

System.out.println(any.get());  // 4 (in sequential stream, usually same as findFirst)
```

In a parallel stream, `findAny()` may return 4 or 5 — whichever thread finds a match first.

---

### 7. `anyMatch()`, `allMatch()`, `noneMatch()` — Test conditions

All return a `boolean`. They are **short-circuiting** — they stop as soon as the answer is determined.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// anyMatch — is there AT LEAST ONE element that matches?
boolean hasEven = numbers.stream().anyMatch(n -> n % 2 == 0);
System.out.println(hasEven);  // true  (2 is even)

// allMatch — do ALL elements match?
boolean allPositive = numbers.stream().allMatch(n -> n > 0);
System.out.println(allPositive);  // true

boolean allEven = numbers.stream().allMatch(n -> n % 2 == 0);
System.out.println(allEven);  // false  (1, 3, 5 are odd)

// noneMatch — do ZERO elements match?
boolean noNegatives = numbers.stream().noneMatch(n -> n < 0);
System.out.println(noNegatives);  // true  (no negative numbers)
```

**Short-circuiting** means:
- `anyMatch` stops at the **first true** — doesn't check remaining elements
- `allMatch` stops at the **first false**
- `noneMatch` stops at the **first true**

---

### 8. `min()` and `max()` — Find smallest/largest

Both take a `Comparator` and return an `Optional`.

```java
List<Integer> numbers = Arrays.asList(3, 1, 4, 1, 5, 9, 2, 6);

Optional<Integer> min = numbers.stream().min(Integer::compareTo);
Optional<Integer> max = numbers.stream().max(Integer::compareTo);

System.out.println(min.get());  // 1
System.out.println(max.get());  // 9

// Find shortest name
List<String> names = Arrays.asList("Pavan", "Sam", "Jo", "Alexander");

String shortest = names.stream()
    .min(Comparator.comparingInt(String::length))
    .get();

System.out.println(shortest);  // Jo
```

---

### 9. `toArray()` — Convert to an array

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Jo");

// To Object array
Object[] arr1 = names.stream().toArray();

// To typed array (String[]) — using constructor reference
String[] arr2 = names.stream().toArray(String[]::new);

System.out.println(Arrays.toString(arr2));  // [Pavan, Sam, Jo]
```

`String[]::new` is a constructor reference — equivalent to `size -> new String[size]`.

---

### Quick Reference — All Terminal Operations

| Operation | Returns | Purpose |
|-----------|---------|---------|
| `collect()` | Collection/value | Gather into List, Set, Map, String |
| `forEach()` | void | Perform action on each element |
| `count()` | long | Count elements |
| `reduce()` | value or Optional | Combine all into one value |
| `findFirst()` | Optional | Get the first matching element |
| `findAny()` | Optional | Get any matching element |
| `anyMatch()` | boolean | Is there at least one match? |
| `allMatch()` | boolean | Do all elements match? |
| `noneMatch()` | boolean | Do zero elements match? |
| `min()` | Optional | Find smallest element |
| `max()` | Optional | Find largest element |
| `toArray()` | array | Convert stream to array |

---

## Topic 4: Collectors

`Collectors` is a utility class full of ready-made methods that tell `collect()` **how** to gather stream elements. Import it with `import java.util.stream.Collectors;`.

---

### 1. `toList()` and `toSet()`

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Jo", "Sam", "Pavan");

// Into a List (preserves order, allows duplicates)
List<String> list = names.stream()
    .collect(Collectors.toList());
// [Pavan, Sam, Jo, Sam, Pavan]

// Into a Set (no duplicates, order not guaranteed)
Set<String> set = names.stream()
    .collect(Collectors.toSet());
// [Jo, Pavan, Sam]
```

---

### 2. `toMap()` — Collect into a Map

Takes two functions: one for the **key**, one for the **value**.

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Jo");

// Name → its length
Map<String, Integer> nameToLength = names.stream()
    .collect(Collectors.toMap(
        name -> name,           // key: the name itself
        name -> name.length()   // value: its length
    ));

System.out.println(nameToLength);
// {Pavan=5, Sam=3, Jo=2}
```

Using method references and `Function.identity()`:

```java
Map<String, Integer> nameToLength = names.stream()
    .collect(Collectors.toMap(
        Function.identity(),    // key: the element itself (same as name -> name)
        String::length          // value: its length
    ));
```

**Problem — duplicate keys crash!**

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Jo", "Sam");

// This CRASHES — "Sam" appears twice, so the key "Sam" is duplicate
names.stream().collect(Collectors.toMap(
    name -> name,
    String::length
));
// IllegalStateException: Duplicate key Sam
```

**Fix — provide a merge function** (what to do when keys collide):

```java
Map<String, Integer> nameToLength = names.stream()
    .collect(Collectors.toMap(
        name -> name,           // key
        String::length,         // value
        (existing, replacement) -> existing   // if duplicate key, keep existing
    ));
// {Pavan=5, Sam=3, Jo=2}
```

---

### 3. `groupingBy()` — Group elements by a classifier

One of the **most powerful** and commonly used collectors. Groups elements into a `Map<K, List<V>>`.

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Jo", "Al", "Alexander", "Bob");

Map<Integer, List<String>> byLength = names.stream()
    .collect(Collectors.groupingBy(String::length));

System.out.println(byLength);
// {2=[Jo, Al], 3=[Sam, Bob], 5=[Pavan], 9=[Alexander]}
```

How it works:

```
"Pavan"     → length 5 → goes into group 5
"Sam"       → length 3 → goes into group 3
"Jo"        → length 2 → goes into group 2
"Al"        → length 2 → goes into group 2
"Alexander" → length 9 → goes into group 9
"Bob"       → length 3 → goes into group 3

Result: {2=[Jo, Al], 3=[Sam, Bob], 5=[Pavan], 9=[Alexander]}
```

With custom objects:

```java
List<Student> students = Arrays.asList(
    new Student("Pavan", "CSE"),
    new Student("Sam", "ECE"),
    new Student("Jo", "CSE"),
    new Student("Alex", "ECE"),
    new Student("Bob", "CSE")
);

// Group students by department
Map<String, List<Student>> byDept = students.stream()
    .collect(Collectors.groupingBy(Student::getDepartment));
// {CSE=[Pavan, Jo, Bob], ECE=[Sam, Alex]}
```

**groupingBy with a downstream collector** — instead of collecting into a List, do something else:

```java
// Count how many in each department
Map<String, Long> countByDept = students.stream()
    .collect(Collectors.groupingBy(
        Student::getDepartment,
        Collectors.counting()
    ));
// {CSE=3, ECE=2}

// Collect only names per department (not full Student objects)
Map<String, List<String>> namesByDept = students.stream()
    .collect(Collectors.groupingBy(
        Student::getDepartment,
        Collectors.mapping(Student::getName, Collectors.toList())
    ));
// {CSE=[Pavan, Jo, Bob], ECE=[Sam, Alex]}
```

---

### 4. `partitioningBy()` — Split into two groups (true/false)

A special case of `groupingBy` — splits into **exactly two groups** based on a `Predicate`.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

Map<Boolean, List<Integer>> partitioned = numbers.stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));

System.out.println(partitioned);
// {false=[1, 3, 5, 7, 9], true=[2, 4, 6, 8, 10]}

List<Integer> evens = partitioned.get(true);   // [2, 4, 6, 8, 10]
List<Integer> odds = partitioned.get(false);   // [1, 3, 5, 7, 9]
```

```java
// Partition students into passed/failed
Map<Boolean, List<Student>> passedOrFailed = students.stream()
    .collect(Collectors.partitioningBy(s -> s.getMarks() >= 40));

List<Student> passed = passedOrFailed.get(true);
List<Student> failed = passedOrFailed.get(false);
```

**`groupingBy` vs `partitioningBy`:**
- `groupingBy` → many groups (by department, by age, by city)
- `partitioningBy` → exactly **two** groups (true/false, pass/fail, even/odd)

---

### 5. `joining()` — Concatenate strings

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Jo");

// Simple join
String result1 = names.stream()
    .collect(Collectors.joining());
// "PavanSamJo"

// With delimiter
String result2 = names.stream()
    .collect(Collectors.joining(", "));
// "Pavan, Sam, Jo"

// With delimiter, prefix, and suffix
String result3 = names.stream()
    .collect(Collectors.joining(", ", "[", "]"));
// "[Pavan, Sam, Jo]"
```

---

### 6. `counting()` — Count elements (as a downstream collector)

Mainly used inside `groupingBy`:

```java
Map<Integer, Long> countByLength = names.stream()
    .collect(Collectors.groupingBy(String::length, Collectors.counting()));
// {2=1, 3=1, 5=1}
```

For simple counting, use the `.count()` terminal operation directly.

---

### 7. `summarizingInt()` / `summarizingDouble()` — Get statistics

Returns an object with count, sum, min, max, and average all at once:

```java
List<Integer> numbers = Arrays.asList(3, 1, 4, 1, 5, 9, 2, 6);

IntSummaryStatistics stats = numbers.stream()
    .collect(Collectors.summarizingInt(Integer::intValue));

System.out.println(stats.getCount());    // 8
System.out.println(stats.getSum());      // 31
System.out.println(stats.getMin());      // 1
System.out.println(stats.getMax());      // 9
System.out.println(stats.getAverage());  // 3.875
```

---

## Topic 5: Optional

### What Problem Does Optional Solve?

The biggest source of bugs in Java: **NullPointerException**.

```java
// Without Optional — dangerous
String name = getUserNameFromDB(userId);  // might return null!
System.out.println(name.toUpperCase());   // CRASH if name is null!
```

`Optional` is a **container** that may or may not hold a value. It forces you to **think about the null case** instead of forgetting it.

```java
// With Optional — safe
Optional<String> name = getUserNameFromDB(userId);  // might be empty
name.ifPresent(n -> System.out.println(n.toUpperCase()));  // only runs if value exists
```

---

### Creating an Optional

```java
// 1. Of a known non-null value
Optional<String> opt1 = Optional.of("Pavan");

// 2. Of a possibly-null value
String value = mightBeNull();
Optional<String> opt2 = Optional.ofNullable(value);  // empty if value is null

// 3. Empty Optional
Optional<String> opt3 = Optional.empty();
```

**`Optional.of(null)` CRASHES** — use `ofNullable` when the value might be null.

---

### Checking and Getting the Value

```java
Optional<String> name = Optional.of("Pavan");

// isPresent() — is there a value?
if (name.isPresent()) {
    System.out.println(name.get());  // Pavan
}

// isEmpty() — is it empty? (Java 11+)
if (name.isEmpty()) {
    System.out.println("No name");
}
```

**Never call `.get()` without checking `.isPresent()` first** — it throws `NoSuchElementException` if empty. But even better, avoid `.get()` entirely and use the methods below.

---

### `ifPresent(Consumer)` — Do something if value exists

```java
Optional<String> name = Optional.of("Pavan");

// Only prints if value is present
name.ifPresent(n -> System.out.println("Hello " + n));
// Output: Hello Pavan

Optional<String> empty = Optional.empty();
empty.ifPresent(n -> System.out.println("Hello " + n));
// Output: (nothing — silently skipped)
```

---

### `orElse()` — Provide a default value

```java
Optional<String> name = Optional.empty();

// If empty, use this default
String result = name.orElse("Unknown");
System.out.println(result);  // Unknown

Optional<String> name2 = Optional.of("Pavan");
String result2 = name2.orElse("Unknown");
System.out.println(result2);  // Pavan  (value exists, default is ignored)
```

---

### `orElseGet(Supplier)` — Compute default lazily

```java
String result = name.orElseGet(() -> "Default-" + System.currentTimeMillis());
```

**`orElse` vs `orElseGet`:**
- `orElse("value")` — the default is **always computed**, even if Optional has a value
- `orElseGet(() -> ...)` — the default is computed **only if needed**

```java
// orElse — expensiveCall() runs EVERY TIME, even when value is present
String r1 = name.orElse(expensiveCall());

// orElseGet — expensiveCall() runs ONLY when Optional is empty
String r2 = name.orElseGet(() -> expensiveCall());
```

Use `orElseGet` when the default is expensive to compute (DB call, API call, etc.).

---

### `orElseThrow()` — Throw exception if empty

```java
// Throw default NoSuchElementException
String name = optional.orElseThrow();

// Throw a custom exception
String name = optional.orElseThrow(
    () -> new RuntimeException("User not found!")
);
```

Very common in Spring Boot:

```java
User user = userRepository.findById(id)
    .orElseThrow(() -> new UserNotFoundException("User " + id + " not found"));
```

---

### `map()` — Transform the value inside Optional

Works like Stream's `map` — transforms the value if present, returns empty Optional if not.

```java
Optional<String> name = Optional.of("pavan");

// Transform to uppercase
Optional<String> upper = name.map(String::toUpperCase);
System.out.println(upper.get());  // PAVAN

// If empty, map is skipped
Optional<String> empty = Optional.empty();
Optional<String> result = empty.map(String::toUpperCase);
System.out.println(result.isPresent());  // false
```

Chaining:

```java
Optional<String> name = Optional.of("pavan");

String result = name
    .map(String::toUpperCase)
    .map(n -> "Hello " + n)
    .orElse("No name");

System.out.println(result);  // Hello PAVAN
```

---

### `flatMap()` — When your transform returns an Optional

If the function inside `map` itself returns an `Optional`, you'd get `Optional<Optional<String>>` — nested. `flatMap` unwraps it:

```java
Optional<String> name = Optional.of("pavan");

// Suppose this method returns Optional
Optional<String> findEmail(String name) {
    if (name.equals("pavan")) return Optional.of("pavan@mail.com");
    return Optional.empty();
}

// map would give Optional<Optional<String>> — bad!
// flatMap gives Optional<String> — good!
Optional<String> email = name.flatMap(n -> findEmail(n));
System.out.println(email.get());  // pavan@mail.com
```

---

### `filter()` — Keep value only if it matches a condition

```java
Optional<String> name = Optional.of("Pavan");

Optional<String> result = name.filter(n -> n.length() > 3);
System.out.println(result.isPresent());  // true  (Pavan has 5 chars)

Optional<String> result2 = name.filter(n -> n.length() > 10);
System.out.println(result2.isPresent());  // false  (doesn't match, becomes empty)
```

---

### Optional in Spring Boot — Why It Matters

Spring Data repositories return `Optional` by default:

```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}

// In your service:
User user = userRepository.findByEmail("pavan@mail.com")
    .orElseThrow(() -> new UserNotFoundException("Email not found"));

// Or get the name safely:
String name = userRepository.findById(id)
    .map(User::getName)
    .orElse("Anonymous");
```

---

## Topic 6: Primitive Streams — IntStream, LongStream, DoubleStream

### Why Do Primitive Streams Exist?

`Stream<Integer>` wraps every `int` into an `Integer` object (auto-boxing). For millions of numbers, this is **slow and wasteful**. Primitive streams avoid boxing entirely.

```java
// Stream<Integer> — each int is boxed into Integer object (slow for large data)
Stream<Integer> boxed = Stream.of(1, 2, 3, 4, 5);

// IntStream — works with raw int values directly (fast)
IntStream primitive = IntStream.of(1, 2, 3, 4, 5);
```

---

### Creating Primitive Streams

```java
// From values
IntStream s1 = IntStream.of(1, 2, 3, 4, 5);

// Range — [start, end) exclusive
IntStream s2 = IntStream.range(1, 6);       // 1, 2, 3, 4, 5

// RangeClosed — [start, end] inclusive
IntStream s3 = IntStream.rangeClosed(1, 5); // 1, 2, 3, 4, 5

// From an array
int[] arr = {10, 20, 30};
IntStream s4 = Arrays.stream(arr);
```

---

### Useful Operations on Primitive Streams

```java
IntStream numbers = IntStream.rangeClosed(1, 10);  // 1 to 10

int sum = IntStream.rangeClosed(1, 10).sum();                // 55
long count = IntStream.rangeClosed(1, 10).count();           // 10
int min = IntStream.rangeClosed(1, 10).min().getAsInt();     // 1
int max = IntStream.rangeClosed(1, 10).max().getAsInt();     // 10
double avg = IntStream.rangeClosed(1, 10).average().getAsDouble();  // 5.5
```

---

### Converting Between Stream Types

```java
List<String> names = Arrays.asList("Pavan", "Sam", "Jo");

// Stream<String> → IntStream (using mapToInt)
IntStream lengths = names.stream()
    .mapToInt(String::length);

int totalChars = names.stream()
    .mapToInt(String::length)
    .sum();
System.out.println(totalChars);  // 10  (5+3+2)

// IntStream → Stream<Integer> (boxing)
Stream<Integer> boxed = IntStream.rangeClosed(1, 5).boxed();

// IntStream → Stream<String> (using mapToObj)
Stream<String> strings = IntStream.rangeClosed(1, 5)
    .mapToObj(n -> "Number " + n);
```

---

## Topic 7: Parallel Streams

### What is a Parallel Stream?

A parallel stream splits the work across **multiple CPU cores** to process data faster.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Sequential — processes on one thread
numbers.stream()
    .filter(n -> n % 2 == 0)
    .forEach(System.out::println);
// Output: 2, 4, 6, 8, 10 (always in order)

// Parallel — splits work across threads
numbers.parallelStream()
    .filter(n -> n % 2 == 0)
    .forEach(System.out::println);
// Output: 6, 8, 2, 10, 4 (order may vary!)
```

You can also convert a sequential stream to parallel:

```java
numbers.stream()
    .parallel()
    .filter(n -> n % 2 == 0)
    .forEach(System.out::println);
```

---

### When to Use Parallel Streams

**Use when:**
- You have a **large** amount of data (thousands/millions of elements)
- Each operation is **independent** (no shared mutable state)
- Operations are **CPU-intensive** (heavy computation)

**Do NOT use when:**
- Data is small (overhead of splitting > benefit)
- Order matters and you're not handling it
- Operations involve I/O (DB calls, API calls) — threads get blocked
- You're modifying shared variables — causes race conditions

```java
// DANGEROUS — modifying shared list from parallel stream
List<Integer> results = new ArrayList<>();
numbers.parallelStream()
    .filter(n -> n % 2 == 0)
    .forEach(n -> results.add(n));   // NOT THREAD-SAFE! Race condition!

// SAFE — use collect instead
List<Integer> results = numbers.parallelStream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());   // thread-safe
```

**In Spring Boot:** Parallel streams are rarely needed. Spring handles concurrency through its thread pool for HTTP requests. Using parallel streams inside a request handler can actually **hurt** performance by competing for threads.

---

## Topic 8: Real-World Patterns in Spring Boot

These are the patterns you'll see and write daily in Spring Boot applications.

---

### Pattern 1: Entity to DTO Conversion

```java
// Convert list of User entities to UserDTO objects
List<UserDTO> userDTOs = userRepository.findAll().stream()
    .map(user -> new UserDTO(user.getName(), user.getEmail()))
    .collect(Collectors.toList());

// Or with a conversion method:
List<UserDTO> userDTOs = userRepository.findAll().stream()
    .map(this::convertToDTO)
    .collect(Collectors.toList());

private UserDTO convertToDTO(User user) {
    return new UserDTO(user.getName(), user.getEmail());
}
```

---

### Pattern 2: Filtering with Repository Results

```java
// Get all active users older than 18
List<User> eligibleUsers = userRepository.findAll().stream()
    .filter(user -> user.isActive())
    .filter(user -> user.getAge() > 18)
    .collect(Collectors.toList());
```

---

### Pattern 3: Optional with Repository

```java
// Find user by ID — returns Optional
User user = userRepository.findById(id)
    .orElseThrow(() -> new ResourceNotFoundException("User not found: " + id));

// Get user's email, or default
String email = userRepository.findById(id)
    .map(User::getEmail)
    .orElse("no-reply@app.com");

// Check if user exists and is active
boolean isActive = userRepository.findById(id)
    .filter(User::isActive)
    .isPresent();
```

---

### Pattern 4: Grouping and Aggregation

```java
// Group orders by status
Map<OrderStatus, List<Order>> ordersByStatus = orderRepository.findAll().stream()
    .collect(Collectors.groupingBy(Order::getStatus));

// Count orders per status
Map<OrderStatus, Long> countByStatus = orderRepository.findAll().stream()
    .collect(Collectors.groupingBy(Order::getStatus, Collectors.counting()));

// Total revenue per product
Map<String, Double> revenueByProduct = orderRepository.findAll().stream()
    .collect(Collectors.groupingBy(
        Order::getProductName,
        Collectors.summingDouble(Order::getAmount)
    ));
```

---

### Pattern 5: Collecting into a Map for Lookup

```java
// Create a lookup map: userId → User
Map<Long, User> userById = userRepository.findAll().stream()
    .collect(Collectors.toMap(User::getId, Function.identity()));

// Now you can do O(1) lookups
User pavan = userById.get(42L);
```

---

### Pattern 6: Extracting and Joining

```java
// Get comma-separated list of user names
String nameList = userRepository.findAll().stream()
    .map(User::getName)
    .collect(Collectors.joining(", "));
// "Pavan, Sam, Jo, Alex"

// Get all unique email domains
Set<String> domains = userRepository.findAll().stream()
    .map(User::getEmail)
    .map(email -> email.substring(email.indexOf("@") + 1))
    .collect(Collectors.toSet());
// [gmail.com, yahoo.com, company.com]
```

---

### Pattern 7: Chaining Stream with map + flatMap

```java
// Get all tags from all posts (each post has a List<String> of tags)
List<String> allTags = postRepository.findAll().stream()
    .flatMap(post -> post.getTags().stream())
    .distinct()
    .sorted()
    .collect(Collectors.toList());
```

---
