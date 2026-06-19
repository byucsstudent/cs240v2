# Java Exceptions

🖥️ [Slides](https://docs.google.com/presentation/d/1CIyKxxGhJXUCQvwsJT64Oaao0p9LcBIZ/edit?usp=sharing&ouid=114081115660452804792&rtpof=true&sd=true)

🖥️ [Lecture Videos](#videos)

📖 **Required Reading**: Core Java for the Impatient

- Chapter 5: Exceptions, Assertions, And Logging. (_Only read sections 5.1-5.1.9: Exception Handling_)

### 🔑 Key points

- The difference between checked and unchecked exceptions in Java
- How and when to handle an exception in Java
- How and when to throw an exception in Java
- How to create custom exception classes
- How to use `try/catch` blocks
- What `finally` blocks are and how to use them

---

Java exceptions allow you to deviate from the normal execution flow of a program when an "exceptional" event occurs. You can then handle the exception at a higher level in the call stack.

Java uses the standard `try`, `catch`, and `throw` syntax found in many modern programming languages. You define a block where exceptions might occur using the `try` statement. This block is followed by one or more `catch` blocks. For each `catch` block, you specify the exception type(s) it handles. That type and any types derived from it will be caught by that block. 

> [!NOTE]
>
> The Java runtime matches catch blocks from top to bottom. You should always list the most specific exceptions first and the most general (like `Exception`) last.

```java
try {
    // Code that might throw an exception
} catch (FileNotFoundException ex) {
    // Specific file error handling
} catch (IOException ex) {
    // Other IO error handling (excluding FileNotFoundException)
    // FileNotFoundException is a subclass of IOException, but won't trigger this block  because it was caught above. 
} catch (Exception ex) {
    // General error handling for everything else
}
```

## Throw and Throws

To raise an exception, use the `throw` keyword followed by an instance of an exception class.

```java
throw new IllegalArgumentException("Missing required parameter");
```

When you throw an exception, the normal flow of the code is interrupted, and the execution pointer jumps to the nearest matching `catch` block in the call stack.

Java requires that a method's signature declares all **checked** exceptions it might throw using the `throws` keyword. This requirement propagates up the call stack: any method calling a function that throws a checked exception must either catch it or declare it in its own signature.

```java
void top() {
    try {
        A();
    } catch (Exception ex) {
        System.out.println("This WILL execute");
    }
}

void A() throws Exception {
    B();
    System.out.println("This will NOT execute");
}

void B() throws Exception {
    C();
    System.out.println("This will NOT execute");
}

void C() throws Exception {
    throw new Exception("Declarations all the way up");
    // System.out.println("This would be unreachable code");
}
```

### Unchecked Exceptions

The exception to the `throws` declaration rule is the **unchecked exception**. Unchecked exceptions are classes derived from `RuntimeException` (or `Error`). Because these can occur almost anywhere (like `NullPointerException` or `ArrayIndexOutOfBoundsException`), Java does not require them to be explicitly declared or caught. These usually indicate logic errors or bugs in your code rather than recoverable environmental problems (like a missing file).

## Finally

The `finally` block follows `try` or `catch` blocks and contains code that **always** executes, regardless of whether an exception was thrown or caught. This is useful for cleaning up resources, such as closing database connections. If an exception is thrown and not caught in the current method, the `finally` block executes before the exception continues up the call stack.

```java
try {
    // Code that may throw an exception
} finally {
    // Code that always gets called
}
```

## Example

Consider a program that requires a configuration file. If the file is missing, you may want to report the error in the `main` function rather than deep inside the initialization logic.

Note the use of multiple `catch` blocks, the `finally` block, and the `throws` declarations.

```java
import java.io.File;
import java.io.FileNotFoundException;

public class ExceptionExample {
    public static void main(String[] args) {
        // Exceptions are handled centrally for this scope
        try {
            var example = new ExceptionExample();
            example.loadConfig();
        } catch (FileNotFoundException ex) {
            System.out.printf("Required file not found: %s%n", ex.getMessage());
        } catch (Exception ex) {
            System.out.printf("General error: %s%n", ex.getMessage());
        } finally {
            System.out.println("Program completed");
        }
    }

    private void loadConfig() throws Exception {
        loadConfigFile("user");
        loadConfigFile("system");
    }

    // This method declares that it can throw a checked exception
    private void loadConfigFile(String location) throws FileNotFoundException {
        var file = new File(location);
        if (!file.exists()) {
            // Signal the caller that something went wrong
            throw new FileNotFoundException("Could not find " + location);
        }

        // Otherwise, proceed to load the configuration
    }
}
```

## Custom Exception Types

While Java provides many built-in exception types, you may need to create your own to represent specific domain errors. You can create a custom exception by subclassing `Exception` (for checked exceptions) or `RuntimeException` (for unchecked exceptions).

```java
public class AlreadyTakenException extends Exception {
    public AlreadyTakenException(String message) {
        super(message);
    }
}
```

## Try-With-Resources

Failing to close resources like file handles or network connections can lead to memory leaks or system instability. Traditionally, developers used `try/finally` to ensure resources were closed:

```java
public void TryWithFinally() throws IOException {
    FileInputStream input = null;
    try {
        input = new FileInputStream("test.txt");
        System.out.println(input.read());
    } finally {
        if (input != null) {
            input.close(); // Ensures the stream is closed
        }
    }
}
```

This pattern is verbose. To simplify this, Java introduced the **try-with-resources** syntax. This can be used with any class that implements the `AutoCloseable` or `Closeable` interface. The Java compiler automatically generates the `finally` block to close the resource for you.

```java
public void tryWithResources() throws IOException {
    // The resource is declared in parentheses; it is closed automatically
    try (FileInputStream input = new FileInputStream("test.txt")) {
        System.out.println(input.read());
    }
}
```

## Where to Use `catch` and `throws`

A key architectural decision is where to handle an exception. At each level of the call stack, you must decide: can I fix this here, or should I pass it up?

A good rule of thumb is: **Handle the exception at the level where you can take meaningful action to resume normal execution.**

For example, in a multi-layered application (UI -> Service -> Database):
1. The **Database** layer throws a `UserNotFoundException`.
2. The **Service** layer cannot fulfill the login request without a user, so it lets the exception propagate (or wraps it).
3. The **UI** layer catches the exception and displays a user-friendly message: "Invalid username. Please try again." The UI layer is the appropriate place to "resume" the program by waiting for new user input.

## Exceptions Should be Exceptional

Do not use exceptions for routine control flow. For example, you should not throw an exception to exit a loop or to return a standard value from a method. Using exceptions for non-exceptional cases makes debugging difficult, hurts performance, and makes code harder to maintain. Exceptions should be reserved for unexpected conditions that the current block of code is not prepared to handle.

## Videos

- 🎥 [Exceptions (35:32)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=83d5acf8-12b7-473d-919d-ad6b0124631b&start=0) - [[transcript]](https://github.com/user-attachments/files/17780908/CS_240_Exceptions_Exceptions_in_Java.pdf)
- 🎥 [Checked vs. Unchecked Exceptions (4:35)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=3e7b6f62-13e5-41e6-9a81-ad6b012e8b25&start=0) - [[transcript]](https://github.com/user-attachments/files/17780909/CS_240_Exceptions_Checked_vs_Unchecked_Exceptions.pdf)

## Demonstration code

📁 [ExceptionRethrowingExample](example-code/ExceptionRethrowingExample.java)

📁 [ExceptionThrowingExample](example-code/ExceptionThrowingExample.java)

📁 [FileReadingWithExceptions](example-code/FileReadingWithExceptions.java)

📁 [FileReadingWithoutExceptions](example-code/FileReadingWithoutExceptions.java)

📁 [FinallyExample](example-code/FinallyExample.java)

📁 [ImageEditorException](example-code/ImageEditorException.java)

📁 [TryCatchExample](example-code/TryCatchExample.java)

📁 [TryWithResourcesExample](example-code/TryWithResourcesExample.java)