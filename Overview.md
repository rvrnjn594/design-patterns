# GoF(Gang of Four) Design Patterns

---

Gof wrote this book in a c++ context but it still remains very relevant to the Java programming.  
C++ and Java are both object oriented language.**Not Unique to C++, can be applied to any object-oriented language.**  
As a Java developer using _Spring Framework_ to develop enterprise class application, you will encounter the Gof Design Patterns on a daily basis.

### Gof Patterns are broken into three categories:

        * Creational Patterns - _for creation of objects_
        * Structural Patterns - _to provide realtionship between objects_
        * Behavioral Patterns - _to help define how objects interact_

### **[Gang of Four Design Pattern](https://springframework.guru/gang-of-four-design-patterns/)**

##### Creational

        1. **Abstract Factory** - allows creation of objects without specifying their concrete type.
        1. **Builder** - uses to create complex objects.
        1. **Factory Method** - creates objects without specifying the exact class.
        1. **Prototype** - creates a new object from existing object.
        1. **Singleton** - ensures only one instance of an object is created.

##### Structural

        1. **Adapter** - allows for two incompatible classes to work together by wrapping an interface.
        1. **Bridge** - decouples an abstraction so two classes can vary accordingly.
        1. **Composite** - takes a group of objects into a songle object.
        1. **Decorator** - allows for an objects's behavior to be extended dynamically at runtime.
        1. **Facade** - provides a simple interface to a more complex underlying object.
        1. **Flyweight** - reduces the cost of complex object models.
        1. **Proxy** - provides a placeholder interface to an underlying object to control access, reduce cost, or reduce complexity.

##### Behavioral

        1. **Chain of Responsibility** - delegates commands to a chain of processing objects.
        1. **Command** - creates objects which encapsulate actions and parameters.
        1. **Interpreter** - implements a specialized language.
        1. **Iterator** - accesses the elements of an object sequentially without exposing its underlying representation.
        1. **Mediator** - allows loose coupling between classes by being the only class that detailed knowledge of their methods.
        1. **Momento** - provide ability to restore an object to its previous state.
        1. **Observer** - is a publish/subscribe pattern which allows a number of observer objects to see an event.
        1. **State** - allows an object to alter its behavior when its internal state changes.
        1. **Strategy** - allows one of a family of algorithms to be selected on-the-fly at run-time.
        1. **Template Method** - defines the skeleton of an algorithm as an abstract class, allowing its subclasses to provide concrete behavios.
        1. **Visitor** - seperates an algorithm from an object structure by moving the hierarchy of methods into one object.
