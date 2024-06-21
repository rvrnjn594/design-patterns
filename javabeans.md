# JavaBeans

In computing based on the Java Platform, JavaBeans is a technology developed by Sun Microsystems and released in 1996, as part of JDK 1.1.

> The 'beans' of JavaBeans are classes that encapsulate one or more objects into a single standardized object (the bean).
>
> This standardization allows the bean to be handled in a more generic fashion, allowing easier code reuse and introspection.
>
> This in turn allows the bean to be treated as software components, and to be manipulated visually by editors and IDEs without needing any initial configuration, or to know any internal implementation details.

As part of the standardization, all beans must be **_serializable_**, **_have a zero-argument constructor_**, and **_allows access to properties using getter and setter methods_**.

##### Serialization

In computing, serialization is the process of translating a data structure or object state into format that can be stored (eg: files in secondary storage devices, data buffers in primary storage devices) or transmitted (eg: data streams over computer networks) and reconstructed later (possibly in a different computer environment.)

**The process of serializing an object is also called marshalling an object in some situation. The opposite operation, extracting a data structure from a series of bytes, is deserialization, (also called unserialization or unmarshalling).**

##### Nullary constructor (Zero-constructor arguments)

In computer programming, **a nullary constructor is a constructor that takes no arguments. Also known as a 0-argument constructor, no-argument constructor, paremeterless constructor or default constructor.**

In object-oriented programming, **a constructor is code that is run when an object is created. Default constructor of objets are usually nullary.**

##### Mutator method

In computer science, _a mutator method is a method used to control changes to a variable_. They are also widely known as **setter methods**.

Often a setter is accompanied by a **getter**, which returns the value of the private member variable. They are also known collectively as accessors.

> - The mutator method is most often used in OOP, in keeping with the principle of **encapsulation**.
>
> > According to this principle, member variables of a class are made private to hide and protect them from other code, and can only be modified by a public member function (the mutator method), which takes the desired new value as a parameter, optionally validates it, and modifies the private member varible.
> >
> > Mutator methods can be compared to assignment operator overloading but they typically appear at differnt levels of the object hierarchy.
>
> - Mutator methods may also be used in non-object-oriented environments.
>
> > In this case, a reference to the variable to be modified is passed to the mutator, along with new value.
>
> > In this scenario, the compiler cannot restrict code from bypassing the mutator method and changing the variable directly. The responsiblity falls to the developers to ensure the variables is only modified through the mutator method and not modified directly.
>
> > In programming languages that support them, **properties offer a convenient alternative without giving up the utility of encapsulation**.

---
