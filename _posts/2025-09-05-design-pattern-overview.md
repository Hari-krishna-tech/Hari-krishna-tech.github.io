---
layout: post
title: Complete Guide to Design Patterns in Java
date: 2025-09-05
tags: [design-patterns, java, low-level-design,creational-patterns, structural-patterns, behavioral-patterns]
---

<div class="message">
  Design patterns are proven solutions to common software design problems, providing a template for writing maintainable, flexible, and reusable code.
</div>

# **Complete Guide to Design Patterns in Java**

Design patterns are proven solutions to common software design problems, providing a template for writing maintainable, flexible, and reusable code. The Gang of Four (GoF) identified 23 fundamental design patterns, organized into three categories: **Creational**, **Structural**, and **Behavioral** patterns.

## **Creational Patterns**

Creational patterns focus on object creation mechanisms, providing ways to create objects while hiding the creation logic and making the system independent of how objects are created.

### **1. Singleton Pattern**

**Purpose**: Ensures a class has only one instance and provides global access to it.

**Key Components**:

- Private constructor
- Static instance variable
- Static method to get the instance

**Java Implementation**:

```java
public class Singleton {
    private static volatile Singleton instance;
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}

// Thread-safe enum implementation (recommended)
public enum SingletonEnum {
    INSTANCE;
    
    public void doSomething() {
        System.out.println("Doing something...");
    }
}
```

**Use Cases**: Database connections, loggers, configuration settings, thread pools.

### **2. Factory Method Pattern**

**Purpose**: Creates objects without specifying their exact classes, delegating object creation to subclasses.

**Key Components**:

- Product interface
- Concrete products
- Creator abstract class
- Concrete creators

**Java Implementation**:

```java
// Product interface
interface Vehicle {
    void start();
}

// Concrete products
class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car started");
    }
}

class Bike implements Vehicle {
    @Override
    public void start() {
        System.out.println("Bike started");
    }
}

// Creator abstract class
abstract class VehicleFactory {
    public abstract Vehicle createVehicle();
    
    public void startVehicle() {
        Vehicle vehicle = createVehicle();
        vehicle.start();
    }
}

// Concrete creators
class CarFactory extends VehicleFactory {
    @Override
    public Vehicle createVehicle() {
        return new Car();
    }
}

class BikeFactory extends VehicleFactory {
    @Override
    public Vehicle createVehicle() {
        return new Bike();
    }
}
```


### **3. Abstract Factory Pattern**

**Purpose**: Creates families of related objects without specifying their concrete classes.

**Java Implementation**:

```java
// Abstract products
interface Car {
    void assemble();
}

interface CarSpecification {
    void display();
}

// Concrete products for North America
class Sedan implements Car {
    @Override
    public void assemble() {
        System.out.println("Assembling Sedan car");
    }
}

class NorthAmericaSpecification implements CarSpecification {
    @Override
    public void display() {
        System.out.println("North America Car Specification");
    }
}

// Abstract factory
interface CarFactory {
    Car createCar();
    CarSpecification createSpecification();
}

// Concrete factory
class NorthAmericaCarFactory implements CarFactory {
    @Override
    public Car createCar() {
        return new Sedan();
    }
    
    @Override
    public CarSpecification createSpecification() {
        return new NorthAmericaSpecification();
    }
}
```


### **4. Builder Pattern**

**Purpose**: Constructs complex objects step by step, allowing different representations using the same construction process.


**Java Implementation**:

```java
class Computer {
    private String cpu;
    private String ram;
    private String storage;
    
    private Computer(Builder builder) {
        this.cpu = builder.cpu;
        this.ram = builder.ram;
        this.storage = builder.storage;
    }
    
    public static class Builder {
        private String cpu;
        private String ram;
        private String storage;
        
        public Builder setCPU(String cpu) {
            this.cpu = cpu;
            return this;
        }
        
        public Builder setRAM(String ram) {
            this.ram = ram;
            return this;
        }
        
        public Builder setStorage(String storage) {
            this.storage = storage;
            return this;
        }
        
        public Computer build() {
            return new Computer(this);
        }
    }
    
    @Override
    public String toString() {
        return "Computer{CPU='" + cpu + "', RAM='" + ram + "', Storage='" + storage + "'}";
    }
}

// Usage
Computer computer = new Computer.Builder()
    .setCPU("Intel i7")
    .setRAM("16GB")
    .setStorage("512GB SSD")
    .build();
```


