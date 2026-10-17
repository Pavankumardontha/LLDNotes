# Java Exceptions - Complete Guide

## Concept 1: What is an Exception?

An **exception** is an unexpected event that occurs **during the execution** of a program and disrupts the normal flow of instructions.

Think of it like this: You're driving on a road (your program is running). Suddenly, a tree falls on the road (an exception occurs). Now you can't continue driving normally — you need to handle the situation.

### What happens without handling exceptions?

```java
public class Demo {
    public static void main(String[] args) {
        int a = 10;
        int b = 0;
        int result = a / b;  // BOOM! Exception occurs here
        System.out.println("Result: " + result);  // This line NEVER executes
        System.out.println("Program ended");       // This line NEVER executes
    }
}
```

**Output:**
```
Exception in thread "main" java.lang.ArithmeticException: / by zero
    at Demo.main(Demo.java:5)
```

Notice two critical things:
1. **The program crashed** — it stopped abruptly
2. **Lines after the exception were skipped** — `"Result"` and `"Program ended"` never printed

### NullPointerException Example:

```java
public class Demo2 {
    public static void main(String[] args) {
        String name = null;
        System.out.println(name.length());  // BOOM! NullPointerException
        System.out.println("This won't print");
    }
}
```

**Output:**
```
Exception in thread "main" java.lang.NullPointerException
    at Demo2.main(Demo2.java:4)
```

You tried to call `.length()` on `null` — there's no object there, so Java throws an exception.

### ArrayIndexOutOfBoundsException Example:

```java
public class Demo3 {
    public static void main(String[] args) {
        int[] arr = {10, 20, 30};
        System.out.println(arr[5]);  // BOOM! ArrayIndexOutOfBoundsException
    }
}
```

**Output:**
```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 5 out of bounds for length 3
```

The array has indices 0, 1, 2 — but you asked for index 5, which doesn't exist.

### Key Takeaways:
- An **exception** = something went wrong at **runtime** (not at compile time)
- Without handling, the program **crashes immediately**
- All code **after** the exception line is **skipped**
- Java tells you **what** went wrong and **where** (class name + line number)

---

## Concept 2: Exception Hierarchy (The Family Tree)

In Java, every exception is an **object** — and all exception classes form a hierarchy (a family tree).

```
                    Object
                      |
                  Throwable          ← The root of all exceptions/errors
                  /       \
               Error      Exception
                |            /    \
          (JVM issues)      /      \
                           /        \
              RuntimeException    (Checked Exceptions)
              (Unchecked)         IOException
              |                   SQLException
              |                   FileNotFoundException
              |                   ClassNotFoundException
              |
              ArithmeticException
              NullPointerException
              ArrayIndexOutOfBoundsException
              ClassCastException
              NumberFormatException
              IllegalArgumentException
```

### 1. `Throwable` — The Grandfather
- The topmost class in the exception hierarchy
- Everything that can be "thrown" in Java extends `Throwable`
- It has two children: `Error` and `Exception`

### 2. `Error` — Things YOU CANNOT fix
These are **serious JVM-level problems**. You should **never try to handle** these.

```java
// StackOverflowError — infinite recursion
public class ErrorDemo {
    public static void main(String[] args) {
        main(args);  // calls itself forever → StackOverflowError
    }
}
```

```java
// OutOfMemoryError — JVM ran out of memory
public class ErrorDemo2 {
    public static void main(String[] args) {
        int[] arr = new int[Integer.MAX_VALUE];  // OutOfMemoryError
    }
}
```

### 3. `Exception` — Things YOU CAN and SHOULD handle

#### a) Checked Exceptions (Compile-time)
- The **compiler forces you** to handle them
- If you don't handle them, your code **won't even compile**

```java
import java.io.*;

public class CheckedDemo {
    public static void main(String[] args) {
        FileReader fr = new FileReader("abc.txt");  // ❌ WON'T COMPILE!
        // Compiler says: "Unhandled exception: FileNotFoundException"
    }
}
```

#### b) Unchecked Exceptions (Runtime)
- The compiler does **NOT** force you to handle them
- They occur due to **programming mistakes**

```java
public class UncheckedDemo {
    public static void main(String[] args) {
        int result = 10 / 0;  // ✅ Compiles fine, but crashes at RUNTIME
    }
}
```

### Quick Comparison:

| Feature | Checked Exception | Unchecked Exception | Error |
|---|---|---|---|
| **Checked by compiler?** | Yes | No | No |
| **Must handle?** | Yes (forced) | No (optional) | No (don't handle) |
| **Extends** | `Exception` (directly) | `RuntimeException` | `Error` |
| **Cause** | External factors (file, network, DB) | Programming bugs | JVM problems |
| **Examples** | IOException, SQLException | NullPointerException, ArithmeticException | StackOverflowError, OutOfMemoryError |

### Golden Rule:
- **Checked** = "Hey developer, this MIGHT go wrong. Handle it NOW or I won't compile."
- **Unchecked** = "This is YOUR bug. Fix your logic."
- **Error** = "The JVM is dying. Nothing you can do."

---

## Concept 3: try-catch Block (How to Actually Handle Exceptions)

Now that you know what exceptions are, let's learn how to **catch and handle** them so your program doesn't crash.

### Syntax:

```java
try {
    // risky code that MIGHT throw an exception
} catch (ExceptionType variableName) {
    // what to do IF the exception occurs
}
```

- **`try` block** — Put the dangerous/risky code here
- **`catch` block** — This runs ONLY if an exception occurs in the try block

### Example 1: Handling ArithmeticException

```java
public class TryCatchDemo {
    public static void main(String[] args) {
        try {
            int result = 10 / 0;                       // exception occurs here
            System.out.println("Result: " + result);  // skipped
        } catch (ArithmeticException e) {
            System.out.println("Cannot divide by zero!");
        }
        System.out.println("Program continues normally!");  // THIS EXECUTES!
    }
}
```

**Output:**
```
Cannot divide by zero!
Program continues normally!
```

Notice the difference from Concept 1 — the program **did NOT crash**! It caught the exception, handled it, and continued running the code after the try-catch block.

### Example 2: Handling NullPointerException

```java
public class TryCatchDemo2 {
    public static void main(String[] args) {
        try {
            String name = null;
            System.out.println(name.length());  // exception here
        } catch (NullPointerException e) {
            System.out.println("Oops! The string was null.");
        }
        System.out.println("Still running!");
    }
}
```

**Output:**
```
Oops! The string was null.
Still running!
```

### Example 3: Handling ArrayIndexOutOfBoundsException

```java
public class TryCatchDemo3 {
    public static void main(String[] args) {
        try {
            int[] arr = {10, 20, 30};
            System.out.println(arr[5]);              // exception here
            System.out.println("This won't print");  // skipped
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Invalid index! Array only has 3 elements.");
        }
        System.out.println("Program is alive!");
    }
}
```

**Output:**
```
Invalid index! Array only has 3 elements.
Program is alive!
```

### The Flow — How try-catch executes:

**Scenario A: Exception OCCURS**
```
try {
    line 1  ✅ executes
    line 2  💥 exception here
    line 3  ❌ SKIPPED (everything after the exception line in try is skipped)
} catch (Exception e) {
    line 4  ✅ catch block executes
}
line 5  ✅ code after try-catch executes normally
```

**Scenario B: NO exception occurs**
```
try {
    line 1  ✅ executes
    line 2  ✅ executes
    line 3  ✅ executes
} catch (Exception e) {
    line 4  ❌ SKIPPED (no exception, so catch is never entered)
}
line 5  ✅ executes normally
```

### Example showing Scenario B (No Exception):

```java
public class NoExceptionDemo {
    public static void main(String[] args) {
        try {
            int result = 10 / 2;  // no problem here
            System.out.println("Result: " + result);  // prints fine
        } catch (ArithmeticException e) {
            System.out.println("This will NOT print");  // skipped entirely
        }
        System.out.println("Done!");
    }
}
```

**Output:**
```
Result: 5
Done!
```

### What is that `e` variable in catch?

The `e` is the **exception object**. It contains details about what went wrong. You can use it:

```java
public class ExceptionObjectDemo {
    public static void main(String[] args) {
        try {
            int result = 10 / 0;
        } catch (ArithmeticException e) {
            System.out.println(e.getMessage());         // "/ by zero"
            System.out.println(e.getClass().getName()); // "java.lang.ArithmeticException"
            e.printStackTrace();                        // full error trace (like the crash message)
        }
    }
}
```

**Output:**
```
/ by zero
java.lang.ArithmeticException
java.lang.ArithmeticException: / by zero
    at ExceptionObjectDemo.main(ExceptionObjectDemo.java:4)
```

- `e.getMessage()` — gives a short description of the error
- `e.getClass().getName()` — gives the full exception class name
- `e.printStackTrace()` — prints the complete stack trace (most useful for debugging)

### Key Takeaways:
- `try` = wrap your risky code
- `catch` = define what to do when things go wrong
- Program **doesn't crash** — it recovers and continues
- If **no exception** occurs, the catch block is **completely skipped**
- The `e` object gives you details about the error

---

## Concept 4: Multiple catch Blocks

What if your code can throw **different types** of exceptions? You can write **multiple catch blocks** to handle each one differently.

### Syntax:

```java
try {
    // risky code
} catch (ExceptionType1 e) {
    // handle ExceptionType1
} catch (ExceptionType2 e) {
    // handle ExceptionType2
} catch (ExceptionType3 e) {
    // handle ExceptionType3
}
```

### Example 1: Different exceptions, different handling

```java
public class MultipleCatchDemo {
    public static void main(String[] args) {
        try {
            int[] arr = {10, 20, 30};

            // Which exception will occur? Depends on the code!
            System.out.println(arr[5]);         // ArrayIndexOutOfBoundsException
            // System.out.println(10 / 0);      // ArithmeticException
            // String s = null; s.length();      // NullPointerException

        } catch (ArithmeticException e) {
            System.out.println("Cannot divide by zero!");
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Invalid array index!");
        } catch (NullPointerException e) {
            System.out.println("Null value found!");
        }

        System.out.println("Program continues...");
    }
}
```

**Output:**
```
Invalid array index!
Program continues...
```

Only **ONE** catch block runs — the one that matches the exception type. Then it jumps to the code after the entire try-catch.

### Example 2: User input scenario (realistic use case)

```java
public class MultipleCatchDemo2 {
    public static void main(String[] args) {
        try {
            String input = "abc";
            int num = Integer.parseInt(input);    // NumberFormatException!
            int result = 100 / num;               // never reaches here
            System.out.println("Result: " + result);
        } catch (NumberFormatException e) {
            System.out.println("Invalid number format: " + e.getMessage());
        } catch (ArithmeticException e) {
            System.out.println("Division by zero!");
        }

        System.out.println("Done!");
    }
}
```

**Output:**
```
Invalid number format: For input string: "abc"
Done!
```

Now change `"abc"` to `"0"`:

```java
String input = "0";
int num = Integer.parseInt(input);  // works fine, num = 0
int result = 100 / num;            // ArithmeticException! (100/0)
```

**Output:**
```
Division by zero!
Done!
```

See? Different inputs trigger different catch blocks.

### IMPORTANT RULE: Order matters — Child before Parent!

```java
// ❌ WRONG — WON'T COMPILE!
try {
    int result = 10 / 0;
} catch (Exception e) {             // Parent catches EVERYTHING
    System.out.println("Exception");
} catch (ArithmeticException e) {    // ❌ UNREACHABLE — never gets here
    System.out.println("Arithmetic");
}
```

**Compiler Error:** `exception ArithmeticException has already been caught`

Since `Exception` is the **parent** of `ArithmeticException`, the first catch grabs it before the second catch ever gets a chance. The second catch becomes **dead/unreachable code**.

```java
// ✅ CORRECT — Specific (child) first, General (parent) last
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {    // specific — checked first
    System.out.println("Arithmetic");
} catch (Exception e) {             // general — catches anything else
    System.out.println("Some other exception");
}
```

**Output:**
```
Arithmetic
```

### Example 3: Using parent `Exception` as a safety net

```java
public class SafetyNetDemo {
    public static void main(String[] args) {
        try {
            int[] arr = {1, 2, 3};
            arr[0] = 10 / 0;               // ArithmeticException
        } catch (ArithmeticException e) {
            System.out.println("Math error: " + e.getMessage());
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Array error: " + e.getMessage());
        } catch (Exception e) {
            System.out.println("Something unexpected: " + e.getMessage());
        }
    }
}
```

**Output:**
```
Math error: / by zero
```

The `catch (Exception e)` at the bottom acts as a **safety net** — if none of the specific catches match, this one will catch whatever is left.

### Multi-catch with pipe `|` operator (Java 7+):

If you want to handle **multiple exceptions the same way**, use the `|` (pipe) operator:

```java
public class MultiCatchPipeDemo {
    public static void main(String[] args) {
        try {
            // some risky code
            int result = Integer.parseInt("abc");
        } catch (ArithmeticException | NumberFormatException | NullPointerException e) {
            System.out.println("Something went wrong: " + e.getMessage());
        }
    }
}
```

**Output:**
```
Something went wrong: For input string: "abc"
```

Instead of writing 3 separate catch blocks with the same body, you combine them with `|`. Much cleaner!

### Key Takeaways:
- You can have **multiple catch blocks** for one try block
- Only **ONE** catch block executes — the first one that matches
- **Order matters**: put specific (child) exceptions **first**, general (parent) exceptions **last**
- Use `catch (Exception e)` at the end as a **safety net** for unexpected exceptions
- Use `|` pipe operator to handle multiple exceptions with the **same logic**

---

## Concept 5: The `finally` Block (Code that ALWAYS Runs)

The `finally` block is a special block that **always executes** — no matter what happens. Whether an exception occurs or not, whether the catch block runs or not — `finally` will run.

### Syntax:

```java
try {
    // risky code
} catch (ExceptionType e) {
    // handle exception
} finally {
    // THIS ALWAYS RUNS — exception or no exception
}
```

### Example 1: Exception occurs — finally still runs

```java
public class FinallyDemo1 {
    public static void main(String[] args) {
        try {
            System.out.println("Step 1: Inside try");
            int result = 10 / 0;                     // exception!
            System.out.println("Step 2: " + result); // skipped
        } catch (ArithmeticException e) {
            System.out.println("Step 3: Caught exception");
        } finally {
            System.out.println("Step 4: Finally block executed");
        }
        System.out.println("Step 5: After try-catch-finally");
    }
}
```

**Output:**
```
Step 1: Inside try
Step 3: Caught exception
Step 4: Finally block executed
Step 5: After try-catch-finally
```

Flow: try (partially) → catch → **finally** → code continues

### Example 2: No exception — finally STILL runs

```java
public class FinallyDemo2 {
    public static void main(String[] args) {
        try {
            System.out.println("Step 1: Inside try");
            int result = 10 / 2;
            System.out.println("Step 2: Result = " + result);
        } catch (ArithmeticException e) {
            System.out.println("Step 3: Caught exception");  // skipped
        } finally {
            System.out.println("Step 4: Finally block executed");
        }
        System.out.println("Step 5: After try-catch-finally");
    }
}
```

**Output:**
```
Step 1: Inside try
Step 2: Result = 5
Step 4: Finally block executed
Step 5: After try-catch-finally
```

Flow: try (fully) → catch skipped → **finally** → code continues

### Example 3: Exception NOT caught — finally STILL runs, then program crashes

```java
public class FinallyDemo3 {
    public static void main(String[] args) {
        try {
            System.out.println("Inside try");
            int result = 10 / 0;                     // ArithmeticException
        } catch (NullPointerException e) {            // WRONG catch — doesn't match!
            System.out.println("Caught NPE");
        } finally {
            System.out.println("Finally runs anyway!");
        }
        System.out.println("This won't print");       // program crashes before this
    }
}
```

**Output:**
```
Inside try
Finally runs anyway!
Exception in thread "main" java.lang.ArithmeticException: / by zero
    at FinallyDemo3.main(FinallyDemo3.java:5)
```

The catch didn't match (we tried to catch `NullPointerException` but `ArithmeticException` was thrown). But **finally still ran!** Then the program crashed because the exception was unhandled.

### Why do we need `finally`? — Real-world use case

The main purpose of `finally` is to **clean up resources** — close files, close database connections, close network connections — things that MUST happen whether your code succeeded or failed.

```java
import java.io.*;

public class FinallyResourceDemo {
    public static void main(String[] args) {
        FileReader reader = null;
        try {
            reader = new FileReader("data.txt");
            // read file contents...
            System.out.println("File read successfully");
        } catch (FileNotFoundException e) {
            System.out.println("File not found: " + e.getMessage());
        } finally {
            // Close the file NO MATTER WHAT
            if (reader != null) {
                try {
                    reader.close();
                    System.out.println("File closed");
                } catch (IOException e) {
                    System.out.println("Error closing file");
                }
            }
        }
    }
}
```

Whether the file was read successfully or not, we **must close it** in the finally block. Leaving files/connections open causes **resource leaks**.

### Valid combinations of try-catch-finally:

```java
// ✅ try + catch
try { } catch (Exception e) { }

// ✅ try + catch + finally
try { } catch (Exception e) { } finally { }

// ✅ try + finally (no catch!)
try { } finally { }

// ❌ INVALID — try alone
try { }   // WON'T COMPILE — needs at least catch or finally

// ❌ INVALID — catch alone
catch (Exception e) { }  // WON'T COMPILE

// ❌ INVALID — finally alone
finally { }  // WON'T COMPILE
```

Notice that `try + finally` without catch is valid! This is useful when you don't want to handle the exception but still need cleanup code to run.

### Key Takeaways:
- `finally` block **ALWAYS executes** — exception or no exception
- Even if the catch **doesn't match**, finally still runs
- Main purpose: **resource cleanup** (close files, connections, streams)
- Valid combos: `try-catch`, `try-catch-finally`, `try-finally`
- `try` alone is **NOT valid** — it needs at least `catch` or `finally`

---

## Concept 6: `throw` Keyword (Manually Throwing Exceptions)

So far, exceptions were thrown **automatically by Java** (dividing by zero, null access, etc.). But `throw` lets **you** manually create and throw an exception yourself.

### Syntax:

```java
throw new ExceptionType("your error message");
```

You create a **new exception object** and `throw` it.

### Example 1: Basic throw

```java
public class ThrowDemo {
    public static void main(String[] args) {
        int age = -5;

        if (age < 0) {
            throw new ArithmeticException("Age cannot be negative: " + age);
        }

        System.out.println("Your age is: " + age);  // never reaches here
    }
}
```

**Output:**
```
Exception in thread "main" java.lang.ArithmeticException: Age cannot be negative: -5
    at ThrowDemo.main(ThrowDemo.java:5)
```

YOU decided that negative age is invalid, so YOU threw the exception. Java didn't detect this problem — **you defined the rule**.

### Example 2: throw inside a method

```java
public class ThrowDemo2 {
    static void validateAge(int age) {
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
        if (age > 150) {
            throw new IllegalArgumentException("Age cannot be more than 150");
        }
        System.out.println("Valid age: " + age);
    }

    public static void main(String[] args) {
        validateAge(25);   // ✅ Valid age: 25
        validateAge(-10);  // 💥 IllegalArgumentException
        validateAge(200);  // never reaches here
    }
}
```

**Output:**
```
Valid age: 25
Exception in thread "main" java.lang.IllegalArgumentException: Age cannot be negative
    at ThrowDemo2.validateAge(ThrowDemo2.java:4)
    at ThrowDemo2.main(ThrowDemo2.java:13)
```

### Example 3: throw + try-catch together

You can **throw** in one place and **catch** in another:

```java
public class ThrowCatchDemo {
    static void withdraw(double balance, double amount) {
        if (amount > balance) {
            throw new ArithmeticException("Insufficient balance! You have: " + balance);
        }
        System.out.println("Withdrawal successful. Remaining: " + (balance - amount));
    }

    public static void main(String[] args) {
        try {
            withdraw(5000, 3000);   // works fine
            withdraw(2000, 8000);   // throws exception
        } catch (ArithmeticException e) {
            System.out.println("Transaction failed: " + e.getMessage());
        }
        System.out.println("Bank system still running");
    }
}
```

**Output:**
```
Withdrawal successful. Remaining: 2000.0
Transaction failed: Insufficient balance! You have: 2000.0
Bank system still running
```

The method `withdraw()` **throws** the exception. The `main()` method **catches** it. This is a very common pattern — the method that detects the problem throws, and the caller handles it.

### Example 4: Re-throwing an exception

You can catch an exception, do something with it, and then **throw it again**:

```java
public class ReThrowDemo {
    public static void main(String[] args) {
        try {
            try {
                int result = 10 / 0;
            } catch (ArithmeticException e) {
                System.out.println("Caught inside. Logging and re-throwing...");
                throw e;  // re-throw the same exception
            }
        } catch (ArithmeticException e) {
            System.out.println("Caught outside: " + e.getMessage());
        }
    }
}
```

**Output:**
```
Caught inside. Logging and re-throwing...
Caught outside: / by zero
```

The inner catch logs the error and re-throws it. The outer catch handles it. This is common when you want to **log** an exception at one level but let a **higher level** decide what to do.

### What can you throw?

You can only throw objects that are **subclasses of `Throwable`**:

```java
// ✅ Valid — all of these extend Throwable
throw new Exception("error");
throw new RuntimeException("error");
throw new ArithmeticException("error");
throw new NullPointerException("error");
throw new IllegalArgumentException("error");

// ❌ INVALID — cannot throw non-Throwable objects
throw new String("error");     // WON'T COMPILE
throw new Integer(5);          // WON'T COMPILE
```

### Key Rules:
- `throw` lets **you** manually throw an exception
- Syntax: `throw new ExceptionType("message");`
- Use it to **enforce your own rules** (validate inputs, business logic)
- Common pattern: method **throws**, caller **catches**
- You can **re-throw** a caught exception
- You can only throw objects that extend `Throwable`

---

## Concept 7: `throws` Keyword (Declaring Exceptions in Method Signature)

Don't confuse `throw` with `throws` — they are **different** keywords!

| | `throw` | `throws` |
|---|---|---|
| **What** | Actually throws an exception | Declares that a method **might** throw an exception |
| **Where** | Inside the method body | In the method **signature** (declaration line) |
| **Followed by** | An exception **object** | Exception **class name(s)** |

### Syntax:

```java
returnType methodName(params) throws ExceptionType1, ExceptionType2 {
    // method body
}
```

### Why do we need `throws`?

Remember from Concept 2 — **checked exceptions** must be handled at compile time. You have two choices:

1. **Handle it yourself** with try-catch (you deal with it right here)
2. **Declare it with `throws`** and pass the responsibility to the caller (let someone else deal with it)

### Example 1: Without `throws` — checked exception won't compile

```java
import java.io.*;

public class ThrowsDemo1 {
    static void readFile() {
        FileReader fr = new FileReader("data.txt");  // ❌ WON'T COMPILE!
        // Compiler: "Unhandled exception: FileNotFoundException"
    }

    public static void main(String[] args) {
        readFile();
    }
}
```

`FileNotFoundException` is a **checked exception**. The compiler forces you to deal with it.

### Choice 1: Handle with try-catch (you handle it)

```java
import java.io.*;

public class ThrowsDemo2 {
    static void readFile() {
        try {
            FileReader fr = new FileReader("data.txt");
            System.out.println("File opened");
        } catch (FileNotFoundException e) {
            System.out.println("File not found: " + e.getMessage());
        }
    }

    public static void main(String[] args) {
        readFile();  // caller doesn't need to worry
    }
}
```

### Choice 2: Declare with `throws` (pass it to the caller)

```java
import java.io.*;

public class ThrowsDemo3 {
    static void readFile() throws FileNotFoundException {
        FileReader fr = new FileReader("data.txt");  // ✅ compiles!
        System.out.println("File opened");
    }

    public static void main(String[] args) {
        try {
            readFile();  // caller MUST handle it now
        } catch (FileNotFoundException e) {
            System.out.println("File not found: " + e.getMessage());
        }
    }
}
```

By writing `throws FileNotFoundException`, the method says: *"I might throw this exception. I'm not handling it — whoever calls me must deal with it."*

### Example 2: Chain of `throws` — passing responsibility upward

```java
import java.io.*;

public class ThrowsChainDemo {

    static void readFile() throws FileNotFoundException {
        FileReader fr = new FileReader("data.txt");
    }

    static void processFile() throws FileNotFoundException {
        readFile();  // doesn't handle, passes it up with throws
    }

    static void startApp() throws FileNotFoundException {
        processFile();  // doesn't handle, passes it up again
    }

    public static void main(String[] args) {
        try {
            startApp();  // finally someone handles it!
        } catch (FileNotFoundException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

The exception responsibility bubbles up: `readFile()` → `processFile()` → `startApp()` → `main()` finally catches it. This is called **exception propagation**.

### Example 3: Multiple exceptions in `throws`

```java
import java.io.*;
import java.sql.*;

public class MultiThrowsDemo {
    static void loadData() throws FileNotFoundException, SQLException, IOException {
        FileReader fr = new FileReader("config.txt");
        // ... database operations ...
    }

    public static void main(String[] args) {
        try {
            loadData();
        } catch (FileNotFoundException e) {
            System.out.println("Config file missing");
        } catch (SQLException e) {
            System.out.println("Database error");
        } catch (IOException e) {
            System.out.println("IO error");
        }
    }
}
```

### Example 4: `throws` with unchecked exceptions — allowed but NOT required

```java
public class ThrowsUncheckedDemo {
    // throws is OPTIONAL for unchecked exceptions
    static void divide(int a, int b) throws ArithmeticException {
        System.out.println(a / b);
    }

    // works exactly the same without throws
    static void divide2(int a, int b) {
        System.out.println(a / b);
    }

    public static void main(String[] args) {
        divide(10, 0);   // crashes
        divide2(10, 0);  // also crashes — no difference
    }
}
```

For **unchecked exceptions** (RuntimeException and its subclasses), `throws` is purely informational — the compiler doesn't enforce it. But for **checked exceptions**, it's mandatory.

### Example 5: `throw` and `throws` working together

```java
public class ThrowAndThrowsDemo {

    static void setAge(int age) throws IllegalArgumentException {
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("Invalid age: " + age);  // throw
        }
        System.out.println("Age set to: " + age);
    }

    public static void main(String[] args) {
        try {
            setAge(25);
            setAge(-5);
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

**Output:**
```
Age set to: 25
Error: Invalid age: -5
```

- `throw` **creates and throws** the exception inside the method
- `throws` **declares** in the method signature that this exception might be thrown

### Key Rules:
- `throws` goes in the **method signature** — declares what exceptions the method might throw
- Used to **pass responsibility** to the caller
- **Required** for checked exceptions if you don't use try-catch
- **Optional** for unchecked exceptions
- Multiple exceptions separated by commas: `throws A, B, C`
- Exception propagation: responsibility can bubble up through multiple methods

---

# Task F2.9 — Hands-on Subtasks (Zenith plan)

Concepts 1–7 above are the theory. This part follows the plan's F2.9 subtasks one at a time,
using Zenith-style examples (seats, events, bookings). Every code example and output below
was compiled and run on Java 21.

### Subtasks covered so far
- [x] Subtask 1 — A checked and an unchecked exception: the compiler nags about only one
- [x] Subtask 2 — The modern convention: most application exceptions are unchecked
- [x] Subtask 3 — Prove `finally` runs no matter what (and the only things that skip it)
- [x] Subtask 4 — Break it on purpose: `return` from `finally`
- [x] Subtask 5 — Closing a file: old `finally` way vs try-with-resources
- [x] Subtask 6 — Your own `AutoCloseable` class
- [ ] Subtask 7 — Multi-catch, and the specific-to-general order
- [ ] Subtask 8 — The shape of a good `try` block

### New words from the plan

| Word | Plain meaning |
|---|---|
| **Exception** | An object describing a failure, thrown at the point of failure and caught somewhere above. |
| **Throw** | To stop normal execution and hand an exception upwards. |
| **Checked exception** | One the compiler forces you to catch or declare. Extends `Exception` but not `RuntimeException`. |
| **Unchecked exception** | One the compiler ignores. Extends `RuntimeException`. Most application exceptions are these. |
| **`throws` clause** | The part of a method signature announcing which checked exceptions it may throw. |
| **`finally`** | A block that runs whether the `try` succeeded, failed, or returned. |
| **`AutoCloseable`** | An interface meaning "I hold something that must be released". Files, sockets and database connections implement it. |
| **try-with-resources** | `try (var x = ...)`. Closes the resource for you, in the right order, even when something throws. |

---

## Subtask 1: A Checked and an Unchecked Exception — the Compiler Nags About Only One

> **From the plan:** Write one method that throws a checked exception and one that throws an
> unchecked one. Call both. Notice the compiler nags about one and says nothing about the
> other. Write the difference in `NOTES.md` in one sentence.

### What it's asking

Concept 2 told you the difference. Now you **feel** it, by writing both kinds side by side
and watching what the compiler does:

1. One method that throws a **checked** exception (here `IOException`, which extends
   `Exception` but not `RuntimeException`).
2. One method that throws an **unchecked** exception (here `IllegalArgumentException`, which
   extends `RuntimeException`).
3. Call both from `main` and try to compile.

### The code (version 1 — doesn't compile)

```java
import java.io.IOException;

public class SeatService {
    static void loadSeatMap(String eventName) throws IOException {
        throw new IOException("Seat map file for " + eventName + " is missing");
    }

    static void bookSeat(int number) {
        if (number <= 0) {
            throw new IllegalArgumentException("Seat number must be positive: " + number);
        }
        System.out.println("Booked seat " + number);
    }

    public static void main(String[] args) {
        loadSeatMap("Coldplay");
        bookSeat(-1);
    }
}
```

- **`loadSeatMap`** throws a **checked** exception. The seat map is a file on disk and the
  file might be missing, so the method must announce it with `throws IOException`.
- **`bookSeat`** throws an **unchecked** exception. A negative seat number is a bug in the
  caller. No `throws` needed.

Compile it with `javac SeatService.java`:

```
SeatService.java:16: error: unreported exception IOException; must be caught or declared to be thrown
        loadSeatMap("Coldplay");
                   ^
1 error
```

(IntelliJ shows the same thing as a red underline: *"Unhandled exception: java.io.IOException"*.)

Look at what the compiler did:
- **`loadSeatMap(...)`** — error. It knows `loadSeatMap` *says* it throws `IOException`
  (its `throws` clause), so it insists you deal with it.
- **`bookSeat(-1)`** — **nothing**. Not even a warning, even though `-1` is obviously going
  to fail. The compiler never checks for `RuntimeException`s.

**1 error**, not 2. That's the whole subtask in one number.

### Reading the error message

`unreported exception IOException; must be caught or declared to be thrown` gives you
exactly two ways out:

| The message says | What you write | Who deals with it |
|---|---|---|
| "must be **caught**" | wrap the call in `try { ... } catch (IOException e) { ... }` | this method (`main`) |
| "or **declared to be thrown**" | add `throws IOException` to `main`'s signature | whoever calls `main` (Concept 7) |

### The code (version 2 — catch the checked one)

```java
public static void main(String[] args) {
    try {
        loadSeatMap("Coldplay");
    } catch (IOException e) {
        System.out.println("Could not load seat map: " + e.getMessage());
    }

    bookSeat(5);
    bookSeat(-1);
    System.out.println("This line never prints");
}
```

It compiles now. Run it with `java SeatService`:

```
Could not load seat map: Seat map file for Coldplay is missing
Booked seat 5
Exception in thread "main" java.lang.IllegalArgumentException: Seat number must be positive: -1
	at SeatService.bookSeat(SeatService.java:10)
	at SeatService.main(SeatService.java:23)
```

Step by step:
1. `loadSeatMap` threw `IOException` → our `catch` handled it → program carried on.
2. `bookSeat(5)` worked.
3. `bookSeat(-1)` threw `IllegalArgumentException`. Nobody caught it (the compiler never
   asked us to), so it rose out of `main` and the program crashed. The stack trace says
   where: `bookSeat` line 10, called from `main` line 23.
4. "This line never prints" — skipped, like Concept 1.

So unchecked doesn't mean "harmless". It means **the compiler won't remind you**; you find
out at runtime.

### Experiments

(Experiments A and B were run as small separate files, so the line numbers in their messages
won't match your `SeatService.java`. Read the message, not the number.)

**Experiment A — remove `throws IOException` from `loadSeatMap` itself.**

```java
static void loadSeatMap(String eventName) {          // no throws
    throw new IOException("Seat map file for " + eventName + " is missing");
}
```

```
SeatService.java:5: error: unreported exception IOException; must be caught or declared to be thrown
        throw new IOException("Seat map file for " + eventName + " is missing");
        ^
```

Same rule, one level down: the method that **throws** a checked exception must also catch it
or declare it. The `throws` clause is a promise to callers: "this might happen to you."

Now try the same thing with `bookSeat`: it has no `throws` and compiles fine. Adding
`throws IllegalArgumentException` would be allowed but changes nothing (Concept 7,
Example 4).

**Experiment B — the other way out: declare it on `main`.**

```java
public static void main(String[] args) throws IOException {
    loadSeatMap("Coldplay");
}
```

It compiles, and running it crashes:

```
Exception in thread "main" java.io.IOException: Seat map file for Coldplay is missing
	at SeatService.loadSeatMap(SeatService.java:5)
	at SeatService.main(SeatService.java:9)
```

The compiler is satisfied because you *declared* it, but nobody above `main` handles it,
so the JVM prints it and stops. Declaring isn't handling; it just passes the problem up.

### How the compiler decides "checked" or "unchecked"

It's only about the **family tree** (Concept 2), never about how serious the problem is:

```
Exception
├── IOException                  ← checked   (extends Exception, not RuntimeException)
└── RuntimeException
    └── IllegalArgumentException ← unchecked (extends RuntimeException)
```

If the class has `RuntimeException` (or `Error`) above it, the compiler ignores it.
Otherwise, it's checked and must be caught or declared.

### Write it in your `NOTES.md` (one sentence, as the plan asks)

Write yours first. A model:

```markdown
## F2.9 — checked vs unchecked
The compiler forces every caller to catch or declare a checked exception (extends Exception
but not RuntimeException, e.g. IOException), but says nothing about an unchecked one
(extends RuntimeException, e.g. IllegalArgumentException), which you only discover at runtime.
```

### Key Takeaways:
- **Checked** (`IOException`): `unreported exception ...; must be caught or declared to be
  thrown` until you add `try`/`catch` or `throws`.
- **Unchecked** (`IllegalArgumentException`): compiles silently, fails at runtime if not
  caught.
- The method that throws a checked exception must declare it (`throws`), and so must every
  caller that doesn't catch it.
- Declaring with `throws` isn't handling: if nobody catches it, the program still crashes.
- Checked or unchecked is decided by the class's parent (`RuntimeException` or not), not by
  how bad the error is.

### Check yourself
1. Why did the first version give **1** error and not 2?
2. What are the two ways to fix `unreported exception IOException`? Who handles the
   exception in each?
3. `bookSeat(-1)` compiled. Does that mean it's safe? What happened when it ran?
4. Is `NumberFormatException` checked or unchecked? How can you tell without running
   anything? (Hint: Concept 2's tree.)
5. In Experiment B, the code compiled but still crashed. Why?

### Subtask 1 — explained again from level 0

*(You asked for this again from scratch. Read this first if the section above felt fast.)*

#### Step 0: two moments in a Java program's life

Every Java program goes through two separate moments:

| Moment | Command | Who's working | What can go wrong |
|---|---|---|---|
| **Compile time** | `javac SeatService.java` | The **compiler** reads your code *without running it* and checks it's valid Java | "error: ..." and **no program is produced** |
| **Run time** | `java SeatService` | The **JVM** actually runs your code, line by line | an exception happens and the program crashes (if nobody catches it) |

Keep these two apart. The whole subtask is about **what the compiler checks before the
program ever runs.**

#### Step 1: what "throwing an exception" means

A method normally finishes its job and returns. Sometimes it **can't** do its job. Then it
does three things:

1. **Stops immediately**: the lines after it don't run.
2. **Writes a note** describing the problem. That note is an object, e.g.
   `new IOException("Seat map file for Coldplay is missing")`.
3. **Throws the note upward** to whoever called it.

`throw new IOException("...")` is exactly that: "create the note, throw it up."

The caller then either **catches** the note (`try`/`catch`) and deals with it, or doesn't,
and the note keeps flying upward. If it flies out of `main`, the program crashes and Java
prints the note (that's the red "Exception in thread main ..." text).

#### Step 2: two kinds of problem

Think about Zenith (a ticket-booking app):

| Problem | Whose fault? | Can good code prevent it? |
|---|---|---|
| The seat map **file is missing** from the disk | Nobody's. The outside world (disk, network, database) | **No.** Even perfect code can't stop a file being deleted. You can only *plan* for it. |
| Someone calls `bookSeat(-1)` | **The programmer's.** Seat numbers start at 1 | **Yes.** Fix the code that passes `-1`. |

Java treats these two differently:

- **Problems from the outside world** → **checked exceptions** (e.g. `IOException`).
  Because they *will* happen one day, Java makes you **plan for them in advance**.
- **Programming mistakes** → **unchecked exceptions** (e.g. `IllegalArgumentException`,
  `NullPointerException`, `ArithmeticException`). Java doesn't make you plan for these;
  the right response is to fix the bug.

#### Step 3: "checked" means the compiler checks you planned for it

The word is literal: for a **checked** exception, the **compiler checks** that every place
that could receive it has a plan. A plan is one of two things:

- **Catch it here:** `try { ... } catch (IOException e) { ... }`. "If it happens, I'll do this."
- **Pass it up, openly:** `throws IOException` in the method's first line. "I won't deal with it;
  my caller must."

No plan → the compiler **refuses to compile**. You can't even run the program.

For an **unchecked** exception, the compiler checks **nothing**. It compiles happily.
You only find out when the program runs and crashes.

An analogy: a school trip (your program) needs a signed permission slip for the risky
activity (the checked exception). The school (compiler) won't let the bus leave until
someone has signed: either you (catch) or your parent (throws, pass it up). For ordinary
mistakes like forgetting your lunch (unchecked), there's no slip. The bus leaves, and you
find out at lunchtime.

#### Step 4: how does Java know which kind an exception is?

Not by how serious it is. **Only by its parent class** (the family tree from Concept 2):

```
Exception
├── IOException                  → parent is Exception          → CHECKED
└── RuntimeException
    └── IllegalArgumentException → has RuntimeException above it → UNCHECKED
```

Rule: **is `RuntimeException` somewhere above it? Then unchecked. Otherwise checked.**

#### Step 5: the code, line by line

```java
import java.io.IOException;
```
`IOException` lives in the `java.io` package, so we import it. (`IllegalArgumentException`
lives in `java.lang`, which is always available, so no import is needed.)

```java
static void loadSeatMap(String eventName) throws IOException {
    throw new IOException("Seat map file for " + eventName + " is missing");
}
```
- `throws IOException` (with an **s**) is a **label** on the method: "warning, calling me
  might throw an IOException at you." This is the method's *plan*: pass it up.
- `throw new IOException(...)` (no **s**) **actually throws** it. To keep the demo
  simple, it always fails, as if the file were always missing.

```java
static void bookSeat(int number) {
    if (number <= 0) {
        throw new IllegalArgumentException("Seat number must be positive: " + number);
    }
    System.out.println("Booked seat " + number);
}
```
- No `throws` label: `IllegalArgumentException` is unchecked, so none is needed.
- If the number is 0 or negative, throw. Otherwise print "Booked seat ...".

```java
public static void main(String[] args) {
    loadSeatMap("Coldplay");
    bookSeat(-1);
}
```
`main` calls both, and has **no plan** for either.

#### Step 6: compile it and read the message word by word

```
SeatService.java:16: error: unreported exception IOException; must be caught or declared to be thrown
        loadSeatMap("Coldplay");
                   ^
1 error
```

| Piece | Meaning |
|---|---|
| `SeatService.java:16` | File and line number: the `loadSeatMap("Coldplay");` call in `main` |
| `unreported exception IOException` | "This line can throw an IOException, and you haven't said what to do about it" |
| `must be caught` | Plan 1: put it in `try`/`catch` |
| `or declared to be thrown` | Plan 2: add `throws IOException` to `main` |
| `^` | Points at the exact spot |
| `1 error` | **Only one.** `bookSeat(-1)` got no complaint. |

Why did the compiler know? It read the label `throws IOException` on `loadSeatMap`, saw
that `main` has no plan for it, and refused. For `bookSeat` there's no label and the
exception is unchecked, so the compiler has nothing to check, even though `-1` is
obviously wrong.

#### Step 7: give `main` a plan, then run it

```java
public static void main(String[] args) {
    try {
        loadSeatMap("Coldplay");
    } catch (IOException e) {
        System.out.println("Could not load seat map: " + e.getMessage());
    }

    bookSeat(5);
    bookSeat(-1);
    System.out.println("This line never prints");
}
```

Now it compiles. Run it:

```
Could not load seat map: Seat map file for Coldplay is missing
Booked seat 5
Exception in thread "main" java.lang.IllegalArgumentException: Seat number must be positive: -1
	at SeatService.bookSeat(SeatService.java:10)
	at SeatService.main(SeatService.java:23)
```

What happened, in order:

1. `loadSeatMap` threw the IOException note → `catch` caught it → printed our friendly
   message → the program **kept going**. (`e.getMessage()` reads the text written on the note.)
2. `bookSeat(5)`: 5 is fine → "Booked seat 5".
3. `bookSeat(-1)`: threw the IllegalArgumentException note. `main` has no `catch` for it
   (the compiler never asked for one), so the note flew out of `main` → **crash**.
4. The last two lines of red text are the **stack trace**: thrown in `bookSeat` at line 10,
   which was called from `main` at line 23.
5. "This line never prints" never ran, because the program had already stopped.

#### Step 8: the one sentence

> **The compiler refuses to compile until a checked exception is caught or declared, but
> says nothing about an unchecked one, which you only find out about when the program
> runs.**

#### Mini quiz (answer in your head)

1. Does `javac` run your program? *(No: it only checks it.)*
2. `throw` vs `throws`: which one actually throws, and which is a label? *(throw throws;
   throws is the label.)*
3. A missing file: checked or unchecked? Why? *(Checked: it's the outside world, so you must
   plan for it.)*
4. `bookSeat(-1)` compiled. Is it safe? *(No: it crashed at run time.)*

### Your question — why is `throws` only required for checked exceptions?

**Short answer:** `throws` is a warning label for problems a caller can *plan for*. Unchecked
exceptions are bugs that can come out of almost **any line**, so labelling them would put
the same long, useless list on every method, and the caller couldn't do anything useful with
it anyway.

**Reason 1 — unchecked exceptions can come from almost any line.** Look at this ordinary
method:

```java
static int pricePerSeat(Event event, int[] prices, int index, Object discount) {
    String name = event.name();          // NullPointerException if event is null
    int price = prices[index];           // ArrayIndexOutOfBoundsException if index is wrong
    int perSeat = price / event.seats(); // ArithmeticException if seats is 0
    Integer off = (Integer) discount;    // ClassCastException if it isn't an Integer
    return perSeat - off;                // NullPointerException if off is null
}
```

Five lines, four different unchecked exceptions, and you wrote `throw` **nowhere**: the JVM
throws them itself. If Java demanded `throws` for these, this method (and nearly every
method in every program) would need:

```java
static int pricePerSeat(...) throws NullPointerException, ArrayIndexOutOfBoundsException,
        ArithmeticException, ClassCastException { ... }
```

and every caller would have to repeat the list, and *their* callers too. The label would be
on everything, so it would tell you nothing. Checked exceptions are different: they only
come from an explicit `throw` or from calling a method that declares them, so the compiler
can track them exactly and the list stays short and meaningful.

**Reason 2 — the caller can do something about a checked one, but not about a bug.**

| | Checked: `IOException` (seat map file missing) | Unchecked: `bookSeat(-1)` |
|---|---|---|
| Can the caller *recover* while the program runs? | Yes: try a backup file, retry, show "seat map unavailable" | No: the code is wrong. Catching it doesn't make `-1` a valid seat |
| Right response | Plan for it, so the label is useful | **Fix the code** that passed `-1` |

The official Java tutorial ("Unchecked Exceptions — The Controversy") says exactly this:

> Runtime exceptions can occur anywhere in a program, and in a typical one they can be very
> numerous. Having to add runtime exceptions in every method declaration would reduce a
> program's clarity. Thus, the compiler does not require that you catch or specify runtime
> exceptions (although you can).

and gives the bottom-line guideline:

> If a client can reasonably be expected to recover from an exception, make it a checked
> exception. If a client cannot do anything to recover from the exception, make it an
> unchecked exception.

**So what's `throws` *for*?** It's part of the method's contract, like its parameters and
return type: "calling me can hand you this, so decide now what you'll do." The compiler
enforces it only where a decision is genuinely possible and the list stays small.

**You *can* still write `throws` for unchecked ones, as documentation.** Java itself does it.
`javap java.lang.Integer` shows:

```
public static int parseInt(java.lang.String) throws java.lang.NumberFormatException;
```

`NumberFormatException` is unchecked, so the compiler ignores the label. It's there to tell
readers "a bad string will throw this." Compare a checked one, which callers *must* handle:

```
public java.io.FileReader(java.lang.String) throws java.io.FileNotFoundException;
```

In practice, unchecked exceptions are usually documented in a Javadoc comment instead:

```java
/**
 * Books a seat.
 * @throws IllegalArgumentException if number is 0 or negative
 */
static void bookSeat(int number) { ... }
```

**A preview of Subtask 2:** the tutorial's guideline is the original 1990s design. Many
modern developers (and Spring) argue that even "recoverable" problems are usually better as
unchecked exceptions, because forcing every layer to catch or declare them creates noise.
That debate is Subtask 2.

---

## Subtask 2: Why Most Exceptions in Modern Java Are Unchecked

> **From the plan:** Understand the modern convention before you form habits: most
> application exceptions are unchecked, because forcing try/catch at every layer produces
> noise that hides real handling. Ask AI: *"Explain the argument for and against checked
> exceptions in Java, and tell me which style Spring itself uses and why."*

### The problem with checked exceptions

A method reads a file:

```java
static String readFile() throws IOException {
    throw new IOException("file not found");
}
```

Method `b` calls it, `a` calls `b`, and `main` calls `a`:

```java
static String b() throws IOException {
    return readFile();
}

static String a() throws IOException {
    return b();
}

public static void main(String[] args) {
    try {
        a();
    } catch (IOException e) {
        System.out.println("Error: " + e.getMessage());
    }
}
```

`a` and `b` **do nothing** about the error, yet both must write `throws IOException`. In a
big app this label spreads to dozens of methods. That's the **noise**.

### The bad habit people fall into: swallowing

```java
static String b() {
    try {
        return readFile();
    } catch (IOException e) {
        return null;   // hide the error
    }
}
```

The compiler is happy, but "file not found" is **lost**. Later the program crashes
somewhere else with a confusing `NullPointerException`. This is called **swallowing** an
exception, and it's the worst habit to form.

### The modern way: convert once, at the bottom

```java
static String readFile() {
    try {
        // ... code that reads the file and can throw IOException ...
    } catch (IOException e) {
        throw new RuntimeException("Could not read file", e);
    }
}

static String b() { return readFile(); }   // no throws
static String a() { return b(); }          // no throws

public static void main(String[] args) {
    try {
        a();
    } catch (RuntimeException e) {
        System.out.println("Error: " + e.getMessage());
    }
}
```

New things here:
- `new RuntimeException("Could not read file", e)`: the second argument `e` is the original
  exception, kept inside the new one as its **cause**. Nothing is lost. Read it back with
  `e.getCause()`.
- `a` and `b` are clean. The exception rises by itself to `main`, the one place that handles it.

**Rule: convert at the bottom, handle at the top, leave the middle alone.**

### For and against checked exceptions

| For | Against |
|---|---|
| The compiler reminds you the error can happen | Every method in between needs `throws` (noise) |
| You see the risk in the method signature | Tempts people to swallow errors |
| | Most in-between methods can't fix the error anyway |

### Which style Spring uses

**Spring uses unchecked exceptions.**
- Database code in plain Java throws `SQLException` (checked). Spring converts it into
  `DataAccessException` (unchecked): the same "convert at the bottom" idea.
- `@Transactional` (undo database changes if the method fails) by default undoes only for
  **unchecked** exceptions.

### For `NOTES.md`

> Modern Java mostly uses unchecked exceptions: convert checked ones at the bottom (keeping the
> cause), let them rise, and handle them in one place at the top. Spring does the same.

---

## Subtask 3: Prove `finally` Runs No Matter What

> **From the plan:** Write a method that returns inside `try` with a `finally` that prints.
> Then make the `try` throw instead. The `finally` prints both times. The only things that
> skip a `finally`: `System.exit`, or the process being killed outright (`kill -9`).

### Part 1 — `return` inside `try`

```java
public class Test1 {
    static String greet() {
        try {
            System.out.println("In try");
            return "Hello";
        } finally {
            System.out.println("In finally");
        }
    }

    public static void main(String[] args) {
        String result = greet();
        System.out.println("Got: " + result);
    }
}
```

**Output:**
```
In try
In finally
Got: Hello
```

1. `try` prints "In try".
2. `return "Hello"` **wants** to leave the method.
3. Java pauses that, runs `finally` first → "In finally".
4. Only then does "Hello" go back to `main`.

`finally` runs **before** the caller gets the value.

### Part 2 — `throw` inside `try`

```java
public class Test2 {
    static void work() {
        try {
            System.out.println("In try");
            throw new RuntimeException("Something broke");
        } finally {
            System.out.println("In finally");
        }
    }

    public static void main(String[] args) {
        try {
            work();
        } catch (RuntimeException e) {
            System.out.println("Caught: " + e.getMessage());
        }
    }
}
```

**Output:**
```
In try
In finally
Caught: Something broke
```

The exception **wants** to leave `work()`, Java runs `finally` first, then the exception
continues to `main`. `finally` printed **both times**.

### Part 3 — the only things that skip `finally`

**1. `System.exit(0)`** — stops the whole program immediately (`0` = "finished normally").

```java
public class Test3 {
    public static void main(String[] args) {
        try {
            System.out.println("In try");
            System.exit(0);
        } finally {
            System.out.println("In finally");
        }
    }
}
```

**Output:**
```
In try
```

The program is shut down, so nothing is left to run `finally`.

**2. `kill -9` from the terminal** — tells the operating system to destroy a running program
instantly. Every running program has a number called its **process id (PID)**; `-9` means
"force it, don't ask the program".

```java
public class Test4 {
    public static void main(String[] args) throws InterruptedException {
        try {
            System.out.println("My process id: " + ProcessHandle.current().pid());
            Thread.sleep(60000);
        } finally {
            System.out.println("In finally");
        }
    }
}
```

- `ProcessHandle.current().pid()` prints this program's process id.
- `Thread.sleep(60000)` waits 60 seconds. It can throw `InterruptedException` (checked),
  so `main` declares it.

Steps:
1. Terminal 1: `java Test4` → prints `My process id: 12345`.
2. While it waits, terminal 2: `kill -9 12345`.
3. Terminal 1 shows `zsh: killed     java Test4`. **"In finally" never prints.**

(Ctrl+C in terminal 1 also stops it without printing "In finally".)

### Summary

| What happens in `try` | Does `finally` run? |
|---|---|
| finishes normally | Yes |
| `return` | Yes, before the value is returned |
| throws an exception | Yes, before the exception moves up |
| `System.exit(0)` | **No** |
| `kill -9` (or Ctrl+C) | **No** |

### For `NOTES.md`

> `finally` runs whether `try` finishes, returns or throws (just before the method actually
> leaves); only stopping the whole program — `System.exit` or `kill -9` — skips it.

---

## Subtask 4: Break It on Purpose — `return` Inside `finally`

> **From the plan:** Return a value from `try` and a different value from `finally`. Note
> which one the caller sees, then write down that returning from `finally` is something you
> must never do.

### Part 1 — two returns: which one wins?

```java
public class Test5 {
    static int getNumber() {
        try {
            return 1;
        } finally {
            return 2;
        }
    }

    public static void main(String[] args) {
        System.out.println("Got: " + getNumber());
    }
}
```

**Output:**
```
Got: 2
```

1. `return 1` **wants** to send back 1.
2. Java runs `finally` first (Subtask 3).
3. `finally` has its own `return 2`, which **replaces** the 1.
4. The caller gets **2**. The 1 is silently lost.

### Part 2 — worse: `return` in `finally` hides an exception

```java
public class Test6 {
    static int divide() {
        try {
            return 10 / 0;
        } finally {
            return 0;
        }
    }

    public static void main(String[] args) {
        System.out.println("Got: " + divide());
    }
}
```

**Output:**
```
Got: 0
```

`10 / 0` threw `ArithmeticException`, but `return 0` in `finally` **threw the exception
away** and returned 0 as if all were fine. No crash, no message: the bug became invisible.

### Why you must never do this
- It **replaces the real return value** silently.
- It **throws away exceptions**, so errors disappear.
- The code says one thing (the `try`) and does another.

IntelliJ warns: *"'return' inside 'finally' block"*. Treat that warning as an error.

### Your question — what does "the same goes for `throw`" mean?

A `throw` inside `finally` does the same damage: it **replaces** the exception the `try`
threw.

```java
public class Test7 {
    static void work() {
        try {
            throw new RuntimeException("Error A: the real problem");
        } finally {
            throw new RuntimeException("Error B: from finally");
        }
    }

    public static void main(String[] args) {
        try {
            work();
        } catch (RuntimeException e) {
            System.out.println("Caught: " + e.getMessage());
        }
    }
}
```

**Output:**
```
Caught: Error B: from finally
```

1. `try` throws **Error A** (the real problem). It wants to go up to `main`.
2. Java runs `finally` first.
3. `finally` throws **Error B**.
4. Only one exception can travel up at a time, so **Error B replaces Error A**. Error A is
   thrown away.
5. `main` sees only Error B and has no idea Error A ever happened.

| Inside `finally` | What gets lost |
|---|---|
| `return 2` | the `try`'s return value, or the `try`'s exception |
| `throw new ...` | the `try`'s exception |

**How it happens by accident:** when the cleanup itself fails:

```java
try {
    // read the file → throws Error A
} finally {
    file.close();   // closing also fails → throws Error B, which replaces Error A
}
```

Try-with-resources (next section) fixes exactly this.

### For `NOTES.md`

> If both `try` and `finally` return, the caller gets the `finally` value, and a `return` in
> `finally` even swallows exceptions; a `throw` in `finally` replaces the `try`'s exception.
> Never `return` or `throw` from `finally` — it's only for cleanup.

---

## Concept 8: try-with-resources (Automatic Closing)

### What is a "resource"?

Something your program **borrows from the computer** and must **give back** when done:
- an open **file**
- a **database connection**
- a **network connection**

"Giving back" means calling **`close()`** on it.

### Why closing matters

Like library books: borrow and never return, and the library runs out. The operating system
only lets a program have a limited number of open files. Forget `close()` and eventually the
program fails ("Too many open files"). Forgetting to close is called a **resource leak**.

### The problem

You must close **even if an exception happens**, so the natural place is `finally`. But
written by hand it's long and easy to get wrong (see Subtask 5).

### The solution

Java 7 added a form of `try` that **closes the resource for you**:

```java
try (open the resource here) {
    use it
}
// Java calls close() automatically here
```

You **open** the resource inside the brackets `( )` after `try`. When the `try` block ends —
for **any** reason: finished normally, returned, or threw — Java **automatically calls
`close()`** on it. You never write `close()` yourself.

### What can go in the brackets?

Only objects whose class implements **`AutoCloseable`**: an interface (a promise) with one
method, `close()`. Files, database connections and network connections all implement it.
You write your own in Subtask 6.

### Two small extra rules

**1. You can open more than one resource**, separated by `;`:

```java
try (BufferedReader in = new BufferedReader(new FileReader("a.txt"));
     FileWriter out = new FileWriter("b.txt")) {
    // use both
}
```

They're closed in **reverse order**: `out` first, then `in` (last opened, first closed, like
plates on a stack).

**2. The variable exists only inside the `try` block.** You can't use `reader` after it,
which is right, because it's already closed.

### Key Takeaways:
- A resource = something borrowed (file, connection) that must be `close()`d
- `try (Resource r = ...) { }` calls `close()` automatically, however the block ends
- Only `AutoCloseable` things can go in the brackets
- Several resources: separate with `;`, closed in reverse order

---

## Subtask 5: Close a File the Old Way, Then with try-with-resources

> **From the plan:** Open a file the old way: try, use it, `finally { reader.close(); }`. Then
> rewrite with try-with-resources. Count the lines you deleted, and note that the new version
> also closes correctly when `close()` itself throws.

### The example

A text file `notes.txt`; we print its first line. Two Java classes do the reading:
- **`FileReader`** — opens the file.
- **`BufferedReader`** — wraps the `FileReader` so you can read **one line at a time** with
  `readLine()`.

Both can throw **`IOException`** (checked): the file might not exist, or reading might fail.

### The old way — `finally` by hand

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class OldWay {
    public static void main(String[] args) {
        BufferedReader reader = null;
        try {
            reader = new BufferedReader(new FileReader("notes.txt"));
            String line = reader.readLine();
            System.out.println("First line: " + line);
        } catch (IOException e) {
            System.out.println("Problem: " + e.getMessage());
        } finally {
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    System.out.println("Problem closing: " + e.getMessage());
                }
            }
        }
    }
}
```

Why it's so long:
1. **`reader = null` outside the `try`**, so `finally` can see it (variables declared inside
   `try` don't exist in `finally`).
2. **`if (reader != null)`**: if the file didn't exist, `new FileReader(...)` threw and
   `reader` is still `null`. Calling `close()` on `null` → `NullPointerException`.
3. **A `try`/`catch` inside `finally`**: `close()` itself can throw `IOException` (checked).

### The new way — try-with-resources

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class NewWay {
    public static void main(String[] args) {
        try (BufferedReader reader = new BufferedReader(new FileReader("notes.txt"))) {
            String line = reader.readLine();
            System.out.println("First line: " + line);
        } catch (IOException e) {
            System.out.println("Problem: " + e.getMessage());
        }
    }
}
```

1. The reader is opened inside `try ( ... )`.
2. The block reads and prints the first line.
3. When the block ends, Java calls `reader.close()` automatically.
4. If anything threw (including `close()`), `catch` handles it. Java closes the reader
   **before** the `catch` runs.
5. If the file didn't exist, opening failed, so there's nothing to close. Java skips it; no
   `null` check needed.

### Count the deleted lines

| Version | Lines inside `main` |
|---|---|
| Old way | 16 |
| New way | 6 |

**10 lines deleted**: the `null` variable, the `null` check, the `finally`, and the
`try`/`catch` around `close()`.

### "It also closes correctly when `close()` itself throws"

Imagine two problems:
- **Error A**: `readLine()` fails (the real problem).
- **Error B**: `close()` also fails.

**Plan's simple old way, `finally { reader.close(); }`:**

```java
try {
    String line = reader.readLine();   // throws Error A
} finally {
    reader.close();                    // throws Error B
}
```

Error B is thrown from `finally`, so it **replaces** Error A (Subtask 4's question). The real
problem is lost.

**Try-with-resources:**
- **Error A stays the main exception**: that's what your `catch` receives.
- **Error B is attached to Error A** as a **"suppressed"** exception, so it isn't lost either.

```java
} catch (IOException e) {
    System.out.println("Main problem: " + e.getMessage());          // Error A
    for (Throwable extra : e.getSuppressed()) {
        System.out.println("Also happened: " + extra.getMessage()); // Error B
    }
}
```

`e.getSuppressed()` gives the list of extra exceptions that happened while closing. In a
crash stack trace, you'd see Error A first and then a line starting with `Suppressed:` for
Error B.

| | Old way (`finally { close(); }`) | Try-with-resources |
|---|---|---|
| Real error (A) | **Lost**, replaced by B | **Kept** as the main exception |
| Close error (B) | Becomes the main exception | Kept as "suppressed" |

### For `NOTES.md`

> Try-with-resources (`try (Resource r = ...) { }`) calls `close()` for you however the block
> ends, needs no `null` check or nested try/catch (10 lines shorter here), and if `close()`
> throws, it keeps the real exception and attaches the close exception as "suppressed" instead
> of losing the real one.

---

## Subtask 6: Your Own `AutoCloseable` Class

> **From the plan:** Write your own tiny class implementing `AutoCloseable` with a print in
> `close()`. Use it in try-with-resources and watch the print happen on both the happy path and
> the failure path. Database connections in Phase 2 work exactly like this.

### The example: a `Door`

A door you open, walk through, and must always close.

```java
class Door implements AutoCloseable {
    Door() {
        System.out.println("Door opened");
    }

    void walkThrough() {
        System.out.println("Walking through");
    }

    @Override
    public void close() {
        System.out.println("Door closed");
    }
}
```

- `implements AutoCloseable`: the promise "I have a `close()` method", which is what lets
  `Door` go inside `try ( ... )`.
- The constructor prints "Door opened", so you can see when it's created.
- `close()` prints "Door closed", so you can see exactly when Java calls it.

**About `close()`'s signature:** in the `AutoCloseable` interface, `close()` is declared as
`void close() throws Exception`. When you write your own `close()`, you're allowed to **leave
out** `throws Exception` (a method that implements an interface may throw *less*, never more).
We left it out, so using `Door` needs no `catch` for checked exceptions. If you kept
`throws Exception`, every try-with-resources using `Door` would have to catch `Exception`.

### Happy path — nothing goes wrong

```java
public class DoorDemo {
    public static void main(String[] args) {
        try (Door door = new Door()) {
            door.walkThrough();
        }
        System.out.println("After the try");
    }
}
```

**Output:**
```
Door opened
Walking through
Door closed
After the try
```

The block finished, then Java called `close()` automatically. You never wrote `door.close()`.

### Failure path — an exception in the middle

```java
public class DoorDemo2 {
    public static void main(String[] args) {
        try (Door door = new Door()) {
            door.walkThrough();
            throw new RuntimeException("Tripped!");
        } catch (RuntimeException e) {
            System.out.println("Caught: " + e.getMessage());
        }
    }
}
```

**Output:**
```
Door opened
Walking through
Door closed
Caught: Tripped!
```

1. Door opened, walked through.
2. Exception thrown → the `try` block stops.
3. Java **closes the door first** → "Door closed".
4. Then the `catch` runs → "Caught: Tripped!".

"Door closed" printed on **both** paths. Note the order: **close happens before catch**.

### Experiment A — two doors: reverse order

```java
try (Door front = new Door(); Door back = new Door()) {
    System.out.println("Inside");
}
```

Give `Door` a name so you can tell them apart:

```java
class Door implements AutoCloseable {
    private final String name;

    Door(String name) {
        this.name = name;
        System.out.println(name + " opened");
    }

    @Override
    public void close() {
        System.out.println(name + " closed");
    }
}

// in main:
try (Door front = new Door("Front"); Door back = new Door("Back")) {
    System.out.println("Inside");
}
```

**Output:**
```
Front opened
Back opened
Inside
Back closed
Front closed
```

Last opened, first closed.

### Experiment B — `close()` itself throws: see "suppressed"

This proves the Subtask 5 claim with your own class.

```java
class BrokenDoor implements AutoCloseable {
    @Override
    public void close() {
        throw new RuntimeException("Error B: door stuck while closing");
    }
}

public class BrokenDoorDemo {
    public static void main(String[] args) {
        try (BrokenDoor door = new BrokenDoor()) {
            throw new RuntimeException("Error A: tripped inside");
        } catch (RuntimeException e) {
            System.out.println("Main problem: " + e.getMessage());
            for (Throwable extra : e.getSuppressed()) {
                System.out.println("Also happened: " + extra.getMessage());
            }
        }
    }
}
```

**Output:**
```
Main problem: Error A: tripped inside
Also happened: Error B: door stuck while closing
```

Error A (the real problem) is the main exception; Error B is kept as suppressed. Nothing lost.

### Where you'll see this again: database connections

In Phase 2 you'll write code like:

```java
try (Connection connection = dataSource.getConnection()) {
    // run database queries
}
```

`Connection` implements `AutoCloseable`, exactly like `Door`. Getting a connection = "Door
opened"; the automatic `close()` = "Door closed", giving the connection back so others can use
it. Forgetting to close connections is a classic way to bring a server down.

### For `NOTES.md`

> Any class implementing `AutoCloseable` can go in try-with-resources; Java calls its `close()`
> on both the happy path and the failure path, before the `catch` runs, closing several
> resources in reverse order. Database connections work exactly like this.

### Key Takeaways:
- `class X implements AutoCloseable` + a `close()` method = usable in `try ( ... )`
- `close()` runs on the happy path **and** the failure path
- Order on failure: close first, then catch
- Several resources close in reverse order
- If `close()` throws, the real exception stays main; the close one is "suppressed"

---

## Concept 9: `AutoCloseable` (Making Your Own Class Work with try-with-resources)

### What is `AutoCloseable`?

`AutoCloseable` is an **interface** built into Java (in `java.lang`, so no import needed). It
has exactly **one method**:

```java
public interface AutoCloseable {
    void close() throws Exception;
}
```

When a class says `implements AutoCloseable`, it **promises** "I have a `close()` method".
That promise is the **only** thing try-with-resources needs: anything that implements
`AutoCloseable` can go inside `try ( ... )`, and Java will call its `close()` automatically.

Java's own classes already do this: `FileReader`, `BufferedReader`, `FileWriter`, `Scanner`,
database `Connection`s, and many more.

### Writing your own: a `Door`

A door you open, walk through, and must always close.

```java
class Door implements AutoCloseable {
    Door() {
        System.out.println("Door opened");
    }

    void walkThrough() {
        System.out.println("Walking through");
    }

    @Override
    public void close() {
        System.out.println("Door closed");
    }
}
```

- `implements AutoCloseable` — the promise that lets `Door` go inside `try ( ... )`.
- The constructor prints "Door opened" so you can see when it's created.
- `close()` prints "Door closed" so you can see exactly when Java calls it.
- `@Override` — tells the compiler "this is the interface's `close()`"; it complains if you
  mistype the name.
- `public` — interface methods are public, so your `close()` must be `public` too.

### About `throws Exception` on `close()`

The interface declares `close() throws Exception`. In your class you may **leave it out**,
because a method that implements an interface may throw **less**, never more.

| Your `close()` | What callers must do |
|---|---|
| `public void close()` (no throws) | Nothing extra — no `catch` needed |
| `public void close() throws Exception` | Every try-with-resources using it must `catch (Exception e)` or declare `throws Exception` |

So leave `throws Exception` out unless your `close()` really can throw a checked exception.

### Example 1 — happy path: nothing goes wrong

```java
public class DoorDemo {
    public static void main(String[] args) {
        try (Door door = new Door()) {
            door.walkThrough();
        }
        System.out.println("After the try");
    }
}
```

**Output:**
```
Door opened
Walking through
Door closed
After the try
```

The block finished, then Java called `close()` automatically. You never wrote `door.close()`.

Note: try-with-resources is allowed **without** a `catch` or `finally` (unlike a normal `try`,
Concept 5), because the automatic close counts as the cleanup.

### Example 2 — failure path: exception in the middle

```java
public class DoorDemo2 {
    public static void main(String[] args) {
        try (Door door = new Door()) {
            door.walkThrough();
            throw new RuntimeException("Tripped!");
        } catch (RuntimeException e) {
            System.out.println("Caught: " + e.getMessage());
        }
    }
}
```

**Output:**
```
Door opened
Walking through
Door closed
Caught: Tripped!
```

1. Door opened, walked through.
2. Exception thrown → the `try` block stops.
3. Java **closes the door first** → "Door closed".
4. Then the `catch` runs → "Caught: Tripped!".

**"Door closed" prints on both paths, and close happens before catch.**

### Example 3 — two doors: closed in reverse order

Give each door a name:

```java
class Door implements AutoCloseable {
    private final String name;

    Door(String name) {
        this.name = name;
        System.out.println(name + " opened");
    }

    @Override
    public void close() {
        System.out.println(name + " closed");
    }
}

public class TwoDoors {
    public static void main(String[] args) {
        try (Door front = new Door("Front"); Door back = new Door("Back")) {
            System.out.println("Inside");
        }
    }
}
```

**Output:**
```
Front opened
Back opened
Inside
Back closed
Front closed
```

Last opened, first closed — like plates on a stack. This matters when the second resource
depends on the first (e.g. a writer that uses an open connection must close before the
connection does).

### Example 4 — `close()` itself throws: "suppressed" exceptions

```java
class BrokenDoor implements AutoCloseable {
    @Override
    public void close() {
        throw new RuntimeException("Error B: door stuck while closing");
    }
}

public class BrokenDoorDemo {
    public static void main(String[] args) {
        try (BrokenDoor door = new BrokenDoor()) {
            throw new RuntimeException("Error A: tripped inside");
        } catch (RuntimeException e) {
            System.out.println("Main problem: " + e.getMessage());
            for (Throwable extra : e.getSuppressed()) {
                System.out.println("Also happened: " + extra.getMessage());
            }
        }
    }
}
```

**Output:**
```
Main problem: Error A: tripped inside
Also happened: Error B: door stuck while closing
```

- **Error A** (the real problem, from the `try`) stays the **main** exception.
- **Error B** (from `close()`) is attached to it as **suppressed**.
- `e.getSuppressed()` returns the list of those extra exceptions.

Compare with a hand-written `finally { door.close(); }`, where Error B would **replace**
Error A (Subtask 4's question).

### Example 5 — if creating the resource fails, `close()` is not called

```java
class LockedDoor implements AutoCloseable {
    LockedDoor() {
        throw new RuntimeException("Door is locked, can't open");
    }

    @Override
    public void close() {
        System.out.println("Door closed");
    }
}

public class LockedDoorDemo {
    public static void main(String[] args) {
        try (LockedDoor door = new LockedDoor()) {
            System.out.println("Inside");
        } catch (RuntimeException e) {
            System.out.println("Caught: " + e.getMessage());
        }
    }
}
```

**Output:**
```
Caught: Door is locked, can't open
```

The door was never opened (the constructor threw), so there's nothing to close: "Door closed"
does **not** print, and "Inside" doesn't either. That's why the old way needed
`if (reader != null)` and the new way doesn't.

### Real-world use: database connections

```java
try (Connection connection = dataSource.getConnection()) {
    // run database queries
}
```

`Connection` implements `AutoCloseable`, exactly like `Door`. Getting the connection = "Door
opened"; the automatic `close()` = "Door closed", handing the connection back so others can
use it. You'll write this in Phase 2.

### Key Takeaways:
- `AutoCloseable` = an interface with one method, `close()`
- `class X implements AutoCloseable` → `X` can be used in `try ( ... )`
- Your `close()` must be `public`; leave out `throws Exception` unless you really need it
- `close()` runs on the happy path **and** the failure path, **before** `catch`
- Several resources close in **reverse** order
- If `close()` throws, the real exception stays main; the close one is **suppressed**
- If creating the resource fails, `close()` isn't called (nothing was opened)

---

# Task F2.10 — Exceptions, Part 2: Your Own Types, Causes, and What Never to Catch

Concepts first (one at a time), then the plan's subtasks.

### Concepts covered so far
- [x] Concept 10 — Writing your own exception class
- [x] Concept 11 — Putting data inside an exception
- [x] Concept 12 — Exception hierarchy (one parent, many children)
- [x] Concept 13 — The cause: chaining exceptions and `Caused by:`
- [x] Concept 14 — Swallowing, and why `e.printStackTrace()` isn't enough
- [x] Concept 15 — What never to catch: `Throwable`, `Error`, `InterruptedException`
- [x] Concept 16 — Exceptions vs ordinary decisions
- [x] Concept 17 — Writing good exception messages (taught inside Subtask 9)

### Subtasks covered so far
- [x] Subtask 1 — Build the hierarchy: `DomainException` → `EventNotFoundException`, `EventNotPublishableException`
- [x] Subtask 2 — Throw the specific one, catch the parent; then catch the specific one
- [x] Subtask 3 — Chain a cause and find `Caused by:`
- [x] Subtask 4 — Break it on purpose: throw without the cause, compare the traces
- [x] Subtask 5 — The cardinal sin: `catch (Exception e) { }`, then `e.printStackTrace()`
- [x] Subtask 6 — The never-catch list, with reasons
- [x] Subtask 7 — Ask AI about `InterruptedException`
- [x] Subtask 8 — Write the rule: exceptions vs ordinary decisions
- [x] Subtask 9 — Review your messages

---

## Concept 10: Writing Your Own Exception Class

### Why make your own?

Java's built-in exceptions have **general** names: `IllegalArgumentException`,
`IllegalStateException`, `RuntimeException`. When one of these appears, you only know
"something was wrong", not **what**.

Your own exception can have a name that says **exactly** what went wrong:

| Built-in (vague) | Your own (precise) |
|---|---|
| `IllegalStateException: not enough money` | `InsufficientBalanceException: Cannot withdraw 500, balance is only 100` |

And a caller can catch **exactly that problem** and nothing else.

### A custom exception is just a class that extends an exception class

From inheritance (F2.7): `extends` makes a child class. An exception is an object, so your own
exception is simply a **child of an existing exception class**.

```java
public class InsufficientBalanceException extends RuntimeException {
    public InsufficientBalanceException(String message) {
        super(message);
    }
}
```

Line by line:
- `extends RuntimeException` — our class is a `RuntimeException`, so it's **unchecked**
  (Concept 2). That's the modern default (F2.9 Subtask 2).
- `public InsufficientBalanceException(String message)` — a constructor that takes the
  error text.
- `super(message)` — calls the **parent's** constructor (`RuntimeException`) and hands it the
  message. The parent **stores** it, and that's what makes `e.getMessage()` work later.

That's the whole class. Three real lines.

### Using it

```java
class BankAccount {
    private int balance = 100;

    void withdraw(int amount) {
        if (amount > balance) {
            throw new InsufficientBalanceException(
                "Cannot withdraw " + amount + ", balance is only " + balance);
        }
        balance = balance - amount;
        System.out.println("Withdrew " + amount + ", left: " + balance);
    }
}

public class BankDemo {
    public static void main(String[] args) {
        BankAccount account = new BankAccount();
        try {
            account.withdraw(30);
            account.withdraw(500);
        } catch (InsufficientBalanceException e) {
            System.out.println("Sorry: " + e.getMessage());
        }
    }
}
```

**Output:**
```
Withdrew 30, left: 70
Sorry: Cannot withdraw 500, balance is only 70
```

- `withdraw(30)` worked.
- `withdraw(500)` threw **our** exception, exactly like throwing a built-in one.
- `catch (InsufficientBalanceException e)` caught **only** this kind of problem.

### If nobody catches it

```java
public static void main(String[] args) {
    BankAccount account = new BankAccount();
    account.withdraw(500);
}
```

**Output:**
```
Exception in thread "main" InsufficientBalanceException: Cannot withdraw 500, balance is only 100
	at BankAccount.withdraw(BankDemo.java:6)
	at BankDemo.main(BankDemo.java:...)
```

The crash message shows **your class name**, so anyone reading it knows immediately what
went wrong.

### What if you forget `super(message)`?

```java
public class InsufficientBalanceException extends RuntimeException {
    public InsufficientBalanceException(String message) {
        // forgot super(message)
    }
}
```

It still compiles, but the parent never received the message, so:

```
Sorry: null
```

`e.getMessage()` returns `null`. **Always pass the message to `super`.**

### Checked or unchecked: you choose by the parent

| Your class extends | It becomes | Callers must |
|---|---|---|
| `RuntimeException` | **unchecked** | nothing (catch only if they want) |
| `Exception` | **checked** | catch it or declare `throws` |

```java
public class InsufficientBalanceException extends Exception { ... }   // checked version
```

With this version, `withdraw` would need `throws InsufficientBalanceException`, and so would
every caller. Following the modern convention, **extend `RuntimeException`** unless you have a
strong reason not to.

### Naming rule

End the name with **`Exception`**: `InsufficientBalanceException`, `BookNotFoundException`,
`UserAlreadyExistsException`. Anyone reading the code instantly knows it's an exception.

### Key Takeaways:
- A custom exception = a class that `extends RuntimeException` (or `Exception`)
- Its constructor passes the message to the parent with `super(message)`
- Forget `super(message)` → `getMessage()` returns `null`
- Extend `RuntimeException` → unchecked (the modern default); extend `Exception` → checked
- Name it after the problem and end with `Exception`
- Callers can catch exactly your exception and nothing else

---

## Concept 11: Putting Data Inside an Exception

### The problem: a message is only text

From Concept 10, our exception carries just a message:

```
Cannot withdraw 500, balance is only 70
```

That's fine for a **human** reading it. But what if the **code** that catches it needs the
numbers? For example, to tell the user *"You need 430 more"*.

### The wrong way: digging numbers out of the message

```java
} catch (InsufficientBalanceException e) {
    String msg = e.getMessage();                       // "Cannot withdraw 500, balance is only 70"
    String[] words = msg.split(" ");                   // split into words
    int requested = Integer.parseInt(words[2].replace(",", ""));  // "500," → 500
    int balance = Integer.parseInt(words[6]);          // "70"
    System.out.println("You need " + (requested - balance) + " more");
}
```

This is fragile:
- If someone changes the message to `"Not enough money: wanted 500, have 70"`, the word
  positions change and this code breaks (wrong numbers, or a `NumberFormatException`).
- The person changing the message has no idea that some `catch` block somewhere depends on
  the exact wording.

**A message is for humans. Code should never have to read it.**

### The right way: store the data in fields

An exception is a class, so it can have **fields** and **getter methods** like any other
class:

```java
public class InsufficientBalanceException extends RuntimeException {
    private final int requested;
    private final int balance;

    public InsufficientBalanceException(int requested, int balance) {
        super("Cannot withdraw " + requested + ", balance is only " + balance);
        this.requested = requested;
        this.balance = balance;
    }

    public int getRequested() {
        return requested;
    }

    public int getBalance() {
        return balance;
    }
}
```

Line by line:
- `private final int requested;` and `balance` — the data, stored in the exception object.
  `final` because an exception describes something that already happened; it shouldn't
  change.
- The constructor now takes the **numbers**, not a ready-made message.
- `super("Cannot withdraw " + ...)` — the constructor **builds the message itself** from the
  numbers and passes it to the parent. So the message is still there for humans.
- `this.requested = requested;` — stores the numbers for code.
- `getRequested()`, `getBalance()` — let the catcher read the numbers.

Note: `super(...)` must be the **first line** of the constructor; the field assignments come
after.

### Throwing it

The thrower now passes numbers, which is simpler too:

```java
class BankAccount {
    private int balance = 100;

    void withdraw(int amount) {
        if (amount > balance) {
            throw new InsufficientBalanceException(amount, balance);
        }
        balance = balance - amount;
        System.out.println("Withdrew " + amount + ", left: " + balance);
    }
}
```

### Catching it and using the data

```java
public class BankDemo {
    public static void main(String[] args) {
        BankAccount account = new BankAccount();
        try {
            account.withdraw(30);
            account.withdraw(500);
        } catch (InsufficientBalanceException e) {
            int shortBy = e.getRequested() - e.getBalance();
            System.out.println(e.getMessage());
            System.out.println("You need " + shortBy + " more");
        }
    }
}
```

**Output:**
```
Withdrew 30, left: 70
Cannot withdraw 500, balance is only 70
You need 430 more
```

- `e.getMessage()` — the human-readable text (built in the constructor).
- `e.getRequested()`, `e.getBalance()` — the real numbers, no text-parsing.

Now anyone can reword the message freely; the `catch` keeps working because it never reads
the text.

### Why this matters in real applications

Later, in a web application, a layer at the top turns exceptions into responses for the
browser or mobile app. With data inside the exception it can build a precise response, like:

```
{ "error": "INSUFFICIENT_BALANCE", "requested": 500, "balance": 70 }
```

The app showing the error then uses `requested` and `balance` directly, without ever parsing
an English sentence.

### Key Takeaways:
- The **message** is for humans; **fields** are for code
- Put the data the catcher needs into `private final` fields, with getters
- Let the constructor build the message from the data, and pass it to `super(...)` as the
  first line
- Never make a `catch` block parse `e.getMessage()` — rewording the message would break it

---

## Concept 12: Exception Hierarchy (One Parent, Many Children)

### The idea

An **exception hierarchy** is your own small **family** of exception classes with one
**shared parent**. Java's exceptions already work this way (Concept 2: `IOException` has
children like `FileNotFoundException`). Now you build one for your own program.

### Example: a library

A library app can fail in two ways:
- the book doesn't exist → `BookNotFoundException`
- the book is already borrowed → `BookAlreadyBorrowedException`

Both are "library problems", so we give them a shared parent, `LibraryException`:

```
RuntimeException
      |
LibraryException          ← parent (abstract)
   /            \
BookNotFoundException   BookAlreadyBorrowedException
```

### The parent

```java
public abstract class LibraryException extends RuntimeException {
    protected LibraryException(String message) {
        super(message);
    }
}
```

- `extends RuntimeException` — the whole family is unchecked.
- `abstract` — nobody can write `new LibraryException(...)`. It's too vague to throw: "some
  library problem" tells the caller nothing. It exists only to **group** its children.
- `protected` constructor — only child classes can call it (through `super`).

### The children

```java
public class BookNotFoundException extends LibraryException {
    public BookNotFoundException(int bookId) {
        super("Book " + bookId + " not found");
    }
}

public class BookAlreadyBorrowedException extends LibraryException {
    public BookAlreadyBorrowedException(int bookId) {
        super("Book " + bookId + " is already borrowed");
    }
}
```

- Each `extends LibraryException`, not `RuntimeException` directly.
- `super(...)` goes to `LibraryException`'s constructor, which passes the message on to
  `RuntimeException`, which stores it. A chain of `super` calls up the family.
- Each message includes the **id** involved, so you know *which* book.

So a `BookNotFoundException` **is a** `LibraryException`, which **is a** `RuntimeException`.

### The code that throws them

```java
class Library {
    void borrow(int bookId) {
        if (bookId == 1) {
            throw new BookNotFoundException(bookId);
        }
        if (bookId == 2) {
            throw new BookAlreadyBorrowedException(bookId);
        }
        System.out.println("Borrowed book " + bookId);
    }
}
```

(To keep it simple: book 1 doesn't exist, book 2 is already borrowed, anything else works.)

### Choice 1 — catch the parent: one catch for the whole family

```java
public class LibraryDemo {
    public static void main(String[] args) {
        Library library = new Library();
        int[] requests = {3, 1, 2};

        for (int bookId : requests) {
            try {
                library.borrow(bookId);
            } catch (LibraryException e) {
                System.out.println("Library problem: " + e.getMessage());
            }
        }
    }
}
```

**Output:**
```
Borrowed book 3
Library problem: Book 1 not found
Library problem: Book 2 is already borrowed
```

One `catch (LibraryException e)` caught **both** children, because both **are**
`LibraryException`s. Use this when you want to treat every library problem the same way.

### Choice 2 — catch a specific child: handle one problem differently

```java
for (int bookId : requests) {
    try {
        library.borrow(bookId);
    } catch (BookAlreadyBorrowedException e) {
        System.out.println(e.getMessage() + " — we'll add you to the waiting list");
    } catch (LibraryException e) {
        System.out.println("Library problem: " + e.getMessage());
    }
}
```

**Output:**
```
Borrowed book 3
Library problem: Book 1 not found
Book 2 is already borrowed — we'll add you to the waiting list
```

- "Already borrowed" gets **special** handling (waiting list).
- Every other library problem falls through to the parent catch.
- The specific (child) catch comes **first**, the parent **last** (Concept 4's ordering rule).

### This is the entire point: the caller chooses how precise to be

| Caller wants | Catch |
|---|---|
| Treat all library problems the same | `catch (LibraryException e)` |
| Handle one problem specially | `catch (BookAlreadyBorrowedException e)`, then the parent |
| Handle every problem differently | One catch per child |

The **thrower** always throws the most specific exception. The **catcher** decides how much
detail it cares about.

### Bonus 1 — adding a new child doesn't break anyone

Later you add a third problem:

```java
public class MembershipExpiredException extends LibraryException {
    public MembershipExpiredException(int memberId) {
        super("Membership " + memberId + " has expired");
    }
}
```

Every existing `catch (LibraryException e)` **already** catches it. No catch block needs
changing.

### Bonus 2 — why not just catch `RuntimeException`?

```java
} catch (RuntimeException e) {     // ❌ too wide
    System.out.println("Library problem: " + e.getMessage());
}
```

`RuntimeException` also catches **bugs**: `NullPointerException`,
`ArrayIndexOutOfBoundsException`, etc. A real bug would be reported as "Library problem" and
hidden. `catch (LibraryException e)` catches **only** your library problems; bugs still
crash loudly, as they should.

### Key Takeaways:
- An exception hierarchy = one shared parent + several specific children
- Make the parent `abstract` (too vague to throw) with a `protected` constructor
- Children `extend` the parent; `super(...)` passes the message up the chain
- Throw the **specific** child; let the caller catch the child **or** the parent
- Catch order: children first, parent last
- Catching the parent automatically covers children added later
- Catching your parent instead of `RuntimeException` keeps real bugs visible

---

## Concept 13: The Cause — Chaining Exceptions and `Caused by:`

### The situation: a low-level error inside a high-level job

A method loads a user's profile. Part of that job is turning the saved age text into a
number with `Integer.parseInt`. If the saved text is `"abc"`, `parseInt` throws
`NumberFormatException`.

Two different levels of information now exist:
- **What** failed (high level): *"Could not load profile for user 7"*
- **Why** it failed (low level): *`NumberFormatException: For input string: "abc"`*

You want to throw your own, meaningful exception (the "what") **without losing** the
original (the "why"). Attaching the original to the new one is called **chaining**, and the
attached original is called the **cause**.

### Step 1 — give your exception a constructor that accepts a cause

```java
public class ProfileLoadException extends RuntimeException {
    public ProfileLoadException(String message) {
        super(message);
    }

    public ProfileLoadException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

- The **first** constructor: just a message (Concept 10).
- The **second** constructor: a message **and** a cause.
  - `Throwable cause` — `Throwable` is the parent of every exception (Concept 2), so **any**
    exception can be a cause.
  - `super(message, cause)` — `RuntimeException` already knows how to store a cause; we just
    hand it over.

Without the second constructor, you'd have no way to attach the original.

### Step 2 — catch the low-level error and wrap it

```java
class ProfileService {
    void loadProfile(int userId, String savedAge) {
        try {
            int age = Integer.parseInt(savedAge);
            System.out.println("User " + userId + " is " + age);
        } catch (NumberFormatException e) {
            throw new ProfileLoadException("Could not load profile for user " + userId, e);
        }
    }
}

public class ProfileDemo {
    public static void main(String[] args) {
        ProfileService service = new ProfileService();
        service.loadProfile(7, "abc");
    }
}
```

The key line:

```java
throw new ProfileLoadException("Could not load profile for user " + userId, e);
//                              ^ the "what" (new message)                 ^ the "why" (original)
```

### Step 3 — read the stack trace

Nobody catches it, so the program crashes and prints (line numbers will differ):

```
Exception in thread "main" ProfileLoadException: Could not load profile for user 7
	at ProfileService.loadProfile(ProfileDemo.java:7)
	at ProfileDemo.main(ProfileDemo.java:15)
Caused by: java.lang.NumberFormatException: For input string: "abc"
	at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:...)
	at java.base/java.lang.Integer.parseInt(Integer.java:...)
	at java.base/java.lang.Integer.parseInt(Integer.java:...)
	at ProfileService.loadProfile(ProfileDemo.java:4)
	... 1 more
```

It has two parts:

| Part | What it tells you |
|---|---|
| **Top part** (`ProfileLoadException: Could not load profile for user 7`) | **What** failed, and where it was thrown (line 7: our `throw`) |
| **`Caused by:` part** (`NumberFormatException: For input string: "abc"`) | **Why** it failed: the original exception, its message, and exactly where it started (inside `parseInt`, called from line 4) |
| `... 1 more` | "The rest of this list is the same as above" (here, the `main` line). Java skips repeating it. |

With both parts you know everything: user 7's profile failed **because** the saved age was
`"abc"`, at line 4.

### Reading the cause from code: `getCause()`

If you catch the wrapped exception, you can get the original back:

```java
try {
    service.loadProfile(7, "abc");
} catch (ProfileLoadException e) {
    System.out.println("Problem: " + e.getMessage());
    System.out.println("Because: " + e.getCause());
}
```

**Output:**
```
Problem: Could not load profile for user 7
Because: java.lang.NumberFormatException: For input string: "abc"
```

### Chains can be longer

If one wrapped exception is wrapped again, the trace shows several `Caused by:` sections:

```
Exception in thread "main" AppStartException: Could not start app
	at ...
Caused by: ProfileLoadException: Could not load profile for user 7
	at ...
Caused by: java.lang.NumberFormatException: For input string: "abc"
	at ...
```

Read it top to bottom as "this failed, because of this, because of this". The **last**
`Caused by:` is the **root cause**, the original problem. When debugging, look there first.

### The two mistakes

**Mistake 1 — no cause at all:**

```java
} catch (NumberFormatException e) {
    throw new ProfileLoadException("Could not load profile for user " + userId);
}
```

Trace:

```
Exception in thread "main" ProfileLoadException: Could not load profile for user 7
	at ProfileService.loadProfile(ProfileDemo.java:7)
	at ProfileDemo.main(ProfileDemo.java:15)
```

No `Caused by:`. You know *that* it failed, but **nothing about why**. Was it the age? A
missing file? A database problem? You'd have to guess.

**Mistake 2 — passing only the message: `e.getMessage()`:**

```java
} catch (NumberFormatException e) {
    throw new ProfileLoadException(e.getMessage());
}
```

Trace:

```
Exception in thread "main" ProfileLoadException: For input string: "abc"
	at ProfileService.loadProfile(ProfileDemo.java:7)
	at ProfileDemo.main(ProfileDemo.java:15)
```

This looks helpful but is still broken:
- The **original exception type** (`NumberFormatException`) is gone.
- **Where** it really started (inside `parseInt`, line 4) is gone.
- The **context** ("profile for user 7") is gone too; the message is now just
  `For input string: "abc"`. Which user? Which field?

### Side by side

| You write | What + context | Original type | Where it started |
|---|---|---|---|
| `new ProfileLoadException("... user 7", e)` | ✅ | ✅ (`Caused by:`) | ✅ |
| `new ProfileLoadException("... user 7")` | ✅ | ❌ | ❌ |
| `new ProfileLoadException(e.getMessage())` | ❌ | ❌ | ❌ |

**Rule: when you wrap an exception, always pass the original itself (`e`) as the cause.**

### Key Takeaways:
- **Chaining** = attaching the original exception (the **cause**) to the one you throw
- Give your exception a `(String message, Throwable cause)` constructor that calls
  `super(message, cause)`
- Wrap with `throw new MyException("what failed + context", e);`
- The stack trace shows the original under **`Caused by:`**; the last one is the root cause
- `e.getCause()` returns the original in code
- Never drop the cause, and never pass only `e.getMessage()`

---

## Concept 14: Swallowing, and Why `e.printStackTrace()` Isn't Enough

### What "swallowing" means

**Swallowing** an exception = catching it and doing **nothing** (or nothing useful). The
failure disappears without a trace, and the program carries on as if all is well.

### Example: adding up an order's prices

The prices arrive as text. One of them is broken:

```java
public class OrderDemo {
    static int total(int orderId, String[] prices) {
        int sum = 0;
        for (String price : prices) {
            try {
                sum = sum + Integer.parseInt(price);
            } catch (Exception e) {
                // swallowed: do nothing
            }
        }
        return sum;
    }

    public static void main(String[] args) {
        String[] prices = {"100", "abc", "50"};
        System.out.println("Order 42 total: " + total(42, prices));
    }
}
```

**Output:**
```
Order 42 total: 150
```

- `"abc"` threw `NumberFormatException`.
- The empty `catch` swallowed it.
- The program printed **150** and kept going. **No error anywhere.**

The real total is unknown (the middle item has a broken price), but the program confidently
reports 150. The customer gets charged the wrong amount, and nobody knows anything went
wrong. **Wrong data with no error is worse than a crash**: a crash at least tells you there's
a problem.

### "Fix" attempt: `e.printStackTrace()`

```java
} catch (Exception e) {
    e.printStackTrace();
}
```

**Output:**
```
java.lang.NumberFormatException: For input string: "abc"
	at java.base/java.lang.NumberFormatException.forInputString(...)
	at java.base/java.lang.Integer.parseInt(...)
	at OrderDemo.total(OrderDemo.java:6)
	at OrderDemo.main(OrderDemo.java:16)
Order 42 total: 150
```

Better, you can see the error now. But it's **still unacceptable in a real service** for three
reasons:

**1. It goes to the console, not to the logs.**

`printStackTrace()` writes to the **console** (technically "standard error", `System.err`).
On your laptop you see it. But a real service runs on a server with nobody watching a
console. Real services write to **logs**: files or systems that store every message with the
**time**, the **severity** (info, warning, error), and **which class** wrote it. Teams search
logs and get **alerts** when errors appear. Console output usually ends up nowhere, or mixed
in with everything else, with no time and no severity.

**2. It carries no context.**

The trace says `For input string: "abc"`. But **which order? which item?** When thousands of
orders are processed, "abc" alone tells you nothing. Nowhere does it say "order 42".

**3. The program still continues as if nothing happened.**

Look at the last line: `Order 42 total: 150`. The wrong total is **still** returned and used.
Printing the error didn't stop the wrong data.

| | Empty `catch { }` | `catch { e.printStackTrace(); }` |
|---|---|---|
| Anyone told? | No | Only someone watching the console |
| Context (which order/item)? | No | No |
| Wrong data stopped? | No | **No** |

### What to do instead — three good options

**Option 1 — don't catch it at all.** If this method can't fix the problem, let it rise
(Concept 13 / F2.9 Subtask 2: handle at the top). Delete the try/catch:

```java
static int total(int orderId, String[] prices) {
    int sum = 0;
    for (String price : prices) {
        sum = sum + Integer.parseInt(price);
    }
    return sum;
}
```

The order fails loudly instead of being charged wrongly.

**Option 2 — wrap it with context and the cause, then throw** (Concept 13):

```java
} catch (NumberFormatException e) {
    throw new OrderTotalException("Invalid price '" + price + "' in order " + orderId, e);
}
```

Now the error says **which order** and **which price**, keeps the original as
`Caused by:`, and **stops** the wrong total.

**Option 3 — really handle it, and log it properly.** Only if there's a genuine, correct
alternative. For example, if the business rule says "skip broken items and flag the order for
review", then do exactly that **and log it with context**:

```java
} catch (NumberFormatException e) {
    log.warn("Skipping invalid price '" + price + "' in order " + orderId, e);
    flagForReview(orderId);
}
```

### What is `log`? (a first look at logging)

`log` is a **logger**: an object whose job is writing messages to the logs. You'll set it up
properly in Spring later; for now just recognise the shape. In Spring applications it looks
like:

```java
private static final Logger log = LoggerFactory.getLogger(OrderService.class);

