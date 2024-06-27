# Proxy Pattern

"**Provide a surrogate or placeholder for another object to control access to it.**"

> The Proxy pattern is part of the classic Gang of Four (GoF) structural pattern family whose common objective is to handle relationships between classes and objects in a simple way. Let’s have a quick recap about the other GoF structural patterns:
>
> > **Adapter:** Convert the interface of a class into another interface clients expect. Adapter lets classes work together that couldn’t otherwise because of incompatible interfaces.
> > **Bridge:** Decouple an abstraction from its implementation so that the two can vary independently.
> > **Composite:** Compose objects into tree structures to represent part-whole hierarchies. Composite lets clients treat individual objects and compositions of objects uniformly.
> > **Decorator:** Attach additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.
> > **Facade:** Provide a unified interface to a set of interfaces in a subsystem. Facade defines a higher-level interface that makes the subsystem easier to use.
> > **Flyweight:** Use sharing to support large numbers of fine-grained objects efficiently.

In this post, we will look at the Proxy pattern the final pattern of the series in the GoF structural pattern family.

### Introduction

The Proxy pattern uses a proxy (surrogate) object “in place of” another object.

The **objective of a proxy object is to control the creation of and access to the real object it represents.** A common use of a proxy is to defer the cost of instantiating of an object (that is expensive to create) until it is actually needed by clients.

> Consider a report viewer application that generates and displays sales reports.
>
> When the application starts, it displays a UI where users can specify the format and amount of data to display in a report.
>
> Users can also specify the type of report to generate, such as a daily sales report, a complex sales forecast report for the next quarter, and on.
>
> The application has a report generator object, a resource consuming (expensive) object that gathers report data from various sources, analyzes them, formats them, and sends it to the UI for display.
>
> In the report viewer application, it is not necessary to create the report generator object when the application loads. We want the UI to be light and start up quickly.
>
> It’s only when a user clicks on the Generate Report button on the UI we will need to instantiate the report generator object and ask it to create the report.

By applying the Proxy pattern, we can create a proxy with the same interface as the real report generator object The UI keeps interacting with the proxy.

It is only when the UI asks the proxy to generate a report, the proxy will instantiate the real report generator object. Such proxy is called **virtual proxy** – It creates expensive objects on demand.

Some other types of proxies are:

**Protection proxy:** Controls access to the real object. As an example, in the report viewer application, a report generator object generates sensitive reports that a protection proxy allows access to only users with the Manager role.
**Remote proxy:** Represents an object running on a remote JVM. Java RMI and Jini used to create distributed applications in Java uses remote proxies, which are called stubs. Pre-Java EE 6 remote proxies were used by JAX-RPC Web services.
**Smart reference proxy:** Performs additional actions on the real object, such as maintaining reference counts to a real object so that the real object can be freed when no more references exist. It can also load and cache a persistent object in memory when first referenced or lock a real object to ensure that no other objects can change it.

### Participants of the Proxy Pattern

> Continuing with the report viewer example, let’s start with a ReportGenerator interface and a ReportGeneratorImpl class that implements the interface.
>
> ReportGeneratorImpl objects generate complex reports for clients and are expensive to create. The proxy is a ReportGeneratorImplProxy class that clients interact with. The ReportGeneratorImplProxy class also implements the ReportGenerator interface.
>
> With the basic class structure of the application set up, we can relate them with the participants of the Proxy pattern as:
>
> > Subject (ReportGenerator): Is an interface for both the real object and its proxy. The Subject enables the proxy to be used anywhere the real object is expected.
>
> > RealSubject (ReportGeneratorImpl): The real object that is expensive to create, requires protection, or running on a remote JVM. RealSubject implements Subject and we create proxy of it.
>
> > Proxy (ReportGeneratorImplProxy): Implements Subject and maintains a reference to RealSubject.

### Applying the Proxy Pattern

> To apply the Proxy pattern to our report viewer example, we will first write the ReportGenerator interface, which is the Subject.

**ReportGenerator.java**

    package guru.springframework.gof.proxy;

    public interface ReportGenerator {
        void displayReportTemplate(String reportFormat,int reportEntries);
        void generateComplexReport(String reportFormat,int reportEntries);
        void generateSensitiveReport();
    }

> Next, let’s write the ReportGeneratorImpl class, which is the RealSubject.

**ReportGeneratorImpl.java**

    package guru.springframework.gof.proxy;

    public class ReportGeneratorImpl implements ReportGenerator {
        public ReportGeneratorImpl(){
            System.out.println("ReportGeneratorImpl instance created");
        }
        @Override
        public void displayReportTemplate(String reportFormat,int reportEntries) { }
        @Override
        public void generateComplexReport(String reportFormat, int reportEntries){
            System.out.println("ReportGeneratorImpl: Generating complex report in "+reportFormat+" format with "+ reportEntries+" entries");
        }
        @Override
        public void generateSensitiveReport(){
            System.out.println("ReportGeneratorImpl: Generating sensitive report");
        }
    }

> In the ReportGeneratorImpl class above, we wrote very simple method implementations of the ReportGenerator interface. We have even left the displayReportTemplate() method empty, as we would like the proxy to implement it.
>
> But in enterprise applications, objects of RealSubject classes, such as our ReportGeneratorImpl will be resource-intensive, and we should be interested in creating their proxies.
>
> Imagine that the generateComplexReport() method needs to communicate with remote repositories and services to perform data mining to discover patterns from large data sets.
>
> For that, we perform all the initialization and setup tasks in the constructor.
>
> Also, the generateSensitiveReport() method has to follow regulatory requirements and security policies and so needs to be protected from unauthorized access.

