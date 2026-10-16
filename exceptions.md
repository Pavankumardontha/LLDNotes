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

