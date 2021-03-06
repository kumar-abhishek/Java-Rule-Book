# Java Rule Book
Basic concepts of Java to answer any question about how Java works specially in job interviews. With combining multiple rules you can answer many java question

**This book is only suitable for someone who has worked in Java**

**Feel free to correct any rule or to suggest any correction needed.**

[![Donate to author](https://www.paypalobjects.com/webstatic/en_US/btn/btn_donate_92x26.png)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=KQJAX48SPUKNC)

##Rules (upto Java 7)

####Final
1. No further change ( So should be assigned either the time of declaration or in of same constructor)
2. JVM allocates same memory location for same constants.

####Static
1. Static members belongs to a class not any instance.
 a. Static member doesn’t belong to any instance hence they can’t access instanced members.
2. One per class. Shared among all class instances. 
 a. Can’t be overridden. But are inherited.

####Concrete class
1. All methods have some functionality.
2. cannot be static
3. Constructor gets called after initializing memory by JVM.
4. Declaration of any member is fully independent from any other class/interface.
5. Can have static member and static block which are executed first in the class and top to bottom.
6. All classes has default constructor, with no statement inside, by default until you create any constructor manually.

####Interface
1. It's all member fields are **public** (since has no implementation private fields can't be used), **final** (since interfance can't be instanciated they can have only 1 value) **static** (since interfance can't be instanciated and member field are not the responsibilities for other class).
2. Methods are public only and can’t be static. (Interface defines external responsibilities (hence public) which may be different for each class (hence non-static).)
3. There is no functionality/implementation, but responsibilities, to be implemented by child class type (class itself or its parent class). So implicitly *abstract* keyword and modifier are optional.
4. Can extend only 1 interface.

#####Common Rules for Concrete class and Interface
1. Cannot extend/implement itself or one of its own member types. If it inherits itself it creates Cycle. If it inherits any child then its hierarchy in inheritance tree becomes inconsistent.
2. Represent the type of its child, sub-child and so on.

####Abstract class
1. Concrete class + Interface = Abstract class
 * Unlike concrete class, it can’t be instantiated.
 * Unlike interface, abstract methods can have any scope and *abstract* keyword is not optional.
 * Unlike interface, fields can’t be static and final.

(Like any simple java class, it may have simple member , static member, static block, constructor etc. It can extend/implement any concrete class, abstract class, or an interface.)

#####Common Rules for Abstract class and Interface
1. Both can have 0 to N number of abstract methods.
2. Both need not to implement abstract methods declared in their parent abstract class/interface.

####Nested/inner class
1. Can access all members of container class.
2. ~~An independent class inside a class which follows rule of a class member.~~ Concrete class rule 5 (So it can be abstract, static abstract, static)

####Anonymous class (closures)
1. Cannot be implemented by any class (as created dynamically)
2. Can access only final external object/var. (as the values of non-final variable can be changed, the behaviour of annonymous class will be unpredictable.)

####Inheritance
1. A class/interface can reference its own instance or any child class instance whichever can be instantiated.
2. A child class can’t refer to its base class instance.
3. A reference can call method of referred instance only.
4. Child class need not to throw an exception while overriding a method, it is automatically get thrown by child class method.
5. Super() must be first statement in child class constructor. If you don’t call super() explicitly, compiler automatically adds empty super().
6. Members can have same name or method signature. In case of ambiguity, *this* is used to represent current class' members and super is used to represent members of parent class. Otherwise the closest declaration will be referred.
7. Until or unless there is no ambiguity of method's declaration inheritance is allowed. (Hence a class can't extend multiple classes but can implement multiple interfaces. Similarly it can extend a class and implement an interface which has methods with same signature.)
8. Child class can't narrow down the scope of method while overriding. (A subclass should always satisfy the contract of the superclass. See Liskov Substitution principle.)