### **5. Prototype Pattern**

**Purpose**: Creates objects by cloning existing instances rather than creating new ones.

**Java Implementation**:

```java
interface Shape extends Cloneable {
    Shape clone();
    void draw();
}

class Circle implements Shape {
    private String color;
    
    public Circle(String color) {
        this.color = color;
    }
    
    @Override
    public Shape clone() {
        return new Circle(this.color);
    }
    
    @Override
    public void draw() {
        System.out.println("Drawing a " + color + " circle");
    }
}

class ShapeClient {
    private Shape shapePrototype;
    
    public ShapeClient(Shape shapePrototype) {
        this.shapePrototype = shapePrototype;
    }
    
    public Shape createShape() {
        return shapePrototype.clone();
    }
}

// Usage
Shape circlePrototype = new Circle("red");
ShapeClient client = new ShapeClient(circlePrototype);
Shape redCircle = client.createShape();
redCircle.draw();
```


## **Structural Patterns**

Structural patterns deal with object composition, creating relationships between objects to form larger structures.

### **6. Adapter Pattern**

**Purpose**: Allows incompatible interfaces to work together by providing a wrapper that translates one interface to another.

**Java Implementation**:

```java
// Target interface
interface PaymentProcessor {
    boolean processPayment(double amount);
    boolean isPaymentSuccessful();
    String getTransactionId();
}

// Adaptee (incompatible interface)
class LegacyGateway {
    public long executeTransaction(double amount, String currency) {
        System.out.println("Processing $" + amount + " via Legacy Gateway");
        return System.currentTimeMillis(); // Return transaction reference
    }
    
    public boolean checkStatus(long transactionRef) {
        return true; // Assume success
    }
    
    public long getReferenceNumber() {
        return System.currentTimeMillis();
    }
}

// Adapter
class LegacyGatewayAdapter implements PaymentProcessor {
    private LegacyGateway legacyGateway;
    private long lastTransactionRef;
    
    public LegacyGatewayAdapter(LegacyGateway legacyGateway) {
        this.legacyGateway = legacyGateway;
    }
    
    @Override
    public boolean processPayment(double amount) {
        lastTransactionRef = legacyGateway.executeTransaction(amount, "USD");
        return lastTransactionRef > 0;
    }
    
    @Override
    public boolean isPaymentSuccessful() {
        return legacyGateway.checkStatus(lastTransactionRef);
    }
    
    @Override
    public String getTransactionId() {
        return String.valueOf(legacyGateway.getReferenceNumber());
    }
}
```


### **7. Bridge Pattern**

**Purpose**: Separates abstraction from implementation, allowing both to vary independently.

**Java Implementation**:

```java
// Implementation interface
interface Workshop {
    void work();
}

// Concrete implementations
class Produce implements Workshop {
    @Override
    public void work() {
        System.out.print("Produced");
    }
}

class Assemble implements Workshop {
    @Override
    public void work() {
        System.out.println(" And Assembled");
    }
}

// Abstraction
abstract class Vehicle {
    protected Workshop workShop1;
    protected Workshop workShop2;
    
    protected Vehicle(Workshop workShop1, Workshop workShop2) {
        this.workShop1 = workShop1;
        this.workShop2 = workShop2;
    }
    
    abstract void manufacture();
}

// Refined abstraction
class Car extends Vehicle {
    public Car(Workshop workShop1, Workshop workShop2) {
        super(workShop1, workShop2);
    }
    
    @Override
    void manufacture() {
        System.out.print("Car ");
        workShop1.work();
        workShop2.work();
    }
}
```


### **8. Composite Pattern**

**Purpose**: Composes objects into tree structures to represent part-whole hierarchies.

**Java Implementation**:

```java
interface FileSystemComponent {
    void showDetails();
}

class File implements FileSystemComponent {
    private String name;
    private int size;
    
    public File(String name, int size) {
        this.name = name;
        this.size = size;
    }
    
    @Override
    public void showDetails() {
        System.out.println("File: " + name + " (Size: " + size + " KB)");
    }
}

class Directory implements FileSystemComponent {
    private String name;
    private List<FileSystemComponent> components = new ArrayList<>();
    
    public Directory(String name) {
        this.name = name;
    }
    
    public void addComponent(FileSystemComponent component) {
        components.add(component);
    }
    
    @Override
    public void showDetails() {
        System.out.println("Directory: " + name);
        for (FileSystemComponent component : components) {
            component.showDetails();
        }
    }
}

// Usage
Directory root = new Directory("root");
root.addComponent(new File("file1.txt", 100));
Directory documents = new Directory("documents");
documents.addComponent(new File("resume.pdf", 200));
root.addComponent(documents);
root.showDetails();
```


### **9. Decorator Pattern**

**Purpose**: Adds new functionality to objects dynamically without altering their structure.

**Java Implementation**:

```java
interface Pizza {
    String getDescription();
    double cost();
}

class PlainPizza implements Pizza {
    @Override
    public String getDescription() {
        return "Plain pizza";
    }
    
    @Override
    public double cost() {
        return 8.0;
    }
}

abstract class PizzaDecorator implements Pizza {
    protected Pizza decoratedPizza;
    
    public PizzaDecorator(Pizza decoratedPizza) {
        this.decoratedPizza = decoratedPizza;
    }
    
    @Override
    public String getDescription() {
        return decoratedPizza.getDescription();
    }
    
    @Override
    public double cost() {
        return decoratedPizza.cost();
    }
}

class CheeseDecorator extends PizzaDecorator {
    public CheeseDecorator(Pizza decoratedPizza) {
        super(decoratedPizza);
    }
    
    @Override
    public String getDescription() {
        return decoratedPizza.getDescription() + ", cheese";
    }
    
    @Override
    public double cost() {
        return decoratedPizza.cost() + 1.5;
    }
}

// Usage
Pizza pizza = new PlainPizza();
pizza = new CheeseDecorator(pizza);
System.out.println(pizza.getDescription() + " $" + pizza.cost());
```


### **10. Facade Pattern**

**Purpose**: Provides a simplified interface to a complex subsystem.

**Java Implementation**:

```java
// Complex subsystem classes
class CPU {
    public void freeze() { System.out.println("CPU frozen"); }
    public void jump(long position) { System.out.println("CPU jumped to " + position); }
    public void execute() { System.out.println("CPU executing"); }
}

class Memory {
    public void load(long position, byte[] data) { 
        System.out.println("Memory loaded at " + position); 
    }
}

class HardDrive {
    public byte[] read(long lba, int size) {
        System.out.println("Hard drive reading " + size + " bytes from " + lba);
        return new byte[size];
    }
}

// Facade
class ComputerFacade {
    private CPU cpu;
    private Memory memory;
    private HardDrive hardDrive;
    
    public ComputerFacade() {
        this.cpu = new CPU();
        this.memory = new Memory();
        this.hardDrive = new HardDrive();
    }
    
    public void start() {
        cpu.freeze();
        memory.load(0, hardDrive.read(0, 1024));
        cpu.jump(0);
        cpu.execute();
    }
}

// Usage
ComputerFacade computer = new ComputerFacade();
computer.start();
```


### **11. Flyweight Pattern**

**Purpose**: Minimizes memory usage by sharing common data among multiple objects.

**Java Implementation**:

```java
interface Shape {
    void draw(Graphics g, int x, int y, int width, int height, Color color);
}

class Circle implements Shape {
    private boolean fill;
    
    public Circle(boolean fill) {
        this.fill = fill;
    }
    
    @Override
    public void draw(Graphics g, int x, int y, int width, int height, Color color) {
        g.setColor(color);
        g.drawOval(x, y, width, height);
        if (fill) {
            g.fillOval(x, y, width, height);
        }
    }
}

class ShapeFactory {
    private static final HashMap<ShapeType, Shape> shapes = new HashMap<>();
    
    public static Shape getShape(ShapeType type) {
        Shape shapeImpl = shapes.get(type);
        
        if (shapeImpl == null) {
            switch (type) {
                case CIRCLE_FILL:
                    shapeImpl = new Circle(true);
                    break;
                case CIRCLE_NO_FILL:
                    shapeImpl = new Circle(false);
                    break;
            }
            shapes.put(type, shapeImpl);
        }
        return shapeImpl;
    }
    
    public enum ShapeType {
        CIRCLE_FILL, CIRCLE_NO_FILL
    }
}
```


### **12. Proxy Pattern**

**Purpose**: Provides a placeholder or surrogate to control access to another object.

**Java Implementation**:

```java
interface BankAccount {
    void withdraw(double amount);
    void deposit(double amount);
    double getBalance();
}

class RealBankAccount implements BankAccount {
    private double balance;
    
    public RealBankAccount(double initialBalance) {
        this.balance = initialBalance;
    }
    
    @Override
    public void withdraw(double amount) {
        if (balance >= amount) {
            balance -= amount;
            System.out.println("Withdrawn: $" + amount);
        }
    }
    
    @Override
    public void deposit(double amount) {
        balance += amount;
        System.out.println("Deposited: $" + amount);
    }
    
    @Override
    public double getBalance() {
        return balance;
    }
}

class BankAccountProxy implements BankAccount {
    private RealBankAccount realAccount;
    private String userRole;
    
    public BankAccountProxy(String userRole, double initialBalance) {
        this.userRole = userRole;
        this.realAccount = new RealBankAccount(initialBalance);
    }
    
    @Override
    public void withdraw(double amount) {
        if ("ADMIN".equals(userRole) || "USER".equals(userRole)) {
            realAccount.withdraw(amount);
        } else {
            System.out.println("Access denied: Insufficient privileges");
        }
    }
    
    @Override
    public void deposit(double amount) {
        realAccount.deposit(amount);
    }
    
    @Override
    public double getBalance() {
        return realAccount.getBalance();
    }
}
```


## **Behavioral Patterns**   

Behavioral patterns focus on communication between objects and the assignment of responsibilities.

### **13. Iterator Pattern**

**Purpose**: Provides a way to access elements of a collection sequentially without exposing its underlying representation.

**Java Implementation**:

```java
interface Iterator<T> {
    boolean hasNext();
    T next();
}

interface IterableCollection<T> {
    Iterator<T> createIterator();
}

class Playlist implements IterableCollection<String> {
    private List<String> songs;
    
    public Playlist() {
        this.songs = new ArrayList<>();
    }
    
    public void addSong(String song) {
        songs.add(song);
    }
    
    @Override
    public Iterator<String> createIterator() {
        return new PlaylistIterator();
    }
    
    private class PlaylistIterator implements Iterator<String> {
        private int currentIndex = 0;
        
        @Override
        public boolean hasNext() {
            return currentIndex < songs.size();
        }
        
        @Override
        public String next() {
            if (hasNext()) {
                return songs.get(currentIndex++);
            }
            throw new NoSuchElementException();
        }
    }
}

// Usage
Playlist playlist = new Playlist();
playlist.addSong("Song 1");
playlist.addSong("Song 2");

Iterator<String> iterator = playlist.createIterator();
while (iterator.hasNext()) {
    System.out.println(iterator.next());
}
```


### **14. Observer Pattern**

**Purpose**: Defines a one-to-many dependency between objects so that when one object changes state, all dependents are notified.

**Java Implementation**:

```java
interface Observer {
    void update(float temperature);
}

class WeatherStation {
    private List<Observer> observers = new ArrayList<>();
    private float temperature;
    
    public void addObserver(Observer observer) {
        observers.add(observer);
    }
    
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }
    
    public void setTemperature(float temperature) {
        this.temperature = temperature;
        notifyObservers();
    }
    
    private void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(temperature);
        }
    }
}

class CurrentConditionsDisplay implements Observer {
    private float temperature;
    
    @Override
    public void update(float temperature) {
        this.temperature = temperature;
        display();
    }
    
    private void display() {
        System.out.println("Current temperature: " + temperature + "°C");
    }
}

// Usage
WeatherStation weatherStation = new WeatherStation();
CurrentConditionsDisplay display = new CurrentConditionsDisplay();
weatherStation.addObserver(display);
weatherStation.setTemperature(25.5f);
```