log.info("Order {} placed", orderId);                         // normal event
log.warn("Skipping invalid price in order {}", orderId);      // something odd, but handled
log.error("Could not total order {}", orderId, e);            // a real failure, with the exception
```

- `{}` is a placeholder; the value after the message fills it in.
- Passing `e` as the **last** argument makes the logger print the full stack trace
  (including `Caused by:`) into the log, with the time and severity.

A log line looks something like:

```
2026-10-17 18:30:12 ERROR OrderService - Could not total order 42
java.lang.NumberFormatException: For input string: "abc"
	at ...
```

Time, severity, class, context, **and** the stack trace: everything
`printStackTrace()` was missing.

### The rule

A `catch` block must do **something real**: fix the problem, or wrap and rethrow with
context, or (if continuing is genuinely correct) log properly with context. **Never** leave it
empty, and never let `printStackTrace()` be the only thing in it.

### Key Takeaways:
- **Swallowing** = catching and doing nothing → the failure vanishes, wrong data continues
- Wrong data with no error is **worse** than a crash
- `e.printStackTrace()` is still not enough: console not logs, no context, program continues
- Good options: **don't catch**, **wrap with context + cause and throw**, or **really handle
  and log with context**
- A logger writes time, severity, class, message, and (if you pass `e`) the full stack trace

---

## Concept 15: What Never to Catch — `Throwable`, `Error`, `InterruptedException`

There are three things you must never catch carelessly in a service.

### 1. Never catch `Error`

From Concept 2: `Error` means **the JVM itself is in trouble**: `OutOfMemoryError` (no memory
left), `StackOverflowError` (infinite recursion). These aren't problems in your data; the
program itself is broken.

```java
try {
    loadHugeReport();
} catch (OutOfMemoryError e) {          // ❌ never
    System.out.println("Out of memory, carrying on");
}
```

Why it's wrong:
- When memory runs out, **any** line can fail next, including the line printing your message,
  or code halfway through updating data. The program is now in an unknown, half-broken state.
- "Carrying on" in that state can produce corrupted data, which is worse than stopping.
- You **cannot fix** an out-of-memory condition by continuing. The right response is to let the
  program crash so it can be **restarted** clean (servers are set up to restart crashed
  services automatically), and then find the cause.

### 2. Never catch `Throwable`

`Throwable` is the parent of **everything**, both `Exception` **and** `Error` (Concept 2):

```
Throwable
├── Error        ← OutOfMemoryError, StackOverflowError
└── Exception    ← all exceptions
```

So:

```java
try {
    doWork();
} catch (Throwable t) {                 // ❌ never
    System.out.println("Something went wrong");
}
```

catches all `Error`s too, so it has every problem of point 1, while **looking** harmless.

**Rule:** catch the most specific type you can actually handle. Never `Error`, never
`Throwable`.

### 3. Never swallow `InterruptedException`

This one needs a short introduction to threads first.

#### What is a thread?

A **thread** is a worker inside your program that runs code. Every program starts with one,
the **main thread**, which runs `main`. You can start **more** threads, so several jobs run
at the same time, like several cooks in one kitchen.

The simplest way to make a thread: a class that `extends Thread` and puts its job in `run()`:

```java
class Worker extends Thread {
    @Override
    public void run() {
        System.out.println("Worker doing its job");
    }
}