####Garbage Collector
1. It fragment and release only heap memory not stack memory.
2. Whether you System.gc(); or objReference=null; it doesn’t invoke GC until JVM really needs it. But 2nd statement helps GC to collect object quickly.
3. finalize() is called only when object memory is released from heap.

####Serialization
1. Transient and static variables are skipped while serialization/deserialization. In alternate of Transient, a class can have *ObjectStreamField[] serialPersistentFields* of class members which can be serialized.
2. While serialization process all default constructor of super classes are called. But deserialization calls default constructor of all non-serialized class from top of inheritance tree till it meets any serialized class. In addition, deserialization and it doesn’t call constructor of current class.
3. At the time of deserialization, first empty constructor gets called then all deserialized values are assigned to relevant property.

Read: [Serialization – a Sci Fi story](https://articlestack.wordpress.com/2016/04/03/serialization-a-sci-fi-story/)

####Multithreading
1. NEW -> RUNNABLE <-> BLOCKED/WAITING/TIMED_WAITING -> TERMINATED
2. More than 1 thread can’t access same monitor at a time, if both are in running state. (Same monitor means who has same memory address)
3. `synchronized method(…` or `Synchronized(this)` locks object itself. Hence `static synchronized method(…` takes lock on whole class. ([External locks](https://articlestack.wordpress.com/2016/01/23/java-multithreading-external-locks/) or ReentrantLock/ReadWriteLock seems better than synchronized blocks)
4. `notify()` or `notifyAll()`, and `wait()` must be in a synchronized block for the object you are waiting on(Otherwise you'll et `IllegalMonitorException`). And the value of the object must not be changed. `notify()` or `notifyAll()` to awake threads which are waiting. `interrupt()` to awake thread which is sleeping.
5. `sleep()` doesn't release the lock. So other threads are supposed to wait<br />**mutual exlusion**: `wait()` release the lock and acquire it again once relevant `notify()` is called. ([CyclicBarrier](https://articlestack.wordpress.com/2016/01/23/cyclicbarrier/) can be used to wait N threads at a time. CountDownLatch can be used to wait N starting threads. `join` can be used to wait until respective thread is completed. `Semaphore` is used to controll number of threads. )
6. Doubles and longs assignment is not atomic. Mark them `volatile` or synchronized their assignment.
7. [**daemon threads**](https://github.com/NaturalIntelligence/Practical-Java/tree/master/Concurrency/src/os/nushi/concurrency/tracer): (eg garbage collector) killed by JVM once all user threads are finished. JVM will exit when only daemon threads remain. If parent thread is daemon, child thread will also be. 8. `Thread.yield()` gives the scheduler a hint that the current thread is willing to free the processor.

**busy waiting**: wait for an event by performing some active computations that let the thread/process occupy the processor. Eg looping long time to wait instead of using `Thread.sleep()`.
<br/>**ThreadLocal**: allows you to have a variable that will be unique to a given thread. (Thus, if the same code runs in different threads, these executions will not share the value, but instead each thread has its own variable that is local to the thread.)
<br/>**Executor**: Make pooling, scheduling, and interaction with running threads/tasks possible.
<br/>**Callable**: Similar to Runnable but returns some value.
<br/>**Future**: Run by some ExecutorService. Let you poll/block/cancel running task.
<br/>**ThreadPoolExecutor**: perform group operation like set pool size, shutdown pool to stop all running threads etc. `ScheduledThreadPoolExecutor` is extention of this.
<br/>**Fork/Join**: It is an implementation of the ExecutorService. It is designed for work that can be broken into smaller pieces recursively. (CompletableFuture is also worth to check)

<br/>[Read](https://articlestack.wordpress.com/category/tutorial/java/multithreading/): [Important Terms](https://articlestack.wordpress.com/2016/01/19/java-multithreading/), [Synchronization & Deadlock](https://articlestack.wordpress.com/2016/01/20/synchronization/), [wait & notify](https://articlestack.wordpress.com/2016/01/20/java-multithreading-notify-wait/), [join](https://articlestack.wordpress.com/2016/01/23/java-multithreading-join/), [Test your threading code](Thread Tracer)

####Exception
1. **try** must be followed by either **catch** or **finally** or both
2. finally is always called.
3. Only 1 catch block is called start from top to bottom.
4. If you call a method throwing some exception, either you need to catch the same or wider exception or throw it again.

####String
1. == checks for reference only.
2. It overrides equals() which checks for value only.
3. Immutable so treated as constant by JVM.
4. It overrides hashcode().

####Enum 
1. Is Self-Type. Declared as  Enum< E extends Enum< E>>
2. Can implement an interface.

####Collection/Map
Have a look on these [charts](https://github.com/NaturalIntelligence/Java-Rule-Book/blob/master/collections.pdf) for better understanding;

1. **HashMap**: key is used to calculate a hash which is used then to find a bucket where that particular key is stored. If there are multiple objects in the same bucket they are stored in linked list( balanced tree after a threshhold in java8) and equals() is used to get an element.
2. **WeakHashMap**: Same as HashMap but entries can be garbage collected.
3. **IdentityHashMap**: Same as HashMap but == is used instead of equals()
4. **HashTable**: Same as HashMap (other than java8 changes) but it is synchronized and doesn't allow `null`.
5. Linked Set/List/Map and ArrayList maintain insertion order.
6. List and Multimap allow duplicate items
7. Sorted Map/Set (Tree Map/Set) requires item to be comparable.( It can help you to maintain insertion order.)

####Generics 

* If B extends A, means B is-a type of A, doesn't mean `SomeClass<B>` is-a type of `SomeClass<A>`. So
* Unbounded Wildcard: `SomeClass<B>` can be assigned to `SomeClass<?>`.

<p text-align="center">
<img src="https://docs.oracle.com/javase/tutorial/figures/java/generics-wildcardSubtyping.gif"/>
</p>

* If A extends B, means B is-a type of A, then `B<E>` is-a type of `A<E>`
* **Upper Bounded Wildcards**(covariance or Narrowing a reference): If A extends B, means B is-a type of A, also means that `SomeClass<B>` is-a type of `SomeClass<? extends A>`.
* **Lower Bounded Wildcards**(contravariance or Widening a reference): `? super T` means T and all classes which are parent of T.
* Multiple bounds: `<T extends A & B>`
* Capture: At the time generic method call, type should be known;
```java
List<? extends Fruit> fruits = new ArrayList<Fruit>();
fruits.set(0, fruits.get(0));//Compile time error
```
* Type inference: `List<A> list = new ArrayList<>()` and `list.<A>add(a); list.add(a);` are allowed
* **Erasure**: During the type erasure process, the Java compiler erases all type parameters and replaces each with its first bound if the type parameter is bounded, or Object if the type parameter is unbounded.
* Cannot Instantiate Generic Types with Primitive Types
* Cannot Declare Static Fields of Parameterized Types
* Cannot Use Casts or instanceof of Parameterized Types
* Cannot Create Arrays of Parameterized Types 
* Cannot Create, Catch, or Throw Objects of Parameterized Types
* Wildcards guideines
 * Use the ? extends wildcard if you need to retrieve object from a data structure
 * Use the ? super wildcard if you need to put objects in a data structure
 * If you need to do both things, don’t use any wildcard.


####I/O
TODO

####Networking
TODO

####Other
1. hashCode() returns same value for two objects if obj1.equals(obj2) is true. hashcode is used to narrow up the results of fast searching. But it doesn't gurantee that the objects are same.
2. equals() method must exhibit the following properties:
  * *Symmetry*: For two references, a and b, a.equals(b) if and only if b.equals(a)
  * *Reflexivity*: For all non-null references, a.equals(a)
  * *Transitivity*: If a.equals(b) and b.equals(c), then a.equals(c)
  * *Consistency* with hashCode(): Two equal objects must have the same hashCode() value