### **15. Strategy Pattern**

**Purpose**: Defines a family of algorithms, encapsulates each one, and makes them interchangeable.

**Java Implementation**:

```java
interface PaymentStrategy {
    void pay(double amount);
}

class CreditCardPayment implements PaymentStrategy {
    private String cardNumber;
    
    public CreditCardPayment(String cardNumber) {
        this.cardNumber = cardNumber;
    }
    
    @Override
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " using Credit Card: " + cardNumber);
    }
}

class PayPalPayment implements PaymentStrategy {
    private String email;
    
    public PayPalPayment(String email) {
        this.email = email;
    }
    
    @Override
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " using PayPal: " + email);
    }
}

class ShoppingCart {
    private PaymentStrategy paymentStrategy;
    
    public void setPaymentStrategy(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }
    
    public void checkout(double amount) {
        paymentStrategy.pay(amount);
    }
}

// Usage
ShoppingCart cart = new ShoppingCart();
cart.setPaymentStrategy(new CreditCardPayment("1234-5678-9012-3456"));
cart.checkout(100.0);
```


### **16. Command Pattern**

**Purpose**: Encapsulates a request as an object, allowing you to parameterize clients with different requests and support undo operations.

**Java Implementation**:

```java
interface Command {
    void execute();
}

class Light {
    public void turnOn() {
        System.out.println("Light is ON");
    }
    
    public void turnOff() {
        System.out.println("Light is OFF");
    }
}

class LightOnCommand implements Command {
    private Light light;
    
    public LightOnCommand(Light light) {
        this.light = light;
    }
    
    @Override
    public void execute() {
        light.turnOn();
    }
}

class LightOffCommand implements Command {
    private Light light;
    
    public LightOffCommand(Light light) {
        this.light = light;
    }
    
    @Override
    public void execute() {
        light.turnOff();
    }
}

class RemoteControl {
    private Command command;
    
    public void setCommand(Command command) {
        this.command = command;
    }
    
    public void pressButton() {
        command.execute();
    }
}

// Usage
Light light = new Light();
Command lightOn = new LightOnCommand(light);
Command lightOff = new LightOffCommand(light);

RemoteControl remote = new RemoteControl();
remote.setCommand(lightOn);
remote.pressButton();
```


### **17. State Pattern**

**Purpose**: Allows an object to alter its behavior when its internal state changes.

**Java Implementation**:

```java
interface State {
    void doAction(Context context);
}

class StartState implements State {
    @Override
    public void doAction(Context context) {
        System.out.println("Player is in start state");
        context.setState(this);
    }
    
    @Override
    public String toString() {
        return "Start State";
    }
}

class StopState implements State {
    @Override
    public void doAction(Context context) {
        System.out.println("Player is in stop state");
        context.setState(this);
    }
    
    @Override
    public String toString() {
        return "Stop State";
    }
}

class Context {
    private State state;
    
    public Context() {
        state = null;
    }
    
    public void setState(State state) {
        this.state = state;
    }
    
    public State getState() {
        return state;
    }
}

// Usage
Context context = new Context();
StartState startState = new StartState();
startState.doAction(context);
System.out.println(context.getState().toString());

StopState stopState = new StopState();
stopState.doAction(context);
System.out.println(context.getState().toString());

```


### **18. Template Method Pattern**

**Purpose**: Defines the skeleton of an algorithm in a base class, letting subclasses override specific steps without changing the algorithm's structure.

**Java Implementation**:

```java
abstract class BeverageMaker {
    // Template method
    public final void makeBeverage() {
        boilWater();
        brew();
        pourInCup();
        addCondiments();
    }
    
    // Abstract methods to be implemented by subclasses
    abstract void brew();
    abstract void addCondiments();
    
    // Common methods
    void boilWater() {
        System.out.println("Boiling water");
    }
    
    void pourInCup() {
        System.out.println("Pouring into cup");
    }
}

class Tea extends BeverageMaker {
    @Override
    void brew() {
        System.out.println("Steeping the tea");
    }
    
    @Override
    void addCondiments() {
        System.out.println("Adding lemon");
    }
}

class Coffee extends BeverageMaker {
    @Override
    void brew() {
        System.out.println("Dripping coffee through filter");
    }
    
    @Override
    void addCondiments() {
        System.out.println("Adding sugar and milk");
    }
}

// Usage
BeverageMaker tea = new Tea();
tea.makeBeverage();
```


### **19. Visitor Pattern**

**Purpose**: Allows adding new operations to existing class hierarchies without modifying the classes.


**Java Implementation**:

```java
interface ShapeVisitor {
    void visit(Circle circle);
    void visit(Square square);
}

interface Shape {
    void accept(ShapeVisitor visitor);
}

class Circle implements Shape {
    private double radius;
    
    public Circle(double radius) {
        this.radius = radius;
    }
    
    public double getRadius() {
        return radius;
    }
    
    @Override
    public void accept(ShapeVisitor visitor) {
        visitor.visit(this);
    }
}

class Square implements Shape {
    private double side;
    
    public Square(double side) {
        this.side = side;
    }
    
    public double getSide() {
        return side;
    }
    
    @Override
    public void accept(ShapeVisitor visitor) {
        visitor.visit(this);
    }
}

class AreaCalculator implements ShapeVisitor {
    private double totalArea = 0;
    
    @Override
    public void visit(Circle circle) {
        totalArea += Math.PI * Math.pow(circle.getRadius(), 2);
    }
    
    @Override
    public void visit(Square square) {
        totalArea += Math.pow(square.getSide(), 2);
    }
    
    public double getTotalArea() {
        return totalArea;
    }
}

// Usage
List<Shape> shapes = Arrays.asList(
    new Circle(5),
    new Square(4)
);

AreaCalculator calculator = new AreaCalculator();
for (Shape shape : shapes) {
    shape.accept(calculator);
}
System.out.println("Total area: " + calculator.getTotalArea());
```


### **20. Mediator Pattern**

**Purpose**: Reduces coupling between components by making them communicate through a central mediator object.

**Java Implementation**:

```java
interface ChatMediator {
    void sendMessage(String msg, User user);
    void addUser(User user);
}

abstract class User {
    protected ChatMediator mediator;
    protected String name;
    
    public User(ChatMediator mediator, String name) {
        this.mediator = mediator;
        this.name = name;
    }
    
    public abstract void send(String msg);
    public abstract void receive(String msg);
}

class ConcreteUser extends User {
    public ConcreteUser(ChatMediator mediator, String name) {
        super(mediator, name);
    }
    
    @Override
    public void send(String msg) {
        System.out.println(name + " sends: " + msg);
        mediator.sendMessage(msg, this);
    }
    
    @Override
    public void receive(String msg) {
        System.out.println(name + " receives: " + msg);
    }
}

class ChatMediatorImpl implements ChatMediator {
    private List<User> users = new ArrayList<>();
    
    @Override
    public void addUser(User user) {
        users.add(user);
    }
    
    @Override
    public void sendMessage(String msg, User user) {
        for (User u : users) {
            if (u != user) {
                u.receive(msg);
            }
        }
    }
}

// Usage
ChatMediator mediator = new ChatMediatorImpl();
User user1 = new ConcreteUser(mediator, "Alice");
User user2 = new ConcreteUser(mediator, "Bob");

mediator.addUser(user1);
mediator.addUser(user2);

user1.send("Hello!");
```


### **21. Memento Pattern**

**Purpose**: Captures and stores an object's internal state so it can be restored later without violating encapsulation.

**Java Implementation**:

```java
class DocumentMemento {
    private String content;
    
    public DocumentMemento(String content) {
        this.content = content;
    }
    
    public String getSavedContent() {
        return content;
    }
}

class Document {
    private String content;
    
    public Document(String content) {
        this.content = content;
    }
    
    public void write(String text) {
        this.content += text;
    }
    
    public String getContent() {
        return this.content;
    }
    
    public DocumentMemento createMemento() {
        return new DocumentMemento(this.content);
    }
    
    public void restoreFromMemento(DocumentMemento memento) {
        this.content = memento.getSavedContent();
    }
}

class History {
    private List<DocumentMemento> mementos = new ArrayList<>();
    
    public void addMemento(DocumentMemento memento) {
        mementos.add(memento);
    }
    
    public DocumentMemento getMemento(int index) {
        return mementos.get(index);
    }
}

// Usage
Document document = new Document("Initial content\n");
History history = new History();

document.write("Additional content\n");
history.addMemento(document.createMemento());

document.write("More content\n");
history.addMemento(document.createMemento());

// Restore to previous state
document.restoreFromMemento(history.getMemento(0));
System.out.println(document.getContent());
```


### **22. Chain of Responsibility Pattern**

**Purpose**: Passes requests along a chain of handlers until one handles it, decoupling sender from receiver.

**Java Implementation**:

```java
enum Priority {
    BASIC, INTERMEDIATE, CRITICAL
}

class Request {
    private Priority priority;
    
    public Request(Priority priority) {
        this.priority = priority;
    }
    
    public Priority getPriority() {
        return priority;
    }
}

interface SupportHandler {
    void handleRequest(Request request);
    void setNextHandler(SupportHandler nextHandler);
}

class Level1SupportHandler implements SupportHandler {
    private SupportHandler nextHandler;
    
    @Override
    public void setNextHandler(SupportHandler nextHandler) {
        this.nextHandler = nextHandler;
    }
    
    @Override
    public void handleRequest(Request request) {
        if (request.getPriority() == Priority.BASIC) {
            System.out.println("Level 1 Support handled the request");
        } else if (nextHandler != null) {
            nextHandler.handleRequest(request);
        }
    }
}

class Level2SupportHandler implements SupportHandler {
    private SupportHandler nextHandler;
    
    @Override
    public void setNextHandler(SupportHandler nextHandler) {
        this.nextHandler = nextHandler;
    }
    
    @Override
    public void handleRequest(Request request) {
        if (request.getPriority() == Priority.INTERMEDIATE) {
            System.out.println("Level 2 Support handled the request");
        } else if (nextHandler != null) {
            nextHandler.handleRequest(request);
        }
    }
}

class Level3SupportHandler implements SupportHandler {
    @Override
    public void handleRequest(Request request) {
        if (request.getPriority() == Priority.CRITICAL) {
            System.out.println("Level 3 Support handled the request");
        } else {
            System.out.println("Request cannot be handled");
        }
    }
    
    @Override
    public void setNextHandler(SupportHandler nextHandler) {
        // No next handler for Level 3
    }
}

// Usage
SupportHandler level1 = new Level1SupportHandler();
SupportHandler level2 = new Level2SupportHandler();
SupportHandler level3 = new Level3SupportHandler();

level1.setNextHandler(level2);
level2.setNextHandler(level3);

level1.handleRequest(new Request(Priority.BASIC));
level1.handleRequest(new Request(Priority.CRITICAL));
```


## **Key Benefits of Design Patterns**

1. **Reusability**: Patterns provide tested solutions that can be applied across different projects
2. **Maintainability**: Well-structured code following established patterns is easier to maintain
3. **Communication**: Patterns provide a common vocabulary for developers
4. **Best Practices**: They encapsulate years of software development experience
5. **Flexibility**: Many patterns promote loose coupling and high cohesion

## **When to Use Design Patterns**

- **Use them judiciously**: Don't force patterns where they don't naturally fit
- **Solve real problems**: Apply patterns to address actual design challenges
- **Consider trade-offs**: Each pattern has benefits and drawbacks
- **Keep it simple**: Sometimes a simple solution is better than a pattern-based one

Design patterns are powerful tools that, when used appropriately, can significantly improve the quality, maintainability, and extensibility of your Java applications. Understanding these patterns and their appropriate use cases is essential for any serious Java developer.


<div style="text-align: center">⁂</div>

