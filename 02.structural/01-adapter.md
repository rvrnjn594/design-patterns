# Adapter Mathod

> "Convert the interface of a class into another interface clients expect. Adapter lets classes work together that couldn't otherwise because of incompatible interfaces."

### Introduction

> Daily example: makes the socket that produces 120V and the device that requires 4V work together.

Similarly, **_by using the adapter pattern in the programming world, you can make incompatible interfaces work together._**

> The adapter pattern is one of the classic Gang of Four structural patterns - **"A set of patterns whose objective is to identify how to realize relationships between classes and objects in a simple way. This pattern is typically used when an incompatible module needs to be integrated with an existing module without making any source code modifications."**

### Participants of the Adapter Pattern

To understand how the adapter pattern works, consider an existing text formatting application comprising of **TextFormatter** interface and a **NewLineFormatter** implementation class.

> A client provides a string to format with a call to the foramtText(String text) method declared in the interface. The implementation class returns a new string by replacing the periods with newline characters.
>
> > As an enhancement, the application now needs to support CSV formatting so that the text can be read and edited in spreadsheet software. The new feature has been implemented and delivered by an external vendor as a _CsvFormatter_ interface along with a _CsvFormatter_ implementation class.

To integrate the new feature in the existing application, several issues require addressing. **Primarily, the interface against which the new requirements are implemented has a different structure with different methods, and this is not what the clients of the application expect.**

> One approach to address such incompatibilities is to ask the vendor to supply a new version with a compatible interface.
> Another is to update the structure of the existing interface to accomodate the new requirement.
>
> > Both the approaches will not only result in major rework but also carry the risk of violating the [SOLID programming](https://springframework.guru/principles-of-object-oriented-design/) principles, specifically _Single Responsibility Principle_ and _Interface Segregation Principle_.
>
> **In such situations, the adaptor pattern comes to rescue.**

Using the adapter pattern, you can create a class, say _CsvAdapterImpl_ that will act as an adaptor to make both the incompatible interfaces (TextFormattable and CsvFormattable) work together.

There are two variants of adapters:

> Object adapter that relies on composition and class adapter that relies on inheritance.
>
> > As Java does not support multiple inheritance, you have to use object adapter when there are multiple classes that the adapter needs to address.
> > Also, the approach to [favors composition over inheritance](https://en.wikipedia.org/wiki/Composition_over_inheritance) should be the driving factor using object adapters in Java.

We can summarize the participants of the adapter pattern in the context of the text formatting example, as:

- Target (TextFormattable): The existing interface that clients communicate with.
- Adaptee (CsvFormattable): The new incompatible interface that needs adapting.
- Adapter (CsvAdapterImpl): A class that adapts the Adaptee to the Target.
- Client: Communicates with the Target.

### Applying the Adapter Pattern

To apply the adapter pattern to the text formatting example, let's look at the existing Target interface and implementation class.

**TextFormattable.java**

    package guru.springframework.gof.adapter.source;

    public interface TextFormattable {
        String formatText(String text);
    }

**NewLineFormatter.java**

    package guru.springframework.gof.adapter.source;

    public class NewLineFormatter implements TextFormattable {
        @Override
        public String formatText(String text) {
            String formattedText=text.replace(".","\n");
            return formattedText;
        }
    }

> In the example above, the _TextFormattable_ interface declares a single _formatText()_ method that the _NewLineFormatter_ class overrides to return a string formatted with new line characters.
> Next, let's look at the Adaptee interface and its implementation class.

**CsvFormattable.java**

    package guru.springframework.gof.adapter.adaptee;

    public interface CsvFormattable {
        String formatCsvText(String text);
    }

**CsvFormatter.java**

    package guru.springframework.gof.adapter.adaptee;

    public class CsvFormatter implements CsvFormattable{
        @Override
        public String formatCsvText(String text){
            String formattedText=text.replace(".",",");
            return formattedText;
        }
    }

> The _CsvFormattable_ interface declares a formatCsvText() method that the CsvFormatter class overrides to return a string formatted as comma seperated values.

**Coming to the main part, we will now write the Adapter class.**

**CsvAdapterImpl.java**

    package guru.springframework.gof.adapter.csvadapter;

    import guru.springframework.gof.adapter.adaptee.CsvFormattable;
    import guru.springframework.gof.adapter.source.TextFormattable;

    public class CsvAdapterImpl implements TextFormattable {
        CsvFormattable csvFormatter;
        public CsvAdapterImpl(CsvFormattable csvFormatter){
            this.csvFormatter=csvFormatter;
        }
        @Override
        public String formatText(String text)
        {
            String formattedText=csvFormatter.formatCsvText(text);
            return formattedText;
        }
    }

> In the **_CsvAdapterImpl_** class above, we implemented the **_TextFormattable_** interface, which is the Target.
> We then declare the _Adaptee_ type (CsvFormattable) as a field and initialized it in the constructor.
> In the overridden formatText() method, we made a call to the formatCsvText() method, and returned back the CSV formatted string to the caller.

Let's write a unit test for the example:

**NewLineFormatterTest.java**

    package guru.springframework.gof.adapter.source;

    import guru.springframework.gof.adapter.adaptee.CsvFormattable;
    import guru.springframework.gof.adapter.adaptee.CsvFormatter;
    import guru.springframework.gof.adapter.csvadapter.CsvAdapterImpl;
    import org.junit.Test;
    import static org.junit.Assert.*;

    public class NewLineFormatterTest {
        @Test
        public void testFormatText() throws Exception {
            String testString=" Formatting line 1. Formatting line 2. Formatting line 3.";
            TextFormattable newLineFormatter=new NewLineFormatter();
            String resultString = newLineFormatter.formatText(testString);
            System.out.println(resultString);
            CsvFormattable csvFormatter=new CsvFormatter();
            TextFormattable csvAdapter=new CsvAdapterImpl(csvFormatter);
            String resultCsvString=csvAdapter.formatText(testString);
            System.out.println(resultCsvString);
        }
    }

> In the test above, we called the formatText() method of TextFormattable to format text without using the adaptor.
> Later, we created a CsvAdapterImpl object passing a CsvFormatter object to its constructor. We then called the formatText() method, which at runtime got forwarded to a call to formatCsvText() on CsvFormatter.

When you run the code above, you will see this output:

    -------------------------------------------------------
     T E S T S
    -------------------------------------------------------
    Running guru.springframework.gof.adapter.source.NewLineFormatterTest
    Formatting line 1
    Formatting line 2
    Formatting line 3
    Formatting line 1, Formatting line 2, Formatting line 3,
    Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 1.05 sec - in guru.springframework.gof.adapter.source.NewLineFormatterTest

### Conclusion

Some programmers argur that the Adapter pattern is workaround for a system, which was not well designed keeping into considerations new possibilities.  
While this is true to some extent, we cannot expect an enterprise application, which will often have pluggable architecture, to be designed considering all components that might get added in future.

> In enterprise application development, it is likely that you might need to hook in other libraries, APIs, and "off the shell" components, and if they are not aligned with the existing system, put the adapter pattern to use.

**_After all, being a core GoF pattern, it is a tested and proven solution used over a long period of time._**

# Adapter Pattern in Spring Framework

**When doing Enterprise Application Development with the Spring Framework, you will be using adaptors built into the framework.**

> Spring Integration uses JMS adaptors to send and receive JMS messages and JDBC messages and JDBC adapters to convert messages to database queries and result sets back to messages.
> Spring also uses the adaptor design pattern to handle load-time-weaving used in Aspect-Oriented Programming (AOP). An adapter is used to inject AspectJ's aspects to bytecode during class loading done by the servlet container.
