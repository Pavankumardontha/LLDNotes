# Task F3.1 — Generics: the problem they solve

Beginner-friendly study notes for Task F3.1 of the Zenith plan. We go through the concepts first,
one at a time, then do the subtasks. Every code example and output below was compiled and run on
Java 21.

## Progress

### Concepts

- [x] Concept 1 — Compile time versus runtime: two moments an error can appear
- [x] Concept 2 — `Object`, casts and `ClassCastException`
- [x] Concept 3 — A generic class: type parameters and type arguments
- [x] Concept 4 — The diamond operator `<>`
- [x] Concept 5 — Raw types and "unchecked" warnings
- [x] Concept 6 — More than one type parameter, and the naming convention
- [x] Concept 7 — Reading generic types aloud
- [x] Concept 8 — Generic methods
- [x] Concept 9 — Generic interfaces, and filling in the blanks with `extends`

### Subtasks

- [ ] Subtask 1 — `Box<T>`: the compiler refuses a `String` in an integer box
- [ ] Subtask 2 — The before-times: an `Object` box and a `ClassCastException`
- [ ] Subtask 3 — A raw `List` and the "unchecked" warning
- [ ] Subtask 4 — Read five real Spring types aloud
- [ ] Subtask 5 — The naming convention: `T`, `E`, `K`, `V`, `R`
- [ ] Subtask 6 — Ask AI: a standard library method with two type parameters
- [ ] Subtask 7 — `Repository<T, ID>` and `SeatRepository`
- [ ] Checkpoint questions

## What this task is about

Every file in Zenith is full of angle brackets: `List<Seat>`, `Map<String, List<Seat>>`,
`Repository<T, ID>`. These are **generics**. You've *used* them since Task F2.4 (`List<String>`);
this task shows what problem they solve and how to write your own.

The one-sentence version: **generics let the compiler catch "wrong type" mistakes while you're
building the program, instead of the program crashing in front of a user.** To understand why that
matters, we start with the two moments an error can appear.

## Where things live

- **This file:** the reference notes for this task.
- **`../NOTES.md`:** your own notes, in your own words.
- **Your code:** type the examples into `.java` files in this folder.

Each example is one complete file. The file name must match the `public class` name inside it. To
run one:

```bash
javac RuntimeErrorDemo.java
java RuntimeErrorDemo
```

---

## Concept 1 — Compile time versus runtime: two moments an error can appear

Running a Java program is always **two separate steps**, and an error can show up in either one.

```text
   You write            Step 1: COMPILE                 Step 2: RUN
   Hello.java   ──►   javac Hello.java   ──►  Hello.class  ──►  java Hello
                      (checks your code,                  (actually does what
                       builds the program)                 the code says)

                      Errors here are                     Errors here are
                      COMPILE-TIME errors                 RUNTIME errors
```

| | Compile time | Runtime |
| --- | --- | --- |
| When | When you run `javac`, before the program starts | When you run `java`, while the program is working |
| Who finds it | The compiler (`javac`), by reading your code | Nobody, until that exact line actually runs |
| Who sees it | You, the programmer, at your desk | Possibly a real user |
| What you get | An `error:` message, and **no program at all** | An `Exception in thread "main" ...`, and the program stops |

### Step 1: A compile-time error

```java
public class CompileErrorDemo {

    public static void main(String[] args) {
        System.out.println("Program started");

        String seat = 42;

        System.out.println(seat);
    }
}
```

```bash
javac CompileErrorDemo.java
```

```text
CompileErrorDemo.java:6: error: incompatible types: int cannot be converted to String
        String seat = 42;
                      ^
1 error
```

- `String seat = 42;` tries to put a number into a variable that's meant for text.
- `javac` reads the code, sees the mismatch, and **refuses to build the program**. No
  `CompileErrorDemo.class` file is created.
- So there's nothing to run. Even the first line, `"Program started"`, never prints.

The compiler could catch this just by **reading** the code, because the types are written right
there: `String` on the left, a number on the right. That's what a compile-time error is: a mistake
visible from the code alone.

### Step 2: A runtime error

```java
public class RuntimeErrorDemo {

    public static void main(String[] args) {
        System.out.println("Program started");

        int tickets = 10;
        int people = 0;

        int ticketsEach = tickets / people;

        System.out.println(ticketsEach);
    }
}
```

```bash
javac RuntimeErrorDemo.java
java RuntimeErrorDemo
```

`javac` gives **no errors**. The program is built. Then `java` runs it:

```text
Program started
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at RuntimeErrorDemo.main(RuntimeErrorDemo.java:9)
```

- Every line is valid Java: dividing an `int` by an `int` is allowed, so the compiler is happy.
- The problem is the **value**: `people` is `0`, and you can't divide by zero. That only becomes a
  problem when the line actually runs.
- `"Program started"` printed, so the program really was running when it crashed.
- An **exception** is Java's way of saying "something went wrong while running, I'm stopping".
  `ArithmeticException` is the kind of problem. `RuntimeErrorDemo.java:9` is the line.

### Step 3: Why runtime errors are the dangerous ones

A runtime error only happens **when that line runs with bad data**. Most of the time it doesn't:

```java
public class HiddenRuntimeError {

    static int ticketsEach(int tickets, int people) {
        return tickets / people;
    }

    public static void main(String[] args) {
        System.out.println(ticketsEach(10, 2));
        System.out.println(ticketsEach(12, 3));
        System.out.println(ticketsEach(9, 0));
        System.out.println("Done");
    }
}
```

```bash
javac HiddenRuntimeError.java
java HiddenRuntimeError
```

Output:

```text
5
4
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at HiddenRuntimeError.ticketsEach(HiddenRuntimeError.java:4)
	at HiddenRuntimeError.main(HiddenRuntimeError.java:10)
```

- The first two calls worked. If you only tested those, you'd think the code was fine.
- The third call, with `0`, crashed. `"Done"` never printed.

Imagine this in Zenith. It works in every test, ships to production, and weeks later one customer
does the unusual thing, and their page crashes. That's what makes runtime errors dangerous: **the
code looks fine until the exact bad case happens, and the person who finds it is a user.**

### Step 4: The big idea of this whole task

| Error found at... | Who suffers | Cost to fix |
| --- | --- | --- |
| compile time | you, at your desk, in seconds | tiny: read the message, fix the line |
| runtime | a user, maybe weeks later | big: a bug report, investigation, a fix, a redeploy |

So programmers always want to **move errors earlier**, from runtime to compile time. The compiler
can only catch what it can see from the code, mostly **types**: is this a `String`? an `int`? a
`Seat`?

Generics exist to give the compiler more type information, so that a whole family of mistakes,
"I put the wrong kind of thing in here", becomes a **compile-time** error instead of a runtime crash.
The next concept shows what that family of mistakes looks like without generics.

### Summary

1. Java runs in two steps: **compile** (`javac`, checks and builds) and **run** (`java`, executes).
2. A **compile-time error** is found by reading the code. The program isn't built at all, so
   nothing runs.
3. A **runtime error** (an *exception*) happens while the program runs, only when the bad line
   meets bad data. It can hide through all your tests.
4. Compile-time errors are cheap and found by you. Runtime errors are expensive and often found by
   users.
5. Generics move "wrong type" mistakes from runtime to compile time. That's their entire value.

---

## Concept 2 — `Object`, casts and `ClassCastException`

Before generics existed (before Java 5, in 2004), the only way to write code that worked with "any
kind of thing" was to use the type `Object`. This concept shows how that works, and the runtime
error it leads to.

### Step 1: `Object` can hold anything

In Java, every class automatically extends a class called `Object`. `String` is an `Object`.
`Integer` is an `Object`. Your own `Seat` class is an `Object`. So a variable of type `Object` can
hold **anything**:

```java
public class ObjectHoldsAnything {

    public static void main(String[] args) {
        Object a = "A1";
        Object b = 42;
        Object c = 9.99;

        System.out.println(a);
        System.out.println(b);
        System.out.println(c);

        System.out.println(a.getClass());
        System.out.println(b.getClass());
        System.out.println(c.getClass());
    }
}
```

Output:

```text
A1
42
9.99
class java.lang.String
class java.lang.Integer
class java.lang.Double
```

- Text, a whole number and a decimal number all went into `Object` variables without complaint.
- `getClass()` asks "what is this object **really**?" while the program runs. The real objects are
  still a `String`, an `Integer` and a `Double`.

A side note you'll need later: `42` on its own is an `int`, a **primitive** (a plain value, not an
object). Only objects fit in an `Object` variable, so Java quietly wraps the `42` in an `Integer`
object for you. This is called **autoboxing**. Every primitive has an object version:

| Primitive (plain value) | Object version (wrapper) |
| --- | --- |
| `int` | `Integer` |
| `double` | `Double` |
| `boolean` | `Boolean` |
| `char` | `Character` |
| `long` | `Long` |

### Step 2: But the compiler forgets what it is

Here's the catch. Try to use the `String` inside an `Object` variable:

```java
public class ObjectForgets {

    public static void main(String[] args) {
        Object seat = "A1";

        System.out.println(seat.length());
    }
}
```

