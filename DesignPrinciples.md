### Design Principles

1. Guide Descision making for clean, maintainable and extensible code.
2. Ans Q's like should this be in separate class ?, Should I use inheritence?, Is abstraction worth it ?

### General Design Principles

#### KISS (Keep it Simple, Stupid)

Start Simple. 
When you're designing a class or choosing between patterns, pick the straightforward approach.
If you can solve the problem with a simple conditional instead of a strategy pattern, do that.
If a single class handles the job without getting messy, don't split it up.

#### DRY (Don't Repeat Yourself)

When you find yourself writing the same logic in multiple places, pull it into one place.

Balancing KISS and DRY. Eg "I expect this validation logic to appear in multiple places, but I'm going to start by keeping it in the User class to avoid adding unnecessary complexity early. If we see it duplicated three or four times, we can pull it into a shared validator."

#### YAGNI (You aren't Gonna Need it)

Design with extension in mind, but only implement what's needed now.
Eg : when you're designing a parking lot system, don't add support for valet parking and electric vehicle charging stations unless the requirements specifically mention them.

#### Separation Of Concern

Different parts of your code should handle different responsibilities, and they shouldn't know about each other's internals. 
Your UI layer shouldn't contain business logic. 
Your business logic shouldn't know how data is stored. 
Your data access layer shouldn't format strings for display.

```
import java.util.Scanner;

class TicTacToe {
  char[][] board = new char[3][3];

  void play() {
    Scanner scanner = new Scanner(System.in);
    while (true) {
      // Display mixed with game logic
      for (int i = 0; i < 3; i++) {
        System.out.println(board[i]);
      }

      // Input handling mixed in
      int row = scanner.nextInt();
      int col = scanner.nextInt();
      board[row][col] = 'X';

      // Win checking mixed in
      if (board[0][0] == board[1][1] && board[1][1] == board[2][2]) {
        System.out.println("Winner!");
        break;
      }
    }
    scanner.close();
  }
}
```

```
class TicTacToe {
  Board board;
  Display display;
  InputHandler input;

  void play() {
    while (!board.hasWinner()) {
      display.render(board);
      Move move = input.getNextMove();
      board.makeMove(move);
    }
    display.showWinner(board.getWinner());
  }
}
```

#### Law of Demeter

Also called the principle of least knowledge.
A method should only talk to its immediate friends, not reach through objects to access distant parts of the system. 
Instead of returning complex objects that callers need to dig through, return the specific data they need or provide higher-level methods that do the work.

```
order.getCustomer().getAddress().getZipCode()

vs

order.getCustomerZipCode()
```

Method chaining itself is not the problem. 
Fluent interfaces like builder.setName("John").setAge(30).build() are fine because they return the same object type.



### Object Oriented Design Principles



