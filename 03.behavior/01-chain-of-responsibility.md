# Chain of Responsibility Pattern

"Avoid coupling the sender of a request to its receiver by giving more than one object a chance to handle the request.  
Chain the receiving objects and pass the request along the chain until an object handles it."

> **The entire family of behavior patterns revolves around the principle of "object composition rather than inheritance".**
>
> This principle is currently been widely adopted by the developer community because of the benefits it offers.
>
> What this principle states is - **instead of extending from an existing class, design your class to refer an existing class that you want to use.**
>
> In Java, it's done by declaring an instance variable referencing the object of the existing class.
>
> Then, by initializing the object either through the constructor or a method, and finally using the referred (composed) object.

## Introduction

> The Chain of Responsibility pattern is easy to understand and apply. >
>
> _In applications there is always a client that initiates a request and an application object that handles it._
>
> > What the Chain of Responsibility pattern states is – _decouple the client who sends the request to the object that handles it._
> >
> > The solution is a list of handler objects, also known as **responding objects** each capable to deal with a specific nature of request.
> >
> > If one handler object can’t handle a request, it passes it to the next object in the chain.
> >
> > At the end of the chain, there will be one or more generic handler objects implementing default behavior for the request.
>
> You can relate the Chain of Responsibility pattern with a Customer Service technical help desk that you call up with a technical query/help for some product or service (think yourself as a request object).
>
> A technical help desk executive tries to resolve it (Think in terms of objects – the first object in the chain).
>
> If they can’t resolve it – maybe for some billing related issues, it moves to a billing help desk executive (the second object).
>
> If the billing help desk can’t resolve either, your request goes to the general help desk (the third object), and so on – until someone handles your request.

