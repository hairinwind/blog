
## final 

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
