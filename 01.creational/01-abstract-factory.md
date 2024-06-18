# Abstract Factory Design Pattern

One of classic Gang of Four creational design patterns used to **create families of objects**, where the _objects of a family are designed to work together_.  
In **abstract factory method**, you provide an interface to create families of related or dependent objects, **but you do not specify the concrete classes of the objects to create**.  
_From the client point of view_, it means that a client can create a family of related objects without knowing about the object definitions and their concrete class names.

Note: It is easy to confuse the abstract factory pattern with the factory method pattern because **_both design patterns deal with object creation_**.

> Both the patterns advocates the Object Oriented Programming (OOP) principle **“Program to an interface, not an implementation”** to abstract how the objects are created.  
> Both design patterns help in creating client code that is _loosely-coupled with object creation code_, but despite the similarities, and the fact that both the patterns are often used together, they do have distinct differences.

### Abstract Factory Pattern vs Factory Method Pattern

1. **Abstract factory adds another level of abstraction to factory method.**

While factory method abstracts the way objects are created, abstract factory abstracts how the factories are created. The factories in turn abstracts the way objects are created.  
You will often hear the abstract factory design pattern referred to as a _"factory of factories"._

2. From implementation point of view, the key difference between the factory method and abstract factory patterns is that **_factory method is just a method to create objects of a single type, while abstract factory is an object to create families of objects._**

3.Another difference is that factory method pattern uses inheritance while abstract factory pattern uses composition. - We say that factory method uses inheritance because this pattern relies on a subclass for the requires object instantiation.

On the other hand, the abstract factory pattern delegates responsibility to seperate a object(abstract factory) dedicated to create a family of related objects. Then through composition, the abstract factory object can be passed to the client who will use it (instead of factory method) to get the family of related objects.

### Participant in Abstract Factory Pattern

- AbstractProduct: Is an interface or an abstract class whose subclasses are instantiated by abstract factory objects.
- ConcreteProduct: Are the concrete subclasses that implement/extend AbstractProduct. The abstract factory objects instantiate these subclasses.
- AbstractFactory: Is an interface or an abstract class whose subclasses instatiates these subclasses.
- ConcreteFactory: Are the concrete subclasses that implement/extend AbstractFactory. An object of this subclass instantiates a family of AbstractProduct objects.
- Client: Uses AbstractFactory to get AbstractProduct objects.

See example in the section:

**[Applying the abstract factory pattern](https://springframework.guru/gang-of-four-design-patterns/abstract-factory-design-pattern/)**

Defintion of abstract factory earlier: our abstract factory is providing an _**"interface to create families of related or dependent objects."**_  
The definition also states "...but you do not specify the concrete classes of the objects to create."

Notice: an abstract factory object _can use_ factory methods, one for each product to create. We are saying "can use" because, though this is the most common approach, it is not the only approach.

Another approach is to use the **Prototype pattern** in an abstract factory to create products.

Follow the basic principle:

> **_"Program to an interface, not an implementation."_**

### Conclusion

As you get further into enterprise application development, you will encounter use cases for the abstract factory pattern, especially as the objects you're creating become more complex. Its not uncommon to start off using the factory method design pattern and have your code evolve into using the abstract factory design pattern. Often you'll need one instance of the factory. If this is the case, you should consider implementing the concrete factory as a Singleton.

---

![Structure](https://refactoring.guru/images/patterns/diagrams/abstract-factory/structure-indexed-2x.png?id=cb6d4e1e89826c42966dc7097374f889)

1. **Abstract Products** declare interfaces for a set of distinct but related products which make up a product family.
2. **Concrete Products** are various implementations of abstract products, grouped by variants. Each abstract product must be implemented in all given variants.
3. **Abstract Factory** interface declares a set of methods for creating each of the abstract products.
4. **Concrete Factories** implement creation methods of the abstract factory. Each concrete factory corresponds to a specific variant of products and creates only those products.
5. Although concrete factories instantiate concrete products, signatures of their creation methods must return corresponding _abstract products_. This way the client code that uses a factory doesn't get coupled to the specific variant of the product it gets from a factory. The **Client** can work any concrete factory/product variant, as long as it communicates with their objects via abstract interfaces.
