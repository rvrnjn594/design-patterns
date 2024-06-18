# Bridge Pattern

> "Decouple an abstraction from its implementation so that the two can vary independently."

### Introduction

We use abstraction to decouple client code from implementations, and the usual way is to use inheritance.

> We define an interface or an abstract class and create inheritance hierarchies from it, one for each of the several possible implementations.  
> Although at first look, this approach approach appears logical and nothing wrong in it, abstractions through inheritance isn't always flexible.  
> When we use inheritance, we are permanently binding the implementation to the abstraction. As a result, any change made to one affects the other.

**A more flexible way is to seperate the abstraction and the implementation, and this is where the bridge pattern comes in.**

> Similar to other patterns of the classic Gang of Four structural pattern family, **the objective of the bridge pattern is to identify how to realize relationships between classes and objects in a simple way.**

The bridge pattern does it by seperating the abstraction and the implementation in seperate class hierarchies.  
The bridge between the class hierarchies is achieved through composition.
