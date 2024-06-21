### Builder Pattern

"Seperate the construction of a complex object from its representation so that the same construction process can create different representations."

> The builder pattern is a classic Gang of Four creational design pattern. This pattern, similar to the other creational patterns, such as factory method and abstract factory, is concerned with the creation of objects.
>
> _But why do we need another pattern to create objects?_  
> To answer this, lets us first look at a problem scenrio.

###### The problem

> Consider that you need to build a house for a customer. Now, building a house will consists of series of steps. You will start with foundation, then the structure, and finally the roof.
>
> But, how will you implement this in programming world?

Answer should be simple in JAVA - right?

> Create a House class with the required fields and initialize them(series of steps) through a constructor...
>
> This builds the house and your customer is happy. But the next customer wants the house to be painted and furnished while another wants it only painted.
>
> You now need to revisit the House class with a set of overloaded constructors.

**Although this will work, we have a flawed design.**

> What we applied here is **telescopic constructor pattern.**, _which is considered an ***anti-pattern***._

---

**AntiPattern** (_Contrast to an_ **AmeliorationPattern**, which is a pattern that tells how to go from a bad solution to a good solution.): An AntiPattern is a pattern that tells how to go from a problem to a bad solution.

---

> - Though this pattern works for simple object creation with a limited number of fields, it becomes unmanageable for complex object creation. Imagine the number of constructors you will need to create a more complex house with options for plumbing, lightning, interiors, and so on.
>
> - Another major problem in this design is related to constructor calls that clients need to make. It is hard and error prone to write client code when there are multiple constructors, each with a large set of parameters. In addition, readability is a major issue with such client code.

**_One solution to telescopic constructor pattern is to follow JavaBeans convention by writing setter methods instead of a set of constructors to initialize the fields._**
[See JavaBeean.md](../javabeans.md)

> - Clients can now call the setter methods with appropriate parameters to create House objects. Also, the client code are now more readable and therefore have lesser chances of errors creeping in.
>
>   Problems:  
>   -> The steps to build a house was not in the correct order.  
>   -> Another problem is the client having an instance of the house class in an inconsistent state. This means, if a client wants to create a House object with values for all its fields then the object will not have a complete state until all the setter methods have been called. As a result, some part of the client application might see and use a House object assuming that it is already constructed while that is actually not the case.
>
> While you might be still pondering over the existing problems on hand, imagine that a customer calls up with a requirement for a prefabricated house, another customer for a tree house, and yet another for an Igloo. **Now, here is a whole new set of problems to solve.**

At this point you should consider yourself lucky because other people have faced a similar problems and have come up with proven solutions.

It is time to learn the classic **GoF Builder Pattern**.

---

# Builder Pattern

### Introduction to the Builder Pattern

The builder pattern allows you to **enforce a step-by-step process to construct a complex object as a finished product**.

In this pattern, **_the step-by-step construction process remains same but the finished products can have different representations_**.

> In the context of the house building example, the step-by-step process includes the steps to create the foundation, structure, and roof followed by the steps to paint and furnish a house and these steps remain the same irrespective of the type of house to build.

The finished product, which is a house, can have different representations. That is, it can be a concrete house, a prefabricated house, or a tree house.

### Participant in the Builder Pattern

To understand how the builder pattern works, let us solve the problems of our house building example.

> The main problem was that we expected the clients to perform the steps to construct a house and that too in the correct order. So, how will we address this in real life?

We will hire a **construction engineer** _who knows the process to construct houses_.

> The second problem was that we require different types of houses, such as concrete, prefabricated, tree house, and even Igloos.

So next, we will hire builders (contractors) who specializes in building specific types of houses.

> A builder knows how to put things together with actual building materials, components, and parts to build a particular type of house.
>
> For example, a concrete house builder knows how to build the structure of a concrete house using concrete, brick, and stone. Similarly, a prefabricated house builder knows how to build the structure of a prefabricated house using structural steels and wooden wall panels.

So from now on, whenever we need a house, the construction engineer will direct a builder to build the house.

- In our application, we can model the construction engineer by creating a _ConstructionEngineer class_.
- Then we can model the builders by first creating a _HouseBuilder interface and then builder classes_, such as ConcreteHouseBuilder and PrefabricatedHouseBuilder that implement the HouseBuilder interface.

> Here, notice that we have added a layer of abstraction by providing an interface (HouseBuilder). **This is because we do not want our construction engineer to be tied with a particular builder**.
>
> The construction engineer should be able to direct any builder that implements the HouseBuilder interface to build a house. **This will also allow us to later add new builders without making changes to the existing application code.**

#### We now summarize the components of the builder pattern in the context of the house building example as:

- Product (House): A class that represents the product to create.
- Builder (HouseBuilder): Is an interface to build the parts of a product.
- ConcreteBuilder (ConcreteHouseBuilder and PrefabricatedHouseBuilder): Are concrete classes that implements Builder to construct and assemble parts of the product and return the finished product.
- Director (ConstructionEngineer): A class that directs a builder to perform the steps in the order that is required to build the product.

### [Applying the Builder Pattern](https://springframework.guru/gang-of-four-design-patterns/builder-pattern/)

To apply the builder pattern to the house building example, let us create the product that the builders will construct.

See the example with code in this section [here](https://springframework.guru/gang-of-four-design-patterns/builder-pattern/).

> As you can see in the example above, **a client is now insulated from the object creation process**.
>
> A client only needs to provide the Director a ConcreteBuilder to use. It is responsibility of the Director to instruct the ConcreteBuilder on the construction process and the ConcreteBuilder in turn will create the finished product.
>
> Finally, the client receives the finished product from the Director.

### Conclusion

If you are familiar with the abstract factory pattern, you might have observed that both the abstract factory and builder patterns are similar, as both can be used to abstract object creation.

But there are distinct differences between the two.

> While abstract factory emphasizes on creating a family of related objects in one go, builder is about creating an object through step-by-step construction process and returning the object as the final step.

In short **_abstracy factory is concerned with what is made_**, while **_the builder with how it is made._**

So as you go further into enterprise application development, whenever you need **to create complex objects independently of the construction algorithm turn to the classic GoF Builder Pattern.**