public class ThreadDemo {
    public static void main(String[] args) {
        Worker worker = new Worker();
        worker.start();                     // starts run() in a NEW thread
        System.out.println("Main carries on");
    }
}
```

- `worker.start()` — starts a new thread that runs `run()`, **alongside** `main`.
- Both lines print; the order can vary, because the two threads run at the same time.

#### What is "interrupting" a thread?

Sometimes one thread needs to ask another to **stop**: for example, when the application is
shutting down. Java's way of asking is:

```java
worker.interrupt();
```

This is a **polite request**, not a force-stop. It does two things:
1. It sets a flag on the worker thread: **"you've been asked to stop"** (the **interrupted
   flag**). The worker can check it with `Thread.currentThread().isInterrupted()`.
2. If the worker is **waiting** (e.g. inside `Thread.sleep(...)`), the wait ends immediately
   by throwing **`InterruptedException`**. That's why `Thread.sleep` declares
   `throws InterruptedException` (you saw this in F2.9 Subtask 3).

**The important detail:** when `InterruptedException` is thrown, Java **clears the flag**.
The exception itself **is** the stop request now. If you swallow the exception, the request
is gone completely.

#### The bug: swallowing it

A worker that prints every second until asked to stop:

```java
class Worker extends Thread {
    @Override
    public void run() {
        while (!Thread.currentThread().isInterrupted()) {   // keep going until asked to stop
            System.out.println("Working...");
            try {
                Thread.sleep(1000);                         // wait 1 second
            } catch (InterruptedException e) {
                // ❌ swallowed
            }
        }
        System.out.println("Worker stopped");
    }
}

