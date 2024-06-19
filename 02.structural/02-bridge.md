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

### Participants of the Bridge Pattern

To understand how the bridge pattern works, consider a messaging application that clients can use to send differnt types of messages, such as a text or an email message.

> The most intuitive approach is to first create an interface or an abstract base class, Message.  
> Next, we create the derived classes: TextMessage and EmailMessage.  
> Finally, to send messages, we create two messages sender classes:  
> -TextMessageSender, that extends TextMessage and EmailMessageSender that extends EmailMessage.

This is how our inheritance hierarchy looks like:  
![Abstraction and Implementation of the classes.](https://springframework.guru/wp-content/uploads/2015/06/Bridge01.png)

> At first sight, there appears nothing wrong in the design above. But if you look deep you will notice that the abstraction part- _the part that clients interact with_, and the implementation part- _the part that provides the core functionality of sending messages_, are tightly integrated.  
> Our design relies on inheritance and one inherent disadvantage is that it breaks encapsulation.  
> As a developer of the EmailMessageSender subclass, you have to know about the internals of the EmailMessage superclass, which means the encapsulation in the superclass is broken.

Our design is also fragile.  
 As an example, if we change the implementation to allow clients to optionally encrypt the message before sending, we will need to update the abstraction part of the encryption functionality available to clients.

Another issue is reusability.  
 If we want to reuse only the implementation(message sending) part in some other application, we will have to take along the abstraction part as extra baggage.

**The bridge pattern addresses all such issues by seperating the abstraction and implementation into two class hierarchies.**
![Design with and without bridge pattern](https://springframework.guru/wp-content/uploads/2015/06/Bridge02.png)

- With the bridge pattern, **the abstraction maintains a Has-A relationship with the implementation instead of a IS-A relationship.**
- The Has-A relationship is achieved through composition where the abstraction maintains a reference of the implementation and forwards client requests to it.

Let's summarize the participants of the bridge pattern in the context of the messaging example:

- Abstraction (Message): Is the interface implemented as an abstract class that clients communicate with.
- RefinedAbstraction (TextMessage and EmailMessage): Are classes that implement or extend Abstraction.
- Implementor (MessageSender): Is the interface of the implementation class hierarchy.
- ConcreteImplementor (TextMessageSender and EmailMessageSender): Are concrete subclasses that implements Implementor.

### Applying the Bridge Pattern

To apply the bridge pattern to the messaging example, let's write the abstraction class hierarchy.

**Message.java**

    package guru.springframework.gof.bridge.abstraction;

    import guru.springframework.gof.bridge.implementation.MessageSender;

    public abstract class Message {
        MessageSender messageSender;
        public Message(MessageSender messageSender) {
            this.messageSender=messageSender;
        }
        abstract public void send();
    }

**TextMessage.java**

    package guru.springframework.gof.bridge.abstraction;

    import guru.springframework.gof.bridge.implementation.MessageSender;

    public class TextMessage extends Message{
        public TextMessage(MessageSender messageSender) {
            super(messageSender);
        }
        @Override
        public void send() {
            messageSender.sendMessage();
        }
    }

**EmailMessage.java**

    package guru.springframework.gof.bridge.abstraction;

    import guru.springframework.gof.bridge.implementation.MessageSender;

    public class EmailMessage extends Message {
        public EmailMessage(MessageSender messageSender) {
            super(messageSender);
        }
        @Override
        public void send(){
            messageSender.sendMessage();
        }
    }

> In the examples above, we first wrote the Message abstract class- the Abstraction. In Message class, we initialized a reference to MessageSender in the hierarchies.  
> We also declared an abstract send() message in Message.  
> Next, we wrote two RefinedAbstraction: TextMessage, and EmailMessage to extend from Message.  
> In the constructors of both the classes, we made calls through super() to the constructor of the parent Message class.  
> In the overridden send() method, we invoked the sendMessage() method of MessageSender.

Now, let's write the implementation class hierarchy.

**MessageSender.java**

    package guru.springframework.gof.bridge.implementation;
    public interface MessageSender {
        public void sendMessage();
    }

**TextMessageSender.java**

    package guru.springframework.gof.bridge.implementation;

    public class TextMessageSender implements MessageSender {
        @Override
        public void sendMessage(){
            System.out.println("TextMessageSender: Sending text message...");
        }
    }

**EmailMessageSender.java**

    package guru.springframework.gof.bridge.implementation;

    public class EmailMessageSender implements MessageSender{
        @Override
        public void sendMessage(){
            System.out.println("EmailMessageSender: Sending email message...");
        }
    }

> In the MessageSender interface **(Implementor)**, we declared a sendMessage() method that both the TextMessageSender and EmailMessageSender **(ConcreteImplementor)** classes override.  
> Observe that this class hierarchy is completely independent of the abstraction class hierarchy that we wrote earlier.

**We can now modify, extend, and reuse one class hierarchy without worrying about the structure and participants of the other class hierarchy.**

Here is a unit test for our bridge pattern example.

**MessageTest.java**

    package guru.springframework.gof.bridge.abstraction;

    import guru.springframework.gof.bridge.implementation.EmailMessageSender;
    import guru.springframework.gof.bridge.implementation.MessageSender;
    import guru.springframework.gof.bridge.implementation.TextMessageSender;
    import org.junit.Test;
    import static org.junit.Assert.*;

    public class MessageTest {
        @Test
        public void testSend() throws Exception {
            MessageSender textMessageSender=new TextMessageSender();
            Message textMessage=new TextMessage(textMessageSender);
            textMessage.send();
            MessageSender emailMessageSender=new EmailMessageSender();
            Message emailMessage=new TextMessage(emailMessageSender);
            emailMessage.send();
        }
    }

When you run the code above, you will see this output:

    -------------------------------------------------------
    T E S T S
    -------------------------------------------------------
    Running guru.springframework.gof.bridge.abstraction.MessageTest
    TextMessageSender: Sending text message...
    EmailMessageSender: Sending email message...
    Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.001 sec - in guru.springframework.gof.bridge.abstraction.MessageTest

### Conclusion

The bridge pattern looks a lot like the adapter pattern and is a common cause of confusion.  
However, **while the adaptor pattern helps two incompatible interfaces work together, the bridge pattern helps decouple the abstraction and implementation by creating two seperate class hierarchies.**

> As stated by GoF: **_"Adapter makes things work after they're designed; Bridge makes them work before they are._**

> So when doing Enterprise Application Development with the Spring Framework, **if you think about creating deep class hierarchies for different implementations, consider seperating the low-level implementations into a seperate class hierarchy connected with a bridge.**  
> Doing so will make your code more flexible and less fragile to changes.  
> Also, with the bridge pattern, in order to test implementation classes, you need their super classes.
>
> > Without the bridge pattern, in order to test implementation classes, you need their super classes.  
> > With the bridge pattern, you can test them independently, and then create mock objects of the implementation classes to test the refined abstraction classes.
