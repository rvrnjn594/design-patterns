# Singleton Design Pattern

### What is Singleton?

> The singleton pattern is ont of the Gang of Four creational design patterns. In software engineering, this is a term that implies a class which can **only be instantiated once**, and a global point of access to that instance is provided.  
> In the Java programming language, there are a few standard implementations of the singleton pattern. Here are some common ways of implementing a singleton.

    public class SingletonClass {
        private static SingletonClass instance = null;
        private SingletonClass() {}
        public static SingletonClass getInstance() {
            if (instance == null) {
                instance = new SingletonClass();
            }
            return instance;
        }
    }

> In the example above, we wrote a class with a method that creates a new instance of the class if one does not exist. If an instance has already been created, the constructor simply returns a reference to that object. This is the simplest implementation of the Singleton design pattern in Java.

**Note**: that the constructor of the SingletonClass class is made private, to make sure that there is no other way to instantiate the class.
**This example is known as lazy initialization – which means that it restricts the creation of the instance until it is requested for the first time.**

There are times you may wish **to eager initialization over lazy initialization**. _This will impact your startup time performance, but it may help improve the runtime performance of your application._

Here is example of eager initialization:

    public class SingletonClass {
        private static final SingletonClass INSTANCE = new SingletonClass();
        private SingletonClass() {}
        public static SingletonClass getInstance() {
            return INSTANCE;
        }
    }

> This approach is similar to the lazy implementations, with the difference that this one **always creates an instance**, even if one is not currently necessary in the program. This might be considered a bad practice, but it's suitable for situations where the creation of the instance is not too expensive or demanding.

> This is useful when the cost of creating the object is not significant or when you know your application will always instantiate the class.

**Note:** that the final keyword here enables the class to actually be a singleton, i.e. ensures that only one instance exists.

## Advanced Examples

### Thread Safe Singletons

> The previous examples will both work just fine in a single-threaded environment. However, what happens if the program is multi-threaded?

If we have two threads, and both of them call the getInstance() method, it is possible that two individual objects of the same class get created, due to different times of accessing the (insatance == null) check.

**This is why we need to modify our implementation to make it thread-safe, and the simplest way of achieving this is by making the getInstance() class synchronized.**

> This will make it impossible for one thread to access the method if another thread hasn't finished with its execution.

**Note:** while using synchronized will ensure thread safety, be aware there is a performance cost overhead.

### Why and when to use the Singleton Design Pattern?

There are situations in which a singleton class is useful.

> Typical examples are classes that we could shortly describe as “managers”. These are classes which manage resources, read data from configuration files, instantiate threads etc. If you were making a role-playing game, you would probably have multiple “Item” objects or multiple “Level” objects, but it would make sense to make the “Score” class singleton – since you only need one object to take care of the player’s score.

> Another example could be a factory with multiple manufacturing tracks. The tracks are the ones that actually do all the work, whether it is packing cans or making car parts. However, it is the "factory manager" that decides how many tracks will be working, for how long, and what they are going to do.

    public class FactoryManager {
        private static FactoryManager instance = null;
        private FactoryManager() {}
        public static FactoryManager getInstance() {
            if (instance == null) {
                instance = new FactoryManager();
            }
            return instance;
        }
        private int numTracks = 5;
        public static void startTracks() {
            for(int i=0;i<numTracks;++i) {
                Track t = new Track();
                t.start();
            }
        }
    }

> Then, in some other part of the code, we can only "instantiate" an object of the _FactoryManger_ class by calling getInstance method.

    public static void main(String[] args) {
        FactoryManager fm = FactoryManager.getInstance();
        fm.startTracks();
    }

### Conclusion

There are some controversies concerning the usage of the singleton pattern among programmers.

> Some argue that it violates the **"single responsibility principle"**, which states that **_"every class should have responsibility over a single part of the functionality provided by the software, and that responsibility should be entirely encapsulated by the class"._**

> Singletons can also make testing more difficult. Some consider it to be a non-desirable coding practice since they introduce a global state of the program.

These are very valid arguments.

> However, Singletons are useful in situations like the ones we've discussed in the previous paragraphs, when one class needs to manage resources for other classes and when having more instances than one would truly be a design flaw.

> With prudent, moderate usage and implementations which can be optimized for thread-safety, serialization or faster performance, the singleton pattern can improve the overall readability and elegance of your code.

Lol: This design pattern should not be avoided since the Singleton Design Pattern is, after all, a classic Gang of Four design pattern.