![chain of responsibility](https://springframework.guru/wp-content/uploads/2015/09/01.png)

## Participants of the Chain of Responsibility Pattern

Continuing with the Customer Service help desk example, let’s model it in terms of a Java program.

> We create an abstract class- _AbstractSupportHandler._ This class will assign request levels and delegate to the different Customer Service handlers.
>
> This class will also manage when a specific request level of a customer can’t’ be handled by a request handler. Then, the request will be delegated to the next handler on the line.
>
> For each specific handler, we will model specific classes extending _AbstractSupportHandler_.
>
> Let’s name them _TechnicalSupportHandler_, _BillingSupportHandler_, and finally _GeneralSupportHandler_.
>
> It will be a _RequestorClient_ class that will initiate a request for the handlers to process.
>
> A classic example of the **Dependency Inversion Principle that states: Modules should be independent.** **_They should depend on abstractions and abstractions should not depend on details._**
>
> Details should depend on abstractions. Our object chain depends on the abstraction (_AbstractSupportHandler_)

On the context of the Customer Service help desk example, the **participants of the Chain of Responsibility pattern** are:

- **Handler** (_AbstractSupportHandler_) Abstract base class acting as the interface to handle request. **Optionally, but most often the Handler implements the chain links.**
- **ConcreteHandler** (_TechnicalSupportHandler_, _BillingSupportHandler_, and _GeneralSupportHandler_) **Handles request, else passes it to the next successor of the handler chain.**
- **Client** (_RequestorClient_): Initiates a request that one of the chain of handlers ( a ConcreteHandler) handles.

## Applying the Chain of Responsibility Pattern

> Moving ahead with our Customer Service help desk example, let's write the _AbstractSupportHandler_ class - **the Handler**.
>
> We first assigned levels to constant variables that represents the type of client requests.
>
> We then wrote a setNextHandler() method that will get called to use the next handler in the chain.
>
> The receiveRequest() is where the passing of a client's request in the object's chain occur.
>
> In this method, we first checked whether the initial object on the chain can handle the request.
>
> If so, we called the handleRequest() method that we declared as abstract.
>
> Else, we sent the control back to receiveRequest() method.
>
> Next, we will write the ConcreteHandler classes. Each of them extends AbstractSupportHandler and implements the handleRequest() method.
>
> Also note that each of the ConcreteHandler classes are initialized with their levels declared as constant variables in the abstract _AbstractSupportHandler_ super class.

> In the client class, we wrote a getHandlerChain() method that returns a AbstractSupportHandler object.
>
> In this method, we started by instantiating the ConcreteHandler: _TechnicalSupportHandler_, _BillingSupportHandler_, and _GeneralSupportHandler_.
>
> In this method, we set up and returned the handler chain.

Few examples of classes:

**TechnicalSupportHandler.java**

    public class TechnicalSupportHandler extends AbstractSupportHandler {
        public TechnicalSupportHandler(int level){
            this.level = level;
        }
        @Override
        protected void handleRequest(String message) {
            System.out.println("TechnicalSupportHandler: Processing request " + message);
        }
    }

**RequestorClient.java**

    import guru.springframework.gof.ChainofResponsibility.handlers.BillingSupportHandler;
    import guru.springframework.gof.ChainofResponsibility.handlers.AbstractSupportHandler;
    import guru.springframework.gof.ChainofResponsibility.handlers.GeneralSupportHandler;
    import guru.springframework.gof.ChainofResponsibility.handlers.TechnicalSupportHandler;
    public class RequestorClient {
        public  static AbstractSupportHandler getHandlerChain(){
            AbstractSupportHandler technicalSupportHandler = new TechnicalSupportHandler( AbstractSupportHandler.TECHNICAL);
            AbstractSupportHandler billingSupportHandler = new BillingSupportHandler(  AbstractSupportHandler.BILLING);
            AbstractSupportHandler generalSupportHandler = new GeneralSupportHandler(AbstractSupportHandler.GENERAL);
            technicalSupportHandler.setNextHandler(billingSupportHandler);
            billingSupportHandler.setNextHandler(generalSupportHandler);
            return technicalSupportHandler;
        }
    }

**RequestorClient.java**

    import guru.springframework.gof.ChainofResponsibility.handlers.AbstractSupportHandler;
    import org.junit.Test;
    import static org.junit.Assert.*;
    public class RequestorClientTest {
        @Test
        public void testGetHandlerChain() throws Exception {
            AbstractSupportHandler handler=RequestorClient.getHandlerChain();
        handler.receiveRequest(AbstractSupportHandler.TECHNICAL, " I'm having problem with my internet connectivity.");
            System.out.println("............................................");
        handler.receiveRequest(AbstractSupportHandler.BILLING, "Please resend my bill of this month.");
            System.out.println("............................................");
        handler.receiveRequest(AbstractSupportHandler.GENERAL, "Please send any other plans for home users.");
        }
    }

[code explained here](https://springframework.guru/gang-of-four-design-patterns/chain-of-responsibility-pattern/)

## Summary

> What I like about the Chain of Responsibility pattern is the complete decoupling between the client and the object chain that handles the client's request.
>
> In this pattern, the first object in the chain receives the client request. It either handles it or forwards it to the next object in the chain.
>
> The process goes on until some object in the chain handles the request.
>
> > What's interesting here is that the client that made the request has no explicit knowledge about the client who initiated the request.

This pattern is classic implementation of the SOLID principles of OOPS.

- Dependency Inversion Principle applies in the design pattern.
- We also have the Single Responsibility Principle which states - _The classes you write, should not be a swiss army knife. They should do one thing, and do that one thing well._
  > In this example, we wrote the _TechnicalSupportHandler_, _BillingSupportHandler_, and _GeneralSupportHandler_ classes. Each class is written for a single specific purpose.
- The open/close principle states that: _"Software entities (classes, modules, functions, etc) should be fixed and yet represent an unbounded group of possible behaviors through concrete subclasses."_

You will also see the Chain of Responsibility pattern applied in the Spring Security project of the Spring Framework.  
 In spring security for authentication and authorization you have chains of 'voters'. This is highly configurable.
This is whay allows Spring Security to be so flexible when applied to enterprise application development.  
 You can add voters for things like database authentication, or LDAP authentication.  
 And its easy to apply your own custom voters because of how Chain of Responsibility Design pattern has been applied.
