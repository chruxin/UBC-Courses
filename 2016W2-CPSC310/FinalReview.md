# Final Exam

## Percentage

30%, or 20% if worse than midterm

## Time

2.5 hours

## Programming Language: 

`Java`

(***Below probably don't apply anymore b/c of MC format***)

- ~~Syntax errors are accepted~~
- ~~Need to have types~~
- ~~Semantic should be correct~~

## Topics

### Design Principles

Code —> presence? absence?

#### SOLID (Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion)

##### Single Responsibility

> Do **one** thing and do it **well**.

- High cohesion, low coupling.
- Example is usually when a class contains lots of functions that are really really closely related.
- Solution is to turn one class into multiple (small) classes that do a single/simple task.

##### Open/Closed

> Classes should be open to **extension** and closed to **modification**.

- Example is usually when we need to override a function.
- Senario: a function is huge and does a lot of things, and now in the subclass we need to do almost the same thing, with one tiny little change in some part of that function.
- Problem occurs in this case because we can't override that method directly, and we have to copy the implementation of that method to subclass, and change maybe just one line of code. Copy and paste, crime! And it's hard to maintain mostly the same code in two places.
- Solution is to break that big method into a few more, hide the parts that don't need to be overriden (private), and override the specific method that contains code the subclass wants to change.

##### Liskov Substitution

> Subclasses should be interchangeable.

- Subclass functions have: looser (or the same) preconditions, stricter (or the same) postconditions. A smiling face!
- Typical example: a square cannot extend rectangle as a square has stricker precondition – width=height.

##### Interface Segregation

> Clients should not be forced to **depend** on interfaces they do **not** use.

- Have small, role-specific interfaces 
- Interfaces should also just do one thing

##### Dependency Inversion

> Depend on **abstractions**, not **implementations**.

### UML Diagram

- Arrow styles are very important

### Design Patterns

- Have to choose the correct pattern
- Watcher —> Observer Pattern
- Look at the slides for design patterns (no proxy), those that have their own slides

#### Observer

One subject, multiple observers.

1. Register observer
2. Notify observers

Variations:

- Lots of subjects vs lots of observers
  - Lots of subjects: observer registers with each and then wait for notification
  - Lots of observers: subject maintains a list of observers and loops through it notifying each one
- Push vs pull model
  - Push model: subject only tells what each observer wants to hear to each observer —> subject has to maintain a lot of knowledge about the observers! coupling!
    - Example: Instructor (subject) note on Piazza updates every student (observer), maybe update different students on different posts that they follow
  - Pull model: subject sends a basic notification, but the observer only gets status details of interest —> this can be slow b/c of 2 calls to get updates
    - Example: Twitter x new tweets

Design recipe:

- Observer: `notify()`

  ```java
  for (Subject subject: Subjects) {
    subject.register();
  }
  ```

- Subject/Oberservable: `registerObservers()`

  ```java
  for (Observer observer: Observers) {
    observer.notify();
  }
  ```

#### Composite

A tree structure of either leaves, or groups.

Before:

```java
for (Leaf leaf : leaves) {
  leaf.print();
}
for (Group subgroup : subgroups) {
  subgroup.print();
}
```

After:

```java
for (Component c : components) {
  c.print();
}
```

#### Factory

It is about taking the construction code and moving it to one place, rather than having it sit in the original class.

Before:

```java
public static Car requestCar() {
  Wheels wheels;
  Chassis chassis;
	if (userSelects == "BIG") {
      wheels = new BigWheels();
      chassis = new BigChassis();
	} else {
      wheels = new BigWheels();
      chassis = new BigChassis();
	}
  Car car = new Car(wheels, chassis);
  return car;
}
```

Instead of creating new wheels and chassis, then a new car in the function, we move the code to a factory. After:

```java
public static Car requestCar() {
  CarFactory carFactory;
	if (userSelects == "BIG") {
      carFactory = new BigCarFactory();
	} else {
      carFactory = new SmallCarFactory();
	}
  Car car = carFactory.makeCar();
  return car;
}
```

#### Strategy

Encapsulation of algorithms.

#### Singleton

A class field of itself, a private constructor, a static `getInstance()` method that checks if x is null, if yes call constructor, if no return the existing instance.

#### Adaptor

Client wants to use a target interface, you have your own implementation (Adaptee), but these two dont' fit. 

Solution is to have an Adapter that implements all the methods in the clientInterface. Then, have a field in the class which is of instance Adaptee, and call adaptee.method() in each method.

### Refactoring

- Identify the code that smells 
- Apply refactoring changes
  - Switch statements —> polymorphism 
- Refactoring using design patterns
  - Problem: shotgun surgery, code duplication, etc
  - Design pattern to fix the problem: Adapter, Observer, etc

#### Magic Numbers/Strings

Problem: Using a specific number/string in code directly without defining it as a constant.

Solution: Symbolic constants

#### Duplicate Code

Problem: `add` and `multiply` in d0.

Solution: Extract methods

Problem: Almost duplication – (almost) the same method in 2 classes

Solution: pull up the method

#### Long Method

Solution: pull code out into separate methods —> each method should have just one task

#### Complicated Conditionals

Solution: decompose conditional

#### Switch Statements

Problem: Choose different behaviour on type of an object.

Solution: polymorphism

#### Large Class

Problem: one class doing the work of two/many

Solution: extract class

#### Data Clump

Problem: parameters that are always passed in together.

Solution: introduce parameter object/compound

#### Divergent Changes

Problem: When you have to alter a class for more than one kind of change.

Solution: split up the behavior of the class into different classes

#### Shotgun Surgeries

Problem: One change leads to changes in multiple places.

Solution: move method, move field

#### Comments

Problem: need comments to explain code

Solution: extract functionality/intorduce assertion

### Intellectual properties

#### Trade Secrets

No law

#### Patents

Novel inventions, expire in 20 years, cannot renew, protect development of product

#### Trade Marks

Brand (logo, word, or color), renewable indefinitely, targeted towards a specific industry (another industry doesn't apply)

#### Copyright

Reproduce and sell **work** asa produced, can transfer copyright to other people, company owns the work you produced instead of yourself, 50 years after death of author in Canada

### Logistics

- Answer questions within the question range (design patterns, principles)
- Lower grade on the final: midterm will have a higher percentage