```text
ObjectForgets.java:6: error: cannot find symbol
        System.out.println(seat.length());
                               ^
  symbol:   method length()
  location: variable seat of type Object
```

But it *is* a `String`. Why can't we call `.length()`?

Because the compiler doesn't look at what's **in** the variable. It only looks at the **declared
type**, the word on the left: `Object seat`. As far as the compiler knows, `seat` could be anything,
a number or a date, and those don't have `.length()`. So it refuses.

```text
                  what the compiler sees     what's really there while running
Object seat  =    "something, an Object"     "A1", a String
```

The same goes for assigning it back to a `String` variable:

```java
public class NoCast {

    public static void main(String[] args) {
        Object seat = "A1";

        String text = seat;
    }
}
```

```text
NoCast.java:6: error: incompatible types: Object cannot be converted to String
        String text = seat;
                      ^
```

"An `Object` might not be a `String`, so I won't let you put it in a `String` variable."

Putting something into `Object` is easy. Getting it back out **as what it really is** is the
problem.

### Step 3: A cast — "trust me, it's a `String`"

A **cast** is how you tell the compiler: "I know better than you. Treat this as a `String`." You
write the type in brackets in front of the value:

```java
public class CastDemo {

    public static void main(String[] args) {
        Object seat = "A1";

        String text = (String) seat;

        System.out.println(text.length());
        System.out.println(text.toLowerCase());
    }
}
```

Output:

```text
2
a1
```

- `(String) seat` means "treat `seat` as a `String`".
- Now `text` is declared as `String`, so the compiler lets you call `String` methods.

The important bit is that **the compiler doesn't check your promise**. It just believes you. The
check happens later, at runtime, when Java looks at the real object.

### Step 4: When the cast is a lie — `ClassCastException`

What if you promise the wrong type?

```java
public class BadCast {

    public static void main(String[] args) {
        System.out.println("Program started");

        Object seat = "A1";

        Integer number = (Integer) seat;

        System.out.println(number + 1);
    }
}
```

`javac` gives **no errors**. Then:

```text
Program started
Exception in thread "main" java.lang.ClassCastException: class java.lang.String cannot be cast to class java.lang.Integer (java.lang.String and java.lang.Integer are in module java.base of loader 'bootstrap')
	at BadCast.main(BadCast.java:8)
```

Read the important part: **`String cannot be cast to class Integer`**.

1. At compile time: the compiler sees `Object` and thinks "an `Object` *could* be an `Integer`.
   OK, I'll trust you." So it builds the program.
2. At runtime: Java checks the real object, finds it's a `String`, and stops with a
   `ClassCastException`, meaning "your cast was a lie".

That's a **runtime** error, exactly the dangerous kind from Concept 1.

### Step 5: The compiler *can* catch it — if it knows the type

Compare with this, where the variable is declared as `String`, not `Object`:

```java
public class ImpossibleCast {

    public static void main(String[] args) {
        String seat = "A1";

        Integer number = (Integer) seat;
    }
}
```

```text
ImpossibleCast.java:6: error: incompatible types: String cannot be converted to Integer
        Integer number = (Integer) seat;
                                   ^
```

Now it's a **compile-time** error. The compiler knows `seat` is a `String`, and a `String` can never
be an `Integer`, so it refuses straight away.

| Variable declared as | `(Integer) seat` | Error found at |
| --- | --- | --- |
| `Object seat` | "could be an `Integer`, I'll trust you" | **runtime** (`ClassCastException`) |
| `String seat` | "can never be an `Integer`" | **compile time** |

**This is the whole story of generics in one table.** The compiler catches the mistake when it
knows the real type. `Object` throws that knowledge away. Generics (next concept) keep it.

### Step 6: Why this hides in real code

Like the divide-by-zero example, a bad cast only explodes when bad data reaches it:

```java
public class HiddenBadCast {

    static int seatCount(Object value) {
        Integer count = (Integer) value;
        return count;
    }

    public static void main(String[] args) {
        System.out.println(seatCount(10));
        System.out.println(seatCount(25));
        System.out.println(seatCount("30"));
        System.out.println("Done");
    }
}
```

Output:

```text
10
25
Exception in thread "main" java.lang.ClassCastException: class java.lang.String cannot be cast to class java.lang.Integer ...
	at HiddenBadCast.seatCount(HiddenBadCast.java:4)
	at HiddenBadCast.main(HiddenBadCast.java:11)
```

- `seatCount` accepts `Object`, so the compiler lets you pass **anything**, even the text `"30"`.
- The first two calls work. The third passes text that *looks* like a number, and crashes.
- Notice where it crashed: **inside `seatCount`** (line 4), not where the wrong value was passed
  in (line 11). With `Object`, the error appears far from the actual mistake, which makes it harder
  to track down.

Before generics, every collection (`List`, `Map`, ...) worked exactly like this: it stored `Object`,
and you cast every item on the way out. Every cast was a little bet that could lose at runtime.

### Summary

1. Every class extends `Object`, so an `Object` variable can hold anything.
2. Primitives like `int` get wrapped into objects like `Integer` automatically (autoboxing).
3. The compiler only knows a variable's **declared** type. Once something is stored as `Object`,
   the compiler forgets what it really is.
4. A **cast** `(String) x` says "trust me, it's a `String`". The compiler doesn't check it; Java
   checks it at runtime.
5. A wrong cast compiles fine and crashes at runtime with a **`ClassCastException`**.
6. When the compiler knows the real type, it catches impossible casts at compile time. Generics
   are a way of keeping that knowledge.

---

## Concept 3 — A generic class: type parameters and type arguments

We want a simple class: a **locker** that holds one item. You `put` something in and `get` it back
out. The question is: what type is the item?

### Step 1: Option A — one class per type (lots of copying)

```java
class StringLocker {
    private String item;

    void put(String item) {
        this.item = item;
    }

    String get() {
        return item;
    }
}

class IntegerLocker {
    private Integer item;

    void put(Integer item) {
        this.item = item;
    }

    Integer get() {
        return item;
    }
}

public class SeparateLockers {

    public static void main(String[] args) {
        StringLocker names = new StringLocker();
        names.put("Ravi");
        System.out.println(names.get());

        IntegerLocker numbers = new IntegerLocker();
        numbers.put(42);
        System.out.println(numbers.get());
    }
}
```

Output:

```text
Ravi
42
```

This is **type-safe**: you can't put a number in `StringLocker`. But look at the two classes. They're
identical except for one word (`String` versus `Integer`). For a `Seat` locker you'd copy it again,
and again for `Order`, `Customer`... Any bug fix has to be made in every copy.

### Step 2: Option B — one class using `Object` (no safety)

You saw this in Concept 2. One class with `Object item` works for every type, but the compiler
forgets what's inside. You need a cast on every `get()`, and a wrong cast crashes at **runtime** with
a `ClassCastException`.

So we're stuck choosing:

| | One class for all types? | Compiler catches wrong types? |
| --- | --- | --- |
| Option A: one class per type | no, copy it for every type | yes |
| Option B: `Object` | yes | **no**, crashes at runtime |
| **Generics** | **yes** | **yes** |

Generics give you both.

### Step 3: Option C — a generic class

Write the class **once**, with a **blank** where the type goes:

```java
class Locker<T> {
    private T item;

    void put(T item) {
        this.item = item;
    }

    T get() {
        return item;
    }
}
```

Read it slowly:

| Code | Meaning |
| --- | --- |
| `class Locker<T>` | "This class has a blank called `T`. Whoever uses `Locker` fills in the blank with a real type." |
| `private T item;` | "The item is of type T, whatever T turns out to be." |
| `void put(T item)` | "`put` accepts a T." |
| `T get()` | "`get` gives back a T." |

`T` is not a real class. It's a **placeholder**, like a blank on a form: "Name: ______". It's
written in angle brackets `< >` right after the class name.

That `T` is called a **type parameter**.

### Step 4: Using it — fill in the blank

```java
class Locker<T> {
    private T item;

    void put(T item) {
        this.item = item;
    }

    T get() {
        return item;
    }
}

public class GenericLockerDemo {

    public static void main(String[] args) {
        Locker<String> nameLocker = new Locker<String>();
        nameLocker.put("Ravi");
        String name = nameLocker.get();
        System.out.println(name);
        System.out.println(name.length());

        Locker<Integer> numberLocker = new Locker<Integer>();
        numberLocker.put(42);
        Integer number = numberLocker.get();
        System.out.println(number);
        System.out.println(number + 1);
    }
}
```

Output:

```text
Ravi
4
42
43
```

- `Locker<String>` means "a locker where T is `String`". Read it as "a locker of strings".
- `Locker<Integer>` means "a locker where T is `Integer`". Read it as "a locker of integers".
- **One class, two different uses.** No copying.
- **No casts.** `nameLocker.get()` gives back a `String` directly, so `name.length()` just works.
  Compare with Concept 2, where you had to write `(String)` every time.

The `String` you put inside the angle brackets is called a **type argument**.

#### A trick for reading it: replace T in your head

