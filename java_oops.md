# Java OOP Concepts — Revision Guide

A step-by-step reference for Object-Oriented Programming in Java.  
Topics are added as we cover them one by one.

---

## Table of Contents

1. [Class and Object](#1-class-and-object) ✅
2. [Encapsulation](#2-encapsulation) ✅
3. [Inheritance](#3-inheritance) ✅
4. [Polymorphism](#4-polymorphism) ✅
5. [Abstraction](#5-abstraction) ✅
6. [Interfaces vs Abstract Classes](#6-interfaces-vs-abstract-classes) ✅
7. [Association, Aggregation & Composition](#7-association-aggregation--composition) ✅

---

## 1. Class and Object

### Definition

| Term | Meaning |
|------|---------|
| **Class** | A blueprint that defines fields (data) and methods (behavior) |
| **Object** | A real instance created from a class |
| **Field** | Variable that holds an object's data/state |
| **Method** | Function that defines an object's behavior |
| **Constructor** | Special method called when `new` creates an object |

**Analogy:** `Car` is a class (the design). Your actual Honda is an object (the real thing).

### Example

```java
// Class = blueprint
class Car {
    // Fields (state/data)
    String color;
    int speed;

    // Constructor - called when object is created
    public Car(String color) {
        this.color = color;
        this.speed = 0;
    }

    // Method (behavior)
    public void accelerate() {
        speed += 10;
        System.out.println(color + " car is now at " + speed + " km/h");
    }
}

public class Main {
    public static void main(String[] args) {
        // Creating objects (instances) from the class
        Car car1 = new Car("Red");
        Car car2 = new Car("Blue");

        car1.accelerate();  // Red car is now at 10 km/h
        car2.accelerate();  // Blue car is now at 10 km/h
    }
}
```

### Key Points

- `new Car("Red")` allocates memory and calls the constructor.
- `car1` and `car2` are **separate objects** — each has its own `color` and `speed`.
- `this.color` refers to the **current object's** field (distinguishes it from a parameter with the same name).

### Mental Model

```
Class Car (blueprint)
    ├── color
    ├── speed
    └── accelerate()

Objects:
    car1 → Red,  speed 10
    car2 → Blue, speed 10
```

### Self-Check

1. What is the difference between a **class** and an **object**?
2. What does **`new`** do?
3. Why do `car1` and `car2` have independent state even though they share the same class?

---

## 2. Encapsulation

### Definition

**Encapsulation** = wrapping data (fields) and the code that operates on it (methods) into a single unit (class), and **restricting direct access** to the internal data.

Think of it as: **hide the internals, expose only what's necessary.**

| Term | Meaning |
|------|---------|
| **Access Modifiers** | Keywords that control who can see a field/method |
| **Getter** | A public method that **reads** a private field |
| **Setter** | A public method that **writes** to a private field (with optional validation) |

### Access Modifiers in Java

| Modifier | Same Class | Same Package | Subclass | Everywhere |
|----------|:----------:|:------------:|:--------:|:----------:|
| `private` | ✅ | ❌ | ❌ | ❌ |
| *(default)* | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

### Bad Example — No Encapsulation

```java
class BankAccount {
    public double balance;  // anyone can directly change this!
}

public class Main {
    public static void main(String[] args) {
        BankAccount acc = new BankAccount();
        acc.balance = -5000;  // invalid state — no one should have negative balance!
    }
}
```

**Problem:** Anyone can set `balance` to garbage values. No validation, no control.

### Good Example — With Encapsulation

```java
class BankAccount {
    private double balance;  // hidden from outside

    public BankAccount(double initialBalance) {
        if (initialBalance >= 0) {
            this.balance = initialBalance;
        }
    }

    // Getter — read access
    public double getBalance() {
        return balance;
    }

    // Controlled write access with validation
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
            System.out.println("Deposited: " + amount);
        } else {
            System.out.println("Invalid deposit amount");
        }
    }

    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
            System.out.println("Withdrew: " + amount);
        } else {
            System.out.println("Invalid withdrawal");
        }
    }
}

public class Main {
    public static void main(String[] args) {
        BankAccount acc = new BankAccount(1000);

        acc.deposit(500);           // Deposited: 500
        acc.withdraw(200);          // Withdrew: 200
        System.out.println(acc.getBalance());  // 1300.0

        // acc.balance = -5000;     // COMPILE ERROR — balance is private!
        acc.withdraw(9999);         // Invalid withdrawal — validation prevents this
    }
}
```

### Why Encapsulation Matters

| Benefit | How |
|---------|-----|
| **Data protection** | Private fields can't be set to invalid values |
| **Validation** | Setters/methods enforce business rules before changing state |
| **Flexibility** | Internal implementation can change without breaking outside code |
| **Debugging** | All changes go through known methods — easy to trace |

### Mental Model

```
Without Encapsulation:          With Encapsulation:

  Outside Code                    Outside Code
      │                               │
      ▼                               ▼
  ┌─────────┐                   ┌───────────────┐
  │ balance  │  (direct access) │ deposit()     │ ← validates first
  └─────────┘                   │ withdraw()    │ ← validates first
                                │ getBalance()  │ ← read-only access
                                │   ┌────────┐  │
                                │   │balance │  │ ← hidden inside
                                │   └────────┘  │
                                └───────────────┘
```

### Self-Check

1. What does `private` mean and why do we use it for fields?
2. What is the difference between a **getter** and a **setter**?
3. In the `BankAccount` example, why can't someone just do `acc.balance = -5000`?
4. What benefit does validation inside `withdraw()` give us?

---

## 3. Inheritance

### Definition

**Inheritance** = a mechanism where a new class (child/subclass) acquires the fields and methods of an existing class (parent/superclass).

Think of it as: **"is-a" relationship.** A `Dog` *is an* `Animal`. A `Car` *is a* `Vehicle`.

| Term | Meaning |
|------|---------|
| **Parent / Superclass** | The existing class being inherited from |
| **Child / Subclass** | The new class that inherits |
| **`extends`** | Keyword used to inherit from a parent |
| **`super`** | Refers to the parent class (call parent constructor/methods) |
| **Method Overriding** | Child provides its own version of a parent's method |

### Why Use Inheritance?

- **Code reuse** — write common logic once in the parent, all children get it free
- **Hierarchy** — model real-world relationships (Animal → Dog, Cat)
- **Extensibility** — add new types without modifying existing code

### Example — Animal Hierarchy

```java
// Parent class
class Animal {
    String name;

    public Animal(String name) {
        this.name = name;
    }

    public void eat() {
        System.out.println(name + " is eating");
    }

    public void makeSound() {
        System.out.println(name + " makes a generic sound");
    }
}

// Child class — inherits everything from Animal
class Dog extends Animal {

    public Dog(String name) {
        super(name);  // calls parent constructor
    }

    // Override — provide dog-specific behavior
    @Override
    public void makeSound() {
        System.out.println(name + " barks: Woof! Woof!");
    }

    // New method only dogs have
    public void fetch() {
        System.out.println(name + " fetches the ball");
    }
}

// Another child class
class Cat extends Animal {

    public Cat(String name) {
        super(name);
    }

    @Override
    public void makeSound() {
        System.out.println(name + " meows: Meow!");
    }
}

public class Main {
    public static void main(String[] args) {
        Dog dog = new Dog("Buddy");
        Cat cat = new Cat("Whiskers");

        dog.eat();        // Buddy is eating         (inherited from Animal)
        dog.makeSound();  // Buddy barks: Woof! Woof! (overridden)
        dog.fetch();      // Buddy fetches the ball   (Dog-only method)

        cat.eat();        // Whiskers is eating       (inherited from Animal)
        cat.makeSound();  // Whiskers meows: Meow!    (overridden)
    }
}
```

### What `super` Does

```java
class Dog extends Animal {
    public Dog(String name) {
        super(name);  // calls Animal(String name) constructor
    }
}
```

- `super(...)` **must** be the first line in a child constructor
- It passes arguments up to the parent constructor
- You can also call parent methods with `super.methodName()`

### Types of Inheritance in Java

| Type | Example | Allowed? |
|------|---------|----------|
| **Single** | Dog extends Animal | ✅ |
| **Multilevel** | Puppy extends Dog extends Animal | ✅ |
| **Hierarchical** | Dog extends Animal, Cat extends Animal | ✅ |
| **Multiple** (via classes) | Dog extends Animal, Pet | ❌ (use interfaces instead) |

### What Gets Inherited?

| Member | Inherited? |
|--------|-----------|
| `public` methods/fields | ✅ Yes |
| `protected` methods/fields | ✅ Yes |
| `private` methods/fields | ❌ No (exist but not directly accessible) |
| Constructors | ❌ No (but called via `super`) |

### Mental Model

```
        Animal (parent)
        ├── name
        ├── eat()
        └── makeSound()
           │
     ┌─────┴─────┐
     ▼           ▼
   Dog          Cat
   ├── makeSound() [overridden]   ├── makeSound() [overridden]
   └── fetch()    [new]           └── (no new methods)
```

### Self-Check

1. What does `extends` mean?
2. What does `super(name)` do inside a child constructor?
3. If `Dog` doesn't override `eat()`, what happens when you call `dog.eat()`?
4. Can a child class access a `private` field of the parent directly?
5. Why doesn't Java allow multiple inheritance with classes?

---

## 3.1 Upcasting and Downcasting (Parent ↔ Child References)

### The Core Rule

In Java, the **reference type** determines **what you can call**, but the **actual object type** determines **what runs**.

### Case 1: Parent Reference → Child Object (Upcasting) ✅

```java
Animal animal = new Dog("Buddy");  // parent reference, child object
```

This is **perfectly valid**. It's called **upcasting** (moving UP the hierarchy).

**Why it works:** A `Dog` *is an* `Animal`. Every dog has everything an animal has, so it's safe to treat a dog as an animal.

#### What can you access?

```java
class Animal {
    String name;
    public Animal(String name) { this.name = name; }
    public void eat() { System.out.println(name + " is eating"); }
    public void makeSound() { System.out.println("Generic sound"); }
}

class Dog extends Animal {
    public Dog(String name) { super(name); }

    @Override
    public void makeSound() { System.out.println(name + " barks!"); }

    public void fetch() { System.out.println(name + " fetches the ball"); }
}

public class Main {
    public static void main(String[] args) {
        Animal animal = new Dog("Buddy");  // upcasting

        animal.eat();        // ✅ Buddy is eating (defined in Animal)
        animal.makeSound();  // ✅ Buddy barks!   (overridden — Dog's version runs!)
        // animal.fetch();   // ❌ COMPILE ERROR — Animal reference can't see Dog methods
    }
}
```

#### The Golden Rules of Upcasting

| Rule | Explanation |
|------|-------------|
| **Compiler checks the reference type** | `Animal` reference → only `Animal` methods are visible |
| **JVM runs the actual object's method** | Object is `Dog` → `Dog`'s overridden `makeSound()` runs |
| **Child-only methods are hidden** | `fetch()` exists but can't be called through `Animal` reference |
| **Upcasting is implicit (automatic)** | No cast needed: `Animal a = new Dog(...)` just works |

#### Why is this useful? — Polymorphic Method Parameters

```java
class Animal {
    String name;
    public Animal(String name) { this.name = name; }
    public void eat() { System.out.println(name + " is eating"); }
    public void makeSound() { System.out.println("Generic sound"); }
}

class Dog extends Animal {
    public Dog(String name) { super(name); }
    @Override
    public void makeSound() { System.out.println(name + " barks!"); }
    public void fetch() { System.out.println(name + " fetches the ball"); }
}

class Cat extends Animal {
    public Cat(String name) { super(name); }
    @Override
    public void makeSound() { System.out.println(name + " meows!"); }
}

public class Main {
    // ONE method that works for ALL animals — accepts parent type
    public static void feedAnimal(Animal a) {
        a.eat();
        a.makeSound();  // correct overridden version runs automatically!
    }

    public static void main(String[] args) {
        feedAnimal(new Dog("Rex"));    // Rex is eating → Rex barks!
        feedAnimal(new Cat("Kitty"));  // Kitty is eating → Kitty meows!

        // You can also store different types in one array
        Animal[] zoo = { new Dog("Buddy"), new Cat("Whiskers"), new Dog("Max") };
        for (Animal a : zoo) {
            a.makeSound();  // each animal responds with its own sound
        }
        // Output: Buddy barks! → Whiskers meows! → Max barks!
    }
}
```

---

### Case 2: Child Reference → Parent Object (Downcasting) ❌

```java
Dog dog = new Animal("Buddy");  // ❌ COMPILE ERROR!
```

This **does NOT compile**. It's called **invalid downcasting**.

**Why it fails:** Not every `Animal` is a `Dog`. An `Animal` might be a `Cat`, a `Bird`, or just a plain `Animal`. It doesn't have `fetch()` or dog-specific behavior. Java prevents this at compile time.

#### Forced Downcasting (Explicit Cast)

You *can* force a cast, but it's dangerous:

```java
Animal animal = new Dog("Buddy");   // actual object is Dog
Dog dog = (Dog) animal;             // ✅ works — actual object IS a Dog
dog.fetch();                        // ✅ Buddy fetches the ball

Animal animal2 = new Cat("Kitty"); // actual object is Cat
Dog dog2 = (Dog) animal2;          // ❌ RUNTIME ERROR! ClassCastException
```

**Forced downcasting only works if the actual object really is that type.** Otherwise you get a `ClassCastException` at runtime.

#### Safe Downcasting with `instanceof`

```java
Animal animal = new Dog("Buddy");

if (animal instanceof Dog) {
    Dog dog = (Dog) animal;  // safe — we checked first
    dog.fetch();
}

// Java 16+ pattern matching (cleaner)
if (animal instanceof Dog d) {
    d.fetch();  // no explicit cast needed
}
```

---

### Summary Table

| Scenario | Code | Result |
|----------|------|--------|
| Parent ref → Child obj (upcasting) | `Animal a = new Dog("B")` | ✅ Compiles, works |
| Call inherited method | `a.eat()` | ✅ Parent's `eat()` runs |
| Call overridden method | `a.makeSound()` | ✅ **Child's** version runs |
| Call child-only method | `a.fetch()` | ❌ Compile error |
| Child ref → Parent obj | `Dog d = new Animal("B")` | ❌ Compile error |
| Explicit downcast (correct type) | `Dog d = (Dog) animal` | ✅ Works if object is actually Dog |
| Explicit downcast (wrong type) | `Dog d = (Dog) catAnimal` | ❌ ClassCastException at runtime |
| Safe downcast | `if (a instanceof Dog)` | ✅ Always safe |

---

### Mental Model: Reference vs Object

```
          Reference (what compiler sees)    Object (what JVM runs)
          ─────────────────────────────    ──────────────────────

  Animal animal = new Dog("Buddy");

  Compiler sees:  "animal is Animal"    →  can call: eat(), makeSound()
                                            can't call: fetch()

  JVM sees:       "actual object is Dog" →  makeSound() → Dog's version runs

  ─────────────────────────────────────────────────────────────────

  Think of it like a TV remote (reference) and the TV (object):
    - Remote with 3 buttons (Animal) can only press those 3
    - But the TV (Dog) responds with its OWN behavior for those buttons
    - TV has extra features (fetch) but remote doesn't have buttons for them
```

### Complete Runnable Example — All Concepts Together

```java
class Animal {
    String name;

    public Animal(String name) {
        this.name = name;
    }

    public void eat() {
        System.out.println(name + " is eating");
    }

    public void makeSound() {
        System.out.println(name + " makes a generic sound");
    }
}

class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }

    @Override
    public void makeSound() {
        System.out.println(name + " barks: Woof!");
    }

    public void fetch() {
        System.out.println(name + " fetches the ball");
    }
}

class Cat extends Animal {
    public Cat(String name) {
        super(name);
    }

    @Override
    public void makeSound() {
        System.out.println(name + " meows: Meow!");
    }

    public void purr() {
        System.out.println(name + " is purring");
    }
}

public class UpcastDowncastDemo {
    public static void main(String[] args) {

        // ===== UPCASTING (Parent ref = Child obj) =====
        System.out.println("=== Upcasting ===");
        Animal a1 = new Dog("Buddy");     // implicit upcast
        Animal a2 = new Cat("Whiskers");  // implicit upcast

        a1.eat();         // ✅ Buddy is eating (inherited)
        a1.makeSound();   // ✅ Buddy barks: Woof! (Dog's overridden version)
        // a1.fetch();    // ❌ Compile error — Animal ref can't see fetch()

        a2.eat();         // ✅ Whiskers is eating
        a2.makeSound();   // ✅ Whiskers meows: Meow! (Cat's overridden version)
        // a2.purr();     // ❌ Compile error — Animal ref can't see purr()


        // ===== INVALID: Child ref = Parent obj =====
        System.out.println("\n=== Invalid Downcasting ===");
        // Dog d = new Animal("X");  // ❌ Compile error — not all Animals are Dogs


        // ===== EXPLICIT DOWNCAST (risky) =====
        System.out.println("\n=== Explicit Downcasting ===");
        Animal animal = new Dog("Rex");
        Dog dog = (Dog) animal;         // ✅ works because actual object IS a Dog
        dog.fetch();                    // ✅ Rex fetches the ball

        // Animal animal2 = new Cat("Kitty");
        // Dog dog2 = (Dog) animal2;    // ❌ ClassCastException at runtime!


        // ===== SAFE DOWNCAST with instanceof =====
        System.out.println("\n=== Safe Downcasting ===");
        Animal[] animals = { new Dog("Max"), new Cat("Luna"), new Dog("Rocky") };

        for (Animal a : animals) {
            a.makeSound();  // always safe — calls overridden version

            if (a instanceof Dog d) {           // Java 16+ pattern matching
                d.fetch();                      // only called for dogs
            } else if (a instanceof Cat c) {
                c.purr();                       // only called for cats
            }
        }
        // Output:
        // Max barks: Woof!
        // Max fetches the ball
        // Luna meows: Meow!
        // Luna is purring
        // Rocky barks: Woof!
        // Rocky fetches the ball
    }
}
```

### Self-Check (Upcasting/Downcasting)

1. `Animal a = new Dog("Rex"); a.makeSound();` — whose `makeSound()` runs?
2. Why does `a.fetch()` fail even though the object is a `Dog`?
3. What exception do you get if you cast a `Cat` object to `Dog`?
4. How does `instanceof` prevent that exception?
5. Is upcasting implicit or explicit? What about downcasting?

---

## 4. Polymorphism

### Definition

**Polymorphism** = "many forms." The same method call behaves differently depending on the object it's called on.

| Term | Meaning |
|------|---------|
| **Compile-time Polymorphism** | Method **overloading** — same name, different parameters (resolved at compile time) |
| **Runtime Polymorphism** | Method **overriding** — child replaces parent method (resolved at runtime) |

### 4.1 Compile-Time Polymorphism (Method Overloading)

**Same method name, different parameter lists** in the **same class**.

The compiler decides which version to call based on the arguments you pass.

```java
class Calculator {

    // Same name "add" — different parameter types/counts
    public int add(int a, int b) {
        return a + b;
    }

    public int add(int a, int b, int c) {
        return a + b + c;
    }

    public double add(double a, double b) {
        return a + b;
    }

    public String add(String a, String b) {
        return a + b;  // string concatenation
    }
}

public class Main {
    public static void main(String[] args) {
        Calculator calc = new Calculator();

        System.out.println(calc.add(2, 3));          // 5        → add(int, int)
        System.out.println(calc.add(2, 3, 4));       // 9        → add(int, int, int)
        System.out.println(calc.add(2.5, 3.5));      // 6.0      → add(double, double)
        System.out.println(calc.add("Hello", " World")); // Hello World → add(String, String)
    }
}
```

#### Overloading Rules

| Valid way to overload | Example |
|-----------------------|---------|
| Different number of params | `add(int a, int b)` vs `add(int a, int b, int c)` |
| Different types of params | `add(int a, int b)` vs `add(double a, double b)` |
| Different order of types | `add(int a, double b)` vs `add(double a, int b)` |

| NOT valid for overloading | Why |
|---------------------------|-----|
| Only change return type | `int add(int a, int b)` vs `double add(int a, int b)` ❌ — ambiguous |
| Only change parameter names | `add(int x, int y)` vs `add(int a, int b)` ❌ — same signature |

---

### 4.2 Runtime Polymorphism (Method Overriding)

**Child class provides its own version** of a method defined in the parent. The JVM decides which version to run **at runtime** based on the actual object type.

This is where **upcasting** comes in — you already learned this!

```java
class Shape {
    public double area() {
        return 0;
    }

    public void describe() {
        System.out.println("I am a shape with area: " + area());
    }
}

class Circle extends Shape {
    private double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
}

class Rectangle extends Shape {
    private double width, height;

    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }

    @Override
    public double area() {
        return width * height;
    }
}

class Triangle extends Shape {
    private double base, height;

    public Triangle(double base, double height) {
        this.base = base;
        this.height = height;
    }

    @Override
    public double area() {
        return 0.5 * base * height;
    }
}

public class Main {
    public static void main(String[] args) {
        // Parent references, child objects — runtime polymorphism in action
        Shape[] shapes = {
            new Circle(5),
            new Rectangle(4, 6),
            new Triangle(3, 8)
        };

        for (Shape s : shapes) {
            s.describe();  // SAME method call, DIFFERENT behavior each time
        }
        // Output:
        // I am a shape with area: 78.539...
        // I am a shape with area: 24.0
        // I am a shape with area: 12.0
    }
}
```

#### Why Runtime Polymorphism is Powerful

Notice `describe()` is **defined once** in `Shape` and calls `area()`. But each subclass overrides `area()` — so `describe()` **automatically** gives the correct answer for every shape without being rewritten.

This is **the whole point of OOP** — write code against the parent type, and it works for all children.

---

### Overloading vs Overriding — Comparison

| Feature | Overloading | Overriding |
|---------|-------------|-----------|
| **Where** | Same class | Parent → Child |
| **Method name** | Same | Same |
| **Parameters** | Must be different | Must be same |
| **Return type** | Can differ | Same (or covariant) |
| **Resolved at** | Compile time | Runtime |
| **Keyword** | None | `@Override` |
| **Also called** | Static polymorphism | Dynamic polymorphism |

---

### Real-World Analogy

Think of a **"Play" button** on different apps:
- On Spotify → plays music
- On YouTube → plays video
- On a Game → starts the game

Same action name ("play"), completely different behavior depending on the object. That's polymorphism.

---

### Complete Runnable Example

```java
class Payment {
    public void pay(double amount) {
        System.out.println("Processing payment of $" + amount);
    }
}

class CreditCardPayment extends Payment {
    @Override
    public void pay(double amount) {
        System.out.println("Charging $" + amount + " to credit card");
    }
}

class UPIPayment extends Payment {
    @Override
    public void pay(double amount) {
        System.out.println("Sending $" + amount + " via UPI");
    }
}

class CryptoPayment extends Payment {
    @Override
    public void pay(double amount) {
        System.out.println("Transferring $" + amount + " in crypto");
    }
}

public class PolymorphismDemo {
    // This method doesn't care WHAT kind of payment — it just works
    public static void checkout(Payment p, double amount) {
        System.out.println("--- Checkout ---");
        p.pay(amount);
        System.out.println("Payment complete!\n");
    }

    public static void main(String[] args) {
        // Same checkout() method, different behavior each time
        checkout(new CreditCardPayment(), 99.99);
        checkout(new UPIPayment(), 49.99);
        checkout(new CryptoPayment(), 199.99);

        // Output:
        // --- Checkout ---
        // Charging $99.99 to credit card
        // Payment complete!
        //
        // --- Checkout ---
        // Sending $49.99 via UPI
        // Payment complete!
        //
        // --- Checkout ---
        // Transferring $199.99 in crypto
        // Payment complete!
    }
}
```

---

### Mental Model

```
        COMPILE-TIME (Overloading)          RUNTIME (Overriding)
        ─────────────────────────           ────────────────────

        Same class, different params        Parent → Child, same params

        calc.add(2, 3)       → int version     Payment p = new UPIPayment();
        calc.add(2.5, 3.5)   → double version  p.pay(100);  → UPI version runs
        calc.add("a", "b")   → String version

        Compiler picks method               JVM picks method
        (based on argument types)           (based on actual object type)
```

### Self-Check

1. What is the difference between overloading and overriding?
2. Can you overload by only changing the return type? Why not?
3. In the Shape example, why does `describe()` give different areas without being overridden itself?
4. `Payment p = new CryptoPayment(); p.pay(100);` — which `pay()` runs?
5. Is method overloading related to inheritance? Is overriding?

---

## 5. Abstraction

### Definition

**Abstraction** = hiding the complex implementation details and showing only the essential features to the user.

Think of it as: **"What" an object does, not "How" it does it.**

| Term | Meaning |
|------|---------|
| **Abstract class** | A class that cannot be instantiated; may have abstract and concrete methods |
| **Abstract method** | A method with no body — child classes MUST provide the implementation |
| **`abstract` keyword** | Marks a class or method as abstract |
| **Concrete class** | A regular class that can be instantiated (provides all implementations) |

**Analogy:** When you drive a car, you interact with steering wheel, pedals, gear shift (the abstraction). You don't need to know how the engine combustion cycle works internally (the hidden implementation).

---

### Why Use Abstraction?

| Problem without abstraction | Solution with abstraction |
|-----------------------------|--------------------------|
| Parent class has methods that make no sense to implement at that level | Declare them `abstract` — force children to provide real implementation |
| Someone creates an object of a generic type that shouldn't exist (e.g., `new Shape()`) | Make the class `abstract` — can't be instantiated |
| No contract — children might forget to implement critical methods | Abstract methods force all children to implement them |

---

### Abstract Class Example

```java
// Abstract class — can't do: new Vehicle()
abstract class Vehicle {
    String brand;

    public Vehicle(String brand) {
        this.brand = brand;
    }

    // Abstract method — NO body, children MUST implement
    public abstract void startEngine();

    // Abstract method
    public abstract double fuelEfficiency();

    // Concrete method — has a body, children inherit it as-is
    public void displayInfo() {
        System.out.println("Brand: " + brand);
        System.out.println("Fuel efficiency: " + fuelEfficiency() + " km/l");
    }
}

class Car extends Vehicle {
    public Car(String brand) {
        super(brand);
    }

    @Override
    public void startEngine() {
        System.out.println(brand + " car: Turn key / press button → engine roars");
    }

    @Override
    public double fuelEfficiency() {
        return 15.5;
    }
}

class ElectricScooter extends Vehicle {
    public ElectricScooter(String brand) {
        super(brand);
    }

    @Override
    public void startEngine() {
        System.out.println(brand + " scooter: Press start → silent motor engages");
    }

    @Override
    public double fuelEfficiency() {
        return 50.0;  // km per charge equivalent
    }
}

public class AbstractionDemo {
    public static void main(String[] args) {
        // Vehicle v = new Vehicle("X");  // ❌ COMPILE ERROR — can't instantiate abstract class

        Vehicle car = new Car("Toyota");
        Vehicle scooter = new ElectricScooter("Ola");

        car.startEngine();      // Toyota car: Turn key / press button → engine roars
        car.displayInfo();      // Brand: Toyota \n Fuel efficiency: 15.5 km/l

        scooter.startEngine();  // Ola scooter: Press start → silent motor engages
        scooter.displayInfo();  // Brand: Ola \n Fuel efficiency: 50.0 km/l
    }
}
```

---

### Key Rules of Abstract Classes

| Rule | Explanation |
|------|-------------|
| Cannot be instantiated | `new Vehicle()` → compile error |
| Can have constructors | Children call them via `super()` |
| Can have abstract methods | No body — children must override |
| Can have concrete methods | Regular methods with body — children inherit them |
| Can have fields | Instance variables, just like normal classes |
| Child MUST implement all abstract methods | Otherwise the child must also be declared abstract |
| Can have a mix of both | Some methods implemented, some left abstract |

---

### What If a Child Doesn't Implement All Abstract Methods?

```java
abstract class Animal {
    public abstract void makeSound();
    public abstract void move();
}

// This class only implements one method — must also be abstract
abstract class Fish extends Animal {
    @Override
    public void move() {
        System.out.println("Swims in water");
    }
    // makeSound() is NOT implemented → Fish must be abstract too
}

// This class implements the remaining method — can be concrete
class Salmon extends Fish {
    @Override
    public void makeSound() {
        System.out.println("...(fish don't make much sound)");
    }
}
```

---

### Abstract Methods vs Concrete Methods — When to Use Which?

```java
abstract class Employee {
    String name;
    double baseSalary;

    public Employee(String name, double baseSalary) {
        this.name = name;
        this.baseSalary = baseSalary;
    }

    // Abstract — each employee type calculates salary differently
    public abstract double calculateSalary();

    // Concrete — same for all employees
    public void printPaySlip() {
        System.out.println("Employee: " + name);
        System.out.println("Salary: $" + calculateSalary());
        System.out.println("---");
    }
}

class FullTimeEmployee extends Employee {
    private double bonus;

    public FullTimeEmployee(String name, double baseSalary, double bonus) {
        super(name, baseSalary);
        this.bonus = bonus;
    }

    @Override
    public double calculateSalary() {
        return baseSalary + bonus;
    }
}

class Intern extends Employee {
    private int hoursWorked;

    public Intern(String name, double hourlyRate, int hoursWorked) {
        super(name, hourlyRate);
        this.hoursWorked = hoursWorked;
    }

    @Override
    public double calculateSalary() {
        return baseSalary * hoursWorked;
    }
}

public class Main {
    public static void main(String[] args) {
        Employee[] team = {
            new FullTimeEmployee("Alice", 5000, 1200),
            new Intern("Bob", 20, 80)
        };

        for (Employee e : team) {
            e.printPaySlip();  // same method, different salary calculation
        }
        // Employee: Alice → Salary: $6200.0
        // Employee: Bob   → Salary: $1600.0
    }
}
```

---

### Abstraction vs Encapsulation — Don't Confuse Them!

| Feature | Abstraction | Encapsulation |
|---------|-------------|---------------|
| **Focus** | Hiding *complexity* (what vs how) | Hiding *data* (protecting fields) |
| **Mechanism** | Abstract classes, interfaces | Access modifiers (private + getters/setters) |
| **Level** | Design level — decides what to expose | Implementation level — protects internals |
| **Example** | `abstract void startEngine()` — user doesn't know HOW | `private double balance` — user can't directly access |

They work **together**: abstraction defines the "what," encapsulation protects the "how."

---

### Mental Model

```
  Without Abstraction:              With Abstraction:

  You call:                         You call:
    engine.injectFuel()               vehicle.startEngine()
    engine.sparkIgnition()
    engine.movePistons()            Implementation hidden inside
    engine.rotateCrankshaft()       each child class
    engine.transferToWheels()

  You deal with ALL complexity      You deal with ONE simple concept
```

### Self-Check

1. Can you create an object of an abstract class? Why not?
2. What happens if a child class doesn't implement all abstract methods?
3. Can an abstract class have a constructor? Who calls it?
4. What's the difference between an abstract method and a concrete method?
5. How is abstraction different from encapsulation?

---

## 6. Interfaces vs Abstract Classes

### What is an Interface?

An **interface** is a 100% abstract contract — it defines **what** a class must do, without any implementation (prior to Java 8).

Think of it as: **a contract/agreement.** If a class "signs" the contract (`implements`), it MUST fulfill all the promises (implement all methods).

**Analogy:** A power outlet is an interface. Any device (phone charger, laptop charger, TV) that follows the plug standard can connect. The outlet doesn't care what device it is — only that it matches the contract.

---

### Basic Interface Syntax

```java
// Interface — defines the contract
interface Flyable {
    void fly();          // implicitly public and abstract
    void land();
}

interface Swimmable {
    void swim();
}

// A class IMPLEMENTS an interface (can implement MULTIPLE)
class Duck implements Flyable, Swimmable {
    @Override
    public void fly() {
        System.out.println("Duck flaps wings and flies");
    }

    @Override
    public void land() {
        System.out.println("Duck lands on water");
    }

    @Override
    public void swim() {
        System.out.println("Duck paddles in the pond");
    }
}

class Airplane implements Flyable {
    @Override
    public void fly() {
        System.out.println("Airplane uses jet engines to fly");
    }

    @Override
    public void land() {
        System.out.println("Airplane lands on runway");
    }
}

public class Main {
    public static void main(String[] args) {
        Flyable f1 = new Duck();
        Flyable f2 = new Airplane();

        f1.fly();  // Duck flaps wings and flies
        f2.fly();  // Airplane uses jet engines to fly

        // A Duck is both Flyable and Swimmable
        Swimmable s = new Duck();
        s.swim();  // Duck paddles in the pond
    }
}
```

---

### Key Feature: Multiple Inheritance Through Interfaces

Java doesn't allow `class A extends B, C` — but it DOES allow:

```java
interface Printable {
    void print();
}

interface Scannable {
    void scan();
}

interface Faxable {
    void fax();
}

// A class can implement MULTIPLE interfaces
class AllInOnePrinter implements Printable, Scannable, Faxable {
    @Override
    public void print() { System.out.println("Printing..."); }

    @Override
    public void scan() { System.out.println("Scanning..."); }

    @Override
    public void fax() { System.out.println("Faxing..."); }
}
```

This solves the "diamond problem" that multiple class inheritance causes.

---

### Java 8+ Interface Features

Interfaces evolved over time. Modern interfaces can have more than just abstract methods:

```java
interface Logger {
    // Abstract method — must be implemented
    void log(String message);

    // Default method (Java 8+) — has a body, optional to override
    default void logWarning(String message) {
        log("WARNING: " + message);
    }

    // Static method (Java 8+) — belongs to the interface itself
    static String formatTimestamp() {
        return java.time.LocalDateTime.now().toString();
    }
}

class ConsoleLogger implements Logger {
    @Override
    public void log(String message) {
        System.out.println("[" + Logger.formatTimestamp() + "] " + message);
    }
    // logWarning() is inherited from the interface — no need to override
}

class FileLogger implements Logger {
    @Override
    public void log(String message) {
        System.out.println("Writing to file: " + message);
    }

    @Override
    public void logWarning(String message) {
        log("⚠️ FILE WARNING: " + message);  // override default behavior
    }
}
```

---

### The Big Comparison: Interface vs Abstract Class

| Feature | Interface | Abstract Class |
|---------|-----------|----------------|
| **Keyword** | `implements` | `extends` |
| **Multiple?** | Class can implement many interfaces | Class can extend only ONE abstract class |
| **Methods** | Abstract + default + static (Java 8+) | Abstract + concrete |
| **Fields** | Only `public static final` (constants) | Any fields (private, protected, etc.) |
| **Constructors** | ❌ No | ✅ Yes |
| **Access modifiers on methods** | All methods are implicitly `public` | Can have any access modifier |
| **State (instance variables)** | ❌ Cannot hold state | ✅ Can hold state |
| **When to use** | Define a **capability** (what can it do?) | Define a **base type** (what is it?) |

---

### When to Use Which? — Decision Guide

```
Ask yourself:
│
├── "Is it a TYPE relationship?" (Dog IS AN Animal)
│   └── Use ABSTRACT CLASS
│       → shared state, constructors, "is-a" relationship
│
├── "Is it a CAPABILITY?" (Duck CAN fly, CAN swim)
│   └── Use INTERFACE
│       → defines behavior, multiple inheritance, "can-do" relationship
│
└── "Do unrelated classes need the same behavior?"
    └── Use INTERFACE
        → Duck and Airplane both fly, but aren't related by type
```

---

### Real-World Example — Combining Both

```java
// Abstract class — base type with shared state
abstract class Animal {
    String name;
    int age;

    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public abstract void makeSound();

    public void sleep() {
        System.out.println(name + " is sleeping");
    }
}

// Interfaces — capabilities
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

interface Trainable {
    void performTrick(String trick);
}

// Duck IS AN Animal, CAN fly, CAN swim
class Duck extends Animal implements Flyable, Swimmable {
    public Duck(String name, int age) { super(name, age); }

    @Override
    public void makeSound() { System.out.println(name + " quacks!"); }

    @Override
    public void fly() { System.out.println(name + " flies south for winter"); }

    @Override
    public void swim() { System.out.println(name + " swims in the lake"); }
}

// Dog IS AN Animal, CAN be trained
class Dog extends Animal implements Trainable {
    public Dog(String name, int age) { super(name, age); }

    @Override
    public void makeSound() { System.out.println(name + " barks!"); }

    @Override
    public void performTrick(String trick) {
        System.out.println(name + " performs: " + trick);
    }
}

// Parrot IS AN Animal, CAN fly, CAN be trained
class Parrot extends Animal implements Flyable, Trainable {
    public Parrot(String name, int age) { super(name, age); }

    @Override
    public void makeSound() { System.out.println(name + " says: Hello!"); }

    @Override
    public void fly() { System.out.println(name + " flies around the room"); }

    @Override
    public void performTrick(String trick) {
        System.out.println(name + " mimics: " + trick);
    }
}

public class InterfaceDemo {
    // Method accepts any Flyable — doesn't care if it's Duck or Parrot
    public static void makeFly(Flyable f) {
        f.fly();
    }

    public static void main(String[] args) {
        Duck duck = new Duck("Donald", 3);
        Dog dog = new Dog("Buddy", 5);
        Parrot parrot = new Parrot("Polly", 2);

        // Using as Animal (abstract class)
        Animal[] animals = {duck, dog, parrot};
        for (Animal a : animals) {
            a.makeSound();
        }
        // Donald quacks! → Buddy barks! → Polly says: Hello!

        // Using as Flyable (interface)
        Flyable[] flyers = {duck, parrot};
        for (Flyable f : flyers) {
            f.fly();
        }
        // Donald flies south → Polly flies around the room

        // Using as Trainable (interface)
        Trainable[] trainable = {dog, parrot};
        for (Trainable t : trainable) {
            t.performTrick("Roll over");
        }
        // Buddy performs: Roll over → Polly mimics: Roll over
    }
}
```

---

### Interface Inheritance (Interfaces Extending Interfaces)

```java
interface Readable {
    void read();
}

interface Writable {
    void write();
}

// Interface can extend MULTIPLE interfaces
interface ReadWritable extends Readable, Writable {
    void seek(int position);
}

class File implements ReadWritable {
    @Override
    public void read() { System.out.println("Reading file"); }

    @Override
    public void write() { System.out.println("Writing file"); }

    @Override
    public void seek(int position) { System.out.println("Seeking to " + position); }
}
```

---

### Mental Model

```
  Abstract Class = "What it IS"         Interface = "What it CAN DO"
  ──────────────────────────           ─────────────────────────────

  Animal                               Flyable    Swimmable   Trainable
    ├── name, age (state)               │ fly()     │ swim()    │ performTrick()
    ├── sleep() (shared behavior)       │           │           │
    ├── abstract makeSound()            │           │           │
    │                                   │           │           │
    ├── Duck ──────── implements ───────┤───────────┤           │
    ├── Dog ───────── implements ───────┼───────────┼───────────┤
    └── Parrot ────── implements ───────┤           │           │
                                                                │
  One parent only                      Multiple interfaces allowed
  Has state + constructors             No state, no constructors
```

### Self-Check

1. Can a class implement multiple interfaces? Can it extend multiple classes?
2. What is a `default` method in an interface? Why was it added in Java 8?
3. Can an interface have instance variables (state)?
4. When would you pick an abstract class over an interface?
5. In the Animal example, why are `Flyable`/`Swimmable` interfaces instead of abstract classes?
6. Can an interface extend another interface?

---

## 7. Association, Aggregation & Composition

### Overview

These describe **how objects relate to each other** — not through inheritance ("is-a"), but through **usage** ("has-a" / "uses-a").

| Relationship | Meaning | Strength | Lifecycle |
|-------------|---------|----------|-----------|
| **Association** | Objects know about each other | Weakest | Independent |
| **Aggregation** | One object "has" another (loosely) | Medium | Can exist independently |
| **Composition** | One object "owns" another (tightly) | Strongest | Part dies with the whole |

**Quick memory aid:**
- Association → "**uses**" (Teacher uses Classroom)
- Aggregation → "**has**" but can exist alone (Team has Players)
- Composition → "**owns**" / "**is made of**" (House owns Rooms)

---

### 7.1 Association ("uses-a")

Two objects are **related but independent**. Neither owns the other. Both can exist on their own.

```java
class Teacher {
    String name;

    public Teacher(String name) {
        this.name = name;
    }

    public void teach(Course course) {
        System.out.println(name + " is teaching " + course.title);
    }
}

class Course {
    String title;

    public Course(String title) {
        this.title = title;
    }
}

public class AssociationDemo {
    public static void main(String[] args) {
        Teacher teacher = new Teacher("Prof. Smith");
        Course course = new Course("Java OOP");

        teacher.teach(course);  // Prof. Smith is teaching Java OOP

        // Both exist independently — teacher can teach other courses,
        // course can have other teachers
    }
}
```

**Key point:** Neither object creates or destroys the other. They just interact.

**Real-world examples:**
- Doctor and Patient (doctor treats patient, but neither owns the other)
- Student and Library (student uses library)
- Driver and Car (driver drives car, but car exists without driver)

---

### 7.2 Aggregation ("has-a", loosely)

One object **contains** another, but the contained object **can exist independently**. If the container is destroyed, the parts survive.

```java
class Player {
    String name;
    int jerseyNumber;

    public Player(String name, int jerseyNumber) {
        this.name = name;
        this.jerseyNumber = jerseyNumber;
    }

    @Override
    public String toString() {
        return name + " (#" + jerseyNumber + ")";
    }
}

class Team {
    String teamName;
    List<Player> players;  // Team HAS players (aggregation)

    public Team(String teamName) {
        this.teamName = teamName;
        this.players = new ArrayList<>();
    }

    public void addPlayer(Player p) {
        players.add(p);
        System.out.println(p.name + " joined " + teamName);
    }

    public void removePlayer(Player p) {
        players.remove(p);
        System.out.println(p.name + " left " + teamName);
    }

    public void showRoster() {
        System.out.println(teamName + " roster:");
        for (Player p : players) {
            System.out.println("  " + p);
        }
    }
}

public class AggregationDemo {
    public static void main(String[] args) {
        // Players exist INDEPENDENTLY of any team
        Player p1 = new Player("Virat", 18);
        Player p2 = new Player("Dhoni", 7);
        Player p3 = new Player("Rohit", 45);

        Team team = new Team("India");
        team.addPlayer(p1);
        team.addPlayer(p2);
        team.addPlayer(p3);
        team.showRoster();

        // If team is dissolved, players still exist!
        team = null;  // team destroyed
        System.out.println(p1.name + " still exists: " + p1);  // Virat still exists

        // Player can join another team
        Team newTeam = new Team("IPL All Stars");
        newTeam.addPlayer(p1);  // Virat joins new team
    }
}
```

**Key point:** Players are **created outside** the team and **passed in**. If the team is destroyed, players still exist and can join another team.

**Real-world examples:**
- Department has Employees (employees survive if department closes)
- Library has Books (books exist without the library)
- University has Students (students exist independently)

---

### 7.3 Composition ("owns" / "is made of", tightly)

One object **owns** another — the part **cannot exist without the whole**. When the whole is destroyed, its parts are destroyed too.

```java
class Room {
    String type;
    double area;

    public Room(String type, double area) {
        this.type = type;
        this.area = area;
    }

    @Override
    public String toString() {
        return type + " (" + area + " sq ft)";
    }
}

class House {
    String address;
    List<Room> rooms;  // House OWNS rooms (composition)

    public House(String address) {
        this.address = address;
        this.rooms = new ArrayList<>();

        // Rooms are CREATED INSIDE the house — they don't exist independently
        rooms.add(new Room("Living Room", 300));
        rooms.add(new Room("Bedroom", 200));
        rooms.add(new Room("Kitchen", 150));
        rooms.add(new Room("Bathroom", 80));
    }

    public void showHouse() {
        System.out.println("House at: " + address);
        for (Room r : rooms) {
            System.out.println("  " + r);
        }
        System.out.println("  Total rooms: " + rooms.size());
    }
}

public class CompositionDemo {
    public static void main(String[] args) {
        House house = new House("123 Main Street");
        house.showHouse();

        // If house is destroyed, rooms are gone too!
        house = null;  // rooms are garbage collected along with the house
        // You can't access those rooms anymore — they only existed inside that house
    }
}
```

**Key point:** Rooms are **created inside** the House. They have no meaning without the house. House dies → rooms die.

**Real-world examples:**
- Human has Heart (heart can't exist without the human)
- Car has Engine (engine is built for that specific car)
- Web Page has DOM elements (elements don't exist outside the page)

---

### Another Example — Composition with Behavior

```java
class Engine {
    private int horsepower;
    private boolean running;

    public Engine(int horsepower) {
        this.horsepower = horsepower;
        this.running = false;
    }

    public void start() {
        running = true;
        System.out.println("Engine (" + horsepower + " HP) started");
    }

    public void stop() {
        running = false;
        System.out.println("Engine stopped");
    }

    public boolean isRunning() { return running; }
}

class Transmission {
    private int currentGear;

    public Transmission() { this.currentGear = 0; }

    public void shiftUp() {
        currentGear++;
        System.out.println("Shifted to gear " + currentGear);
    }

    public void shiftDown() {
        if (currentGear > 0) currentGear--;
        System.out.println("Shifted to gear " + currentGear);
    }
}

class Car {
    private String model;
    private Engine engine;             // composition — Car OWNS engine
    private Transmission transmission; // composition — Car OWNS transmission

    public Car(String model, int hp) {
        this.model = model;
        this.engine = new Engine(hp);           // created inside
        this.transmission = new Transmission(); // created inside
    }

    public void drive() {
        System.out.println("--- " + model + " ---");
        engine.start();
        transmission.shiftUp();
        transmission.shiftUp();
        System.out.println(model + " is cruising!");
    }

    public void park() {
        transmission.shiftDown();
        transmission.shiftDown();
        engine.stop();
        System.out.println(model + " is parked\n");
    }
}

public class Main {
    public static void main(String[] args) {
        Car car = new Car("Honda Civic", 150);
        car.drive();
        car.park();
        // Engine and Transmission are part of this car — can't be shared or separated
    }
}
```

---

### The Real Distinction: Exclusive vs Shared Ownership

The **definitive** way to distinguish aggregation from composition:

> **Composition** = the part is **exclusively owned** by ONE object. It cannot be shared.  
> **Aggregation** = the part **can be shared** by several aggregating objects.

#### Classic Example: Student, Name, and Address

```java
class Name {
    String firstName;
    String lastName;

    public Name(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    @Override
    public String toString() {
        return firstName + " " + lastName;
    }
}

class Address {
    String street;
    String city;
    String zipCode;

    public Address(String street, String city, String zipCode) {
        this.street = street;
        this.city = city;
        this.zipCode = zipCode;
    }

    @Override
    public String toString() {
        return street + ", " + city + " - " + zipCode;
    }
}

class Student {
    private Name name;       // COMPOSITION — name is exclusively owned by THIS student
    private Address address; // AGGREGATION — address can be shared with other students

    public Student(String firstName, String lastName, Address address) {
        this.name = new Name(firstName, lastName); // created inside — exclusive
        this.address = address;                     // passed in — can be shared
    }

    public void display() {
        System.out.println("Student: " + name);
        System.out.println("Address: " + address);
    }
}

public class OwnershipDemo {
    public static void main(String[] args) {
        // One address shared by multiple students (e.g., roommates)
        Address sharedApartment = new Address("42 MG Road", "Bangalore", "560001");

        Student s1 = new Student("Pavan", "Kumar", sharedApartment);
        Student s2 = new Student("Rahul", "Sharma", sharedApartment);
        Student s3 = new Student("Priya", "Reddy", sharedApartment);

        s1.display();
        s2.display();
        // All three students share the SAME Address object → Aggregation

        // But each student's Name is EXCLUSIVELY theirs
        // s1's Name "Pavan Kumar" belongs ONLY to s1 → Composition
        // No other student can share that exact Name object
    }
}
```

#### Why "Student has a Name" is Composition:

- A student's name is **exclusively owned** by that student
- No other object shares the same Name instance
- If the student object is destroyed, the Name goes with it
- The name has no meaning outside of that specific student

#### Why "Student has an Address" is Aggregation:

- Multiple students **can share** the same address (roommates, siblings)
- The address exists independently — it existed before the student enrolled
- If a student leaves, the address still exists for other students
- The same Address object is referenced by several Student objects

---

### How to Identify: Aggregation vs Composition (Updated)

| Question to ask | Aggregation | Composition |
|-----------------|-------------|-------------|
| **Can the part be SHARED by multiple objects?** | ✅ Yes (key distinction!) | ❌ No — exclusively owned |
| **Where is the part created?** | Outside, passed in | Inside the whole |
| **Can the part exist alone?** | Yes | No meaningful existence alone |
| **If whole is destroyed?** | Part survives (shared by others) | Part is destroyed |
| **Relationship strength** | "has" (loosely) | "owns" (exclusively) |

---

### All Three Together — University System

```java
class Professor {
    String name;
    public Professor(String name) { this.name = name; }
}

class Syllabus {
    String content;
    public Syllabus(String content) { this.content = content; }
}

class Course {
    String title;
    Professor professor;  // AGGREGATION — professor exists independently
    Syllabus syllabus;    // COMPOSITION — syllabus is created for this course

    public Course(String title, Professor prof) {
        this.title = title;
        this.professor = prof;                        // passed in (aggregation)
        this.syllabus = new Syllabus("Week 1: Intro, Week 2: Deep dive..."); // created inside (composition)
    }

    public void display() {
        System.out.println("Course: " + title);
        System.out.println("Professor: " + professor.name);  // association/aggregation
        System.out.println("Syllabus: " + syllabus.content); // composition
    }
}

class Student {
    String name;
    // Student USES Course — association (neither owns the other)
    public void enroll(Course c) {
        System.out.println(name + " enrolled in " + c.title);
    }
}
```

In this example:
- **Student ↔ Course** = Association (uses)
- **Course → Professor** = Aggregation (has, but professor exists independently)
- **Course → Syllabus** = Composition (owns, syllabus created inside and dies with course)

---

### Mental Model

```
  ASSOCIATION (weakest)        AGGREGATION (medium)        COMPOSITION (strongest)
  ─────────────────────       ─────────────────────       ────────────────────────

  Teacher ···> Course          Student ◇───> Address       Student ◆───> Name

  "uses"                       "has" (shared)              "owns" (exclusive)

  Both independent             Part CAN be shared          Part CANNOT be shared
  No ownership                 by multiple objects         Exclusively owned by one
  Temporary interaction        Part survives alone         Part dies with whole

  teacher.teach(course)        address shared by           name belongs only to
                               roommates                   this one student
```

### Self-Check

1. What is the **key** difference between aggregation and composition?
2. "Student has a Name" — why is this composition?
3. "Student has an Address" — why is this aggregation?
4. If a `Department` is deleted, should its `Employees` be deleted? What relationship is this?
5. A `Car` has an `Engine` built exclusively for it — aggregation or composition?
6. A `Library` has `Books` that existed before the library and can be moved — aggregation or composition?
7. Can an aggregated part be shared by multiple objects? Can a composed part?

---

## Summary — All 7 OOP Concepts at a Glance

| # | Concept | Core Idea | Keyword/Mechanism |
|---|---------|-----------|-------------------|
| 1 | Class & Object | Blueprint vs Instance | `class`, `new` |
| 2 | Encapsulation | Hide data, expose controlled access | `private`, getters/setters |
| 3 | Inheritance | Child gets parent's features | `extends`, `super` |
| 3.1 | Upcasting/Downcasting | Parent ref ↔ Child obj | `(Type)` cast, `instanceof` |
| 4 | Polymorphism | Same call, different behavior | Overloading / Overriding |
| 5 | Abstraction | Hide complexity, show essentials | `abstract` class/method |
| 6 | Interfaces | Contract for capabilities | `interface`, `implements` |
| 7 | Association/Aggregation/Composition | How objects relate ("has-a") | Object references |

### Relationships Cheat Sheet

```
  "is-a"    → Inheritance (extends)
  "can-do"  → Interface (implements)
  "uses"    → Association (independent objects interact)
  "has"     → Aggregation (part can be SHARED by multiple owners)
  "owns"    → Composition (part is EXCLUSIVELY owned by one object)
```

---

*Last updated: Topic 7 — Association, Aggregation & Composition (ALL TOPICS COMPLETE)*
