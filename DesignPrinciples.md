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

SOLID - apply specifically when you're designing classes and their relationships.

#### Single Responsbility Principle 

A class should have one reason to change. 

If a class mixes multiple concerns, split them. This is the foundation of good class design.

```
class Report {
  String generateContent() { return "content"; }
  void printToPDF() { /* PDF formatting */ }
  void saveToFile() { /* file I/O */ }
}

```

```
class Report {
  String generateContent() { return "content"; }
}

class PDFPrinter {
  void print(Report report) { /* PDF formatting */ }
}

class FileStorage {
  void save(String content) { /* file I/O */ }
}

```

Now when the PDF formatting library changes, you only touch PDFPrinter. When you switch from files to a database, you only modify FileStorage. When the report content logic changes, you only update Report. Each change is isolated.

#### Open/Closed Principle

Classes should be open for extension but closed for modification.
You should be able to add new behavior without changing existing code.
This usually means using interfaces or abstract classes so you can add new implementations without touching the original code.

```
class PaymentProcessor {
  void process(String type, double amount) {
    if (type.equals("credit")) {
      // credit card logic
    } else if (type.equals("paypal")) {
      // paypal logic
    }
    // Adding crypto means modifying this method
  }
}

```

```
interface PaymentMethod {
  void process(double amount);
}

class CreditCardPayment implements PaymentMethod {
  public void process(double amount) { /* credit card logic */ }
}

class PayPalPayment implements PaymentMethod {
  public void process(double amount) { /* paypal logic */ }
}

class CryptoPayment implements PaymentMethod {
  public void process(double amount) { /* crypto logic */ }
}

class PaymentProcessor {
  void process(PaymentMethod method, double amount) {
    method.process(amount);
  }
}
```

#### Liskov Substitution Principle

Subclasses must work wherever the base class works.

The subclass can add new behavior, but it can't remove or break behavior that the parent promised. When a subclass throws an exception for a method the parent class provides, that's a red flag you're violating LSP. If a subclass forces callers to add special-case logic (e.g., if (bird instanceof Penguin)), you violated LSP.

```
class Bird {
  void fly() { /* flying logic */ }
}

class Penguin extends Bird {
  void fly() {
    throw new UnsupportedOperationException("Penguins can't fly");
  }
}

```

```
interface Bird {
  void eat();
}

interface FlyingBird extends Bird {
  void fly();
}

class Sparrow implements FlyingBird {
  public void eat() { /* eating */ }
  public void fly() { /* flying */ }
}

class Penguin implements Bird {
  public void eat() { /* eating */ }
}
```

#### Interface Segregation Principle

Prefer small, focused interfaces over large, general-purpose ones.
If a class only needs two methods from an interface with ten methods, that interface is too big.

```
interface Worker {
  void work();
  void eat();
  void sleep();
}

class Robot implements Worker {
  public void work() { /* working */ }
  public void eat() { /* robots don't eat */ }
  public void sleep() { /* robots don't sleep */ }
}

```

```
interface Workable {
  void work();
}

interface Feedable {
  void eat();
}

interface Restable {
  void sleep();
}

class Human implements Workable, Feedable, Restable {
  public void work() { /* working */ }
  public void eat() { /* eating */ }
  public void sleep() { /* sleeping */ }
}

class Robot implements Workable {
  public void work() { /* working */ }
}
```

#### Dependency Inversion Principle

code should depend on abstractions, not concrete implementations

DIP is a design principle, while dependency injection (passing dependencies through the constructor) is a technique for achieving it.

makes mocking easier

With DIP, define an interface based on what your business logic needs, then have implementations conform to that interface. The implementation adapts to the business logic.

```
class EmailSender {
  void send(String message) { /* send email */ }
}

class NotificationService {
  EmailSender emailSender = new EmailSender();

  void notify(String message) {
    emailSender.send(message);
  }
}
```

```
interface MessageSender {
  void send(String message);
}

class EmailSender implements MessageSender {
  public void send(String message) { /* send email */ }
}

class NotificationService {
  private final MessageSender sender;

  NotificationService(MessageSender sender) {
    this.sender = sender;
  }

  void notify(String message) {
    sender.send(message);
  }
}
```

Instead of NotificationService creating an EmailSender directly, it should accept a MessageSender interface through its constructor.