When you write `Locker<String>`, imagine Java crossing out every `T` and writing `String`:

```text
class Locker<T> {                     what Locker<String> means:
    private T item;          ──►          private String item;
    void put(T item)         ──►          void put(String item)
    T get()                  ──►          String get()
}
```

And for `Locker<Integer>`, every `T` becomes `Integer`. That's exactly how the compiler checks your
code.

(Behind the scenes Java doesn't really make two copies of the class. There's still only one
`Locker`. But the compiler checks your code *as if* the T's had been replaced, which is all that
matters for now.)

### Step 5: Type parameter versus type argument

These two names get mixed up constantly, so here they are side by side:

| | Type **parameter** | Type **argument** |
| --- | --- | --- |
| What it is | the **blank** | the **real type** you fill the blank with |
| Where you see it | where the class is **declared**: `class Locker<T>` | where the class is **used**: `Locker<String>` |
| Example | `T` | `String`, `Integer`, `Seat` |

It's the same idea as method parameters and arguments, which you already know:

```text
void greet(String name) { ... }      name    = parameter (the blank in the declaration)
greet("Ravi");                       "Ravi"  = argument  (the real value you pass in)

class Locker<T> { ... }              T       = type parameter (the blank)
Locker<String>                       String  = type argument  (the real type you pass in)
```

The only difference: a method parameter is a blank for a **value**, a type parameter is a blank for
a **type**.

### Step 6: The compiler now catches wrong types

Put a `String` into an integer locker:

```java
class Locker<T> {
    private T item;

    void put(T item) {
        this.item = item;
    }

    T get() {
        return item;
    }
}

public class WrongTypeIn {

    public static void main(String[] args) {
        Locker<Integer> numberLocker = new Locker<Integer>();
        numberLocker.put("Ravi");
    }
}
```

```text
WrongTypeIn.java:17: error: incompatible types: String cannot be converted to Integer
        numberLocker.put("Ravi");
                         ^
```

For a `Locker<Integer>`, `put` means `put(Integer item)`, and `"Ravi"` is not an `Integer`. The
program is **never built**.

It works on the way out too:

```java
public class WrongTypeOut {

    public static void main(String[] args) {
        Locker<String> nameLocker = new Locker<String>();
        nameLocker.put("Ravi");
        Integer number = nameLocker.get();
    }
}
```

(Same `Locker` class at the top of the file.)

```text
WrongTypeOut.java:18: error: incompatible types: String cannot be converted to Integer
        Integer number = nameLocker.get();
                                       ^
```

For a `Locker<String>`, `get()` returns a `String`, which can't go into an `Integer` variable.

Compare this with Concept 2:

| | `Object` version | Generic version |
| --- | --- | --- |
| Put a `String` where an `Integer` belongs | allowed | **compile error** |
| Take it out as the wrong type | compiles, then `ClassCastException` at **runtime** | **compile error** |
| Casts needed | on every `get()` | **none** |

The mistake moved from runtime to compile time. That's the whole point of generics.

### Step 7: Type arguments must be objects, not primitives

```java
public class PrimitiveArg {

    public static void main(String[] args) {
        Locker<int> numberLocker = new Locker<int>();
    }
}
```

(Same `Locker` class at the top of the file.)

```text
PrimitiveArg.java:16: error: unexpected type
        Locker<int> numberLocker = new Locker<int>();
               ^
  required: reference
  found:    int
```

"Reference" means "an object type". The blank can only be filled with a class, not with a primitive
like `int`. So you use the wrapper from Concept 2: `Locker<Integer>`, not `Locker<int>`. You can
still call `put(42)` with a plain number, because Java wraps it into an `Integer` for you
(autoboxing).

### Step 8: It works with your own classes too

```java
class Locker<T> {
    private T item;

    void put(T item) {
        this.item = item;
    }

    T get() {
        return item;
    }
}

class Seat {
    private final String id;

    Seat(String id) {
        this.id = id;
    }

    String getId() {
        return id;
    }
}

public class SeatLocker {

    public static void main(String[] args) {
        Locker<Seat> seatLocker = new Locker<Seat>();
        seatLocker.put(new Seat("A1"));

        Seat seat = seatLocker.get();
        System.out.println(seat.getId());
    }
}
```

Output:

```text
A1
```

The same `Locker` class, never changed, now holds a `Seat`. `get()` gives back a `Seat`, so
`seat.getId()` works with no cast.

### You've been using this all along

`ArrayList` is written exactly like `Locker`, just with more methods. Inside the Java library it's
declared roughly like this:

```java
public class ArrayList<E> {
    public boolean add(E e) { ... }
    public E get(int index) { ... }
}
```

So when you wrote `List<String>` in Task F2.4, you were filling in the blank `E` with `String`.
That's why `names.add(42)` didn't compile and `names.get(0)` gave back a `String` with no cast.

### Summary

1. A **generic class** is written once with a blank for the type: `class Locker<T>`.
2. The blank `T` is the **type parameter**. The real type you fill it with, `String` in
   `Locker<String>`, is the **type argument**.
3. Read `Locker<String>` as "a locker of strings": every `T` in the class becomes `String`.
4. One class works for every type, with no copying and no casts.
5. Putting in or taking out the wrong type is a **compile-time** error, not a runtime crash.
6. Type arguments must be classes: `Integer`, not `int`.

---

## Concept 4 — The diamond operator `<>`

A small concept, but you'll see it on almost every line of real code.

For this concept, `Locker` lives in its own file, `Locker.java`, so we don't have to repeat it at
the top of every example:

```java
class Locker<T> {
    private T item;

    void put(T item) {
        this.item = item;
    }

    T get() {
        return item;
    }
}
```

When several `.java` files are in the same folder, they can all use each other's classes.

### Step 1: The type is written twice

So far we've written:

```java
public class WithoutDiamond {

    public static void main(String[] args) {
        Locker<String> nameLocker = new Locker<String>();
        nameLocker.put("Ravi");
        System.out.println(nameLocker.get());
    }
}
```

Output:

```text
Ravi
```

Look at the first line: `String` appears **twice**, once on the left and once on the right.

```text
Locker<String> nameLocker = new Locker<String>();
       ^^^^^^                         ^^^^^^
       left side                      right side
```

The left side already says it's a `Locker<String>`, so writing `String` again on the right tells the
compiler nothing new.

### Step 2: The diamond — leave the right side empty

```java
public class WithDiamond {

    public static void main(String[] args) {
        Locker<String> nameLocker = new Locker<>();
        nameLocker.put("Ravi");
        System.out.println(nameLocker.get());

        Locker<Integer> numberLocker = new Locker<>();
        numberLocker.put(42);
        System.out.println(numberLocker.get() + 1);
    }
}
```

Output:

```text
Ravi
43
```

`new Locker<>()`: the empty angle brackets `<>` are called the **diamond operator** (because `<>`
looks a bit like a diamond shape). It means:

> "Fill in this blank yourself, by looking at the left side."

The compiler sees `Locker<String>` on the left and works out that the right side must be
`new Locker<String>()`. Working something out like this is called **inferring** the type.

```text
Locker<String> nameLocker = new Locker<>();
       ──────                         ▲
          └─── compiler copies it ────┘
```

### Step 3: It's exactly as safe

The diamond is **only a shortcut for typing**. The object is still a `Locker<Integer>`, and the
compiler still checks everything:

```java
public class DiamondStillChecks {

    public static void main(String[] args) {
        Locker<Integer> numberLocker = new Locker<>();
        numberLocker.put("Ravi");
    }
}
```

```text
DiamondStillChecks.java:5: error: incompatible types: String cannot be converted to Integer
        numberLocker.put("Ravi");
                         ^
```

Same error as in Concept 3. Nothing was lost.

### Step 4: Where it really helps — long types

With nested types the saving is big:

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class LongTypes {

    public static void main(String[] args) {
        Map<String, List<String>> seatsByRow = new HashMap<>();

        List<String> rowA = new ArrayList<>();
        rowA.add("A1");
        rowA.add("A2");

        seatsByRow.put("A", rowA);

        System.out.println(seatsByRow);
    }
}
```

Output:

```text
{A=[A1, A2]}
```

Without the diamond, the first line would be:

```java
Map<String, List<String>> seatsByRow = new HashMap<String, List<String>>();
```

With it:

```java
Map<String, List<String>> seatsByRow = new HashMap<>();
```

(Don't worry about reading `Map<String, List<String>>` yet. That's Concept 7.)

Notice the diamond also works when the left and right are **different classes**: the left is the
interface `Map`, the right is the class `HashMap`. The compiler copies the type arguments
`<String, List<String>>` across.

### Step 5: Trap 1 — the diamond with `var`

`var` (Java 10+) means "work out the variable's type from the right side". The diamond means "work
out the type argument from the left side". Put them together and **each one is waiting for the
other**:

```java
public class VarWithDiamond {

    public static void main(String[] args) {
        var locker = new Locker<>();
        locker.put("Ravi");
        locker.put(42);

        String name = locker.get();
    }
}
```

```text
VarWithDiamond.java:8: error: incompatible types: Object cannot be converted to String
        String name = locker.get();
                                ^
