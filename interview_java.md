
## JVM memroy and garbage collection
The JVM uses different types of memory to store different types of objects. The heap memory space, which is what you will usually be watching for memory leaks, contains two distinct areas: young generation and old generation. Non–heap memory is used by the JVM to store loaded classes and methods, and other low-level data. Memory leaks are usually only an issue in Heap memory. Without going into too much detail, it is useful to know how these zones are used when tracking down memory-related issues.

The young generation area is split into three distinct zones. The first zone, known as the Eden Space, is where the JVM places newly created objects (hence the name), and is mainly used for short-lived variables such as local variables within a method. When this space becomes too crowded, a fast, lightweight garbage collection process cleans it up and reclaims any unreferenced objects. Objects that are not freed by garbage collections in the Eden space are placed in a second zone, called the Survivor space. In fact, there are two, identically sized survivor spaces. When the first survivor space starts to fill up, a garbage collection frees dead objects and copies live ones into the second survivor space. Objects that stand the test of time and are not recycled from the second survivor space are placed in the old generation, also know as the Tenured Generation. This zone is usually reserved for long-lived objects such as static variables or frequently used cached objects. This space is usually much bigger than the others (by an order of magnitude or two), and memory can only be recycled here by a particularly expensive operation called a "mark and sweep" garbage collection.

When old generation space is running low, a longer, full-scale garbage collection process scans the entire heap space for recyclable objects. Again, this "mark and sweep" garbage collection is a particularly expensive operation and is the sort of thing that can bring your server to its knees if it happens too frequently.

## garbage collection 
https://detailfocused.blogspot.com/2008/03/garbage-collection.html

## Java 8 Lambda shall not modify external data
Lambda is a function. It shall only manipulate the passed in variable. It shall not modify the external data/variable. 

## ConcurrentModificationException 
Essentially, the ConcurrentModificationException is used to fail-fast when something we are iterating on is modified.   
https://www.baeldung.com/java-concurrentmodificationexception
Regarding the item is removed when iterating, this article introduces how to avoid ConcurrentModificationException
- iterator.remove
- add remove item to another list, then call removeAll with that list
- java 8 removeIf
- java 8 filter

## ArrayIndexOutOfBoundsException
The code below can throw ArrayIndexOutOfBoundsException
```
List<Integer> matched = new ArrayList<>();
List<Integer> elements = new ArrayList<>();
public void sampleFunction() {
    for (int i=0; i< 10000; i++) {
        elements.add(i)
    }

    elements.parallelStream().forEach(
        e -> {
            if (e>=100) {
                matched.add(e); // this throws ArrayIndexOutOfBoundsException
                // also, here the lambda tries to modify the external data "matched" 
                // which shall be avoided
            }
        }
    );
    System.out.println(matched.size()) 
}
```
The good solution is like this
```
matched = elements.parallelStream().filter(e -> e>=100).collect(Collectors.toList()); 
```
Why ArrayList causes java.lang.ArrayIndexOutOfBoundsException when multiple threads are adding Items.  
List increases its space when it finds out the space is not enough. When mulitple threads access one list, let's say list has one room, both threads think the list still have room, so it does not increase the room before add the item into the arraylist. When the later thread add the object, actually the list has no room. So it throws out ArrayIndexOutOfBoundsException.

## how does java 8 stream parallel work
When a stream executes in parallel, the Java runtime partitions the stream into multiple sub-streams. Aggregate operations iterate over and process these sub-streams in parallel and then combine the results.
https://examples.javacodegeeks.com/core-java/java-8-parallel-streams-example/

## final 
final variable - create constant variable 
final methods - prevent method overriding
final classes - prevent class extension/inheritance 

## multiple threads 
- thread states http://detailfocused.blogspot.com/2008/03/thread-states-and-transitions.html 
- sleep() vs wait() vs yield()
- join()
- volatile http://detailfocused.blogspot.com/2008/06/volatile.html
- synchronization and locks http://detailfocused.blogspot.com/2008/03/synchronization-and-locks.html  


