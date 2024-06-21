# Composite Pattern

“Compose objects into tree structures to represent part-whole hierarchies. Composite lets clients treat individual objects and compositions of objects uniformly.”

### Introduction

> As a programmer you will deal with hierarchical trees of objects at some point or other.
>
> Hierarchical tree structures can come in different flavors, and one can be a tree of components (think as objects) that can be either leaf or node.
>
> A leaf is an object that doesn’t have children, while a node does. A node can have one or more leaves or other nodes.
>
> This is called recursive composition and can be best illustrated through a file system directory structure.

![System Directory Structure](https://springframework.guru/wp-content/uploads/2015/07/HierarchialTreeStructure.png)

> A challenge in creating such hierarchical tree structures is to provide clients a uniform way to access and manipulate objects of the tree.

Clients should remain unaware whether any operation is being done on a leaf or a node, and this is where the composite design pattern comes in.

> As an example, composite design pattern can ensure that the process to add or delete a directory (node) and a file (leaf) remains the same for a user.
>
> The composite pattern is part of the classic Gang of Four structural pattern group.

**Using this pattern, you can create hierarchical object trees in a uniform manner without going through complexities, such as object casting, type evaluations, and conditional checks to see if one object is independent or contains other objects.**

### Participanta of the Composite Pattern

To understand how the composite pattern works, consider a shopping store that provides a catalog to help users browse products before buying.

> Initially, the shopping store had few products manufactured in-house. With expansion, gradually several products got added, some of which are purchased from other manufactures.
>
> For different categories of products, sub catalogs were created, and some sub catalogs further had their own sub catalogs.
>
> The requirement here is to organize the products and sub catalogs efficiently in a single main catalog.

For such requirements, we have the composite pattern.

> Let’s review what the GoF authors say about the composite pattern:
>
> > “Compose objects into tree structures to represent part-whole hierarchies”:
> >
> > > A part-whole hierarchy is composed of smaller individual objects called Parts and larger objects called Wholes that are aggregation of Parts. What the pattern says is- for part-whole hierarchies, create tree structures to represent relationships between the Parts and Wholes.
>
> > “Composite lets clients treat individual objects and compositions of objects uniformly”:
>
> > > This means that a client should be able to apply the same operations over both aggregation of objects (Wholes) and individual objects (Parts).
>
> To apply the composite pattern to the catalog example, we can create a tree structure (upside down) and model the root of the tree as an abstract class, CatalogComponent.
>
> This class defines the behavior of both individual and composite objects and acts as an interface of the tree to clients.
>
> > > Note that we could have also gone for a Java interface instead, but in the context of our example, that would force classes down the tree to provide the implementation of methods not relevant to them. >
> > >
> > > For example, a getProductDiscount() method for a product is not relevant to a catalog, and we don’t expect catalog to implement it.
>
> Next, we need to create a class to model leaves, Product and one to model nodes, ProductCatalog.

Let’s look at how our **classes map to the participants of the composite pattern**:

- Component (CatalogComponent): An abstract base class for the objects in the tree structure. This class defines the default behavior for all objects and behaviors to access and manage child components in the tree.
- Leaf(Product): Is a class that extends Component to represent leaves in the tree structure that does not have any children.
- Composite (ProductCatalog): Is a class that extends Component to represent nodes (contain children) in the tree structure. This class stores Leaf components and implements the behaviors defined in Component to access and manage child components. As mentioned earlier, child components can be one or more Leaf or other Composite components.
- Client: Interacts with Component to access and manipulate objects in the composition.

Class Hierarachy Structure of the Catalog:
![Class Hierarachy Structure](http://springframework.guru/wp-content/uploads/2015/07/ClassDiagram-1024x507.png)

> As you can see above, _Product_ and _ProductCatalog_ extend _CatalogComponent_.
>
> So, the relation here is an inheritance, _ProductCatalog_ can contain instances of _Product_ and occasionally other _ProductCatalog_, both of which are _CatalogComponent_. **This is called aggregation.**

### Applying the Composite Pattern

> To apply the composite pattern to the Catalog example, let's write the abstract base class- the **Component**.

**CatalogComponent.java**

    package guru.springframework.gof.composite;

    public abstract class CatalogComponent {
        public void add(CatalogComponent catalogComponent){
            throw new UnsupportedOperationException("Cannot add item to catalog.");
        }
        public void remove(CatalogComponent catalogComponent){
            throw new UnsupportedOperationException("Cannot remove item from catalog.");
        }
        public String getName(){
            throw new UnsupportedOperationException("Cannot return name.");
        }
        public double getPrice(){
            throw new UnsupportedOperationException("Cannot return price.");
        }
        public void print(){
            throw new UnsupportedOperationException("Cannot print.");
        }
    }

> In the CatalogComponent class above, we wrote two types of methods and it's important to identify and distinguish them:
>
> > > we wrote the composite methods add() and remove() that adds and removes CatalogComponent objects respectively. Both the methods can add or remove product from a catalog, or a catalog from the parent catalog.
>
> > > we wrote the operation methods: getName(), getPrice(), and print(). These methods apply to both Product and ProductCatalog. Although at first look, getPrice() might not seem relevant to ProductCatalog– but being good programmers, we know that we might later need to find the price of the products in a catalog.
>
> > > In the CatalogComponent class above, notice that each method throws an exception of type UnsupportedOperationException.
>
> From the composite pattern design point of view, this is not mandatory. But, we did it because some methods will only be relevant to Product, while some to ProductCatalog. For the irrelevant methods, Product and ProductCatalog don’t need to do anything- just inherit them by default. If some code unknowingly calls them, the exception will get thrown. We will next write the Leaf of the tree hierarchy.

**Product.java**

    package guru.springframework.gof.composite;

    public class Product extends CatalogComponent{
        private String name;
        private double price;
        public Product (String name, double price){
            this.name=name;
            this.price=price;
        }
        @Override
        public String getName() {
            return  this.name;
        }
        @Override
        public double getPrice() {
            return this.price;
        }
        @Override
        public void print(){
            System.out.println("Product name: "+name+" Price: "+price);
        }
    }

> The Product class that we wrote above is simple.
>
> Being a leaf, we don’t have to worry about children. So, we only extended it from CatalogComponent and provided implementations of the operation methods: getName(), getPrice(), and print().
>
> Next is the ProductCatalog class- the Composite and this is where we will implement both the operation and composite methods:

**ProductCatalog.java**

    package guru.springframework.gof.composite;
    import java.util.ArrayList;

    public class ProductCatalog extends CatalogComponent{
        private ArrayList<CatalogComponent> items=new ArrayList<>();
        private String name;
        public ProductCatalog(String name){
            this.name=name;
        }
        @Override
        public String getName() {
            return name;
        }
        @Override
        public void print(){
        for(CatalogComponent comp : items)
            {
                comp.print();
            }
        }
        @Override
        public void add(CatalogComponent catalogComponent){
            items.add(catalogComponent);
        }
        @Override
        public void remove(CatalogComponent catalogComponent){
            items.remove(catalogComponent);
        }
    }

> In the ProductCatalog class above, we instantiated an ArrayList to hold CatalogComponent objects. We then implemented the following composite methods:
>
> > > we wrote the getName() method to return the name of a CatalogComponent object.
>
> > > we wrote the print() method and used a for-each loop to traverse the products in ArrayList and print out their information.
>
> > > We also implemented the following composite methods:
>
> > > we wrote the add() method to add a CatalogComponent object, passed to it, to the ArrayList.
>
> > > we wrote the remove() method to remove a CatalogComponent object, passed to it, from the ArrayList.

Let’s write some code using JUnit to test our implementation of the
composite pattern in Java.

**CatalogComponentTest.java**

    package guru.springframework.gof.composite;

    import org.junit.Test;
    import static org.junit.Assert.*;

    public class CatalogComponentTest {
        @Test
        public void testPrint() throws Exception {
            /*Create primary products for main catalog*/
            CatalogComponent mJeanProduct=new Product("M: Jeans 32", 65.00);
            CatalogComponent mTShirtProduct=new Product("M: T Shirt 38", 45.00);

            /*Create a composite product catalog and add female products to it*/
            CatalogComponent newCatalog = new ProductCatalog("Female Products");
            CatalogComponent fJeans=new Product("F: Jeans 32", 65.00);
            CatalogComponent fTShirts=new Product("F: T Shirt 38", 45.00);
            newCatalog.add(fJeans);
            newCatalog.add(fTShirts);

            /*Create a composite product catalog and add kid products to it*/
            CatalogComponent kidCatalog = new ProductCatalog("Kids Products");
            CatalogComponent kidShorts=new Product("Return Gift", 23.00);
            CatalogComponent kidPlayGears = new Product("Summer Play Gear", 65.00);
            kidCatalog.add(kidShorts);
            kidCatalog.add(kidPlayGears);

            /*Create primary catalog and add primary products and new catalogs to it*/
            CatalogComponent mainCatalog=new ProductCatalog("Primary Catalog");
            mainCatalog.add(mJeanProduct);
            mainCatalog.add(mTShirtProduct);
            mainCatalog.add(newCatalog);
            mainCatalog.add(kidCatalog);

            /*Print out product/catalog information*/
            mainCatalog.print();
        }
    }

> -In the test class above, we instantiated and initialized some Product objects and added few of them to ProductCatalog objects.
>
> -We then added independent Product and composite ProductCatalog objects, to a main catalog.
>
> - Finally, we called print() to print out information on all products. In the code, notice that we called the add() method on both Product and ProductCatalog, which internally are different- Leaf vs Node. Doing so, we can say that our client can “treat individual objects and compositions of objects uniformly”.

When you run the code above, you will see this output:

    ---
    T E S T S
    ---
    Running guru.springframework.gof.composite.CatalogComponentTest
    Product name: M: Jeans 32 Price: 65.0
    Product name: M: T Shirt 38 Price: 45.0
    Product name: F: Jeans 32 Price: 65.0
    Product name: F: T Shirt 38 Price: 45.0
    Product name: Return Gift Price: 23.0
    Product name: Summer Play Gear Price: 65.0
    Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.034 sec - in guru.springframework.gof.composite.CatalogComponentTest

### Conclusion

**The essence of composite pattern is the ability for a client to perform operations on an object without knowing if there are any nested objects.**

But like all good things, composite pattern too comes with a price.

> The pattern can make your design overly general.
>
> > In the composite pattern, you have to define the composite methods at the root of the class hierarchy, and clients might mistakenly attempt to add or remove objects from leaf objects. But, keep in mind that design patterns do not come out of the box ready for any scenario.
>
> > Design patterns are just a starting point to work from. You will need to adapt them to your requirements. That is what we exactly did by making the root component an abstract class and throwing exceptions in its composite methods.

When using the composite pattern during Enterprise Application Development with the Spring Framework, you might encounter the exception of type **BeanCreationException** during constructor injection because of circular references.

> This is not Spring-specific and the problem can also arise in plain Java using regular constructors- Constructor invocations should be done in proper order.
>
> An alternative in Spring is to configure the components through setter injections to break any cyclic reference required during construction.
