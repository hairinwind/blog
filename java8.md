
## How will you call a default method of an interface in a class?

Using super keyword along with interface name.
```
interface Vehicle {
   default void print() {
      System.out.println("I am a vehicle!");
   }
}
class Car implements Vehicle {
   public void print() {
      Vehicle.super.print();                  
   }
}
```

## How will you call a static method of an interface in a class?
```
interface Vehicle {
   static void blowHorn() {
      System.out.println("Blowing horn!!!");
   }
}
class Car implements Vehicle {
   public void print() {
      Vehicle.blowHorn();                  
   }
}
```
The differences between default method and static method:
- Default methods can be overriden in implementing class, while static cannot.
- Static method belongs only to Interface class, so you can only invoke static method on Interface class, not on class implementing this Interface. In the above example, Car.blowHorm() is not valid.
- Both class and interface can have static methods with same names, and neither overrides other!
https://stackoverflow.com/questions/27833168/difference-between-static-and-default-methods-in-interface

## What are functional interfaces?

Functional interfaces have a single functionality to exhibit. Java 8 has defined a lot of functional interfaces to be used extensively in lambda expressions.  
For example: 
```Interface Function<T,R>```  
This is a functional interface and can therefore be used as the assignment target for a lambda expression or method reference.
If you need refer a function by a variable, the variable need the type. Java defined a bunch of functional interface in the java.util.function package, one arguement, two argument, same return, different return or no return, or return boolean. 
This is actually the embarrassment to use functional programming in type restrict language. In javascript, you don't have this headache. 

## use AtomicReference to implement Lazy Initialization
https://dzone.com/articles/functionalfun-states-in-functions-cache-lazy-and-c?edition=376215&utm_source=Daily%20Digest&utm_medium=email&utm_campaign=Daily%20Digest%202018-04-27
```
public static <T> Supplier<T> lazy(Supplier<T> supplier) {
  val dummy     = new Object();
  val reference = new AtomicReference<Object>();
  return ()->{
    if (reference.compareAndSet(null, dummy)) { //compareAndSet means if reference == null then set it to be dummy
      try {

        val value = supplier.get();
        reference.set((Supplier<T>)(()->value));
      } catch (RuntimeException e) {
        reference.set(e);
      }
    }
    while (!(reference.get() instanceof Supplier)) {
      if (reference.get() instanceof RuntimeException)
        throw (RuntimeException)reference.get();
    }
    return ((Supplier<T>)reference.get()).get();
  };
}
```
http://tutorials.jenkov.com/java-util-concurrent/atomicreference.html

## stream terminal functions vs intermediate operations
Stream operations are divided into intermediate (Stream-producing) operations and terminal (value- or side-effect-producing) operations. Intermediate operations are always lazy.  
https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html 
Terminal operations include:
- forEach
- toArray
- reduce
- collect
- finAny or findFirst
- ...
https://www.leveluplunch.com/java/examples/stream-terminal-operations-example/  

## Function.identity
```
Stream<String> stream = Stream.of("I", "love", "you", "too");
Map<String, Integer> map = stream.collect(Collectors.toMap(Function.identity(), String::length));
```
- identity is a static method on Function 
- It returns the input parameter
http://www.importnew.com/24245.html  

## java command line parse
http://jcommander.org/

## Optional orElse vs orElseGet
orElse takes a default value as parameter 
orElseGet takes a function as parameter
orElseGet has better performance than orElse
// TODO

## var to declare local variables
https://dzone.com/articles/finally-java-10-has-var-to-declare-local-variables

## get last element after split
```
String last = string.substring(string.lastIndexOf('-') + 1);
```

## Collections.synchronizedList() 
https://docs.oracle.com/javase/7/docs/api/java/util/Collections.html#synchronizedList(java.util.List)  
Collections.synchronizedList() Returns a synchronized (thread-safe) list backed by the specified list. In order to guarantee serial access, it is critical that all access to the backing list is accomplished through the returned list.

It is imperative that the user manually synchronize on the returned list when iterating over it:
```
  List list = Collections.synchronizedList(new ArrayList());
      ...
  synchronized (list) {
      Iterator i = list.iterator(); // Must be in synchronized block
      while (i.hasNext())
          foo(i.next());
  }
``` 
Failure to follow this advice may result in non-deterministic behavior.

https://howtodoinjava.com/java/collections/arraylist/synchronize-arraylist/
No explicit synchronization is needed to add, remove elements from synchronized arraylist.

I used to need call "contains" function. It actually needs to do the iteration inside the function, so it has to be in the synchronized block.

## java Volatile/synchronization on arraylist
Volatile on arraylist cannot make arraylist to be synchronized. 
https://stackoverflow.com/questions/32549628/java-volatile-synchronization-on-arraylist
The meaning of volatile is that changes made by thread A to a shared variable are visible to thread B immediately. Usually such changes may be in some cache visible only to the thread that made them, and volatile just tells the JVM not to do any caching or optimization that will result in the value update being delayed.

So it is not a means of synchronization. It's just a means of ensuring visibility of change. Moreover, it's change to the variable, not to the object referenced by that variable. That is, if you mark list as volatile, it will only make any difference if you assign a new list to list, not if you change the content of the list!

## spring jdbcTemplate to retrieve database auto-generated keys
https://www.logicbig.com/tutorials/spring-framework/spring-data-access-with-jdbc/key-holder.html

```
String sql = "insert into CUSTOMER (NAME, PHONE, ADDRESS) values (?, ?, ?)";
      KeyHolder keyHolder = new GeneratedKeyHolder();

      jdbcTemplate.update(
              connection -> {
                  PreparedStatement ps = connection.prepareStatement(sql, new String[]{"ID"});
                  ps.setString(1, "Jake");
                  ps.setString(2, "234-333-627");
                  ps.setString(3, "345 Move Dr, Shine Hill");
                  return ps;
              }, keyHolder);

      Number key = keyHolder.getKey();
      System.out.println("Newly persisted customer generated id: " + key.longValue());
```

## create list with value
```
List<String> places = Arrays.asList("Buenos Aires", "Córdoba", "La Plata");
```

## SimpleDateFormat is not thread-safe
I used to work on a spring-batch project. Multiple threads read data chunk at the same moment and try to parse them. Somebody put the SimpleDateFormat as final static class variable. Under multiple threads, it throw some weird exception, like cannot parse the date.  
The solution is to move the SimpleDateFormat into method so it is not shared by different threads.