```

There's no type written anywhere, so the compiler falls back to the most general type, `Object`.
`locker` became a `Locker<Object>`, which is why `put("Ravi")` *and* `put(42)` were both accepted.
That's exactly the "holds anything, forgets what it is" problem from Concept 2.

**Rule:** the type has to be written **somewhere**. Either on the left with the diamond on the right
(`Locker<String> locker = new Locker<>();`), or on the right with `var`
(`var locker = new Locker<String>();`). Never neither.

### Step 6: Trap 2 — forgetting the `<>` completely

The diamond is `<>`, with the brackets. Leave the brackets out and it means something different:

```java
public class ForgotDiamond {

    public static void main(String[] args) {
        Locker<String> nameLocker = new Locker();
        nameLocker.put("Ravi");
        System.out.println(nameLocker.get());
    }
}
```

```bash
javac ForgotDiamond.java
java ForgotDiamond
```

Output:

```text
Note: ForgotDiamond.java uses unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.
Ravi
```

It compiles and runs, but `javac` printed a **note** about "unchecked or unsafe operations". If you
ask for the details with `-Xlint:unchecked`:

```bash
javac -Xlint:unchecked ForgotDiamond.java
```

```text
ForgotDiamond.java:4: warning: [unchecked] unchecked conversion
        Locker<String> nameLocker = new Locker();
                                    ^
  required: Locker<String>
  found:    Locker
```

`new Locker()` with no angle brackets at all creates a **raw** `Locker`: a locker with the type
checking switched off. The compiler warns that it can't promise this is really a `Locker<String>`.
Raw types are a whole concept of their own. That's Concept 5.

| You write | Meaning |
| --- | --- |
| `new Locker<String>()` | a locker of strings |
| `new Locker<>()` | a locker of strings (the compiler copies `String` from the left) |
| `new Locker()` | a **raw** locker: type checking off, and a warning |

### Summary

1. The **diamond operator** `<>` means "compiler, copy the type argument from the left side".
2. `Locker<String> x = new Locker<>();` is exactly the same as `new Locker<String>()`, and exactly
   as safe. It just saves typing.
3. It saves the most on long types like `Map<String, List<String>>`.
4. Don't combine `var` with `<>`. With no type written anywhere, you get `Object`.
5. `<>` and no brackets at all are different: `new Locker()` is a **raw type**, with type checking
   off.

---

## Concept 5 — Raw types and "unchecked" warnings

A **raw type** is a generic class used with **no type argument at all**: `Locker` instead of
`Locker<String>`, or `List` instead of `List<String>`.

### Why raw types exist

Generics were added in Java 5 (2004). Millions of lines of older code already said `List list = new
ArrayList();`. If Java had made that an error, all of that code would have stopped compiling
overnight. So Java kept it legal, for backwards compatibility only, and made the compiler **warn**
you when you use it.

A raw type isn't "the simple version" or "an older style". It's a generic class with **the type
checking switched off**.

`Locker.java` is in its own file, the same as in Concept 4:

```java
class Locker<T> {
    private T item;

    void put(T item) {
        this.item = item;
    }

    T get() {
        return item;
    }
}
```

### Step 1: A raw `Locker` accepts anything and forgets everything

```java
public class RawLockerDemo {

    public static void main(String[] args) {
        Locker locker = new Locker();
        locker.put("Ravi");
        locker.put(42);

        Object thing = locker.get();
        System.out.println(thing);

        String name = (String) locker.get();
        System.out.println(name);
    }
}
```

```bash
javac RawLockerDemo.java
java RawLockerDemo
```

Output:

```text
Note: RawLockerDemo.java uses unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.
42
Exception in thread "main" java.lang.ClassCastException: class java.lang.Integer cannot be cast to class java.lang.String ...
	at RawLockerDemo.main(RawLockerDemo.java:11)
```

What happened:

1. `Locker locker` has no `<...>`, so it's raw. **There's no T any more**, and Java treats every T
   as `Object`.
2. `put("Ravi")` and then `put(42)` were both accepted. The `42` replaced `"Ravi"`.
3. `get()` returns `Object`, so we needed a cast `(String)` to use it as text.
4. The real item was `42`, an `Integer`, so the cast was a lie: **`ClassCastException` at
   runtime**.

This is exactly the `Object` world from Concept 2. The raw type threw away everything generics gave
us.

The compiler won't even let you skip the cast:

```java
public class RawNoCast {

    public static void main(String[] args) {
        Locker locker = new Locker();
        locker.put("Ravi");

        String name = locker.get();
    }
}
```

```text
RawNoCast.java:7: error: incompatible types: Object cannot be converted to String
        String name = locker.get();
                                ^
```

On a raw `Locker`, `get()` returns `Object`, the same as the old box.

### Step 2: Reading the warning

By default `javac` only prints a short note:

```text
Note: RawLockerDemo.java uses unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.
```

Notice it's a **note**, not an error. The program still gets built. That's why people miss it. To
see the full warning, compile with `-Xlint:unchecked`:

```bash
javac -Xlint:unchecked RawLockerDemo.java
```

```text
RawLockerDemo.java:5: warning: [unchecked] unchecked call to put(T) as a member of the raw type Locker
        locker.put("Ravi");
                  ^
  where T is a type-variable:
    T extends Object declared in class Locker
RawLockerDemo.java:6: warning: [unchecked] unchecked call to put(T) as a member of the raw type Locker
        locker.put(42);
                  ^
  where T is a type-variable:
    T extends Object declared in class Locker
2 warnings
```

Word by word:

| Part | Meaning |
| --- | --- |
| `[unchecked]` | "I couldn't check this." |
| `unchecked call to put(T)` | "You called `put`, which expects a `T`..." |
| `as a member of the raw type Locker` | "...but you used `Locker` without saying what T is, so I have no idea what T should be." |
| `T extends Object` | "So I'm treating T as `Object`, which means anything goes." |

In plain English: **"I've stopped protecting you on this line."**

### Step 3: Why it's a *hole* — breaking a properly typed locker

So far, raw types just bring back the `Object` problem. It gets worse. A raw type can break
a locker that **was** properly typed:

```java
public class RawHole {

    static void addDefault(Locker locker) {
        locker.put("none");
    }

    public static void main(String[] args) {
        Locker<Integer> ticketCount = new Locker<>();
        ticketCount.put(5);

        addDefault(ticketCount);

        System.out.println("Still running...");

        Integer count = ticketCount.get();
        System.out.println(count + 1);
    }
}
```

```bash
javac -Xlint:unchecked RawHole.java
java RawHole
```

Output:

```text
RawHole.java:4: warning: [unchecked] unchecked call to put(T) as a member of the raw type Locker
        locker.put("none");
                  ^
  where T is a type-variable:
    T extends Object declared in class Locker
1 warning
Still running...
Exception in thread "main" java.lang.ClassCastException: class java.lang.String cannot be cast to class java.lang.Integer ...
	at RawHole.main(RawHole.java:15)
```

Follow it step by step:

1. `ticketCount` is a proper `Locker<Integer>`. It holds `5`. All good.
2. We pass it to `addDefault`, whose parameter is a **raw** `Locker`. Java allows that, with only a
   warning.
3. Inside `addDefault` the locker is raw, so `put("none")` is allowed. **A `String` is now inside
   a `Locker<Integer>`.**
4. `"Still running..."` prints. Nothing has noticed.
5. Line 15, `Integer count = ticketCount.get();`, crashes with `ClassCastException`.

Look at line 15. **There's no cast written on it.** You did everything right in `main`: you used
`Locker<Integer>` and never wrote `(Integer)`. It still crashed, because a different method, maybe
in a different file written by someone else, used a raw type and slipped a `String` in.

(Where does the cast come from? When you write `Integer count = ticketCount.get();`, the compiler
quietly inserts a hidden cast to `Integer` for you. Normally that cast can never fail, because the
compiler checked every `put`. The raw type let one `put` through unchecked, so the hidden cast
failed.)

That's why it's called a **hole in the type system**: the protection only works if *every* piece
of code that touches the locker is checked. One raw type anywhere and the guarantee is gone, and the
crash happens far away from the real mistake.

### Step 4: Raw `Locker` versus `Locker<Object>`

"But I really do want a locker that holds anything." Then say so: `Locker<Object>`. That's
**not** the same as a raw `Locker`.

```java
public class ObjectLockerDemo {

    public static void main(String[] args) {
        Locker<Object> locker = new Locker<>();
        locker.put("Ravi");
        locker.put(42);

        Object thing = locker.get();
        System.out.println(thing);
    }
}
```

```bash
javac -Xlint:unchecked ObjectLockerDemo.java
java ObjectLockerDemo
```

Output:

```text
42
```

It accepts anything too, but there are **no warnings**. You told the compiler honestly, "T is
`Object`", and it checks that.

Now the important difference. Try the `RawHole` trick with `Locker<Object>` instead of raw
`Locker`:

```java
public class ObjectLockerProtects {

    static void addDefault(Locker<Object> locker) {
        locker.put("none");
    }