public class StopDemo {
    public static void main(String[] args) throws InterruptedException {
        Worker worker = new Worker();
        worker.start();

        Thread.sleep(3000);                                 // let it work for 3 seconds
        System.out.println("Main: asking worker to stop");
        worker.interrupt();

        worker.join();                                      // wait for the worker to finish
        System.out.println("Main: done");
    }
}
```

- `worker.join()` — main **waits** here until the worker thread finishes. (It can also throw
  `InterruptedException`, so `main` declares it.)

**Output:**
```
Working...
Working...
Working...
Main: asking worker to stop
Working...
Working...
Working...
... (forever)
```

What happened:
1. Main asks the worker to stop with `interrupt()`.
2. The worker is inside `sleep` (almost always: it spends nearly all its time sleeping), so
   `sleep` throws `InterruptedException` and the flag is **cleared**.
3. The empty `catch` swallows it. The stop request is **gone**.
4. The loop checks the flag: not set, so it keeps working.
5. "Worker stopped" and "Main: done" **never** print. The program never ends; you have to
   press Ctrl+C.

In a real server this is **broken shutdown**: the server asks its workers to stop, they ignore
it, and shutdown hangs until the operating system kills the process outright (`kill -9`, F2.9
Subtask 3), skipping every `finally` and every cleanup.

#### Correct way 1 — restore the flag: `Thread.currentThread().interrupt()`

```java
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();     // ✅ put the stop request back
}
```

`Thread.currentThread()` = "the thread running this line" (the worker). Calling
`interrupt()` on it sets the flag again.

**Output:**
```
Working...
Working...
Working...
Main: asking worker to stop
Worker stopped
Main: done
```

The loop checks the flag, sees it's set, and exits. Shutdown works.

#### Correct way 2 — don't catch it: rethrow it to the caller

If you're in a normal method (not `run()`), just let it go up by declaring it:

```java
static void waitForReport() throws InterruptedException {
    Thread.sleep(5000);
}
```

The caller now gets the stop request and decides what to do. (`run()` itself can't declare
checked exceptions, which is why inside `run()` you use way 1.)

#### The hidden trap: `catch (Exception e)` swallows it too

`InterruptedException` is a checked exception, a child of `Exception`. So this:

```java
try {
    Thread.sleep(1000);
} catch (Exception e) {
    // swallowed
}
```

swallows the stop request **without you even noticing** it was there. One more reason not to
catch `Exception` broadly.

### The never-catch list

| Never | Why |
|---|---|
| `catch (Error e)` | The JVM is broken (e.g. out of memory). Continuing can corrupt data; you can't fix it by carrying on. Let it crash and restart. |
| `catch (Throwable t)` | Catches all `Error`s too, so the same problem, disguised. |
| `catch (InterruptedException e) { }` | Destroys another thread's stop request; shutdown hangs. Either rethrow it, or call `Thread.currentThread().interrupt()`. |

### Key Takeaways:
- Never catch `Error` or `Throwable`: you can't fix a broken JVM by continuing
- A **thread** is a worker running code at the same time as others; `interrupt()` politely
  asks it to stop
- `InterruptedException` **is** the stop request, and throwing it clears the interrupted flag
- Swallowing it = ignoring the stop request = shutdown that never finishes
- Two correct ways: **rethrow** it (`throws InterruptedException`) or **restore the flag**
  (`Thread.currentThread().interrupt()`)
- `catch (Exception e)` silently swallows it too

---

## Concept 16: Exceptions vs Ordinary Decisions

### The rule

> **Exceptions are for exceptional conditions, not for ordinary decisions.**

- **Ordinary decision** — something that happens **normally and often**, and the caller
  **expects** to deal with it. Example: a user types an invalid email in a sign-up form.
  People mistype all the time; that's not a failure of the system.
- **Exceptional condition** — something that **shouldn't happen** when the system is healthy.
  Example: the database disappears in the middle of saving.

Deciding "what the program does next" is called **control flow** (`if`, loops, `return`).
Exceptions are a poor tool for ordinary control flow.

### Bad: using an exception for an ordinary decision

```java
static void signUp(String email) {
    if (!email.contains("@")) {
        throw new IllegalArgumentException("Invalid email: " + email);
    }
    System.out.println("Signed up " + email);
}

