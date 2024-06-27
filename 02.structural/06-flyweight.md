# Flyweight Pattern

"Use sharing to support large numbers of fine-grained objects efficiently."

### Introduction

> As a Java programmer you’ve probably heard to “Think in terms of objects”.
>
> **_Designing objects to the lowest levels of system “granularity” promote flexibility in the application._**
>
> But, as a good programmer **you also need to think about the performance of the application,** in terms of the amount of resources required to run it.
>
> When you are dealing with a large number of objects, you need to be concerned about the resources the objects will consume. Each will need memory, and creating each will need CPU cycles.

Imagine you need 1000 car objects in an online car racing game, and the car objects vary only in their current position on the race track.  
Instead of creating 1000 car objects and adding more as users join in, you can create a single car object with common data and a client object to maintain the state (position of a car).

**The Flyweight Pattern** reduces repeated data, thus reduces memory consumption when dealing with large numbers of objects.

> The Flyweight pattern is a part of the classic Gang of Four structural pattern family. We have already learned about some of the other patterns of this family: Adapter, Bridge, Composite, and Decorator.

The Flyweight pattern, similar to those patterns is concerned with relationship between classes and objects, but through a whole new concept of object sharing.

> What this pattern says is that if an application requires a large number of fine-grained objects, share them instead of repeated instantiation. The object you share is referred as a flyweight.

To become familiar with the Flyweight pattern, **a concept to understand is how the internal state of a flyweight is represented**.

You can categorize the internal state into:

- **Intrinsic data:** Information that is stored in the flyweight object.
  The information is independent of the flyweight’s context, thereby making it sharable. While applying the pattern, you take all of the objects that have the same intrinsic data and replace them with a single flyweight.
- **Extrinsic data:** Information that depends on the flyweight’s context and hence cannot be shared.
  Client objects stores and computes extrinsic data and passes it to the flyweight when it needs it.

### Participants of the Flyweight Pattern

> Continuing with the car racing example, consider we need two types of cars: **_Midget and Sprint race cars_**. We can create an abstract base class RaceCar with three fields:
>
> - name of type String for the name of the car
> - speed of type int for the maximum car speed
> - horsepower of type int for the engine power
>
> Objects of a concrete subclass, such as FlyweightMidgetCar will store the same set of values for the preceding fields.

    . . .
    name="Midget Car";
    speed=140;
    horsepower=400;
    . . .

> This means, these values can be shared between various FlyweightMidgetCar objects, and so we consider them as intrinsic data. This is common data that will not change and can be shared with other objects.
>
> > > Consider in the base class, we have a moveCar (int currentX, int currentY, int newX ,int newY) method that we override in the FlyweightMidgetCar subclass.
>
> > > This method defines the position of each FlyweightMidgetCar object at a given time, and hence cannot be shared. So we consider the car position passed to moveCar() as extrinsic data.

Now that we have categorized the internal states of FlyweightMidgetCar into intrinsic and extrinsic data, we can make it a flyweight object.

Similarly, we can make FlyweightSprintCar or any other object that extend RaceCar as flyweight objects.

To manage our flyweight objects, we need **a factory – a class that uses a pool (implemented as some data structure) to store flyweight objects.**

> When client request a flyweight object for the first time, the factory instantiates a flyweight, initializes it with its intrinsic data, and puts it in the pool, before returning the flyweight to the client.
>
> For subsequent requests, the factory retrieves the flyweight from the pool and returns it to the client. We will name the class CarFactory.
>
> We will also need a client class, RaceCarClient that will retrieve a flyweight (already initialized with its intrinsic data) from the factory and pass the extrinsic data to the flyweight.
>
> In the context of our example, the client will manage and pass the position (extrinsic data) of a car by calling the moveCar(int currentX, int currentY, int newX ,int newY) method of the flyweight.

#### We can now categorize the participants of the Flyweight pattern as:

- **Flyweight (RaceCar):** Abstract class or interface for flyweight objects.
  Declares method through which flyweights can receive and act on extrinsic state. Although Flyweight enables sharing, it is not mandatory that all Flyweight subclasses must be shared.