    public static void main(String[] args) {
        Locker<Integer> ticketCount = new Locker<>();
        ticketCount.put(5);

        addDefault(ticketCount);
    }
}
```

```text
ObjectLockerProtects.java:11: error: incompatible types: Locker<Integer> cannot be converted to Locker<Object>
        addDefault(ticketCount);
                   ^
```

**The compiler blocks it.** A `Locker<Integer>` is not allowed where a `Locker<Object>` is
expected, precisely because the method could put a `String` in it. The hole is closed.

| | raw `Locker` | `Locker<Object>` |
| --- | --- | --- |
| What it says | "I'm not telling you what's inside" | "anything can be inside, and I mean it" |
| Holds anything? | yes | yes |
| Warnings | yes, `unchecked` | none |
| Can you pass a `Locker<Integer>` into it? | **yes**, silently | **no**, compile error |
| Can it sneak a `String` into a `Locker<Integer>`? | **yes**: the hole | **no** |

So a raw `List` is worse than `List<Object>`. Both hold anything, but `List<Object>` is still type
checked, and the raw `List` switches checking off and can corrupt other, properly typed lists.

### Step 5: Don't "fix" the warning by hiding it

You can silence the warning:

```java
@SuppressWarnings("unchecked")
```

That doesn't fix anything. The hole is still there; you've just stopped the compiler from telling
you about it. The warning is the compiler saying "the design is wrong here". The real fix is almost
always to **add the type argument**: change `Locker` to `Locker<Integer>` (or whatever type it
really holds).

### Summary

1. A **raw type** is a generic class used with no type argument: `Locker`, `List`.
2. It exists only so pre-2004 code still compiles. It's not a style to copy.
3. In a raw type, every T becomes `Object`, so you're back to casts and `ClassCastException`.
4. `javac` reports it as a quiet **note**. `-Xlint:unchecked` shows the full warning:
   "unchecked call ... as a member of the raw type". It means "I've stopped protecting you here."
5. It's a **hole**: a raw reference can put the wrong type into a properly typed object, and the
   crash happens later, on a line with no cast.
6. `Locker<Object>` is the honest version of "holds anything". It's still checked, and the compiler
   won't let a `Locker<Integer>` be passed into it.
7. Don't silence the warning with `@SuppressWarnings`. Add the missing type argument.

---

## Concept 6 — More than one type parameter, and the naming convention

`Locker<T>` had one blank. A class can have **as many blanks as it needs**, separated by commas.
You already know one with two: `Map<String, Integer>`.

### Step 1: A class with two blanks — `Pair<A, B>`

A pair holds two things, which may be of different types: a seat and its price, a first name and a
last name.

```java
class Pair<A, B> {
    private final A first;
    private final B second;

    Pair(A first, B second) {
        this.first = first;
        this.second = second;
    }

    A getFirst() {
        return first;
    }

    B getSecond() {
        return second;
    }
}

public class PairDemo {

    public static void main(String[] args) {
        Pair<String, Integer> seatAndPrice = new Pair<>("A1", 50);

        String seat = seatAndPrice.getFirst();
        Integer price = seatAndPrice.getSecond();

        System.out.println(seat);
        System.out.println(price + 10);

        Pair<String, String> firstAndLast = new Pair<>("Ravi", "Kumar");
        System.out.println(firstAndLast.getFirst() + " " + firstAndLast.getSecond());

        Pair<Integer, Boolean> rowAndVip = new Pair<>(7, true);
        System.out.println("Row " + rowAndVip.getFirst() + ", VIP: " + rowAndVip.getSecond());
    }
}
```

Output:

```text
A1
60
Ravi Kumar
Row 7, VIP: true
```

- `class Pair<A, B>` has **two type parameters**, `A` and `B`, separated by a comma.
- `first` is an `A`, `second` is a `B`. They can be different types.
- The constructor takes an `A` and a `B`. Generic classes can have constructors just like normal
  classes.

### Step 2: The blanks are filled **by position**

`Pair<String, Integer>` fills the blanks **in order**: the first type argument goes into the first
type parameter, and so on.

```text
class Pair< A,       B       >
            │        │
Pair<    String,  Integer    >

So:  A = String     B = Integer
     getFirst()  returns String
     getSecond() returns Integer
```

Replacing in your head, like in Concept 3:

```text
private final A first;         ──►   private final String first;
private final B second;        ──►   private final Integer second;
Pair(A first, B second)        ──►   Pair(String first, Integer second)
A getFirst()                   ──►   String getFirst()
B getSecond()                  ──►   Integer getSecond()
```

The other uses in Step 1:

| Written | A is | B is |
| --- | --- | --- |
| `Pair<String, Integer>` | `String` | `Integer` |
| `Pair<String, String>` | `String` | `String` (both blanks can get the same type) |
| `Pair<Integer, Boolean>` | `Integer` | `Boolean` |

Each blank is filled **independently**. One class, endless combinations.

### Step 3: Mixing up the order is caught at compile time

Pass the values the wrong way round:

```java
public class PairWrongOrder {

    public static void main(String[] args) {
        Pair<String, Integer> seatAndPrice = new Pair<>(50, "A1");
    }
}
```

(Same `Pair` class at the top of the file.)

```text
PairWrongOrder.java:22: error: incompatible types: cannot infer type arguments for Pair<>
        Pair<String, Integer> seatAndPrice = new Pair<>(50, "A1");
                                                     ^
    reason: inference variable A has incompatible bounds
      equality constraints: String
      lower bounds: Integer
  where A is a type-variable:
    A extends Object declared in class Pair
```

This is the kind of generics error message that looks frightening. Decode it one line at a time:

| Line of the error | Plain English |
| --- | --- |
| `cannot infer type arguments for Pair<>` | "I tried to fill in the diamond `<>` and couldn't." |
| `inference variable A has incompatible bounds` | "I got two different answers for what `A` should be." |
| `equality constraints: String` | "The left side says A **must be** `String`." |
| `lower bounds: Integer` | "But the first value you passed, `50`, is an `Integer`." |
| `where A is a type-variable ... declared in class Pair` | "`A` is the first blank of `Pair`." |

So the whole message just says: **"The first value should be a `String`, but you gave an
`Integer`."** Most generics errors are this simple underneath. Look for the `required` / `found`
or `equality constraints` / `lower bounds` lines, which tell you what was expected and what you
gave.

Taking a value out as the wrong type is caught too:

```java
public class PairWrongGet {

    public static void main(String[] args) {
        Pair<String, Integer> seatAndPrice = new Pair<>("A1", 50);
        Integer price = seatAndPrice.getFirst();
    }
}
```

```text
PairWrongGet.java:23: error: incompatible types: String cannot be converted to Integer
        Integer price = seatAndPrice.getFirst();
                                             ^
```

`getFirst()` returns an `A`, and `A` is `String` here, so it can't go into an `Integer` variable.

### Step 4: The letters are just names

`T`, `A`, `B` aren't special keywords. A type parameter is just a **name**, like a variable name.
This compiles and works perfectly:

```java
class Pair<Banana, Apple> {
    private final Banana first;
    private final Apple second;

    Pair(Banana first, Apple second) {
        this.first = first;
        this.second = second;
    }

    Banana getFirst() {
        return first;
    }

    Apple getSecond() {
        return second;
    }
}

public class AnyNames {

    public static void main(String[] args) {
        Pair<String, Integer> seatAndPrice = new Pair<>("A1", 50);
        System.out.println(seatAndPrice.getFirst() + " costs " + seatAndPrice.getSecond());
    }
}
```

Output:

```text
A1 costs 50
```

`Banana` and `Apple` work exactly like `A` and `B`. Nobody writes this, though. Java programmers
follow a **convention** (an agreed habit), so that everyone can read everyone else's code quickly.

### Step 5: The naming convention

Type parameters are written as **one capital letter**, so you can tell them apart from real classes
at a glance (`T` is a blank; `Seat` is a real class). Each letter hints at what the blank is for:

| Letter | Stands for | Used when | Real example from Java |
| --- | --- | --- | --- |
| `T` | **T**ype | general "some type", the default choice | `Optional<T>`, our `Locker<T>` |
| `E` | **E**lement | the things stored **in a collection** | `List<E>`, `Set<E>` |
| `K` | **K**ey | the key in a key → value lookup | `Map<K, V>` |
| `V` | **V**alue | the value in a key → value lookup | `Map<K, V>` |
| `R` | **R**esult | what a function **returns** | `Function<T, R>` |
| `S`, `U` | 2nd, 3rd type | when you need more general types after `T` | `BiFunction<T, U, R>` |
| `ID` | **ID** | the type of an identifier | `Repository<T, ID>` (Spring) |

`ID` breaks the one-letter habit. Spring uses it because `I` alone would be unclear.

Here's the convention in use, a tiny version of one entry in a map:

```java
class Entry<K, V> {
    private final K key;
    private final V value;

    Entry(K key, V value) {
        this.key = key;
        this.value = value;
    }

    K getKey() {
        return key;
    }

    V getValue() {
        return value;
    }
}

public class EntryDemo {

