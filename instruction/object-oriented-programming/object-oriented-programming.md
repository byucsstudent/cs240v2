# Object Oriented Programming


#### 🔑 Key points

- Represent real world objects as code
- Use encapsulation to restrict access to private data
- Use abstraction to only expose is required
- Use inheritance to share commonality
- Use polymorphism to morph an object to the context it is used

---

Object-Oriented Programming (OOP) is a fundamental paradigm in modern software engineering that organizes code around "objects" rather than "actions." While procedural programming focuses on writing functions or blocks of code that perform computations on data, OOP shifts the focus to the data itself. By grouping related data and behaviors into distinct units, developers can build complex systems that better represent the real world and are more modular, flexible, and easier to maintain.

In the context of software architecture, OOP serves as the blueprint for creating scalable applications. It allows developers to map real-world entities—like a user, a bank transaction, or a chess piece—into digital structures. This approach not only makes the code more intuitive to read but also facilitates collaboration across large teams, as different developers can work on separate objects without inadvertently breaking the entire system.

## The Four Pillars of OOP

To master Object-Oriented Programming, one must understand the four core principles that govern how objects interact and how code is structured: Encapsulation, Abstraction, Inheritance, and Polymorphism.

### Encapsulation

Encapsulation is often referred to as the "black box" principle. It involves bundling the data (fields) and the methods (functions) that operate on that data into a single unit called a class. More importantly, encapsulation allows us to restrict direct access to some of an object's components, which is a crucial aspect of data security and integrity.

By using access modifiers like `private`, we prevent external code from reaching into an object and changing its internal state in unexpected ways. Instead, we provide controlled access through public methods known as "getters" and "setters."

**Practical Example:**
Imagine a `BankAccount` class. You wouldn't want any part of the program to be able to set the `balance` to any arbitrary number. By encapsulating the balance, you ensure that it can only be modified through a `deposit()` or `withdraw()` method, which can include logic to prevent negative balances.

```java
public class BankAccount {
    private double balance; // Encapsulated data

    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }

    public double getBalance() {
        return balance;
    }
}
```

### Abstraction

Abstraction is the process of hiding the complex internal details of an application and only showing the necessary features to the user. It reduces complexity by allowing the programmer to focus on *what* an object does rather than *how* it does it.

Think of a microwave. To use it, you only need to know how to press the buttons on the interface. You do not need to understand how the magnetron generates microwaves or how the internal cooling system functions. In programming, abstraction is achieved through the use of abstract classes and interfaces.

**Thoughtful Engagement:** Consider the software you use daily. How many "interfaces" do you interact with where you have no idea how the underlying code is written? This is the power of abstraction: it allows us to build upon complex tools without being overwhelmed by their internal mechanics.

### Inheritance

Inheritance allows a new class to adopt the properties and behaviors of an existing class. This creates a hierarchical relationship between a "superclass" (parent) and a "subclass" (child). Inheritance is the primary mechanism for code reuse in OOP.

When a subclass inherits from a superclass, it gains all its non-private fields and methods. The subclass can then add its own unique features or "override" existing ones to change their behavior.

**The Hierarchy of Objects:**

```mermaid
%%{init: { 'theme': 'neutral', 'themeVariables': { 'lineColor': '#000000', 'primaryTextColor': '#000000', 'actorBorder': '#000000', 'participantBorder': '#000000', 'noteBorderColor': '#000000' } }}%%

classDiagram
    class Vehicle {
        +String brand
        +startEngine()
    }
    class Car {
        +int numberOfDoors
        +openTrunk()
    }
    class Motorcycle {
        +boolean hasSidecar
        +popWheelie()
    }
    Vehicle <|-- Car
    Vehicle <|-- Motorcycle
```

In this diagram, both `Car` and `Motorcycle` inherit from `Vehicle`. They share the `startEngine()` method but maintain their own specific attributes.

### Polymorphism

Polymorphism, meaning "many shapes," allows objects of different types to be treated as objects of a common superclass. It is most commonly seen when a single method call behaves differently depending on the type of object it is called upon.

There are two main types:
1.  **Static Polymorphism (Overloading):** Multiple methods in the same class have the same name but different parameters.
2.  **Dynamic Polymorphism (Overriding):** A subclass provides a specific implementation of a method that is already defined in its superclass.

**Practical Example:**
If we have a method `makeSound()` in a superclass `Animal`, and subclasses `Dog` and `Cat` override that method, calling `animal.makeSound()` on a list of animals will result in "Woof" for the dog and "Meow" for the cat, even though the code calling the method doesn't know the specific breed at compile time.

### Common Challenges and Solutions

Transitioning to an object-oriented mindset can present several hurdles:

*   **The "God Object" Problem:** Beginners often create a single class that tries to do everything. 
    
    *Solution:* Follow the Single Responsibility Principle. Each class should have one, and only one, reason to change.
*   **Fragile Base Classes:** If an inheritance hierarchy is too deep, changing the parent class can inadvertently break dozens of child classes.
    
    *Solution:* Favor "composition over inheritance" when possible. Instead of saying a class *is* something, ask if it *has* something.
*   **Over-Engineering:** It is easy to get carried away with abstraction, creating interfaces for things that will only ever have one implementation.
    
    *Solution:* Keep it simple. Don't add abstraction until you actually need to support multiple variations of a behavior.

### Summary

Object-Oriented Programming is a powerful way to structure software by mimicking the way we perceive the real world. By utilizing **Encapsulation**, we protect our data; through **Abstraction**, we manage complexity; with **Inheritance**, we promote code reuse; and via **Polymorphism**, we create flexible and interchangeable code components. Mastering these four pillars is the first step toward becoming a proficient software architect capable of building robust, maintainable systems.