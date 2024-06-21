# Decorator Pattern

“Attach additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.”

# Introduction

> The common objective of the classic Gang of Four structural patterns that we’ve learned – **Adapter, Bridge, and Composite is to identify how to realize complex relationships between classes and objects in a simple and flexible way.** The decorator pattern also has the same objective but addresses a different problem area.

While thinking about extending functionality, the first thing which is likely to occur to a new programmer is inheritance.

> However, inheritance may not be the ideal solution in all situations.
>
> When you inherit functionality through subclassing, the functionality is statically set at compile time and it doesn’t always lead to the most flexible nor maintainable designs.

If you need to add new features, code modifications are required, which is a violation of the Open Closed Principle.

Instead, you can attach new responsibility to an object dynamically.

> This is exactly the intended use of the decorator pattern.
>
> Per the Gang of Four – “Attach additional responsibilities to an object dynamically”. But how do you do this?
>
> > By using Composition.

**With composition, you can dynamically add multiple new responsibilities to objects at run time.**

> The great thing here is that the object doesn’t need to be aware of it, and your design conforms to the Open Closed Principle. We often look to the elegance of a great object-oriented programming language such as Java, while overlooking the simplicity of using composition to solve a problem.

Let’s look at a problem scenario and see how the decorator pattern can solve it.

### Participants of the Decorator Pattern

Consider a florist who sells flower bouquets, such as rose bouquet, orchid bouquet, and so on.

> When a customer walks in, the florist describes the bouquets and tells their prices.  
> We can model the requirements with a FlowerBouquet abstract base class and subclasses for specific bouquets.