    public static void main(String[] args) {
        Entry<String, Integer> stock = new Entry<>("A1", 3);

        System.out.println("Key: " + stock.getKey());
        System.out.println("Value: " + stock.getValue());
    }
}
```

Output:

```text
Key: A1
Value: 3
```

Exactly the same mechanics as `Pair<A, B>`, but the names `K` and `V` tell any reader immediately:
"this is a key and a value". When you see `Map<K, V>` in Java's documentation, you'll know `K` is
the key type and `V` the value type, before reading a word of explanation.

### How this helps you read real code

| You see | You immediately know |
| --- | --- |
| `interface List<E>` | E is the type of the elements in the list |
| `interface Map<K, V>` | K is the key type, V is the value type |
| `interface Function<T, R>` | it takes a T and gives back an R (result) |
| `interface Repository<T, ID>` | it stores T's, looked up by an ID |

### Summary

1. A generic class can have several type parameters: `class Pair<A, B>`.
2. Type arguments fill them **by position**: in `Pair<String, Integer>`, A = `String` and
   B = `Integer`.
3. Each blank is independent. They can be different types or the same type.
4. Mistakes (wrong order, wrong type out) are compile-time errors. Long generics errors usually just
   mean "expected X, found Y". Look for those two lines.
5. Type parameter names are just names. By **convention**: `T` type, `E` element, `K` key,
   `V` value, `R` result. They carry no magic, they just make code faster to read.

---

## Concept 7 — Reading generic types aloud

Types like `Map<String, List<Seat>>` look like noise at first. But there are only **two reading
rules**, and once you know them every generic type reads like a normal sentence.

### Rule 1: `X<Y>` reads as "an X of Y"

| Type | Read aloud |
| --- | --- |
| `List<Seat>` | "a list **of** seats" |
| `Set<String>` | "a set **of** strings" |
| `Locker<Integer>` | "a locker **of** integers" |
| `Optional<Seat>` | "an optional seat" (maybe a seat, maybe nothing) |

### Rule 2: `Map<K, V>` reads as "a map **from** K **to** V"

A map looks things up: you give it a key, it gives you back a value.

| Type | Read aloud | Meaning |
| --- | --- | --- |
| `Map<String, Integer>` | "a map from strings to integers" | give it text, get a number back |
| `Map<String, Seat>` | "a map from strings to seats" | give it a seat id like `"A1"`, get the `Seat` |
| `Map<Seat, Boolean>` | "a map from seats to booleans" | give it a seat, find out true/false (for example, "is it sold?") |

### Nested types: read from the outside in

When there are brackets inside brackets, read the **outer** type first, then go one layer inward
at a time, and keep applying the two rules.

`Map<String, List<Seat>>`:

```text
Map<String, List<Seat>>
│    │       │    │
│    │       │    └── "...of seats"
│    │       └─────── "...lists..."
│    └─────────────── "...from strings..."
└──────────────────── "a map..."

= "a map from strings to lists of seats"
```

Word order: `Map<String, ...>` gives "a map from strings to ...", then `List<Seat>` gives "lists of
seats".

### What does one entry hold?

A map is made of **entries**: one key paired with one value. To understand a map type, ask: "What's
the key? What's the value?"

For `Map<String, List<Seat>>`:

- **key** = a `String`, for example the row name `"A"`
- **value** = a `List<Seat>`, all the seats in that row

So **one entry** is "one row name, and the list of all seats in that row".

```text
   key (String)       value (List<Seat>)
   ────────────       ───────────────────────────
   "A"          ──►   [ Seat A1, Seat A2 ]
   "B"          ──►   [ Seat B1 ]
```

### Step 1: See it in real code, peeling one layer at a time

Here, `Seat` is a **record** (Task F2.8): a short way to declare a class that just holds data.
`record Seat(String id) { }` gives you a constructor `new Seat("A1")`, a getter `id()` and a
readable `toString()`.

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

record Seat(String id) { }

public class ReadAloudDemo {

    public static void main(String[] args) {
        List<Seat> rowA = new ArrayList<>();
        rowA.add(new Seat("A1"));
        rowA.add(new Seat("A2"));

        List<Seat> rowB = new ArrayList<>();
        rowB.add(new Seat("B1"));

        Map<String, List<Seat>> seatsByRow = new HashMap<>();
        seatsByRow.put("A", rowA);
        seatsByRow.put("B", rowB);

        System.out.println("The whole map:");
        System.out.println(seatsByRow);

        List<Seat> seatsInA = seatsByRow.get("A");
        System.out.println("One value (a list of seats):");
        System.out.println(seatsInA);

        Seat firstSeat = seatsInA.get(0);
        System.out.println("One seat:");
        System.out.println(firstSeat);

        String id = firstSeat.id();
        System.out.println("Its id:");
        System.out.println(id);
    }
}
```

Output:

```text
The whole map:
{A=[Seat[id=A1], Seat[id=A2]], B=[Seat[id=B1]]}
One value (a list of seats):
[Seat[id=A1], Seat[id=A2]]
One seat:
Seat[id=A1]
Its id:
A1
```

Each `.get(...)` removes **one layer** of the type:

| Code | Type of the result | Read aloud |
| --- | --- | --- |
| `seatsByRow` | `Map<String, List<Seat>>` | a map from strings to lists of seats |
| `seatsByRow.get("A")` | `List<Seat>` | a list of seats |
| `seatsInA.get(0)` | `Seat` | a seat |
| `firstSeat.id()` | `String` | a string |

In the printed map, `{ }` is the map, `A=` is a key, and `[ ]` is a list value.

The compiler tracks every layer. Skip a layer, and it tells you:

```java
import java.util.HashMap;
import java.util.List;
import java.util.Map;

record Seat(String id) { }

public class WrongLayer {

    public static void main(String[] args) {
        Map<String, List<Seat>> seatsByRow = new HashMap<>();

        Seat seat = seatsByRow.get("A");
    }
}
```

```text
WrongLayer.java:12: error: incompatible types: List<Seat> cannot be converted to Seat
        Seat seat = seatsByRow.get("A");
                                  ^
```

"Getting from this map gives you a **list** of seats, not one seat." Reading the type aloud tells you
the same thing before you even compile.

### Step 2: A list of lists

`List<List<String>>` reads as "a list **of lists of** strings". Picture a grid: a list of rows, where
each row is a list of seat names.

```java
import java.util.List;

public class GridDemo {

    public static void main(String[] args) {
        List<List<String>> grid = List.of(
            List.of("A1", "A2", "A3"),
            List.of("B1", "B2")
        );

        System.out.println(grid);

        List<String> secondRow = grid.get(1);
        System.out.println(secondRow);

        String seat = secondRow.get(0);
        System.out.println(seat);

        System.out.println(grid.get(0).get(2));
    }
}
```

Output:

```text
[[A1, A2, A3], [B1, B2]]
[B1, B2]
B1
A3
```

- `List.of(...)` is a quick way to build a small list that can't be changed afterwards.
- `grid.get(1)` gives a `List<String>`: the second row.
- `secondRow.get(0)` gives a `String`: the first seat in that row.
- `grid.get(0).get(2)` does both in one line: row 0, seat 2, which is `A3`.

### Step 3: A map of maps

`Map<String, Map<String, Integer>>` reads as "a map from strings to **maps from strings to
integers**". For example: venue name, then tier name, then price.

```java
import java.util.HashMap;
import java.util.Map;

public class MapOfMapsDemo {

    public static void main(String[] args) {
        Map<String, Integer> wembleyPrices = new HashMap<>();
        wembleyPrices.put("GENERAL", 50);
        wembleyPrices.put("VIP", 150);

        Map<String, Map<String, Integer>> pricesByVenue = new HashMap<>();
        pricesByVenue.put("WEMBLEY", wembleyPrices);

        System.out.println(pricesByVenue);

        Map<String, Integer> prices = pricesByVenue.get("WEMBLEY");
        Integer vipPrice = prices.get("VIP");
        System.out.println(vipPrice);
    }
}
```

Output:

```text
{WEMBLEY={GENERAL=50, VIP=150}}
150
```

One entry: key `"WEMBLEY"`, value **another whole map** `{GENERAL=50, VIP=150}`. Two `.get`s peel
the two layers: venue gives a price map, and tier gives the price.

### More practice — read these aloud

| Type | Read aloud | One item / entry holds |
| --- | --- | --- |
| `List<Seat>` | a list of seats | one seat |
| `Set<SeatId>` | a set of seat ids | one seat id |
| `Map<String, Seat>` | a map from strings to seats | a string key, and one seat |
| `Map<String, List<Seat>>` | a map from strings to lists of seats | a string key, and a whole list of seats |
| `List<List<String>>` | a list of lists of strings | one inner list of strings |
| `Map<Long, Set<String>>` | a map from longs to sets of strings | a number key, and a set of strings |
| `Optional<List<Seat>>` | an optional list of seats | maybe a list of seats, maybe nothing |
| `Pair<String, Integer>` | a pair of a string and an integer | a string and an integer |

### Summary