public static void main(String[] args) {
    try {
        signUp("pavan.gmail.com");
    } catch (IllegalArgumentException e) {
        System.out.println("Please fix: " + e.getMessage());
    }
}
```

**Output:**
```
Please fix: Invalid email: pavan.gmail.com
```

It works, but it's the wrong tool:
1. **A mistyped email is expected**, so every caller is forced to write a try/catch for a
   completely normal situation.
2. **It stops at the first problem.** If the password is also too short, the user only
   finds out after fixing the email and submitting again.
3. **It mixes normal outcomes with real failures.** A `catch` meant for "user typed it wrong"
   can accidentally catch a real bug of the same type.
4. **It's slower.** Creating an exception records the whole stack trace, which is wasted
   work for something that happens all the time.

### Good: return a result instead

Make the answer a normal **return value**. The simplest version: return a list of problems
(empty = all good).

```java
import java.util.ArrayList;
import java.util.List;

static List<String> validate(String email, String password) {
    List<String> problems = new ArrayList<>();
    if (!email.contains("@")) {
        problems.add("Email must contain @");
    }
    if (password.length() < 8) {
        problems.add("Password must be at least 8 characters");
    }
    return problems;
}

public static void main(String[] args) {
    List<String> problems = validate("pavan.gmail.com", "123");
    if (problems.isEmpty()) {
        System.out.println("Signed up!");
    } else {
        System.out.println("Please fix: " + problems);
    }
}
```

**Output:**
```
Please fix: [Email must contain @, Password must be at least 8 characters]
```

- A plain `if` decides what happens next; no try/catch.
- **All** problems are reported at once.
- Real failures (bugs, a dead database) stay as exceptions, separate from this.

This is called **validation**: checking input and returning the result.

### Good: an exception for a truly exceptional condition

```java
static void saveUser(String email) {
    boolean databaseReachable = false;   // pretend the database just went down
    if (!databaseReachable) {
        throw new IllegalStateException("Database unavailable while saving user " + email);
    }
    System.out.println("Saved " + email);
}
```

The caller **can't** sensibly plan for this in normal flow; the system is unhealthy. This is
exactly what exceptions are for: stop, carry the information up, and handle it at the top
(Concept 14: log it, return an error).

### In between: "not found"

A user asks for book 99, and it doesn't exist. Is that ordinary or exceptional? **It depends.**

**Option A — return a result: `Optional`**

`Optional` is a Java box that **either holds a value or is empty**. It makes "maybe there's
nothing" visible in the return type:

```java
import java.util.Optional;

