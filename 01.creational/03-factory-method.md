# Factory Method Design Pattern

### What is Factory Method Pattern?

> In Java applications, you might be often using the new operator to create an object of a class. This is often fine for small Java programs. But when you work on large scale enterprise class applications, the amount of code to create objects will gradually increase and will become scattered across the application. If class names are hard coded in such code, the complexities of managing the code will keep increasing as you add new classes to the application.

**To address such concerns, you can use the factory method pattern.**  
This pattern is a classic Gang of Four creational design pattern that is concerned with the creation of objects in an application. As the name suggests, **the factory method pattern makes use of classes that acts as factories to create objects**.

##### **_This pattern favors method invocation instead of making direct constructor calls to create objects_**.

> **_In the factory method pattern, you provide an interface, which can be a Java interface or an abstract class to create objects_**.
> **A factory method in the interface defers the object creation to one or more concrete subclasses at run time**. The subclasses implement the factory method to select the class whose objects need to be created.

### Participants in the Factory Method Pattern

To understand how the factory method pattern works, consider a pizza store. In an application, you can model the pizzas as concrete Pizza objects, such as CheezePizza, PepperoniPizza, and VeggiePizza. Just as in any pizza store, a customer can only order a pizza and not make it. In the application, you can create an abstract class, say BasePizzaFactory with a factory method to create a pizza.  
You can then create a subclass of BasePizzaFactory, called PizzaFactory to implement the factory method. In the factory method, you can create and return a proper Pizza object. The object returned will be proper subtype, and configured for use in your code.

**The components of the factory method pattern in the context of the pizza store can be summarized as:**

- Product (Pizza): Is an interface or an abstract class whose subclasses are instantiated by the factory method.
- ConcreteProduct (CheesePizza, PepperoniPizza, and VeggiePizza): Are the concrete subclasses that implement/extend Product. The factory method instantiates these subclasses.
- Creator (BaseFactoryPizza): Is an interface or an abstract class that declares the factory method, which returns an object of type Product.
- ConcreteCreator (PizzaFactory): Is a concrete class that implements the factory method to crete and return a ConcreteProduct to Client.
- Client: Asks the Creator for a Product.

A Client that requires a ConcreteProduct does not create any object but instead asks the Creator for it. The ConcreteCreator implements the factory method to create the object transparently from the Client
As a result, the Client is not required to be aware of any ConcreteProduct and how they are created. This approach advocates the OOP principle: **"Program to an interface, not to an implementation"**, which leads to polymorphism, a key feature of OOP.  
In addition, as object creation is centralized in the ConcreteCreator, any changes made to a Product or any ConcreteProduct does not affect the Client.

### [Applying the Factory Method Pattern](https://springframework.guru/gang-of-four-design-patterns/factory-method-design-pattern/)

To apply the factory method pattern in the pizza store application, let us first create the abstract Pizza class and its subclasses.

    //package guru.springframework.gof.factoryMethod;
    public abstract class Pizza {
        public abstract void addIngredients();
        public void bakePizza() {
        System.out.println("Pizza baked at 400 for 20 minutes.");
        }
    }

    //package guru.springframework.gof.factoryMethod;
    public class CheesePizza extends Pizza {
        @Override
        public void addIngredients() {
        System.out.println("Preparing ingredients for cheese pizza.");
        }
    }

    //package guru.springframework.gof.factoryMethod;
    public class PepperoniPizza extends Pizza {
        @Override
        public void addIngredients() {
            System.out.println("Preparing ingredients for pepperoni pizza.");
        }
    }

    //package guru.springframework.gof.factoryMethod;
    public class VeggiePizza extends Pizza {
        @Override
        public void addIngredients() {
            System.out.println("Preparing ingredients for veggie pizza.");
        }
    }

See the example described [here](https://springframework.guru/gang-of-four-design-patterns/factory-method-design-pattern/).

> In the example above, we wrote the Pizza class with an abstract addIngredient() method and defined a bakePizza() method. We then wrote three subclasses: CheezePiza, PepperoniPizza, and VeggiePizza that provide their own implementation of the addIngredient() method. The subclasses being derived from Pizza inherit the bakePizza() method defined in Pizza.  
> Next, we will create the abstract BaseFactory class, which is the Creator in the application.

     //package guru.springframework.gof.factoryMethod;
    public abstract class BasePizzaFactory {
        public abstract Pizza createPizza(String type);
    }

> In the example, we wrote the BasePizzaFactory class with a createPizza() factory method. Notice that we have declared the createPizza() method as abstract. We will provide the factory method implementation in a concrete subclass, PizzaFactory, which will be ConcreteCreator of the application.

    //package guru.springframework.gof.factoryMethod;
    public class PizzaFactory extends BasePizzaFactory{
        @Override
        public Pizza createPizza(String type){
            Pizza pizza;
        switch (type.toLowerCase()) {
            case "cheese":
                pizza = new CheesePizza();
                break;
            case "pepperoni":
                pizza = new PepperoniPizza();
                break;
            case "veggie":
                pizza = new VeggiePizza();
                break;
            default: throw new IllegalArgumentException("No such pizza.");
        }
        pizza.addIngredients();
        pizza.bakePizza();
        return pizza;
        }
    }

> In the example above, we wrote the PizzaFactory class and implemented the createPizza() factory method in it. In the createPizza() method, we used a switch statement to create a Pizza object based on the parameter passed to the method. We then made calls to the addIngredients() and bakePizza() methods on the Pizza object before returning it to the caller.

###### Unit test

> Now, let us write a unit test for our example class to observe the factory method pattern at work. In our test, we make two calls to the factory method with different String parameters. The parameter tells our factory what kind of pizza we want.

    import org.junit.Test;
    public class PizzaFactoryTest {
        @Test
        public void testMakePizzas(){
        BasePizzaFactory pizzaFactory = new PizzaFactory();
        Pizza cheesePizza = pizzaFactory.createPizza("cheese");
        Pizza veggiePizza = pizzaFactory.createPizza("veggie");
        }
    }

> As you can see in the above example, we have not created any concrete Pizza objects in the code. Therefore, if we enhance the Pizza implementation classes in a newer release, we do not require updating any client or test code. We can safely rely on the Creator to provide us the enhanced object. When you run the code above, you will see this output:

    -------------------------------------------------------
    T E S T S
    -------------------------------------------------------
    Running guru.springframework.gof.factoryMethod.PizzaFactoryTest
    Preparing ingredients for cheese pizza.
    Pizza baked at 400 for 20 minutes.
    Preparing ingredients for veggie pizza.
    Pizza baked at 400 for 20 minutes.
    Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.346 sec - in g
    uru.springframework.gof.factoryMethod.PizzaFactoryTest

### Conclusion

Java APIs and frameworks extensively use the factory method pattern.  
You should also consider using it because of the advantages it provides.  
The key advantage, that you have already learned, is how the factory method pattern helps encapsulate object creation code from the client code.  
This decouples your client code from the concrete classes you need to instantiate. Therefore, while developing applications, if you see an oppurtunity to encapsulate object creation code, apply the factory method pattern. Also, by using this pattern you will have a centeralized location for object creation code, which will make it easy for you to debug and troubleshoot....