1. `X<Y>` reads as "an X of Y": `List<Seat>` is "a list of seats".
2. `Map<K, V>` reads as "a map from K to V": `Map<String, Seat>` is "a map from strings to seats".
3. For nested types, read from the outside in, one layer at a time.
4. For a map, ask "what's one entry?": one key of type K, paired with one value of type V. The value
   can itself be a whole list or map.
5. Each `.get(...)` peels off one layer, and the compiler knows the exact type at every layer.

---

## Concept 8 — Generic methods

So far the blank `T` belonged to a whole **class** (`class Locker<T>`). A single **method** can also
have its own blank, even inside a class that isn't generic at all. This is how most of Java's
helper methods (like `List.of` and `Map.of`) are written.

### Step 1: The problem — one method per type

We want a helper that gives back the first item of a list. Without generics:

```java
import java.util.List;

record Seat(String id) { }

public class CopyPasteMethods {

    static String firstString(List<String> list) {
        return list.get(0);
    }

    static Seat firstSeat(List<Seat> list) {
        return list.get(0);
    }

    public static void main(String[] args) {
        List<String> names = List.of("Ravi", "Asha");
        List<Seat> seats = List.of(new Seat("A1"), new Seat("A2"));

        System.out.println(firstString(names));
        System.out.println(firstSeat(seats));
    }
}
```

Output:

```text
Ravi
Seat[id=A1]
```

The two methods are identical except for the type, the same copying problem as `StringLocker` and
`IntegerLocker` in Concept 3. The other option, returning `Object`, brings back casts and
`ClassCastException` (Concept 2).

### Step 2: A generic method

```java
import java.util.List;

record Seat(String id) { }

public class GenericMethodDemo {

    static <T> T firstOf(List<T> list) {
        return list.get(0);
    }

    public static void main(String[] args) {
        List<String> names = List.of("Ravi", "Asha");
        List<Seat> seats = List.of(new Seat("A1"), new Seat("A2"));
        List<Integer> prices = List.of(50, 150);

        String name = firstOf(names);
        Seat seat = firstOf(seats);
        Integer price = firstOf(prices);

        System.out.println(name.toUpperCase());
        System.out.println(seat.id());
        System.out.println(price + 1);
    }
}
```

Output:

```text
RAVI
A1
51
```

**One method, three types, no casts.** `name.toUpperCase()`, `seat.id()` and `price + 1` all work
directly, because the compiler knows exactly what came back each time.

### Step 3: Reading the method's first line

```java
static <T> T firstOf(List<T> list)
```

Take it piece by piece:

| Part | Meaning |
| --- | --- |
| `static` | a normal static method, nothing new |
| `<T>` | **"This method has its own blank called T."** This is where T is *declared*. |
| `T` (the second one) | the **return type**: "this method gives back a T" |
| `firstOf` | the method name |
| `List<T> list` | the parameter: "a list of T" |

Read the whole line aloud: **"For any type T, `firstOf` takes a list of T and gives back a T."**

The rule to remember: **for a generic method, the `<T>` goes just before the return type.** That's
how Java knows `T` is a new blank and not a class called `T`.

```text
class Locker<T>                      blank declared after the CLASS name, for the whole class
static <T> T firstOf(List<T> list)   blank declared before the RETURN TYPE, for this method only
```

Notice `GenericMethodDemo` itself is **not** generic (no `<T>` after the class name). Only the method
is.

### Step 4: The compiler fills in T for you

With a generic class you wrote the type argument yourself: `Locker<String>`. With a generic method
you usually don't. The compiler looks at **what you passed in** and works T out (it **infers** it, as
with the diamond in Concept 4):

| Call | You passed | So T is | So it returns |
| --- | --- | --- | --- |
| `firstOf(names)` | a `List<String>` | `String` | `String` |
| `firstOf(seats)` | a `List<Seat>` | `Seat` | `Seat` |
| `firstOf(prices)` | a `List<Integer>` | `Integer` | `Integer` |

You *can* write T explicitly if you want to. The syntax is unusual: the type goes in front of the
method name, after a dot:

```java
import java.util.List;

public class ExplicitTypeArg {

    static <T> T firstOf(List<T> list) {
        return list.get(0);
    }

    public static void main(String[] args) {
        List<String> names = List.of("Ravi", "Asha");

        String a = firstOf(names);
        String b = ExplicitTypeArg.<String>firstOf(names);

        System.out.println(a);
        System.out.println(b);
    }
}
```

Output:

```text
Ravi
Ravi
```

Both lines do exactly the same thing. You'll almost never need the explicit form, but now you'll
recognise it if you see it.

### Step 5: Still checked at compile time

```java
import java.util.List;

record Seat(String id) { }

public class GenericMethodWrong {

    static <T> T firstOf(List<T> list) {
        return list.get(0);
    }

    public static void main(String[] args) {
        List<String> names = List.of("Ravi", "Asha");

        Seat seat = firstOf(names);
    }
}
```

```text
GenericMethodWrong.java:14: error: incompatible types: inference variable T has incompatible bounds
        Seat seat = firstOf(names);
                           ^
    equality constraints: String
    upper bounds: Seat,Object
  where T is a type-variable:
    T extends Object declared in method <T>firstOf(List<T>)
```

Decode it the same way as in Concept 6:

| Error line | Plain English |
| --- | --- |
| `inference variable T has incompatible bounds` | "I got two different answers for T." |
| `equality constraints: String` | "You passed a list of strings, so T must be `String`." |
| `upper bounds: Seat,Object` | "But you want to store the result in a `Seat`, so T would need to be a `Seat`." |
| `declared in method <T>firstOf(List<T>)` | "This T is the method's own blank." |

"The first item of a list of strings is a string, not a seat." Caught before the program runs.

### Step 6: Two type parameters on a method

Just like classes, methods can have several blanks:

```java
import java.util.HashMap;
import java.util.Map;

public class TwoTypeParamsMethod {

    static <K, V> Map<K, V> singleEntry(K key, V value) {
        Map<K, V> map = new HashMap<>();
        map.put(key, value);
        return map;
    }

    public static void main(String[] args) {
        Map<String, Integer> stock = singleEntry("A1", 3);
        Map<Integer, Boolean> vipRows = singleEntry(7, true);

        System.out.println(stock);
        System.out.println(vipRows);

        Integer count = stock.get("A1");
        System.out.println(count + 1);
    }
}
```

Output:

```text
{A1=3}
{7=true}
4
```

Read `static <K, V> Map<K, V> singleEntry(K key, V value)` aloud: **"For any types K and V,
`singleEntry` takes a key of type K and a value of type V, and gives back a map from K to V."**

- `singleEntry("A1", 3)`: K = `String`, V = `Integer`, so it returns `Map<String, Integer>`.
- `singleEntry(7, true)`: K = `Integer`, V = `Boolean`, so it returns `Map<Integer, Boolean>`.

### Step 7: You already use generic methods from Java's library

```java
import java.util.List;
import java.util.Map;

public class JdkGenericMethods {

    public static void main(String[] args) {
        List<String> seats = List.of("A1", "A2", "A3");
        Map<String, Integer> prices = Map.of("VIP", 150);

        System.out.println(seats);
        System.out.println(prices);
        System.out.println(prices.get("VIP") + 10);
    }
}
```

Output:

```text
[A1, A2, A3]
{VIP=150}
160
```

In Java's own source code, these two methods are declared like this:

```java
static <E> List<E> of(E e1, E e2, E e3)
static <K, V> Map<K, V> of(K k1, V v1)
```

You can read them now:

- `List.of`: "For any type E, `of` takes three E's and gives back a list of E." You passed three
  strings, so E = `String` and you get a `List<String>`.
- `Map.of`: "For any types K and V, `of` takes a key K and a value V and gives back a map from K to
  V." You passed `"VIP"` and `150`, so you get a `Map<String, Integer>`.

That's why `prices.get("VIP") + 10` works with no cast: the compiler knows the value is an
`Integer`.

### Step 8: The real fix when the compiler fights you