static Optional<String> findBook(int id) {
    if (id == 1) {
        return Optional.of("Java Basics");   // box with a value
    }
    return Optional.empty();                 // empty box
}

public static void main(String[] args) {
    Optional<String> book = findBook(99);
    if (book.isPresent()) {
        System.out.println("Found: " + book.get());
    } else {
        System.out.println("No such book");
    }
}
```

**Output:**
```
No such book
```

- `Optional.of(value)` — a box with a value; `Optional.empty()` — an empty box.
- `isPresent()` — is there a value? `get()` — take it out.
- Good when "not found" is a **normal** answer, e.g. a search box.

(Never return `null` for "not found": the caller can forget to check and get a
`NullPointerException`. `Optional` forces the question.)

**Option B — throw: `BookNotFoundException`** (Concept 12)

Good when the caller **expected** it to exist. Example: a link to "book 99" was clicked, so the
book should be there; if it isn't, something is wrong.

Which one a project uses is a design decision; for this plan, Task 1.8 (the error-contract
task) decides it.

### Quick test: exception or result?

Ask: **"If the system is working perfectly, can this still happen often?"**

| Situation | Happens normally? | Use |
|---|---|---|
| User typed an invalid email | Yes, often | **Return a result** (validation) |
| Password too short | Yes, often | **Return a result** |
| Searching for a book that doesn't exist | Yes | **`Optional`** |
| Book id from a link that should exist, but doesn't | No | **Exception** |
| Database disappears mid-save | No | **Exception** |
| Out of memory | No | `Error` (don't catch) |

### Key Takeaways:
- **Exceptions for exceptional conditions, return values for ordinary decisions**
- Invalid user input is **validation**: return a result (e.g. a list of problems), not an
  exception
- Exceptions for normal cases force try/catch everywhere, stop at the first problem, hide
  real bugs, and are slower
- "Not found" is in between: `Optional` when it's a normal answer, an exception when it
  shouldn't happen; never `null`
- Test: "Can this happen often when everything is working?" Yes → result. No → exception.

---

## F2.10 Subtask 1: Build the Hierarchy

> **From the plan:** Build the hierarchy Phase 1 will ask for: an abstract
> `DomainException extends RuntimeException`, with `EventNotFoundException` and
> `EventNotPublishableException` beneath it. Give each a message that includes the id involved.

### Two words first

- **Domain** — the real-world area your app is about. For a ticket app, the domain is events,
  tickets, customers. A **`DomainException`** is the parent for every "a business rule was
  broken" problem, as opposed to technical problems like a crashed database.
- **Event** — here, something like a concert. It's created as a **draft** first, then
  **published** (made visible so tickets can be sold). Publishing can fail, e.g. if the event
  has no date yet.

### The family tree

```
RuntimeException
      |
DomainException                 ← abstract parent: "a business rule was broken"
   /                  \
EventNotFoundException   EventNotPublishableException
```

Same shape as Concept 12's library example, with the plan's names.

### The parent: `DomainException`

```java
public abstract class DomainException extends RuntimeException {
    protected DomainException(String message) {
        super(message);
    }

    protected DomainException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

- `abstract` — never thrown directly; too vague (Concept 12).
- `protected` constructors — only children can call them.
- Two constructors: message only (Concept 10), and message + cause (Concept 13), so any child
  can chain a cause later.

### Child 1: `EventNotFoundException`

```java
public class EventNotFoundException extends DomainException {
    private final long eventId;

    public EventNotFoundException(long eventId) {
        super("Event " + eventId + " not found");
        this.eventId = eventId;
    }

    public long getEventId() {
        return eventId;
    }
}
```

- The message includes the **id**: `Event 77 not found`, not just `not found`.
- The id is also stored as a **field** with a getter (Concept 11), so code can use it without
  reading the message.
- `long` — ids are usually `long` (bigger range than `int`).

### Child 2: `EventNotPublishableException`

```java
public class EventNotPublishableException extends DomainException {
    private final long eventId;
    private final String reason;

    public EventNotPublishableException(long eventId, String reason) {
        super("Event " + eventId + " cannot be published: " + reason);
        this.eventId = eventId;
        this.reason = reason;
    }

    public long getEventId() {
        return eventId;
    }

    public String getReason() {
        return reason;
    }
}
```

- Two pieces of data: **which** event and **why** it can't be published.
- Message: `Event 12 cannot be published: it has no date`.

### Using them

A tiny service that pretends event 77 doesn't exist and event 12 has no date:

```java
class EventService {
    void publish(long eventId) {
        if (eventId == 77) {
            throw new EventNotFoundException(eventId);
        }
        if (eventId == 12) {
            throw new EventNotPublishableException(eventId, "it has no date");
        }
        System.out.println("Event " + eventId + " published");
    }
}

public class EventDemo {
    public static void main(String[] args) {
        EventService service = new EventService();
        service.publish(5);

        try {
            service.publish(77);
        } catch (EventNotFoundException e) {
            System.out.println(e.getMessage() + " (id field: " + e.getEventId() + ")");
        }

        try {
            service.publish(12);
        } catch (EventNotPublishableException e) {
            System.out.println(e.getMessage());
            System.out.println("Reason field: " + e.getReason());
        }
    }
}
```

**Output:**
```
Event 5 published
Event 77 not found (id field: 77)
Event 12 cannot be published: it has no date
Reason field: it has no date
```

### Try this: you can't throw the parent

```java
throw new DomainException("something");
```

Compile error: `DomainException is abstract; cannot be instantiated`. That's the point of
`abstract`: you're forced to throw a **specific** child.

### Checklist for this subtask
- [ ] `DomainException` is `abstract`, extends `RuntimeException`, has both constructors
- [ ] Both children extend `DomainException`
- [ ] Each message includes the event id
- [ ] Each child stores its data in `private final` fields with getters

---

## F2.10 Subtask 2: Throw the Specific One, Catch the Parent — Then Catch the Specific One

> **From the plan:** Throw the specific one and catch the parent. Then catch the specific one.
> Note that the hierarchy gives the caller the choice of how precise to be, which is the
> entire point.

Uses `DomainException`, `EventNotFoundException`, `EventNotPublishableException` and
`EventService` from Subtask 1 (event 77 doesn't exist, event 12 has no date).

### Part 1 — catch the parent

```java
public class CatchParentDemo {
    public static void main(String[] args) {
        EventService service = new EventService();
        long[] ids = {5, 77, 12};

        for (long id : ids) {
            try {
                service.publish(id);
            } catch (DomainException e) {
                System.out.println("Could not publish: " + e.getMessage());
            }
        }
    }
}
```

**Output:**
```
Event 5 published
Could not publish: Event 77 not found
Could not publish: Event 12 cannot be published: it has no date
```

- `publish` threw **specific** exceptions (`EventNotFoundException`,
  `EventNotPublishableException`).
- One `catch (DomainException e)` caught **both**, because both **are** `DomainException`s.
- The caller here doesn't care *which* rule was broken; it treats them all the same.

### Part 2 — catch the specific ones

```java
public class CatchSpecificDemo {
    public static void main(String[] args) {
        EventService service = new EventService();
        long[] ids = {5, 77, 12};

        for (long id : ids) {
            try {
                service.publish(id);
            } catch (EventNotFoundException e) {
                System.out.println("No event with id " + e.getEventId() + ". Check the id.");
            } catch (EventNotPublishableException e) {
                System.out.println("Event " + e.getEventId() + " needs fixing first: " + e.getReason());
            }
        }
    }
}
```

**Output:**
```
Event 5 published
No event with id 77. Check the id.
Event 12 needs fixing first: it has no date
```

- Each problem gets its **own** reaction.
- Each catch uses the child's **data** (`getEventId()`, `getReason()`), which only the
  specific type has.

### Part 3 — mix both: one special, the rest general

```java
for (long id : ids) {
    try {
        service.publish(id);
    } catch (EventNotPublishableException e) {
        System.out.println("Event " + e.getEventId() + " needs fixing first: " + e.getReason());
    } catch (DomainException e) {
        System.out.println("Could not publish: " + e.getMessage());
    }
}
```

**Output:**
```
Event 5 published
Could not publish: Event 77 not found
Event 12 needs fixing first: it has no date
```

Child catch first, parent catch last (Concept 4). If you swap them, the compiler rejects it:
`exception EventNotPublishableException has already been caught`.

### Why this is "the entire point"

The **thrower** (`publish`) always throws the most specific exception. It doesn't know or
care how the caller will react. The **caller** chooses how precise to be:

| Caller | Catches | Why |
|---|---|---|
| A screen showing a generic "couldn't publish" message | `DomainException` | Doesn't care which rule |
| An admin screen showing a fix-it hint | each child | Different advice per problem |
| A web layer (Phase 1) turning errors into responses | each child | "not found" → 404, "not publishable" → 409 (more on this later) |

Without the shared parent, the first caller would have to list every exception type and
update the list each time a new one is added. With it, `catch (DomainException e)` covers
all of them, including future ones.

### For `NOTES.md`

> I threw specific exceptions and caught them either by the parent (`DomainException`, one
> reaction for all) or by each child (a different reaction per problem, using its data). The
> hierarchy lets each caller choose how precise to be; the thrower doesn't have to know.

---

## F2.10 Subtask 3: Chain a Cause and Find `Caused by:`

> **From the plan:** Chain a cause:
> `throw new OrderFailedException("could not reserve seats for order " + id, cause);`. Print
> the trace and find the `Caused by:` section.

### The situation

Placing an order needs seats to be reserved first. Reserving can fail (not enough seats left).
When it fails, we throw a meaningful **`OrderFailedException`** ("order 42 failed") and attach
the original reservation error as the **cause** ("only 2 seats left") — Concept 13.

### The new exception

It goes in our family from Subtask 1, using the parent's `(message, cause)` constructor:

```java
public class OrderFailedException extends DomainException {
    public OrderFailedException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

`super(message, cause)` → `DomainException(message, cause)` → `RuntimeException(message, cause)`,
which stores the cause.

### The code (all in one file, `OrderDemo.java`)

```java
class SeatReserver {
    void reserve(long orderId, int seatsWanted) {
        int seatsLeft = 2;
        if (seatsWanted > seatsLeft) {
            throw new IllegalStateException("only " + seatsLeft + " seats left, " + seatsWanted + " requested");
        }
        System.out.println("Reserved " + seatsWanted + " seats for order " + orderId);
    }
}

class OrderService {
    private final SeatReserver reserver = new SeatReserver();

    void placeOrder(long orderId, int seats) {
        try {
            reserver.reserve(orderId, seats);
        } catch (IllegalStateException cause) {
            throw new OrderFailedException("could not reserve seats for order " + orderId, cause);
        }
    }
}

public class OrderDemo {
    public static void main(String[] args) {
        OrderService service = new OrderService();
        service.placeOrder(42, 5);
    }
}
```

- `SeatReserver.reserve` — only 2 seats left; asking for 5 throws `IllegalStateException`
  (line 5).
- `OrderService.placeOrder` — catches it (the variable is named `cause` to make the intent
  clear) and throws `OrderFailedException` with the original attached (line 18).
- `main` — places order 42 for 5 seats (line 26). Nobody catches, so Java prints the trace.

### The printed trace

```
Exception in thread "main" OrderFailedException: could not reserve seats for order 42
	at OrderService.placeOrder(OrderDemo.java:18)
	at OrderDemo.main(OrderDemo.java:26)
Caused by: java.lang.IllegalStateException: only 2 seats left, 5 requested
	at SeatReserver.reserve(OrderDemo.java:5)
	at OrderService.placeOrder(OrderDemo.java:16)
	... 1 more
```

### Reading it

**Top part — WHAT failed:**
```
OrderFailedException: could not reserve seats for order 42
	at OrderService.placeOrder(OrderDemo.java:18)    ← our throw
	at OrderDemo.main(OrderDemo.java:26)             ← who called it
```

**`Caused by:` part — WHY it failed:**
```
Caused by: java.lang.IllegalStateException: only 2 seats left, 5 requested
	at SeatReserver.reserve(OrderDemo.java:5)        ← where the real problem started
	at OrderService.placeOrder(OrderDemo.java:16)    ← the call to reserve()
	... 1 more                                       ← the rest (main, line 26) is the same as above
```

The full story in one trace: **order 42 failed, because only 2 seats were left when 5 were
requested, detected at line 5 of `SeatReserver.reserve`.**

### Printing it yourself while catching

If you catch the exception, you can still see everything:

```java
public static void main(String[] args) {
    OrderService service = new OrderService();
    try {
        service.placeOrder(42, 5);
    } catch (OrderFailedException e) {
        System.out.println("What: " + e.getMessage());
        System.out.println("Why:  " + e.getCause().getMessage());
        e.printStackTrace();
    }
}
```

**Output:**
```
What: could not reserve seats for order 42
Why:  only 2 seats left, 5 requested
OrderFailedException: could not reserve seats for order 42
	at OrderService.placeOrder(OrderDemo.java:18)
	at OrderDemo.main(...)
Caused by: java.lang.IllegalStateException: only 2 seats left, 5 requested
	at SeatReserver.reserve(OrderDemo.java:5)
	at OrderService.placeOrder(OrderDemo.java:16)
	... 1 more
```

(`printStackTrace()` is fine **here**, in a learning demo, to see the trace. In a real
service you'd log it instead — Concept 14.)

### For `NOTES.md`

> Wrapped `IllegalStateException` in `OrderFailedException("could not reserve seats for order
> 42", cause)`. The trace's top part says what failed and where I threw it; the `Caused by:`
> part shows the original exception, its message, and the line where it really started.

---

## F2.10 Subtask 4: Break It on Purpose — Throw Without the Cause

> **From the plan:** Throw the same exception without passing the cause. Compare the two
> traces side by side. The second one tells you an order failed and nothing about why. That
> difference is a two-hour outage.

### Step 1 — give `OrderFailedException` a message-only constructor

To throw it without a cause, it needs a constructor that takes only a message:

```java
public class OrderFailedException extends DomainException {
    public OrderFailedException(String message) {
        super(message);
    }

    public OrderFailedException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

### Step 2 — change one line in `OrderService`

Same code as Subtask 3, but the `throw` no longer passes `cause`:

```java
void placeOrder(long orderId, int seats) {
    try {
        reserver.reserve(orderId, seats);
    } catch (IllegalStateException cause) {
        throw new OrderFailedException("could not reserve seats for order " + orderId);   // cause dropped
    }
}
```

### Step 3 — compare the two traces

**With the cause (Subtask 3):**
```
Exception in thread "main" OrderFailedException: could not reserve seats for order 42
	at OrderService.placeOrder(OrderDemo.java:18)
	at OrderDemo.main(OrderDemo.java:26)
Caused by: java.lang.IllegalStateException: only 2 seats left, 5 requested
	at SeatReserver.reserve(OrderDemo.java:5)
	at OrderService.placeOrder(OrderDemo.java:16)
	... 1 more
```

**Without the cause (this subtask):**
```
Exception in thread "main" OrderFailedException: could not reserve seats for order 42
	at OrderService.placeOrder(OrderDemo.java:18)
	at OrderDemo.main(OrderDemo.java:26)
```

| Question when debugging | With cause | Without cause |
|---|---|---|
| What failed? | Order 42 | Order 42 |
| Where was it thrown? | line 18 | line 18 |
| **Why** did it fail? | Only 2 seats left, 5 requested | **Unknown** |
| What was the original error type? | `IllegalStateException` | **Unknown** |
| Where did the real problem start? | `SeatReserver.reserve`, line 5 | **Unknown** |

The second trace says an order failed and **nothing about why**.

### Why the plan calls it "a two-hour outage"

Imagine this is a real system and orders start failing at 3am. With only the second trace,
the person on call has to **guess**: Was it seats? Payment? The database? A bug? They add
extra logging, redeploy, wait for it to happen again, and dig. That can take hours, while
customers can't buy anything.

With the first trace, the answer is in the very first error they read: *only 2 seats left,
line 5*. Minutes, not hours.

Dropping `cause` saved typing **one word** at the time of writing. It cost hours at the
worst possible moment.

### The other common mistake: `cause.getMessage()`

```java
throw new OrderFailedException(cause.getMessage());
```

**Trace:**
```
Exception in thread "main" OrderFailedException: only 2 seats left, 5 requested
	at OrderService.placeOrder(OrderDemo.java:18)
	at OrderDemo.main(OrderDemo.java:26)
```

Looks better, but:
- The **context** is gone: which order? ("order 42" isn't anywhere.)
- The **original type** (`IllegalStateException`) is gone.
- **Where it started** (line 5 in `reserve`) is gone.

### The rule

```java
throw new OrderFailedException("could not reserve seats for order " + orderId, cause);
//                              ^ your context                                 ^ ALWAYS pass this
```

### For `NOTES.md`

> Without the cause, the trace only says order 42 failed, with nothing about why, the
> original type, or where it started; with the cause, `Caused by:` answers all three. Always
> pass the original exception itself, never drop it and never pass only `getMessage()`.

---

## F2.10 Subtask 5: The Cardinal Sin — `catch (Exception e) { }`

> **From the plan:** Write `catch (Exception e) { }` around something that fails. Watch the
> program carry on as if all is well, with wrong data. Then change it to
> `catch (Exception e) { e.printStackTrace(); }` and work out why that is still unacceptable in
> a service: it goes to the console rather than the logs, carries no context, and still
> continues as if nothing happened.

"Cardinal sin" = the worst possible mistake.

### The code: the order system from Subtask 3, with an empty catch

```java
class SeatReserver {
    void reserve(long orderId, int seatsWanted) {
        int seatsLeft = 2;
        if (seatsWanted > seatsLeft) {
            throw new IllegalStateException("only " + seatsLeft + " seats left, " + seatsWanted + " requested");
        }
        System.out.println("Reserved " + seatsWanted + " seats for order " + orderId);
    }
}

class OrderService {
    private final SeatReserver reserver = new SeatReserver();

    void placeOrder(long orderId, int seats) {
        try {
            reserver.reserve(orderId, seats);
        } catch (Exception e) {
            // ❌ the cardinal sin: swallowed
        }
        System.out.println("Order " + orderId + " confirmed. Charging customer for " + seats + " seats.");
    }
}

public class SinDemo {
    public static void main(String[] args) {
        OrderService service = new OrderService();
        service.placeOrder(42, 5);
        System.out.println("All good!");
    }
}
```

**Output:**
```
Order 42 confirmed. Charging customer for 5 seats.
All good!
```

What happened:
1. `reserve` threw: only 2 seats left, 5 requested. **No seats were reserved.**
2. The empty `catch` swallowed it.
3. The program carried on: confirmed the order and **charged the customer for 5 seats they
   don't have**.
4. "All good!" Nothing anywhere says anything went wrong.

**Wrong data, no error.** The customer arrives at the venue with tickets for seats that
don't exist. Nobody can even find out why, because no trace of the failure exists.

### "Fix" attempt: `e.printStackTrace()`

```java
} catch (Exception e) {
    e.printStackTrace();
}
```

**Output:**
```
java.lang.IllegalStateException: only 2 seats left, 5 requested
	at SeatReserver.reserve(SinDemo.java:5)
	at OrderService.placeOrder(SinDemo.java:16)
	at SinDemo.main(SinDemo.java:...)
Order 42 confirmed. Charging customer for 5 seats.
All good!
```

You can see the error now. Why is it **still unacceptable in a service**?

**1. It goes to the console, not the logs.**
A real service runs on a server; nobody watches its console. Teams read **logs**, which store
each message with a **time**, a **severity** (INFO / WARN / ERROR) and the **class** that wrote
it, and can be searched and trigger alerts. Console output usually ends up nowhere, with no
time and no severity.

**2. It carries no context.**
The trace says `only 2 seats left, 5 requested`. **Which order? Which customer?** Nothing
says "order 42". With thousands of orders, you can't tell which one this was.

**3. It still continues as if nothing happened.**
Look at the output: `Order 42 confirmed. Charging customer for 5 seats.` The wrong charge
**still happens**. Printing didn't stop anything.

| | `catch { }` | `catch { e.printStackTrace(); }` |
|---|---|---|
| Wrong data stopped? | ❌ | ❌ |
| Anyone told? | ❌ | Only someone staring at the console |
| Context (which order)? | ❌ | ❌ |
| Time + severity, searchable? | ❌ | ❌ |

### Bonus: `catch (Exception e)` also hides bugs

`Exception` catches **everything**, including real bugs like `NullPointerException`. If
`reserve` had a bug, this catch would silently hide that too. And it would swallow
`InterruptedException` (Concept 15).

### What to write instead

**Best here: wrap with context and the cause, and stop** (Subtask 3):

```java
void placeOrder(long orderId, int seats) {
    try {
        reserver.reserve(orderId, seats);
    } catch (IllegalStateException e) {
        throw new OrderFailedException("could not reserve seats for order " + orderId, e);
    }
    System.out.println("Order " + orderId + " confirmed. Charging customer for " + seats + " seats.");
}
```

**Output:**
```
Exception in thread "main" OrderFailedException: could not reserve seats for order 42
	at OrderService.placeOrder(...)
	at SinDemo.main(...)
Caused by: java.lang.IllegalStateException: only 2 seats left, 5 requested
	at SeatReserver.reserve(...)
	...
```

- Catches the **specific** type, not `Exception`.
- **Stops**: "confirmed / charging" never runs, so no wrong charge.
- Has **context** ("order 42") and the **cause**.
- At the top of a real service, one handler **logs** it properly (with time, severity, and
  the full trace) and shows the customer a clear error.

### For `NOTES.md`

> `catch (Exception e) { }` let the order be confirmed and charged with no seats reserved,
> and no error anywhere. `e.printStackTrace()` still isn't acceptable in a service: it goes to
> the console not the logs, has no context (which order?), and the wrong charge still happens.
> Instead: catch the specific type, wrap with context + cause, and stop; log it once at the top.

---

## F2.10 Subtask 6: The Never-Catch List

> **From the plan:** Learn the never-catch list and write the reason for each: `Throwable` and
> `Error` (you cannot fix an out-of-memory condition by continuing), and `InterruptedException`
> without either rethrowing it or calling `Thread.currentThread().interrupt()`.

(Full explanation with threads: Concept 15. This is the list to write down.)

### 1. `Error`

```java
} catch (OutOfMemoryError e) {      // ❌
```

**Reason:** an `Error` means the JVM itself is broken (no memory left, stack overflow). After
that, any line can fail, and data may be half-updated. You can't fix an out-of-memory condition
by continuing; carrying on risks corrupted data. Let it crash so it restarts clean, then find
the cause.

### 2. `Throwable`

```java
} catch (Throwable t) {             // ❌
```

**Reason:** `Throwable` is the parent of both `Exception` **and** `Error`, so it catches every
`Error` too. Same danger as point 1, just hidden behind a harmless-looking catch.

### 3. `InterruptedException` without rethrowing or restoring the flag

```java
} catch (InterruptedException e) {  // ❌ swallowed
}
```

**Reason:** `InterruptedException` is another thread's request for this thread to **stop**
(e.g. during shutdown). When it's thrown, Java clears the "please stop" flag, so swallowing
it destroys the request: the thread keeps running and shutdown hangs.

The two correct ways:

```java
// ✅ Way 1: rethrow — declare it and let the caller decide
void waitForData() throws InterruptedException {
    Thread.sleep(1000);
}

// ✅ Way 2: restore the flag, so the code above you still sees the stop request
try {
    Thread.sleep(1000);
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}
```

### Also avoid: `catch (Exception e)` as a habit

Not on the plan's list, but related: `catch (Exception e)` catches real bugs
(`NullPointerException`) **and** silently swallows `InterruptedException` (it's a child of
`Exception`). Catch the **specific** type you can handle.

### The list, for `NOTES.md`

| Never | Reason |
|---|---|
| `catch (Error e)` | The JVM is broken (e.g. out of memory); you can't fix it by continuing, and continuing risks corrupted data. Let it crash and restart. |
| `catch (Throwable t)` | Catches all `Error`s too — the same problem, disguised. |
| `catch (InterruptedException e) { }` | Destroys another thread's stop request, so shutdown hangs. Always rethrow it or call `Thread.currentThread().interrupt()`. |

---

## F2.10 Subtask 7: Ask AI About `InterruptedException`

> **From the plan:** Ask AI: *"Why is swallowing InterruptedException harmful, and what are
> the two correct ways to handle it? Show me the version that breaks graceful shutdown."*

### First: what is "graceful shutdown"?

**Shutting down gracefully** = stopping an application **cleanly**: each thread finishes or
stops its current job, `finally` blocks run, files and connections are closed, and then the
program exits.

The opposite is being **killed**: the operating system destroys the program instantly
(`kill -9`), so nothing gets cleaned up (no `finally`, no `close()`).

Servers try to shut down gracefully first (by interrupting their threads). If a thread
ignores the request, the server waits, gives up, and kills the process.

### The answer

**Why swallowing it is harmful:**
`InterruptedException` is how one thread tells another "please stop". When it's thrown, Java
clears the thread's "interrupted" flag, so the exception is now the **only** record of the
request. Swallowing it deletes the request. The thread keeps working as if nobody asked, so
graceful shutdown can't finish and the process ends up killed.

**The two correct ways:**
1. **Rethrow it**: declare `throws InterruptedException` and let the caller handle it.
2. **Restore the flag**: in the catch, call `Thread.currentThread().interrupt()`, so code
   further up still sees the stop request.

### The version that breaks graceful shutdown

The dangerous version is often **not** in the loop itself, but in a small helper method deep
inside, where it looks harmless:

```java
class Worker extends Thread {
    @Override
    public void run() {
        while (!Thread.currentThread().isInterrupted()) {   // the loop DOES check for stop
            System.out.println("Working...");
            pause();
        }
        System.out.println("Worker stopped cleanly");
    }

