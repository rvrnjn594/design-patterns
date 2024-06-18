# Prototype Pattern

> "Specify the kinds of objects to create using a prototypical instance, and create new objects by copying this prototype."

The prototype pattern is a classic Gang of Four creational pattern, and similar to the other members of the creational pattern family: singleton , factory method, abstract factory, and builder, _prototype is also concerned with object creation, but with a difference_.

> Using the prototype pattern, you do not create a new object for each client requesting the object.
> **_Instead, you start by creating a single object, called a prototype and make copies of it for each client requesting the object_**.
> In Java, this is achieved through object cloning, a way to make a copy of an object with the same state as the original object.

But why create copies if we can create new objects through constructor calls, which is much simpler?

> Most of the time you will not need to create copies of objects. But, as you move into enterprise application development where application performance is critical, you will encounter situations where construction of an object involves time consuming operations, such as network communication, database reads, and disk I/O.
> If a large number of such objects needs to be created, you can avoid repeating those steps for each object by initially creating a prototype and then making copies of it.

While the prototype pattern is itself simple, **_it is important to learn the intricacies involved in Java object cloning before you start applying the prototype pattern to applications_**.

### Object Cloning: Shallow Copy vs Deep Copy

**_Java provides the Cloneable interface to mark objects that permit cloning. This interface is a marker interface and therefore does not contain any method declaration._**

> When implemented in a class, Cloneable marks that objects of the class can be cloned.
> To perform cloning, you need to call the protected clone() method of the Object class through a call to super.clone().

**Note:** If an object calls super.clone() but does not implements Cloneable, the method throws an exception of type, CloneNotSupportedException.

> A call to super.clone() performs a shallow copy where all the fields values of the original object are copied to the new object.
>
> > If a field value is a primitive type, a shallow copy copies the value of the primitive type.
> > But, if a field value is a reference type, then only the reference is copied, and not the referred object itself.
> >
> > > **_Therefore, both the original and its clone refer to the same object and if either one modifies the referred object, the modification will be visible to the other. This might result in unexpected behavior in an application._**
>
> > **In such situation, you should perform a deep copy that makes copies of dynamically allocated memory pointed to by the reference type fields.**
>
> > > In a deep copy, the original and the copied objects are independent of each other and therefore the objects can update their own fields without worrying about any referencing problems.