- **ConcreteFlyweight(FlyweightMidgetCar and FlyweightSprintCar):** Extends/Implements Flyweight to represent flyweight objects that can be shared.
- **FlyweightFactory(CarFactory):** Creates and manages flyweight objects. When a client requests a flyweight object, FlyweightFactory provides an existing one or creates a new one, if it does not exists.
- **Client(RaceCarClient):** Requests FlyweightFactory for a flyweight object, and then computes and passes the extrinsic data to it.

### Applying the Flyweight Pattern

To apply the flyweight pattern to our example, let’s first write the Flyweight.

**RaceCar.java**

    package guru.springframework.gof.flyweight;

    public abstract class RaceCar {
        /_Intrinsic state stored and shared in the Flyweight object_/
        String name;
        int speed;
        int horsePower;
        /_ Extrinsic state is stored or computed by client objects, and passed to the Flyweight._/
        abstract void moveCar(int currentX, int currentY, int newX ,int newY);
    }

> In the RaceCar abstract class, we wrote the fields to hold the intrinsic data and an abstract moveCar() method through which the client will pass extrinsic data to flyweight objects.
>
> Notice how the extrinsic data are values that will not change. The horsepower of the car is not a value that will change while application is in use. Our race cars can have different horsepower values, but once set, this is not something what will be changing.

Let’s write the concrete FlyweightMidgetCar and FlyweightSprintCar classes.

**FlyweightMidgetCar.java**

    package guru.springframework.gof.flyweight;

    public class FlyweightMidgetCar extends RaceCar {
        /_Track number of flyweight instantiation_/
        public static int num;
        public FlyweightMidgetCar() {
            num++;
        }
        /*This method accepts car location (extrinsic). No reference to current
        *or new location is maintained inside the flyweight implementation*/
        @Override
        public void moveCar(int currentX, int currentY, int newX ,int newY) {
            System.out.println("New location of "+this.name+" is X"+newX + " - Y"+newY);
        }
    }

**FlyweightSprintCar.java**

    package guru.springframework.gof.flyweight;

    public class FlyweightSprintCar extends RaceCar{
        /*Track number of flyweight instantiation*/
        public static int num;
        public FlyweightSprintCar() {
            num++;
        }
        /*This method accepts car location (extrinsic). No reference to current or new location is maintained inside the flyweight implementation*/
        @Override
        public void moveCar(int currentX, int currentY, int newX ,int newY) {
            System.out.println("New location of "+this.name+" is X"+newX + " - Y"+newY);
        }
    }

> In both the classes above, we maintained a counter through their constructor that will later enable us to check the number of flyweight objects created.
>
> We have also overridden the moveCar() method to output the new location of a car. NOTE
>
> – Notice how the moveCar() method does not store state in the flyweight object. Let’s now write the CarFactory class – the FlyweightFactory.

**CarFactory.java**

    package guru.springframework.gof.flyweight;

    import java.util.HashMap;
    import java.util.Map;
    public class CarFactory {
        private static Map<String, RaceCar> flyweights = new HashMap<>();
        /_If key exist, return flyweight from Map_/
        public static RaceCar getRaceCar(String key) {
            if(flyweights.containsKey(key)){
                return flyweights.get(key);
            }
            RaceCar raceCar;
            /_If key does not exist in Map, create flyweight, put it in Map, and return the object_/
            switch (key) {
                case "Midget":
                    raceCar = new FlyweightMidgetCar();
                    raceCar.name="Midget Car";
                    raceCar.speed=140;
                    raceCar.horsePower=400;
                    break;
                case "Sprint":
                    raceCar = new FlyweightSprintCar();
                    raceCar.name="Sprint Car";
                    raceCar.speed=160;
                    raceCar.horsePower=1000;
                    break;
                default:
                    throw new IllegalArgumentException("Unsupported car type.");
            }
            flyweights.put(key, raceCar);
            return raceCar;
        }
    }