![Abstract Class for the example above](https://springframework.guru/wp-content/uploads/2015/07/Decorator01.png)

> Customers, in addition to a bouquet, can ask the florist to decorate it with paper wrappings, ribbon bows, and glitters for which the florist charges extra.
>
> To address the new requirement, we can add new subclasses of FlowerBouquet, one each to represent a bouquet with an additional decoration, and this is how our design looks like now.

![Additional decoration](https://springframework.guru/wp-content/uploads/2015/07/Decorator02.png)

> What we have is “class explosion” and that too while adding only a single decoration to a bouquet.
>
> Imagine representing a bouquet with multiple decorations, for example, a rose bouquet with both ribbon bow and glitter, or an orchid bouquet with a paper wrap, ribbon bow, and glitter. Some more considerations:
>
> > > What if we want a rose bouquet with double paper wrap?  
> > > What if we want to add a new lily bouquet?  
> > > What if we want to add new ornamental leaves decoration?
>
> Clearly, our design is flawed, but this is an ideal use case for the decorator pattern.

By using the decorator pattern, we can create a flower bouquet and dynamically decorate it with any numbers of features at run time.

Without the flower bouquet knowing it that is “being decorated”.

> For that, we will create an abstract base class FlowerBouquet and specific subclasses RoseBouquet and OrchidBouquet to extend from it.
>
> We will also create an abstract FlowerBouquetDecorator class to extend FlowerBouquet. For each decorator, we will create the Glitter, PaperWrapper, and RibbonBow classes to extend from FlowerBouquetDecorator. This is how the design will be.

![FlowerBouquetDecorator](https://springframework.guru/wp-content/uploads/2015/07/Decorator03.png)

> In the diagram above, the most important thing to observe is the relationship between FlowerBouquet and FlowerBouquetDecorator. We have two types of relationships:
>
> > > Inheritance by subclassing FlowerBouquetDecorator from FlowerBouquet in order to have the correct type.
>
> > > Composition by composing FlowerBouquetDecorator with FlowerBouquet in order to add new behavior.

Let’s now look at how our **classes map to the participants of the decorator pattern**:

- Component (FlowerBouquet): Is an abstract base class that can be decorated with responsibilities dynamically.
- ConcreteComponent(RoseBouquet and OrchidBouquet): Are concrete classes that extends Component to represent objects to which additional responsibilities can be attached.
- Decorator (FlowerBouquetDecorator): Is an abstract class that extends Component and acts as the base class for concrete decorator classes.
- ConcreteDecorator (PapperWrapper, RibbonBow, and Glitter): Are concrete classes that extends Decorator to decorate Components with responsibilities.

### Applying the Decorator Pattern

To apply the decorator pattern to the flower bouquet example, let's write the abstract base class - the Component.

**FlowerBouquet.java**

    package guru.springframework.gof.decorator.components;

    public abstract class FlowerBouquet {
        String description;
        public String getDescription() {
            return description;
        }
        public abstract double cost();
    }

> In the FlowerBouquet class above, we wrote a description instance variable, a getDescription() mathod that returns it, and an abstract cost() method.
>
> We will next write teh ConcreteComponent classes.

**RoseBouquet.java**

    package guru.springframework.gof.decorator.components;

    public class RoseBouquet extends FlowerBouquet{
        public RoseBouquet(){
            description = "Rose bouquet";
        }
        public  double cost(){
            return 12.0;
        }
    }

**OrchidBouquet.java**

    package guru.springframework.gof.decorator.components;

    public class OrchidBouquet extends FlowerBouquet{
        public OrchidBouquet(){
        description = "Orchid bouquet";
        }
        public double cost(){
            return 29.0;
        }
    }

> We extended both the classes above from FlowerBouquet and initialized the description varaible in their constructors. We also implemented the cost() method in each class to return their cost.

Now that we have written the components, we will write the **Decorator**.

**FlowerBouquetDecorator.java**

    package guru.springframework.gof.decorator.decorators;

    import guru.springframework.gof.decorator.components.FlowerBouquet;

    public abstract class FlowerBouquetDecorator extends FlowerBouquet {
        public abstract String getDescription();
    }

> We extended the FlowerDecorator class from FlowerBouquet and declared an abstract getDescription() method.
>
> > We did it because we want all our flower decorator classes to implement getDescription() to include both the description of the decorator and the flower bouquet it is decorating. This will get clearer once we write the ConcreteDecorator classes. Lets, start with Glitter.

**Glitter.java**

    package guru.springframework.gof.decorator.decorators;

    import guru.springframework.gof.decorator.components.FlowerBouquet;

    public class Glitter extends FlowerBouquetDecorator {
        FlowerBouquet flowerBouquet;
        public Glitter(FlowerBouquet flowerBouquet){
            this.flowerBouquet=flowerBouquet;
        }
        public  String getDescription(){
            return flowerBouquet.getDescription()+", glitter";
        }
        public double cost()
        {
            return 4+flowerBouquet.cost();
        }
    }

> We extended the Glitter class from FlowerDecorator, and added a FlowerBouquet instance variable (Composition) to hold the flower bouquet we are wrapping.
>
> > The instance variable gets initialized at run time through the constructor call.
>
> > We then implemented the getDescription() method and returned both the description of this decorator and the flower bouquet it is decorating. We also implemented the cost() method to add the cost of this decorator to the cost of the flower bouquet it is decorating. We will similarly write the other decorator classes.

**PaperWrapper.java**

    package guru.springframework.gof.decorator.decorators;

    import guru.springframework.gof.decorator.components.FlowerBouquet;

    public class PaperWrapper extends FlowerBouquetDecorator{
        FlowerBouquet flowerBouquet;
        public PaperWrapper(FlowerBouquet flowerBouquet) {
            this.flowerBouquet=flowerBouquet;
        }
        public  String getDescription() {
            return flowerBouquet.getDescription()+", paper wrap";
        }
        public double cost() {
            return 3+flowerBouquet.cost();
        }
    }

**RibbonBow.java**

    package guru.springframework.gof.decorator.decorators;

    import guru.springframework.gof.decorator.components.FlowerBouquet;

    public class RibbonBow extends FlowerBouquetDecorator {
        FlowerBouquet flowerBouquet;
        public RibbonBow(FlowerBouquet flowerBouquet) {
            this.flowerBouquet=flowerBouquet;
        }
        public String getDescription() {
            return flowerBouquet.getDescription()+", ribbon bow";
        }
        public double cost() {
            return 6.5+flowerBouquet.cost();
        }
    }

Let's now write some test code to see how the decorator pattern works at runtime.

**FlowerBouquetTest.java**

    package guru.springframework.gof.decorator.components;

    import guru.springframework.gof.decorator.decorators.Glitter;
    import guru.springframework.gof.decorator.decorators.PaperWrapper;
    import guru.springframework.gof.decorator.decorators.RibbonBow;
    import org.junit.Test;

    public class FlowerBouquetTest {
        @Test
        public void testFlowerBouquet() {
            /*Rose bouquet with no decoration*/
            FlowerBouquet roseBouquet = new RoseBouquet();
            System.out.println(roseBouquet.getDescription()
                    + " $ " + roseBouquet.cost());

            /*Rose bouquet with paper wrapper, ribbon bow, and glitter*/
            FlowerBouquet decoratedRoseBouquet = new RoseBouquet();
            decoratedRoseBouquet=new PaperWrapper(decoratedRoseBouquet);
            decoratedRoseBouquet=new RibbonBow(decoratedRoseBouquet);
            decoratedRoseBouquet=new Glitter(decoratedRoseBouquet);
            System.out.println(decoratedRoseBouquet.getDescription()
                    + " $ " + decoratedRoseBouquet.cost());

            /*Orchid bouquet with double paper wrapper and ribbon bow*/
            FlowerBouquet decoratedOrchidBouquet = new OrchidBouquet();
            decoratedOrchidBouquet=new PaperWrapper(decoratedOrchidBouquet);
            decoratedOrchidBouquet=new PaperWrapper(decoratedOrchidBouquet);
            decoratedOrchidBouquet=new RibbonBow(decoratedOrchidBouquet);
            System.out.println(decoratedOrchidBouquet.getDescription()
                    + " $ " + decoratedOrchidBouquet.cost());
        }
    }

> we first instantiated a RoseBouquet and printed its description and cost.
>
> we again instantiated a RoseBouquet, and this time we wrapped it with the PaperWrapper, RibbonBow, and Glitter decorators before printing its description and cost.
>
> we instantiated a OrchidBouquet, and wrapped it twice with PaperWrapper and then once with RibbonBow before printing its description and cost.

The output of the code above is this.

    ---
    T E S T S
    ---
    Running guru.springframework.gof.decorator.components.FlowerBouquetTest
    Rose bouquet $ 12.0
    Rose bouquet, paper wrap, ribbon bow, glitter $ 25.5
    Orchid bouquet, paper wrap, paper wrap, ribbon bow $ 41.5
    Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.589 sec - in guru.springframework.gof.decorator.components.FlowerBouquetTest

### Conclusion

> The decorator pattern is a great solution to dynamically extend behavior without subclassing, but it often results in a large number of decorator classes.
>
> While there are no complexities in creating the classes and applying the pattern, it can sometimes become overwhelming for new programmers to figure out and use the existing decorator-based code.
>
> The java.io API is largely based on the decorator pattern, and now that you have learned it, you will be at ease while wrapping input and output streams with decorator classes, such as BufferedInputStream and DataInputStream.
>
> Also, if you feel that clients might find your decorator-based code complex, you can always encapsulate the complexity by using the Factory Method, Abstract Factory, or Builder creational patterns.

Decorator pattern also comes in use during Enterprise Application Development with the Spring Framework.

You will often need to perform tasks, such as transaction, cache synchronization, and security-related tasks. If you don’t want to risk cluttering up the business code, use decorators.

Often, use of the decorator pattern will lead you to cleaner and more maintainable code.
