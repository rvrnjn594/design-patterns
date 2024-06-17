# Prototype Pattern

> "Specify the kinds of objects to create using a prototypical instance, and create new objects by copying this prototype."

The prototype pattern is a classic Gang of Four creational pattern, and similar to the other members of the creational pattern family: singleton , factory method, abstract factory, and builder, _prototype is also concerned with object creation, but with a difference_.

> Using the prototype pattern, you do not create a new object for each client requesting the object. **_Instead, you start by creating a single object, called a prototype and make copies of it for each client requesting the object_**. In Java, this is achieved through object cloning, a way to make a copy of an object with the same state as the original object.

But why create copies if we can create new objects through constructor calls, which is much simpler?

> Most of the time you will not need to create copies of objects. But, as you move into enterprise application development where application performance is critical, you will encounter situations where construction of an object involves time consuming operations, such as network communication, database reads, and disk I/O. If a large number of such objects needs to be created, you can avoid repeating those steps for each object by initially creating a prototype and then making copies of it.

While the prototype pattern is itself simple, **_it is important to learn the intricacies involved in Java object cloning before you start applying the prototype pattern to applications_**.

### Object Cloning: Shallow Copy vs Deep Copy

**_Java provides the Cloneable interface to mark objects that permit cloning. This interface is a marker interface and therefore does not contain any method declaration._**

When implemented in a class, Cloneable marks that objects of the class can be cloned. To perform cloning, you need to call the protected clone() method of the Object class through a call to super.clone().

**Note:** If an object calls super.clone() but does not implements Cloneable, the method throws an exception of type, CloneNotSupportedException.

> A call to super.clone() performs a shallow copy where all the fields values of the original object are copied to the new object. If a field value is a primitive type, a shallow copy copies the value of the primitive type. But, if a field value is a reference type, then only the reference is copied, and not the referred object itself. **_Therefore, both the original and its clone refer to the same object and if either one modifies the referred object, the modification will be visible to the other. This might result in unexpected behavior in an application._**  
> **In such situation, you should perform a deep copy that makes copies of dynamically allocated memory pointed to by the reference type fields.**
>
> > In a deep copy, the original and the copied objects are independent of each other and therefore the objects can update their own fields without worrying about any referencing problems.

The difference between shallow and deep copy is illustrated in this figure:  
![Shallow vs Deep Copy](https://springframework.guru/wp-content/uploads/2015/04/4-23-2015-1-55-29-AM.jpg)  
Important distinction to remember:

> In the figure above, an object X references an object O. A shallow copy of X creates a new object X2 that also references object O. In contrast, a deep copy of X creates a new object X2 that references the new object O2(copy of O).

When using the prototype pattern, should we go for shallow copy or for deep copy?

> There is no hard and fast rule, it all depends on the requirement. If an object has only primitive fields or immutable objects (whose state cannot change, once created), use a shallow copy. When the object has references to other mutable objects, then either choose shallow copy or deep copy. For example, if the references are not modified anytime, avoid deep copy and go for shallow copy. But, if you know that the references will be modified and the modification might affect the intended behavior of the application, then you need to go for deep copy.

> > When you go for a deep copy, you will need to override the Object.clone() method in all the member classes and then recursively clone their objects.

> > Alternatively, you can get a deep copy by serializing an object and then restoring it back through Java object serialization. Using serialization is a simple approach, keep in mind serialization is resource intensive and can produce unexpected results if not done properly.