> In the CarFactory class, we created a HashMap to store flyweight (RaceCar) objects and written a getRaceCar() method- a factory method that returns a flyweight object.
>
> In the factory method, we checked the HashMap for the client specified key. If an entry is present, we returned the corresponding flyweight object.
>
> If an entry is not present, we used a switch statement to create and initialize a flyweight object, which can either be FlyweightMidgetCar or FlyweightSprintCar, based on the client specified value.
>
> We then stored the object in the HashMap, and finally returned the object to the client.

**RaceCarClient.java**

    package guru.springframework.gof.flyweight;

    public class RaceCarClient {
        private RaceCar raceCar;
        public RaceCarClient(String name) {
           /_Ask factory for a RaceCar_/
            raceCar = CarFactory.getRaceCar(name);
        }
        \* The extrinsic state of the flyweight is maintained by the client */

        private int currentX = 0;
        private int currentY=0;
        public void moveCar(int newX, int newY){
            /_Car movement is handled by the flyweight object_/
            raceCar.moveCar(currentX, currentY, newX, newY);
            currentX = newX;
            currentY = newY;
        }
    }

> In the client class above, we have a reference to a flyweight object and initialized it in the constructor through a call to the getRaceCar() factory method.
>
> Once we got the shared flyweight object, we calculated the current position (extrinsic data) and passed it to the flyweight object through a call to the moveCar() method of the object.
>
> Notice how the client code is responsible for maintaining the state of the individual object.
>
> If state was not maintained in the client object, but in the flyweight object, the changes would be seen by all objects having a reference to the flyweight object.
>
> Clearly this is not what we want. Things like location will change for each client object, while things like horsepower do not change and can be shared across the client objects. Let’s write some test code to see how this behaves in actual use.

    package guru.springframework.gof.flyweight;

    import org.junit.Test;
    import static org.junit.Assert.*;
    public class RaceCarClientTest {
        @Test
        public void testRaceCar() throws Exception {
            RaceCarClient raceCars[] = {
            new RaceCarClient("Midget"),
            new RaceCarClient("Midget"),
            new RaceCarClient("Midget"),
            new RaceCarClient("Sprint"),
            new RaceCarClient("Sprint"),
            new RaceCarClient("Sprint")
        };
        raceCars[0].moveCar(29, 3112);
        raceCars[1].moveCar(39, 2002);
        raceCars[2].moveCar(49, 1985);
        raceCars[3].moveCar(59, 2543);
        raceCars[4].moveCar(69, 2322);
        raceCars[5].moveCar(79, 2135);
        /*Output and observe the number of instances created\*/
        System.out.println("Midget Car Instances: " + FlyweightMidgetCar.num);
        System.out.println("Sprint Car Instances: " + FlyweightSprintCar.num);
        }
    }

> In the test code we created an array of RaceCarClient with six RaceCarClient instances and called the moveCar() method on each instance. We also printed out the number of FlyweightMidgetCar and FlyweightSprintCar instances created.

When you run the code above, you will see this output:

    ---

    ## T E S T S

    Running guru.springframework.gof.flyweight.RaceCarClientTest
    New location of Midget Car is X29 - Y3112
    New location of Midget Car is X39 - Y2002
    New location of Midget Car is X49 - Y1985
    New location of Sprint Car is X59 - Y2543
    New location of Sprint Car is X69 - Y2322
    New location of Sprint Car is X79 - Y2135
    Midget Car Instances: 1
    Sprint Car Instances: 1
    Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.01 sec - in guru.springframework.gof.flyweight.RaceCarClientTest
    In the output above, notice that even though there are six moving cars: three midget and three sprint cars, only one instance for each got created.

### Conclusion

The flyweight pattern might appear complex to some, and I agree it is one of the lesser used design patterns.

Its probably used even less as computers have become more and more powerful.

But when you’re dealing with large scale systems, the Flyweight Pattern is something to consider.

It can help save system resources. There’s a lot of buzz about ‘Big Data’ in the industry. What happens when you’re processing big data?? You may see a use case for the Flyweight Pattern.

When you are developing Enterprise Applications with the Spring Framework, **if you are in situations where you have a large amount of objects sharing some common state and consuming large amount of memory,** the Flyweight pattern may be exactly what you are looking for.
