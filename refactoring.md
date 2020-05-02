## Java functional refactoring
We have this code in project and we found it was quite slow the first time to run it. We guess the "createMarshaller" is expensive. We decide to add the spring stopwatch around it to see how long it takes.  
```
    public String xmlStringCleaner(Object object, Type bodyType) throws JAXBException {
        Marshaller marshaller = jaxbContextFactory.createMarshaller((Class<?>) bodyType);
        StringWriter stringWriter = new StringWriter();
        marshaller.marshal(object, stringWriter);
        return stringWriter.toString().replaceAll("<((?!na=\\\"true\\\")[^>])*/>", "");
    }
```
The StopWatch impacts the current code a lot. We can see the stopWatch code is mixed with business logic. 
```
    public String xmlStringCleaner(Object object, Type bodyType) throws JAXBException {
    	StopWatch sw = new StopWatch("stopWatch");
    	sw.start("createMarshaller");
        Marshaller marshaller = jaxbContextFactory.createMarshaller((Class<?>) bodyType);
        sw.stop();
        System.out.println(sw.prettyPrint());
        StringWriter stringWriter = new StringWriter();
        marshaller.marshal(object, stringWriter);
        return stringWriter.toString().replaceAll("<((?!na=\\\"true\\\")[^>])*/>", "");
    }
```
So, we need do refactoring. I am not going to use AOP as it usually only works when the call happens between two classes. I am going to put the stopWatch code in an Util class and pass in the function/supplier which needs to be monitored. 
```
public class StopWatchUtil {
	
	public static <T, E extends Exception> T stopwatch(String stopWatchName, ThrowingSupplier<T, E> supplier) throws E {
		StopWatch sw = new StopWatch(stopWatchName);
		sw.start(stopWatchName);
		
		T result = supplier.get();
		
		sw.stop();

		log.info(sw.prettyPrint());
		
		return result;
	}
	
	@FunctionalInterface
	public interface ThrowingSupplier<T, E extends Exception> {
	    T get() throws E;
	}
```
The stopwatch method takes one supplier argument and execute it between sw.start and sw.stop.  
Now our original code is like this. For the method need stopwatch monitoring, I create a supplier
```
ThrowingSupplier<Marshaller, JAXBException> marshallerSupplier = () -> jaxbContextFactory.createMarshaller((Class<?>) bodyType);
Marshaller marshaller = StopWatchUtil.stopwatch("createMarshaller", marshallerSupplier);
```

At the beginning, I use Supplier from JDK. But it gives me compile error. That is because the code in my supplier "jaxbContextFactory.createMarshaller" throws error.  
Then I have to create a new supplier which allows throwing exception. That is the inconvenient part of java functional programming. 
```
	@FunctionalInterface
	public interface ThrowingSupplier<T, E extends Exception> {
	    T get() throws E;
	}
```

Last, let's have a look about Java generic. for example  
for the method needs to use generic 
```
public static <T, E extends Exception> T stopwatch(String stopWatchName, ThrowingSupplier<T, E> supplier) throws E
```
We need declare the generic type in the angle brackets, like "<T, E extends Exception>" in the example above.  
This declaration shall be put right before the return type. In this example, the return type is T.  
Then in the arguments or exception, the reference of the type can be used, like 
```
(... ThrowingSupplier<T, E> supplier) throws E
```

for the class needs to use generic, the type declaration is right after class name
```
public interface ThrowingSupplier<T, E extends Exception>
```