    private void pause() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            // ❌ swallowed inside a helper: "it's just a pause, who cares"
        }
    }
}

public class ShutdownDemo {
    public static void main(String[] args) throws InterruptedException {
        Worker worker = new Worker();
        worker.start();

        Thread.sleep(3000);
        System.out.println("Main: shutting down, asking worker to stop");
        worker.interrupt();

        worker.join();
        System.out.println("Main: shutdown complete");
    }
}
```

**Output:**
```
Working...
Working...
Working...
Main: shutting down, asking worker to stop
Working...
Working...
... (forever — "Worker stopped cleanly" and "shutdown complete" never print)
```

1. Main asks the worker to stop (`interrupt()`).
2. The worker is inside `pause()` → `sleep` throws `InterruptedException` → flag **cleared**.
3. `pause()` swallows it. The request is gone.
4. Back in `run()`, the loop checks the flag correctly, but it's no longer set, so it keeps
   going.
5. `worker.join()` waits forever. Shutdown never completes; in a real server the process
   would be killed.

The loop was written correctly. One "harmless" empty catch in a helper broke shutdown for the
whole program.

### Fix with way 2 — restore the flag in the helper

```java
private void pause() {
    try {
        Thread.sleep(1000);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();   // ✅ put the stop request back
    }
}
```

**Output:**
```
Working...
Working...
Working...
Main: shutting down, asking worker to stop
Worker stopped cleanly
Main: shutdown complete
```

### Fix with way 1 — rethrow from the helper

```java
private void pause() throws InterruptedException {
    Thread.sleep(1000);
}

@Override
public void run() {
    try {
        while (!Thread.currentThread().isInterrupted()) {
            System.out.println("Working...");
            pause();
        }
    } catch (InterruptedException e) {
        // run() is the top of this thread: receiving the request here means "stop", so we exit
    }
    System.out.println("Worker stopped cleanly");
}
```

- `pause()` no longer catches; it passes the request up.
- `run()` is the **top** of the thread (it can't declare `throws`), so it catches the
  exception **and stops** by leaving the loop. This catch isn't swallowing: it **acts** on
  the request.

Same output as the previous fix.

### If you ask a different AI, check its answer contains:
- [ ] `InterruptedException` is a request to stop, and throwing it clears the interrupted flag
- [ ] Swallowing it loses the request, so the thread keeps running
- [ ] Way 1: rethrow (`throws InterruptedException`)
- [ ] Way 2: `Thread.currentThread().interrupt()` in the catch
- [ ] A broken example where shutdown hangs because the thread never stops

### For `NOTES.md`

> Swallowing `InterruptedException` deletes another thread's stop request (the flag is cleared
> when it's thrown), so the thread keeps running and graceful shutdown hangs until the process
> is killed. Fix it by rethrowing or by calling `Thread.currentThread().interrupt()`. Even one
> empty catch in a small helper breaks shutdown for the whole program.

---

## F2.10 Subtask 8: Write the Rule — Exceptions vs Ordinary Decisions

> **From the plan:** Write the rule down: exceptions are for exceptional conditions, not for
> ordinary decisions. "The user typed an invalid email" is validation and belongs in a
> returned result. "The database vanished mid-transaction" is an exception. "Not found" sits
> between the two, and Task 1.8 — the error-contract task — decides it for this project.

(Full explanation: Concept 16. This is the rule with one example of each case.)

### The rule

> **Exceptions are for exceptional conditions, not for ordinary decisions.**
> Test: *"If the system is working perfectly, can this still happen often?"*
> Yes → return a result. No → throw an exception.

### Case 1 — "The user typed an invalid email" → validation, returned as a result

Happens all the time, even when everything works. So it's an ordinary decision:

```java
static List<String> validateEmail(String email) {
    List<String> problems = new ArrayList<>();
    if (!email.contains("@")) {
        problems.add("Email must contain @");
    }
    return problems;
}

List<String> problems = validateEmail("pavan.gmail.com");
if (problems.isEmpty()) {
    System.out.println("Signed up!");
} else {
    System.out.println("Please fix: " + problems);
}
```

**Output:**
```
Please fix: [Email must contain @]
```

No try/catch; a normal `if` decides what happens.

### Case 2 — "The database vanished mid-transaction" → exception

("Transaction" = a group of database changes that must all succeed or all be undone.)

Should never happen when the system is healthy, and the code can't sensibly continue:

```java
static void saveOrder(long orderId) {
    boolean databaseReachable = false;   // pretend it just went down
    if (!databaseReachable) {
        throw new IllegalStateException("Database unavailable while saving order " + orderId);
    }
    System.out.println("Saved order " + orderId);
}
```

It stops the work, carries the information up, and gets logged and handled at the top.

### Case 3 — "Not found" → in between

A request for book 99, which doesn't exist:

**As a result** (when "not found" is a normal answer, e.g. a search):

```java
static Optional<String> findBook(int id) {
    if (id == 1) {
        return Optional.of("Java Basics");
    }
    return Optional.empty();
}
```

**As an exception** (when it was expected to exist, e.g. opening a link to book 99):

```java
throw new BookNotFoundException(99);
```

Both are reasonable. A project must pick **one consistent rule**; for this plan, Task 1.8 (one
error contract for the whole platform) decides it.

### The rule, for `NOTES.md`

> Exceptions are for exceptional conditions, not ordinary decisions.
> - Invalid user input (e.g. a bad email) is **validation**: return a result listing the
>   problems.
> - The database vanishing mid-transaction is an **exception**: the system is unhealthy and
>   the code can't continue.
> - "Not found" is **in between** (`Optional` if it's a normal answer, an exception if it
>   should have existed); Task 1.8 decides it for this project.
> - Test: "Can this happen often when everything is working?" Yes → result. No → exception.

---

## F2.10 Subtask 9 (and Concept 17): Writing Good Exception Messages

> **From the plan:** Review your own messages. A good exception message names the thing and the
> context: `"seat A-14 for event 77 is already held"` beats `"invalid state"`.

### Who reads an exception message?

A **person debugging**, usually under pressure, often at night, looking at a log line with no
other information. The message must let them understand the problem **without** opening the
code.

### Bad vs good

| ❌ Bad | ✅ Good |
|---|---|
| `invalid state` | `seat A-14 for event 77 is already held` |
| `not found` | `event 77 not found` |
| `error` | `could not reserve 5 seats for order 42` |
| `bad input` | `seat number must be positive, got -3` |
| `failed` | `payment for order 42 declined by bank: insufficient funds` |

The bad ones are all true, but useless: **which** seat? **which** event? **what** value?

### The checklist for a good message

**1. Name the thing.** *What* is involved: seat, event, order, payment.

**2. Include the id(s) / context.** *Which* one: `seat A-14`, `event 77`, `order 42`.

**3. Include the actual value when it's about a value.** Say what you got, and ideally what
was expected:

```java
throw new IllegalArgumentException("seat number must be positive, got " + number);
```

**4. Say what went wrong in plain words.** `is already held`, `not found`,
`cannot be published: it has no date`.

**5. Don't put secrets in it.** Messages end up in logs, which many people can read. Never
include passwords, full card numbers or tokens:

```java
// ❌ throw new LoginException("wrong password 'hunter2' for user pavan");
// ✅ throw new LoginException("wrong password for user pavan");
```

**6. Keep it one short sentence.** A stack trace, the cause and the logs carry the rest.

### Build the message inside the exception class

From Concept 11: let the constructor build the message from the data, so every throw gets the
same good format automatically and nobody can forget the id:

```java
public class SeatAlreadyHeldException extends DomainException {
    private final String seatId;
    private final long eventId;

    public SeatAlreadyHeldException(String seatId, long eventId) {
        super("seat " + seatId + " for event " + eventId + " is already held");
        this.seatId = seatId;
        this.eventId = eventId;
    }

    public String getSeatId() { return seatId; }
    public long getEventId() { return eventId; }
}

// throwing it: just pass the data
throw new SeatAlreadyHeldException("A-14", 77);
```

**Message:** `seat A-14 for event 77 is already held`

### Reviewing the messages from this task

| Where | Message | Verdict |
|---|---|---|
| Subtask 1 | `Event 77 not found` | ✅ thing + id |
| Subtask 1 | `Event 12 cannot be published: it has no date` | ✅ thing + id + reason |
| Subtask 3 | `could not reserve seats for order 42` | 👍 good; better with the count: `could not reserve 5 seats for order 42` |
| Subtask 3 | `only 2 seats left, 5 requested` | 👍 has the values; better with *which* event: `only 2 seats left for event 77, 5 requested` |
| Concept 14 | `Invalid price 'abc' in order 42` | ✅ value + context |

Improved version of Subtask 3's throw:

```java
throw new OrderFailedException(
    "could not reserve " + seats + " seats for order " + orderId, cause);
```

### For `NOTES.md`

> A good exception message names the thing, which one (id/context), and the actual value,
> in one plain sentence, with no secrets: `"seat A-14 for event 77 is already held"`, not
> `"invalid state"`. Build it inside the exception's constructor from its data, so every throw
> gets it right.

---

## F2.10 Checkpoint — answer out loud

1. Why give your domain exceptions a common parent rather than making each one unrelated?
   *(Subtask 2; Concept 12.)*
2. What is lost when you throw a new exception without passing the cause?
   *(Subtask 4; Concept 13.)*
3. Why is `e.printStackTrace()` inside a catch block not good enough for a real service?
   *(Subtask 5; Concept 14.)*
4. What are the two correct ways to handle `InterruptedException`, and what breaks if you
   ignore it? *(Subtask 7; Concept 15.)*
5. Give one failure that deserves an exception and one that does not, and justify both.
   *(Subtask 8; Concept 16.)*

**Done when** (from the plan): you have a three-class exception hierarchy, a printed trace
with a `Caused by:` section you can explain, and a written list of what you will never catch.

---