### sleep() vs wait() vs yield()
https://stackoverflow.com/questions/1036754/difference-between-wait-and-sleep
- sleep(n) says “I’m done with my timeslice, and please don’t give me another one for at least n milliseconds.” Once the wait time is done, the thread is go back to "Runnable" and waiting for CPU to allocate time to run. It does not gaurantee it will be run right after the time is over. Sleep function does not release the lock while wait function releases the object lock. Sleep() is a static method of Thread while wait() is a instance method of Object class. Sleep() function can be called anywhere while wait() can be only called in synchronized block/context.
```
synchronized(LOCK) {
    Thread.sleep(1000); // LOCK is held
}


synchronized(LOCK) {
    LOCK.wait(); // LOCK is not held
}
```
- wait() says “I’m done with my timeslice. Don’t give me another timeslice until someone calls notify().” A wait can be "woken up" by another thread calling notify on the monitor which is being waited on whereas a sleep cannot.
- yield() says “I’m done with my timeslice, but I still have work to do.” The OS is free to immediately give the thread another timeslice, or to give some other thread or process the CPU the yielding thread just gave up. yield() is a static function of Thread. The CPU may run the same thread again after yield() depends on priority. 

### wait() and notify()
https://www.baeldung.com/java-wait-notify  
```
Object mon = ...;
synchronized (mon) {
    ...
    mon.wait();
} 
```
In another thread
```
synchronized (mon) { 
    ...
    mon.notify(); 
}
```

### join()
java.lang.Thread class provides the join() method which allows one thread to wait until another thread completes its execution.  
When we invoke the join() method on a thread, the calling thread goes into a waiting state. It remains in a waiting state until the referenced thread terminates.  
https://www.baeldung.com/java-thread-join
```
class SampleThread extends Thread {
    ...
}

//In the main Thread
Thread t1 = new SampleThread();
Thread t2 = new SampleThread();
t1.start();
t2.start();
//both threads are started and running in parallel
t1.join();
t2.join();
// join() is to let the current main thread wait for the t1 and t2 to complete
System.out.println("both threads are completed...");
// at this point, both t1 and t2 are completed
```

### notifyAll() vs notify()
- notifyAll() simply wakes all threads that are waiting on this object’s monitor.
- notify() wakes up a single random thread. It is more viable to implement notifyAll(). 
- notify/notifyAll don't release locks like wait does

## java class immutable
How 
- Mark the class final
- Mark all the fields private and final
- for get method, return a copy of any mutuable properties, e.g. Collections.unmodifiableList() to return a copy of the list
- Force all the callers to construct an object of the class directly, i.e. do not use any setter methods
- Do not change the state of the objects in any methods of the class  

Benefit
- thread-safe
- good for Map keys and Set elements, since these typically do not change once created.
- easier to parallelize your program as there are no conflicts among objects.
- The internal state of your program will be consistent even if you have exceptions.
- References to immutable objects can be cached as they are not going to change.

## Exception
- when an exception is thrown from final section, it is thrown to the upper level
- Catch childException after parentException is not allowd
- CheckedException vs UncheckedExcpetion

## Java Geneirc
https://detailfocused.blogspot.com/2009/08/java-generics.html  
https://www.baeldung.com/java-generics-interview-questions

##　Java 8 interview questins 
https://www.baeldung.com/java-8-interview-questions

## Java 8 functional interfaces
functional interface only contains one abstract (non-default) method.  
- Function – it takes one argument and returns a result
- Consumer – it takes one argument and returns no result (represents a side effect)
- Supplier – it takes not argument and returns a result
- Predicate – it takes one argument and returns a boolean
- BiFunction – it takes two arguments and returns a result
- BinaryOperator – it is similar to a BiFunction, taking two arguments and returning a result. The two arguments and the result are all of the same types
- UnaryOperator – it is similar to a Function, taking a single argument and returning a result of the same type

## Java 8 stream

### Terminal Function vs Intermediated Function

### Function.Identity()
  
### Java new types to support function programming...
