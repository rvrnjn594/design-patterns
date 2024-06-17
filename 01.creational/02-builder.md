# Builder Pattern

> "Seperate the construction of a complex object from its >representation so that the same construction process can create >different representations."

The builder pattern is a classic Gang of Four creational design pattern. This pattern, similar to the other creational patterns, such as factory method and abstract factory, is concerned with the creation of objects. _But why do we need another pattern to create objects?_  
To answer this, lets us first look at a problem scenrio.

###### The problem

Consider that you need to build a house for a customer. Now, building a house will consists of series of steps. You will start with foundation, then the structure, and finally the roof.  
But, how will you implement this in programming world?  
Answer should be simplr in JAVA - right?

> Create a House class with the required fields and initialize them(series of steps) through a constructor...

This builds the house and your customer is happy. But the next customer wants the house to be painted and furnished while another wants it only painted.

> You now need to revisit the House class with a set of overloaded constructors.

**Although this will work, we have a flawed design.**

> What we applied here is **telescopic constructor pattern.**, _which is considered an [anti-pattern][anti_pattern_definition]._

[anti_pattern_definition]:An AntiPattern is a pattern that tells how to go from a problem to a bad solution