The task warns about this: when the types don't line up, people often "fix" it with a cast or
`@SuppressWarnings`. That hides the problem and brings back runtime crashes (Concept 2's
`HiddenBadCast`, Concept 5's `RawHole`).

Almost always, the real fix is a **type parameter**: on the class (Concept 3) if the whole class
works with that type, or on the method (this concept) if only one method does. That way the compiler
can follow the type through, and no cast is needed.

### Summary

1. A **generic method** has its own type parameter, declared just **before the return type**:
   `static <T> T firstOf(List<T> list)`.
2. Read it as: "for any type T, `firstOf` takes a list of T and gives back a T."
3. The class doesn't need to be generic. The blank belongs to that one method.
4. The compiler **infers** T from the arguments you pass, so you rarely write it. (The explicit
   form is `ClassName.<String>firstOf(...)`.)
5. Methods can have several type parameters: `static <K, V> Map<K, V> singleEntry(K key, V value)`.
6. `List.of` and `Map.of` are generic methods. That's why they return correctly typed collections.
7. When types don't line up, add a type parameter instead of a cast.

---

## Concept 9 — Generic interfaces, and filling in the blanks with `extends`

Interfaces can have type parameters too, in exactly the same way as classes. This is what
`Repository<T, ID>` in Subtask 7 is, and what Spring's repositories are built on.

### Step 1: Reminder — a normal interface (Task F2.6)

```java
interface Greeter {
    String greet(String name);
}

class PoliteGreeter implements Greeter {
    public String greet(String name) {
        return "Good morning, " + name;
    }
}

public class PlainInterfaceDemo {

    public static void main(String[] args) {
        Greeter greeter = new PoliteGreeter();
        System.out.println(greeter.greet("Ravi"));
    }
}
```

Output:

```text
Good morning, Ravi
```

- An **interface** lists methods that a class promises to have, but doesn't write their code.
- `implements Greeter` means "`PoliteGreeter` promises to provide `greet`". It must write the code.
- The method is `public` in the class because interface methods are always public.

### Step 2: A generic interface

A **converter** turns one thing into another. What goes in and what comes out can be any types, so
both are blanks:

```java
interface Converter<T, R> {
    R convert(T input);
}
```

Read it aloud: **"A converter from T to R has a method `convert` that takes a T and gives back an
R."** (T = the type that goes in, R = the **r**esult, by the naming convention from Concept 6.)

It's declared just like a generic class: the blanks go in `< >` after the name.

### Step 3: Implementing it — the class fills in the blanks

```java
interface Converter<T, R> {
    R convert(T input);
}

record Seat(String id) { }

class SeatIdToSeat implements Converter<String, Seat> {
    public Seat convert(String input) {
        return new Seat(input);
    }
}

class TextLength implements Converter<String, Integer> {
    public Integer convert(String input) {
        return input.length();
    }
}

public class ConverterDemo {

    public static void main(String[] args) {
        Converter<String, Seat> toSeat = new SeatIdToSeat();
        Seat seat = toSeat.convert("A1");
        System.out.println(seat);
        System.out.println(seat.id());

        Converter<String, Integer> toLength = new TextLength();
        Integer length = toLength.convert("Ravi");
        System.out.println(length + 1);
    }
}
```

Output:

```text
Seat[id=A1]
A1
5
```

Look at `class SeatIdToSeat implements Converter<String, Seat>`. The class **fills in the blanks**
right there in the `implements` line: T = `String`, R = `Seat`. Then the "replace in your head" trick
from Concept 3 tells you exactly which method the class must write:

```text
In the interface:              R    convert(T      input)
With T = String, R = Seat:     Seat convert(String input)    ◄── what SeatIdToSeat must write
With T = String, R = Integer:  Integer convert(String input) ◄── what TextLength must write
```

**One interface, two implementations, each with its own types.** And the variables are declared with
the interface type: `Converter<String, Seat> toSeat = new SeatIdToSeat();`, read aloud as "a
converter from strings to seats".

### Step 4: The compiler checks the class wrote the right method

Suppose `TextLength` promises `Converter<String, Integer>` but writes the method with the wrong
parameter type:

```java
interface Converter<T, R> {
    R convert(T input);
}

class TextLength implements Converter<String, Integer> {
    public Integer convert(Integer input) {
        return input;
    }
}

public class WrongSignature {

    public static void main(String[] args) {
        System.out.println(new TextLength().convert(5));
    }
}
```

```text
WrongSignature.java:5: error: TextLength is not abstract and does not override abstract method convert(String) in Converter
class TextLength implements Converter<String, Integer> {
^
```

Read the error: **"does not override abstract method `convert(String)`"**. The compiler itself shows
the method with the blank already filled in: it's looking for `convert(String)`, because T =
`String`. The class wrote `convert(Integer)` instead, so the promise isn't kept.

("Is not abstract" just means "this is a normal class that you can create objects from, so it must
provide every method its interface asks for.")

### Step 5: And it checks the people using it

```java
interface Converter<T, R> {
    R convert(T input);
}

class TextLength implements Converter<String, Integer> {
    public Integer convert(String input) {
        return input.length();
    }
}

public class WrongUse {

    public static void main(String[] args) {
        Converter<String, Integer> toLength = new TextLength();
        toLength.convert(42);
    }
}
```

```text
WrongUse.java:15: error: incompatible types: int cannot be converted to String
        toLength.convert(42);
                         ^
```

A converter from strings to integers only accepts strings. Both sides are checked: the class that
**implements** the interface, and the code that **calls** it.

### Step 6: An interface that extends a generic interface and fills in the blanks

This is the step Subtask 7 is about. An interface can **extend** a generic interface and fill in its
blanks, without adding anything else:

```java
interface Converter<T, R> {
    R convert(T input);
}

record Seat(String id) { }

interface SeatConverter extends Converter<String, Seat> {
}

class SimpleSeatConverter implements SeatConverter {
    public Seat convert(String input) {
        return new Seat(input.toUpperCase());
    }
}

public class ExtendsFillsBlanks {

    public static void main(String[] args) {
        SeatConverter converter = new SimpleSeatConverter();
        Seat seat = converter.convert("b7");
        System.out.println(seat);
    }
}
```

Output:

```text
Seat[id=B7]
```

Look closely at this line:

```java
interface SeatConverter extends Converter<String, Seat> {
}
```

Its body is **empty**, yet it has a method. `SeatConverter` gets everything from `Converter`, with
T = `String` and R = `Seat` filled in. So, without writing a single method, `SeatConverter` now has:

```java
Seat convert(String input);
```

What that one line did to the method:

| | Method signature |
| --- | --- |
| In `Converter<T, R>` (generic) | `R convert(T input)` |
| In `SeatConverter` (blanks filled) | `Seat convert(String input)` |

And `SeatConverter` itself has **no** type parameters. Anyone using it doesn't see any `< >` at
all: `SeatConverter converter = ...`, and `converter.convert("b7")` returns a `Seat`. The compiler
still checks it:

```java
public class ExtendsWrong {

    public static void main(String[] args) {
        SeatConverter converter = new SimpleSeatConverter();
        Integer number = converter.convert("b7");
    }
}
```

(Same `Converter`, `Seat`, `SeatConverter` and `SimpleSeatConverter` at the top of the file.)

```text
ExtendsWrong.java:20: error: incompatible types: Seat cannot be converted to Integer
        Integer number = converter.convert("b7");
                                          ^
```

The compiler knows `SeatConverter`'s `convert` returns a `Seat`, even though `SeatConverter`'s body
is empty.

Why bother? It gives the specific version a **clear name**. `SeatConverter` says what it is, and is
much easier to read than `Converter<String, Seat>` everywhere. You write the general rule once
(`Converter<T, R>`), then create specific, named versions with one line each.

### Step 7: Or keep a blank open

A class doesn't have to fill in every blank. It can pass its own blank through:

```java
interface Converter<T, R> {
    R convert(T input);
}

class NoChange<T> implements Converter<T, T> {
    public T convert(T input) {
        return input;
    }
}

public class KeepBlankOpen {

    public static void main(String[] args) {
        Converter<String, String> same = new NoChange<>();
        System.out.println(same.convert("A1"));

        Converter<Integer, Integer> sameNumber = new NoChange<>();
        System.out.println(sameNumber.convert(42) + 1);
    }
}
```

Output:

```text
A1
43
```

`class NoChange<T> implements Converter<T, T>`: "`NoChange` has its own blank T, and it's a
converter from T to the **same** T." The blank is only filled when someone creates a
`NoChange<String>` or a `NoChange<Integer>`.

| How you write it | Blanks after this line |
| --- | --- |
| `class SeatIdToSeat implements Converter<String, Seat>` | none: fully filled in |
| `interface SeatConverter extends Converter<String, Seat>` | none: fully filled in |
| `class NoChange<T> implements Converter<T, T>` | one (T), filled in later by whoever uses it |

### You'll meet these generic interfaces everywhere

| Interface | Read aloud | Its main method |
| --- | --- | --- |
| `List<E>` | a list of E | `E get(int index)` |
| `Map<K, V>` | a map from K to V | `V get(Object key)` |
| `Comparable<T>` | something that can be compared to a T | `int compareTo(T other)` |
| `Function<T, R>` | a function from T to R (Java's built-in version of our `Converter`) | `R apply(T t)` |
| `JpaRepository<T, ID>` (Spring) | a repository of T's, looked up by ID | `Optional<T> findById(ID id)` and more |

In Phase 1 you'll write `interface SeatRepository extends JpaRepository<Seat, String> {}`, exactly
the pattern from Step 6.

### Summary

1. Interfaces can be generic, just like classes: `interface Converter<T, R> { R convert(T input); }`.
2. A class fills in the blanks in its `implements` line:
   `class TextLength implements Converter<String, Integer>`. It must then write the method with
   those types: `Integer convert(String input)`.
3. The compiler checks both sides: the class must write the right method ("does not override
   abstract method `convert(String)`"), and callers must pass and receive the right types.
4. An interface can `extend` a generic interface and fill in its blanks:
   `interface SeatConverter extends Converter<String, Seat> {}`. With an empty body, it still has
   `Seat convert(String input)`.
5. A class can also keep a blank open: `class NoChange<T> implements Converter<T, T>`.