So what we will do is create a proxy – the ReportGeneratorImplProxy class that we will write next.

**ReportGeneratorImplProxy.java**

    package guru.springframework.gof.proxy;

    public class ReportGeneratorImplProxy implements ReportGenerator{
        ReportGenerator reportGeneratorImpl;
        private Role accessRole;
        public ReportGeneratorImplProxy(Role accessRole){
            this.accessRole=accessRole;
        }
        @Override
        public void displayReportTemplate(String reportFormat,int reportEntries) {
            System.out.println("ReportGeneratorImplProxy: Displaying blank report template in " + reportFormat + " format with " + reportEntries + " entries");
        }
        @Override
        public void generateComplexReport(String reportFormat,int reportEntries){
            if(reportGeneratorImpl==null)
                reportGeneratorImpl = new ReportGeneratorImpl();
            reportGeneratorImpl.generateComplexReport(reportFormat,reportEntries);
        }
        @Override
        public void generateSensitiveReport(){
            if(accessRole.getRole().equals("Manager")){
                if(reportGeneratorImpl==null)
                    reportGeneratorImpl = new ReportGeneratorImpl();
                reportGeneratorImpl.generateSensitiveReport();
            } else {
                System.out.println("You are not authorized to access sensitive reports.");
            }
        }
    }

> In the proxy class above, we implemented the same ReportGenerator interface of ReportGeneratorImpl.
>
> This will allow our proxy to be used wherever a ReportGeneratorImpl object is expected.
>
> We declared a reference to the ReportGeneratorImpl object.
>
> By going with the principle of “Prefer composition over inheritance”, we me made our proxy composed of the real ReportGeneratorImpl object.
>
> We also declared a Role instance variables that we initialized through the constructor. But, we intentionally refrained from instantiating ReportGeneratorImpl in the constructor – we want to create it only when it is required.
>
> We implemented the displayReportTemplate() method. Again, what I want to point out is that if the client makes a request to display how a report looks like in a particular format (but, without any data), we don’t want to create the expensive ReportGeneratorImpl object just to display the template.
>
> It is only when the client calls generateComplexReport() that we wrote , we create a ReportGeneratorImpl object and pass the method call to it. What we implemented is the functionality of a virtual proxy.
>
> We wrote the generateSesnsitiveReport() method to implement the functionality of a protection proxy.
>
> In this method, we checked the role name of aRole object that the proxy was initialized with.
>
> We allowed access to the ReportGeneratorImpl object only if the role name is Manager. The Role class we used in ReportGeneratorImplProxy is this.

**Role.java**

package guru.springframework.gof.proxy;
public class Role {
private String role;
public String getRole() {
return role;
}
public void setRole(String role) {
this.role = role;
}
}
Let’s write some code to test this out.

ReportGeneratorImplProxyTest.java
package guru.springframework.gof.proxy;
import org.junit.Test;
import static org.junit.Assert.\*;
public class ReportGeneratorImplProxyTest {
@Test
public void testGenerateReport() throws Exception {
Role accessRole=new Role();
accessRole.setRole("Manager");
ReportGenerator proxy=new ReportGeneratorImplProxy(accessRole);
proxy.displayReportTemplate("Pdf",150);
proxy.generateComplexReport("Pdf",150);
proxy.generateSensitiveReport();
}
}
The output of the test code above is this.

---

## T E S T S

Running guru.springframework.gof.proxy.ReportGeneratorImplProxyTest
ReportGeneratorImplProxy: Displaying blank report template in Pdf format with 150 entries
ReportGeneratorImpl instance created
ReportGeneratorImpl: Generating complex report in Pdf format with 150 entries
ReportGeneratorImpl: Generating sensitive report
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.06 sec - in guru.springframework.gof.proxy.ReportGeneratorImplProxyTest
In the output, note that the call to displayReportTemplate() was handled by the proxy. It was only during the generateComplexReport() call, the proxy instantiated ReportGeneratorImpl to delegate the call to it.

Conclusion
You may have observed that many patterns in the GoF structural family are about “wrapping” a class, but with variations. They are structurally very similar, if not identical. What makes the Proxy Pattern distinct is that it has the same interface as the real object. So if you are working on a system and feel the need to introduce a proxy either to instantiate a real object on demand, or hide the fact that the object is running on a remote location, or control access to the object, you can easily do so. You don’t need to worry about the client being aware about any changes from the introduction of the proxy. The client will keep using the same interface thinking it is interacting with the real object, while the proxy will be mediating in between.

While developing Enterprise Applications with the Spring Framework, you will find lots of opportunities to apply the Proxy pattern or see it naturally at work within the framework APIs. This pattern is particularly used heavily in Spring AOP. As an example, In Spring AOP you create proxies of the objects that handle the cross cutting concern code. The Proxy pattern also forms the core foundation of remoting technologies that Spring supports, such as RMI, Spring’s HTTP Invoker, Hessian, and Burlap. Through the use of a proxy, the upstream software is unaware it is using a remoting technology. And when coupled with dependency injection in Spring, you could easily run all locally for development tasks, while using a remoting proxy once deployed to the production environment.
