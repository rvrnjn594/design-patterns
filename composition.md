# Object composition

In computer science, **object composition** and **object aggregation** are _closely related ways to combine objects or data types into complex ones_.  
In conversation the distinction between composition and aggregation is often ignored.

Common kinds of compositions are objects used in object-oriented programming, tagged unions, sets, sequences, and various graph structures.  
Object compositions relate to, but are not the same as, data structures.

Oject composition refers to **_the logical or conceptual structure of the information, not the implementation or physical data structure used to represent it_**.

### Programming technique

- Object-oriented programming is based on objects to encapsulate data and behavior. It uses two main techniques for assembling and composing functionality into more complex ones, **sub-typing and object composition**.
  Object composition is about combining objects within compound objects, and at the same time, ensuring the encapsulation of each object by using their well-defined interface without visibility of their internals. In this regard, object composition differs from data structures, which do not enforce encapsulation.

- Object composition may also be about a group of multiple related objects, such as a set or a sequence of objects. Delegation may enrich composition by forwarding requests or calls made to the enclosing composite object to one of its internal components.

- In class-based and typed programming languages, types can be divided into composite and non-composite types, and composition can be regarded as a relationship between types: an object of a composite type (e.g. car) "has" objects of other types (e.g. wheel). When a composite object contains several sub-objects of the same type, they may be assigned to particular roles, often distinguished by names or numbers. For example, a Point object might contain 3 numbers, each representing distance along a different axis, such as 'x', 'y', and 'z'. The study of part-whole relationships in general, is **mereology**.

- Composition must be distinguished from subtyping, which is the process of adding detail to a general data type to create a more specific data type. For instance, cars may be a specific type of vehicle: car is a vehicle. Subtyping doesn't describe a relationship between different objects, but instead, says that objects of a type are simultaneously objects of another type. The study of such relationships is **ontology**.

- In **prototype-based programming languages such as JavaScript**, objects can dynamically inherit the behaviors from a prototype object at the moment of their instantiation. Composition must be distinguished from prototyping: the newly instantiated object inherits the composition of its prototype, but it may itself be composed on its own.

- Composite objects may be represented in storage by co-locating the composed objects, by co-locating references, or in many other ways. The items within a composite object may be referred to as attributes, fields, members, properties, or other names, and the resulting composition as composite type, storage record, structure, tuple, or a user-defined type (UDT). For details, see the aggregation section below.

### UML modeling technique

In UML modeling, _objects can be conceptually composed, independently of the implementation_ with a programming language.

There are four ways of composing objects in UML: - property - assosiation - aggregation - composition

> A property represents an attribute of the class.  
> An association represents a semantic relationship between instances of the associated classes. The member-end of an association corresponds to a property of the associated class.  
> An aggregation is a kind of association that models a part/whole relationship between an aggregate(whole) and a group of related components(parts).
> A composition, also called a composite aggregation, is a kind of aggregation that models a part/whole relationship between a composite(whole) and a group of exclusively owned parts.

![UML properties to compose objects](https://upload.wikimedia.org/wikipedia/commons/4/4f/UML_properties_of_a_bicycle.png)

**The relationship between the aggregate and its components is a weak "has-a" relationship**: The components may be part of several aggregates, may be accessed through other objects without going through the aggregate, and may outlive the aggregate object.  
The state of component object still forms part of the aggregate object.

**The relationship between the composite and its parts is a strong "has-a" realtionship**: The composite object has sole _"responsibility for the existence and storage of the composed objects"_, the composed object can be part of at most one composite, and "If a composite object is deleted, all of its part instances that are objects are deleted with it".  
Thus in UML, composition has a more narrow meaning than the usual object composition.

The graphical notation represents:

- the property as a typed element in the box of the enclosing class,
- the association as a plain line between the associated classes,
- the aggregation as an unfilled diamond on the side of the aggregate and a solid line,
- the composition as a filled diamond on the side of the composite and a solid line.
  ![UML notation for association, composition and aggregation](https://upload.wikimedia.org/wikipedia/commons/thumb/2/21/UML_association%2C_aggregation_and_composition_examples_for_a_bicycle.png/440px-UML_association%2C_aggregation_and_composition_examples_for_a_bicycle.png)

### Aggregation

Aggregation differs from ordinary composition in that **it does not imply ownership**.

In composition, when the owning object is destroyed, so are the contained objects.  
In aggregation, this is not necessarily true.

> example: a university can be seen as a composition of departments, >whereas departments have an aggregation of professors. In addition, a >professor could work in more than one department, but a department >could not be part of more than one university.

**_Composition is usually implemented such that an object contains another object._**  
**_In aggregation, the object may only contain a reference or pointer to the object (and not have lifetime responsibility for it)_**

##### Aggregation in COM

In Microsoft's Component Object Model, aggregation means that an object exports, as if it were their owner, one or several interfaces of another object it owns. **_Formally, this is more similar to composition or encapsulation than aggregation._**  
However, instead of implementing the exported interfaces by calling the interfaces of the owned object, the interfaces of the owned object themselves are exported. The owned object is responsible for assuring that methods of those interfaces inherited from IUnknown actually invoke the corresponding methods of the owner. This is to guarantee that the reference count of the owner is correct and all the interfaces of the owner are accessible through the exported interface, while no other (private) interfaces of the owned objects are accessible.

---

### Special forms

##### Containment

- **Composition that is used to store the several instances of the composited data type is referred to as containment.** Examples of such containers are arrays, associative arrays, binary trees, and linked lists.  
  In UML, containment is depicted with a multiplicity of 0.._ or 1.._, indicating that the composite object is composed of an unknown number of instances of the composed class.

##### Recursive composition

- **Objects can be composed recursively, and their type is then called recursive type.** Examples includes various kinds of trees, DAGs, and graphs. Each node in a tree may be a branch or leaf; in other words, each node is a tree at the same time when it belongs to another tree.  
  In UML, recursive composition is depicted with an association, aggregation or composition of a class with itself.

##### Composite pattern

- The composite design pattern is an object-oriented design based on composite types, that combines recursive composition and containment to inmplement complex part-whole hierarchies.

---

---
