# ♕ Phase 0: Chess Moves

- [Chess Application Overview](../chess.md)
- 🖥️ [Videos](#videos)
- [TA Tips](../../instruction/chess-tips/chess-tips.md#phase-0---chess-moves): A collection of common problems for this phase

In this part of the Chess Project, you will implement a basic representation of the game of chess. This includes the setting up of the board and defining how pieces move.

> [!NOTE]
>
> Review the [Game of Chess](the-game-of-chess.md) instruction to learn how to set up the board and how each of the pieces move.

## Getting Started

Complete the [Getting Started](getting-started.md) instructions before working on this phase.

## Starter Code

To begin building your chess application, you must first follow the instructions in the [Getting Started Document](getting-started.md).

This provides you with an IntelliJ project that contains the following three modules.

| Phase      | Description                                                                                                                                                                         |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Server** | A program that handles network requests to create and play games. It stores games persistently in a database and sends out notifications to all the players of a game.              |
| **Client** | The command line program that players use to create and play a game of chess. The client communicates with your server over the network in order to play a game with other clients. |
| **Shared** | A code library that contains the rules and representation of a chess game that both the `client` and `server` use to exercise and validate game play.                               |

![Modules](modules.png)

In this phase you will implement the shared code that defines the board, pieces, and the rules of chess. In later phases you will use the code you create to create a fully playable game.

The starter code contains the classes defined by the following diagram. However, instead of a full implementation, the methods all simply throw an exception if invoked. The classes are found in the `shared/src/main/java/chess` directory.

```mermaid
%%{init: { 'theme': 'neutral', 'themeVariables': { 'lineColor': '#000000', 'primaryTextColor': '#000000', 'actorBorder': '#000000', 'participantBorder': '#000000', 'noteBorderColor': '#000000' } }}%%

classDiagram

    class ChessGame {
        getTeamTurn():TeamColor
        validMoves(ChessPosition):Collection~ChessMove~
        makeMove(ChessMove)

        isInCheck(TeamColor):Boolean
        isInCheckmate(TeamColor):Boolean
        isInStalemate(TeamColor):Boolean

        setBoard(ChessBoard)
        getBoard():ChessBoard
    }
    ChessGame --> InvalidMoveException


    class ChessBoard {
        addPiece(ChessPosition, ChessPiece)
        getPiece(ChessPosition):ChessPiece
        resetBoard()
    }
    ChessGame "1" o-- ChessBoard

    class ChessMove {
        getStartPosition():ChessPosition
        getEndPosition():ChessPosition
        getPromotionPiece():PieceType
    }

    class ChessPosition {
        getRow():int
        getColumn():int
    }
    ChessMove o-- "2" ChessPosition

    class ChessPiece {
        getTeamColor():TeamColor
        getPieceType():PieceType
        pieceMoves(ChessBoard, ChessPosition):Collection~ChessMove~
    }

    ChessBoard o-- "*" ChessPiece
```

> [!NOTE]
>
> You are not limited to this representation. However, you must not change the existing class names or method signatures since they are used by the pass off tests. You will likely need to add new classes and methods to complete the work required by this phase.

## Chess Classes

### ChessGame

This class serves as the top-level management of the chess game. It is responsible for executing moves as well as recording the game status.

> [!IMPORTANT]
>
> Although the `ChessGame` class is presented now, it will not be used until **Phase 1. ChessGame**. Related tests will be [added to the working directory](../1-chess-game/getting-started.md) later.

### ChessBoard

This class stores all the uncaptured pieces in a Game. It needs to support adding and removing pieces for testing, as well as a `resetBoard()` method that sets the standard Chess starting configuration.

### ChessPiece

This class represents a single chess piece, with its corresponding type and team color. It contains the `PieceType` enumeration that defines the different types of pieces.

```java
public enum PieceType {
    KING,
    QUEEN,
    BISHOP,
    KNIGHT,
    ROOK,
    PAWN
}
```

`ChessPiece` implements rules that define how a piece moves independent of other chess rules such as whose turn it is, check, stalemate, or checkmate.

#### Key Methods

- **`pieceMoves`**: Given a board configuration, this method returns all the moves a specific piece can legally make independant of whose turn it is or if the King is being attacked. It considers the edges of the board and the location of both enemy and friendly pieces. In the next phase, you will implement `ChessGame.validMoves`, which considers not only how a piece moves, but all of the rules of chess.

### ChessMove

This class represents a possible move a chess piece could make. It contains the starting and ending positions. It also contains a field for the type of piece a pawn is being promoted to. If the move would not result in a pawn being promoted, the promotion type field should be null.

### ChessPosition

This represents a location on the chessboard. This should be represented as a row number from 1-8, and a column number from 1-8. For example, (1,1) corresponds to the bottom left corner (which in chess notation is denoted `a1`). (8,8) corresponds to the top right corner (`h8` in chess notation).

> [!NOTE]
>
> If you implement your Chess Board with an array or a similar data structure accessed with an index, remember that those indexes start with 0, where Chess Positions should go from 1 to 8. Make sure you account for the difference between these two numbers and how you translate between them, such as using a Chess Position to get a Piece from the Board.

## Object Overrides

In order for the tests to pass, you are required to override the `equals()` and `hashCode()` methods in your class implementations as necessary. This includes the `ChessPosition`, `ChessPiece`, `ChessMove`, and `ChessBoard` classes in particular. To do this automatically in IntelliJ, right click on the class code and select `Code > Generate... > equals() and hashCode()`. It is a good idea to generate these methods fairly early. However, IntelliJ will not be able to generate them properly until you have added the required fields to the class.

In most cases, the default methods provided by IntelliJ will suffice. However, there are cases when the generated code will not completely work. You should fully understand all code in your project, even code that was generated for you. Take the time to carefully review and debug what the generated code does. Add a breakpoint, inspect the variables, and step through each line.

The tests and autograder rely on these methods in order to compare your objects. If you can't pass any tests even though the output seems the same check to make sure that these methods are created and working properly.

To understand why we need to override the `equals()` and `hashCode()` methods, see the instruction page on [Java Object Class](../../instruction/java-object-class/java-object-class.md).

> [!TIP]
>
> Debugging is often much easier if you also override the `toString()` method and return a concise representation of the object. This is not required, but highly recommended. This can be generated in the same way that the `equals()` and `hashcode()` were.

## Testing

The test cases found in `src/test/java/passoff/chess` contain a collection of tests that assert the correct movement of individual pieces.

To run the tests, you can click the play icon next to an individual test, or you can right click on a package or class and select `Debug` or `Debug Tests in …`

![Debug Test](debug-test.png)

## Recommended Development Process

For this project, you are free to implement the classes described above in whatever order you choose. However, it is suggested that you follow the principles of test driven development. Each test is designed for a specific aspect of the overall project. You should look at what each test needs to function, and build piece by piece to the functionality of the project.

A good process for this is to:

- Identify a test that you feel is the simplest or next in the process.
- Add the code for the functionality
- Run the test, debug, and continue implementing until the test passes.
- Ensure you did not break any tests you previously passed.
- Commit your changes to GitHub. Smaller changes will be much easier to follow and verify for this project.
- Repeat the above process for each of the tests until they all pass.

If you are confused at what a test is doing reread the specs to understand what functionality it is looking for, and you can ask for clarification on Discord or to a TA.

## Code Quality

You want to write quality code that promotes consistency and readability for all team members. Code quality is discussed in a future [instruction topic](../../instruction/quality-code/quality-code.md), and you will be graded on quality starting with phase 3. However, you can use the auto grader at any time to check your chess repositories quality. The rubric used to evaluate code quality is found here: [Rubric](../code-quality-rubric.md).

![Code Quality](../codeQuality.png)


## Architectural Patterns in Phase 0

Building a chess engine—even just the movement logic—is a classic exercise in applying fundamental software engineering principles. By moving away from a monolithic "God Object" that controls every piece, we can implement a system that is modular, extensible, and easy to test. In this phase, we primarily focus on **Encapsulation**, **Polymorphism**, and the **Single Responsibility Principle (SRP)**.

### The Power of Polymorphism

Instead of using a massive `switch` statement or a long chain of `if-else` blocks to determine how a piece moves, we use polymorphism. By defining a common `ChessPiece` interface, the `ChessBoard` can treat every piece the same way. When the board needs to know the valid moves for a piece at a specific location, it simply calls `piece.pieceMoves(board, position)`. It doesn't need to know if the piece is a Pawn, a Queen, or a Knight; it only cares that the piece knows its own rules.

```mermaid
%%{init: { 'theme': 'neutral', 'themeVariables': { 'mainBkg': '#ffffff', 'lineColor': '#000000', 'primaryTextColor': '#000000', 'actorBorder': '#000000', 'participantBorder': '#000000', 'noteBorderColor': '#000000' } }}%%
classDiagram
    direction TB
    class ChessBoard {
        +getPiece(ChessPosition) ChessPiece
    }
    class ChessPiece {
        <<interface>>
        +pieceMoves(ChessBoard, ChessPosition) Collection~ChessMove~
    }
    class Knight {
        +pieceMoves(ChessBoard, ChessPosition) Collection~ChessMove~
    }
    class Bishop {
        +pieceMoves(ChessBoard, ChessPosition) Collection~ChessMove~
    }
    
    ChessBoard o-- ChessPiece : contains
    ChessPiece <|.. Knight
    ChessPiece <|.. Bishop
```

### Applying the Single Responsibility Principle (SRP)

In a well-designed chess application, each class has one clear reason to change:
*   **ChessPosition:** Responsible only for representing a coordinate on the board (row/col).
*   **ChessMove:** Responsible only for representing the transition from a start position to an end position (and potential promotion).
*   **ChessPiece:** Responsible for the movement logic specific to that piece type.
*   **ChessBoard:** Responsible for maintaining the state of the 8x8 grid and providing access to pieces.

### Code Example: Decoupled Movement Logic

By delegating movement logic to the piece itself, we adhere to the **Open/Closed Principle**. If we wanted to add a new piece type (like a "Camel" or "Archbishop" from Chess variants), we could do so by creating a new class without modifying the existing board or piece code.

```java
public class Camel implements ChessPiece {
    @Override
    public Collection<ChessMove> pieceMoves(ChessBoard board, ChessPosition myPosition) {
        List<ChessMove> moves = new ArrayList<>();
        int[][] offsets = { {3, 1}, {3, -1}, {-3, 1}, {-3, -1}, {1, 3}, {1, -3}, {-1, 3}, {-1, -3} };

        for (int[] offset : offsets) {
            ChessPosition target = myPosition.addOffset(offset[0], offset[1]);
            if (board.isValidMove(target, this.getTeamColor())) {
                moves.add(new ChessMove(myPosition, target, null));
            }
        }
        return moves;
    }
}
```

### Data Transfer Objects (DTOs)

The `ChessMove` and `ChessPosition` classes act as **Data Transfer Objects**. They are often immutable, meaning once created, their state cannot change. This is a critical pattern in software engineering that prevents "side effects"—where changing a value in one part of the program accidentally breaks another part. When a piece calculates its moves, it returns a collection of these DTOs to the caller, ensuring a clean boundary between the "rules engine" and the "game state."

```masteryls
{"id":"2aba68a0-07ea-4f09-8b23-a4c5ac90ef86","title":"Polymorphism in Chess","type":"multiple-choice"}
Why is it better to use a `ChessPiece` interface with a `pieceMoves()` method rather than a single `calculateMoves(Piece p)` method inside the `ChessBoard` class?

- [ ] It makes the code run faster by reducing memory overhead
- [x] It follows the Open/Closed Principle, allowing new piece types to be added without modifying the board class
- [ ] It ensures that the board has direct control over the internal state of every piece
- [ ] It prevents the use of Data Transfer Objects (DTOs), which simplifies the architecture
```




## ☑ Deliverable

### Pass Off, Submission, and Grading

All of the tests in your project must succeed in order to complete this phase.

To pass off this assignment use the course [auto-grading](https://cs240.click/) tool. If your code passes then your grade will automatically be entered in Canvas.

### Grading Rubric

> [!IMPORTANT]
>
> You are required to commit to GitHub with every minor milestone. For example, after you successfully pass a test. This should result in a commit history that clearly details your work on this phase. If your Git history does not demonstrate your efforts then your submission may be rejected.

| Category       | Criteria                                                                                          |       Points |
| :------------- | :------------------------------------------------------------------------------------------------ | -----------: |
| GitHub History | At least 8 GitHub commits evenly spread over the assignment period that demonstrate proof of work | Prerequisite |
| Functionality  | All pass off test cases succeed                                                                   |          125 |
|                | **Total**                                                                                         |      **125** |

## Videos

> [!WARNING]
>
> Do not use videos during the programming exam.

- 🎥 [Phase 0 Overview (21.17)](https://youtu.be/mTtK8iRXsZo) - transcript
- 🎥 [Phase 0 Design Tips (5:31)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=88653eac-78a8-4f59-a12a-b170014f61f1) - [[transcript]](https://github.com/user-attachments/files/17706812/CS_240_Phase_0_Design_Tips_Transcript.pdf)
- 🎥 [Phase 0 Creating Chess GitHub Repository (4:34)](https://www.loom.com/share/2b2dd64e7b524b3f9b396318cf140159?sid=a6c1b75f-a73f-455e-976c-ba19052117a6) - [[transcript]](creating-chess-github-repo-transcript.txt)