The difference between shallow and deep copy is illustrated in this figure:  
![Shallow vs Deep Copy](https://springframework.guru/wp-content/uploads/2015/04/4-23-2015-1-55-29-AM.jpg)  
Important distinction to remember:

(Important difference to remember)

> In the figure above, an object X references an object O. A shallow copy of X creates a new object X2 that also references object O. In contrast, a deep copy of X creates a new object X2 that references the new object O2(copy of O).

When using the prototype pattern, should we go for shallow copy or for deep copy?

> There is no hard and fast rule, it all depends on the requirement.
>
> > If an object has only primitive fields or immutable objects (whose state cannot change, once created), use a shallow copy.
> > When the object has references to other mutable objects, then either choose shallow copy or deep copy.
>
> For example, if the references are not modified anytime, avoid deep copy and go for shallow copy. But, if you know that the references will be modified and the modification might affect the intended behavior of the application, then you need to go for deep copy.
>
> > When you go for a deep copy, you will need to override the Object.clone() method in all the member classes and then recursively clone their objects.
>
> > Alternatively, you can get a deep copy by serializing an object and then restoring it back through Java object serialization.
> >
> > > > Using serialization is a simple approach, keep in mind serialization is resource intensive and can produce unexpected results if not done properly.

### Example of the Prototype Pattern

#### Participants of the Prototype Pattern

To understand how the prototype pattern works, **_consider a content production house that employs vendors to write content for the organization._**

> For each project assigned to a vendor, the HR department provides the vendor a terms and conditions and a non-disclosure agreement that the vendor must accept before commencing work.
> The content of the agreements remains same for all vendors and an HR employee only needs to fill in the vendor name before sending an agreement to the vendor.
> Assuming that the agreement content are stored in a remote database, you can **apply the prototype pattern to avoid making a network trip and a database read each time the HR employee needs to create an agreement.**

To implement the requirements of the agreement creation example, you can **_create an abstract class_**, named **PrototypeCapableDocument** that implements Cloneable.

> This class will act as the base class for all classes that represent agreements and that allows its objects to be cloned.
> Next, you can model the terms and conditions and non-disclosure agreements with the TAndC and NDAgreement classes respectively.
> As a non-disclosure agreement is signed by both parties, you can create an AuthorizedSignatory class to represent the signing authority of the content production house and make it a member of the NDAgreement class.
> Finally, you can create a DocumentPrototypeManager class that will create and store the prototype objects and return a copy whenever a client asks for an agreement.
> The client will receive a copy of the prototype object and the HR manager will be able to update the copy with the vendor name to create an agreement.

Summary of the participants of the prototype pattern in the context of the agreement creation example as:

- Prototype (PrototypeCapableDocument): Is a Java interface or abstract class that defines the contract for class that permits cloning of its objects.
- ConcretePrototype(TAndC and NDAgreement): Are classes that provide operations to clone its objects.
- PrototypeManager(DocumentPrototypeManager): A class that implements a registry to manage available prototypes for clients. On a client request, this class creates a copy of a prototype.
- Client: Asks the PrototypeManager for copies of prototypes.

### Applying the Prototype Pattern

To apply the prototype pattern to the agreement creation example, let us first create the prototype as an abstract class.

**PrototypeCapableDocument.java**

    package guru.springframework.gof.prototype;

    public abstract class PrototypeCapableDocument implements Cloneable {
        private String vendorName;
        private String content;
        // getters and setters
        public String getVendorName() {
            return vendorName;
        }
        public void setVendorName(String vendorName) {
            this.vendorName = vendorName;
        }
        public String getContent() {
            return content;
        }
        public void setContent(String content) {
            this.content = content;
        }
        // prototype as an abstract class
        public abstract PrototypeCapableDocument cloneDocument() throws CloneNotSupportedException;
    }

> In the example above, we wrote a **_PrototypeCapableDocument_** class that implements the **Cloneable** interface. In the class, we defined the vendorName and content fields with their corresponding getter and setter methods.
> We also defined an abstract **cloneDocument()** method that subclasses will override to return clones of its objects.
> Next, we will create the subclass, TAndC, which is one of **ConcretePrototype** components in the example.

**TAndC.java**

    package guru.springframework.gof.prototype;

    public class TAndC extends PrototypeCapableDocument {
        @Override
        public PrototypeCapableDocument cloneDocument() {
            /*Clone with shallow copy*/
            TAndC tAndC = null;
            try {
                tAndC = (TAndC) super.clone();
            } catch (CloneNotSupportedException e) {
                e.printStackTrace();
            }
            return tAndC;
        }
        @Override
        public String toString() {
            return "[TAndC: Vendor Name - " + getVendorName() + ", Content - " + getContent() + "]";
        }
    }

> In the examples above, we wrote the TAndC class that extends PrototypeCapableDocument and overrides the cloneDocument() method.
> In this method, we made a call to super.clone(), which will result in a shallow copy of a TAndC, which will result in a shallow copy of a TAndC object at run time. We do not require a deep copy of a TAndC object because its fields, vendorName and content (inherited from PrototypeCapableDocument) are of the type String, which is immutable.

> Next, let's create the NDAgreement and AuthorizedSignatory classes. At this point we need to decide on the type of cloning that we should use.
> NDAgreement is concrete prototype composed of AuthorizedSignatory. If we go for shallow copy, both the prototype and its cloned objects will point to the same AuthorizedSignatory object.
>
> > In our example, we want to prevent any referencing problems later by performing a deep copy operation. In order to do use a deep copy operation, we will need to ensure that AuthorizedSignatory permits itself to be cloned.

**AuthorizedSignatory.java**

    package guru.springframework.gof.prototype;

    public class AuthorizedSignatory implements Cloneable {
        // class members
        private String name;
        private String designation;
        // setters and getters
        public String getName() {
            return name;
        }
        public void setName(String name) {
            this.name = name;
        }
        public String getDesignation() {
            return designation;
        }
        public void setDesignation(String designation) {
            this.designation = designation;
        }
        @Override
        protected Object clone() throws CloneNotSupportedException {
            return super.clone();
        }
        @Override
        public String toString() {
            return "[AuthorizedSignatory: Name - " + getName() + ", Designation - " + getDesignation() + "]";
        }
    }

> In the example above, the AuthorizedSignatory class implements Cloneable. In the class, we defined the name and designation fields of type String with their corresponding getter and setter methods.
> In the overridden clone() method, we made a call to super.clone() to obtain and return back a shallow copy of AuthorizedSignatory to the caller.
> In our example, the caller will be NDAgreement, which will create now.

**NDAgreement.java**

    package guru.springframework.gof.prototype;

    public class NDAgreement extends PrototypeCapableDocument {
        private AuthorizedSignatory authorizedSignatory;
        public AuthorizedSignatory getAuthorizedSignatory() {
            return authorizedSignatory;
        }
        public void setAuthorizedSignatory(AuthorizedSignatory authorizedSignatory) {
            this.authorizedSignatory = authorizedSignatory;
        }
        @Override
        public PrototypeCapableDocument cloneDocument() throws CloneNotSupportedException {
        /*Clone with deep copy*/
            NDAgreement nda;
            nda = (NDAgreement) super.clone();
            AuthorizedSignatory clonedAuthorizedSignatory = (AuthorizedSignatory) nda.getAuthorizedSignatory().clone();
            nda.setAuthorizedSignatory(clonedAuthorizedSignatory);
            return nda;
        }
        @Override
        public String toString() {
            return "[NDAgreement: Vendor Name - " + getVendorName() + ", Content - " + getContent() + ", Authorized Signatory - " + getAuthorizedSignatory() + "]";
        }
    }

> In the example above, we wrote the NDAgreement class that extends PrototypeCapableDocument. In the class, we added a field of the type AuthorizedSignatory along with its getter and setter methods.
> In the overridden cloneDocument() method, we first made a call to super.clone() for a shallow copy of NDAgreement.
> Then we created a copy of AuthorizedSignatory and set it on the shallow copy of NDAgreement.
> Finally, we returned the NDAgreement copy to the caller.

**These are the prototype classes we will use in our implementation of the prototype design pattern.**  
Next let’s create the DocumentPrototypeManager class, which is the PrototypeManager in our example.

**DocumentPrototypeManager.java**

    package guru.springframework.gof.prototype;

    public class DocumentPrototypeManager {
        private static java.util.Map<String, PrototypeCapableDocument> prototypes = new java.util.HashMap<String, PrototypeCapableDocument>();
        static {
            TAndC tAndC = new TAndC();
            tAndC.setVendorName("Vendor Name Placeholder");
            /*Retrieve Terms and Conditions from database/network call/disk I/O here*/
            tAndC.setContent("Please read and accept the terms and conditions...");
            prototypes.put("tandc", tAndC);
            AuthorizedSignatory authorizedSignatory = new AuthorizedSignatory();
            authorizedSignatory.setName("Andrew Clark");
            authorizedSignatory.setDesignation("Operation Head");
            NDAgreement nda = new NDAgreement();
            nda.setVendorName("Vendor Name Placeholder");
            /*Retrieve Non Disclosure Agreement from database/network call/disk I/O here*/
            nda.setContent("Please read and accept the NDA...");
            nda.setAuthorizedSignatory(authorizedSignatory);
            prototypes.put("nda", nda);
        }
        public static PrototypeCapableDocument getClonedDocument(final String type) {
            PrototypeCapableDocument clonedDoc = null;
            try {
                PrototypeCapableDocument doc = prototypes.get(type);
                clonedDoc = doc.cloneDocument();
            } catch (CloneNotSupportedException e) {
                e.printStackTrace();
            }
            return clonedDoc;
        }
    }

> In the example above, we wrote the **DocumentPrototypeManager** class. In this class, we created an object of type Map that acts as a registry of the prototypes. We then used a static initialization block to create and add the prototypes (TAndC and NDAgreement) to the Map.
> Finally, we added a getCloneDocument() static method. This method based on the parameter retrieves a prototype from the Map, calls the cloneDocument() method on the prototype for a copy of it, and returns the copy to the caller.

### Testing the Prototype Pattern

Let's write a Unit test and observe the prototype pattern at work.
**DocumentPrototypeManagerTest.java**

    package guru.springframework.gof.prototype;
    import org.junit.Test;
    import static org.junit.Assert.*;
    public class DocumentPrototypeManagerTest {
        @Test
        public void testGetClonedDocument() throws Exception {
            PrototypeCapableDocument clonedTAndC = DocumentPrototypeManager.getClonedDocument("tandc");
            clonedTAndC.setVendorName("Mary Parker");
            System.out.println(clonedTAndC);
            PrototypeCapableDocument clonedNDA = DocumentPrototypeManager.getClonedDocument("nda");
            clonedNDA.setVendorName("Patrick Smith");
            System.out.println(clonedNDA);
        }
    }

When you run the code above, you will see this output:

    -------------------------------------------------------
    T E S T S
    -------------------------------------------------------
    Running guru.springframework.gof.prototype.DocumentPrototypeManagerTest
    [TAndC: Vendor Name - Mary Parker, Content - Please read and accept the terms and conditions...]
    [NDAgreement: Vendor Name - Patrick Smith, Content - Please read and accept the NDA..., Authorized Signatory - [AuthorizedSignatory: Name - Andrew Clark, Designation - Operation Head]]
    Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0 sec - in guru.springframework.gof.prototype.DocumentPrototypeManagerTest

### Conclusion

The prototype pattern is a simple pattern but a common drawback to using it is with the complexities involved in making objects copies.

> When an object refers other objects, some of which in turn refer other objects.
> While this is true to some extent, there are circumstances where the benifits that prototype provide far outwieght the inherent complexities of object copying.
> Especially in enterprises application development, you will encounter situations where copying an object can be significantly efficient than creating a new object.

In such situations, use the protype pattern. It is after all, it is a classic GoF design pattern.
