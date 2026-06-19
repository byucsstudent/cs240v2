# Object-Oriented Design

🖥️ [Slides](https://docs.google.com/presentation/d/17S-Y7Og08S9kRWHZfnH8k2wTBht39aCd/edit?usp=sharing&ouid=114081115660452804792&rtpof=true&sd=true)

🖥️ [Lecture Videos](#videos)

### 🔑 Key points

- First, understand the application domain.
- Represent the domain using classes.
- Classes are nouns representing real-world objects.
- Classes have methods (verbs) and properties (nouns), mirroring real-world objects.
- Classes have relationships: Is-a, Has-a, and Uses-a.
- Encapsulate data to hide implementation details.
- Adhere to the Single Responsibility Principle.
- Use Class and Sequence diagrams to model the system.

---

`Object-oriented` design focuses on describing objects in the application domain as literal programming constructs. For example, if your application involves people eating fruit, you model the application by creating `Person` and `Fruit` objects. These objects have properties such as name, ripeness, and color, as well as operations (methods) such as `eat`, `plant`, `grow`, or `purchase`. You then write your code to reflect real-world interactions between these core objects. In this example, a `Person` might `purchase` a `Fruit` object and then `eat` it.


```mermaid
%%{init: { 'theme': 'neutral', 'look': 'handDrawn',
  'sequence': { 
    'mirrorActors': false,
    'showSequenceNumbers': true,
    'actorMargin': 10,
    'boxMargin': 5
  }, 'themeVariables': { 'mainBkg': '#ffffff', 'lineColor': '#000000', 'primaryTextColor': '#000000' } }}%%

sequenceDiagram
    participant Person
    participant Fruit

    Person->>Fruit: plant
    Fruit-->>Fruit: grow
    Person->>Fruit: isRipe
    Person->>Fruit: eat
```

Object-oriented design owes much of its popularity to its natural representation of the real world. By carefully modeling the actual application domain, the resulting code avoids the complications that often arise in other [programming paradigms](https://en.wikipedia.org/wiki/Programming_paradigm) that focus more strictly on functional logic or declarative constructs.

In object-oriented programming (OOP), everything revolves around the `Class` construct, which serves as a template for actual objects. Classes represent nouns, or "things," such as a cat, car, word, database row, or even abstract concepts like a thought or behavior. A class's operations, or methods, are verbs, such as `build`, `run`, `speak`, `compute`, or `destroy`. When you instantiate a class, you convert the template into an actual object. For example, we can create an object named `James` from a class named `Person`. While the `Person` class has a `birthPlace` field, the specific object `James` has a birth place value of "Calgary, Alberta."

| Real World                       | Class Representation               | Object Representation |
| -------------------------------- | ---------------------------------- | --------------------- |
| ![James Gosling](smallJames.jpg) | ![class diagram](classDiagram.jpg) | {name: James, birthPlace: Calgary }       |

> _Source: Wikipedia_

## Object Relationships

To fully model the real world, you must describe the relationships between objects. In Object-Oriented Design (_OOD_), these are categorized into three primary types: `is-a`, `has-a`, and `uses-a`.

| Relationship | Formal Name | Description | Example |
| ------------ | ----------- | ----------- | ------- |
| **Is-a** | Generalization | Inheritance. A specialized class (subclass) extends a more general class (superclass). | A `Programmer` **is a** `Person`. |
| **Has-a** | Association | Ownership or containment. One object contains another as a member field. | A `Programmer` **has a** `Computer`. |
| **Uses-a** | Dependency | Transient interaction. An object depends on another temporarily, often as a method parameter. | A `Person` **uses a** `Taxi`. |

### Refining "Has-a" Relationships
In technical OOD, the `has-a` relationship is often further distinguished by the lifecycle of the objects:
- **Composition (`*--`)**: Strong ownership. The "part" cannot exist without the "whole" (e.g., a `Room` in a `House`).
- **Aggregation (`o--`)**: Weak ownership. The "part" can exist independently of the "whole" (e.g., a `Professor` in a `Department`).

### Example

The following diagram illustrates these relationships using standard UML notation.

```mermaid
%%{init: { 'theme': 'neutral', 'themeVariables': { 'mainBkg': '#ffffff', 'lineColor': '#000000', 'primaryTextColor': '#000000' } }}%%

classDiagram
    class Car {
        +travel(Person p, Route r)
    }
    class Person {
        +String name
        +String birthPlace
    }
    class Route {
        -String start
        -String destination
    }
    class Programmer {
        -GitHubRepo repo
        -Computer pc
        +writeCode()
    }
    class Computer {
        +String type
        +save(code)
    }
    class GitHubRepo {
        +push()
        +pull()
    }

    Car ..> Person : uses-a (dependency)
    Car ..> Route : uses-a (dependency)
    Programmer --|> Person : is-a (inheritance)
    Programmer o-- Computer : has-a (aggregation)
    Programmer *-- GitHubRepo : has-a (composition)
```

The key is to understand your domain and distill the important fields, operations, and interactions down to the minimal representation that meets the application's needs. Your model does not need to be a perfect 1:1 replica of reality; you can often make a model easier to understand by omitting unnecessary details. However, if the literal domain representation conflicts with how users interact with the system, prioritize the users' mental model.

For example, in the diagram above, a `Programmer` is modeled as having a single `Computer`. In reality, a programmer might use multiple computers or only use a computer transiently (`uses-a`). If our application doesn't require that complexity, we can simplify the model by assuming every programmer has one computer. This allows us to encapsulate (hide) the `Computer` and `GitHubRepo` details when the `writeCode` method is called.

### Composition over Inheritance
While `is-a` (inheritance) is powerful, a common design principle is to **favor composition over inheritance**. Inheritance creates a rigid, tight coupling between classes. Composition (`has-a`) allows for greater flexibility because you can change the internal components of an object at runtime or swap implementations without breaking the class hierarchy.

The goal is to avoid missing key objects, merging distinct objects into one, or introducing unnecessary complexity that obscures the user's mental model. Someone who understands the domain should be able to review your model and find the choice of objects and their relationships intuitive.
## Encapsulation

Good object-oriented design is easy to enhance over time. Encapsulation—hiding details that do not need to be shared—makes it easier to evolve the model as requirements change. For example, by encapsulating the `Computer` object within the `Programmer` object, the rest of the system only needs to know how to call `writeCode`, without needing to know how the computer functions.

We can expose the `Computer` later if necessary, but keeping it hidden allows us to change the internal relationship between the `Programmer` and the `Computer` without breaking other parts of the code.

Encapsulation is often preferable to inheritance because it is more extensible. Inheritance (using `extends`) explicitly and publicly exposes both the methods and the implementation of the parent class. Encapsulation keeps these details private and decoupled.

## Simplicity

Simplicity is a vital characteristic of effective OOP. One form of simplicity is restricting the system to the smallest necessary number of objects. This applies to the number of interfaces, the depth of inheritance, and the operations an object exposes.

However, you can simplify too far. Avoid creating thousands of classes that each contain only one line of code, or a single "God Object" that tries to represent everything. Aim for a straightforward model that stays as close to the real-world domain as possible.

### Problem: Too many classes (Over-engineered)

```mermaid
%%{init: { 'theme': 'neutral', 'themeVariables': { 'mainBkg': '#ffffff', 'lineColor': '#000000', 'primaryTextColor': '#000000' } }}%%

classDiagram
    Creature --|> Object
    Animal --|> Creature
    Mammal --|> Animal
    Person --|> Mammal

    Animal o-- Soul
    Animal *-- Body

    Animal o-- Dwelling

    Body o-- Head
    Body *-- Torso
    Body o-- Appendage

    Leg --|> Appendage
    Arm --|> Appendage
```

This design suffers from **speculative generality**. By creating deep inheritance hierarchies and over-decomposing objects (like splitting a `Body` into a `Head`, `Torso`, and `Appendage`), the system becomes brittle and difficult to navigate. Each additional layer adds cognitive load and makes the code harder to maintain, as changes to a base class like `Creature` ripple through every subclass. Unless the application specifically requires these fine-grained distinctions, this level of abstraction creates unnecessary complexity that obscures the actual domain logic.

### Problem: Not enough classes (Anemic/Generic)

```mermaid
%%{init: { 'theme': 'neutral', 'themeVariables': { 'mainBkg': '#ffffff', 'lineColor': '#000000', 'primaryTextColor': '#000000' } }}%%

classDiagram
    class Object {
        value
    }

    Object o-- Object : has-a
```

The "Not enough classes" design is problematic because it lacks semantic meaning and specific behavior. By reducing every concept to a generic `Object` with a recursive relationship, you lose the benefits of type safety and domain modeling. Instead of a `Person` who `eats` a `Fruit`, you have an anonymous `Object` interacting with another anonymous `Object`. This forces logic that should be encapsulated within classes out into the rest of the application, making the code significantly harder to maintain and debug.

## Videos

- 🎥 [Object-Oriented Design Overview](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=77c184e5-8afd-4c56-84c8-ad64013f7a4b&start=0) - [[transcript]](https://github.com/user-attachments/files/17805111/CS_240_Object_Oriented_Design_Overview.pdf)