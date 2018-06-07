
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

