---
title: "Is your Singleton Thread-Safe?"
date: "2014-08-31"
categories: 
  - "technical-posts"
tags: 
  - "ios"
  - "multithreading"
  - "threads"
---

##### First, lets have a look into the concepts of some **T****erminologies:**

### **Singleton**

A singleton class returns the same instance no matter how many times an application requests it. A typical class permits callers to create as many instances of the class as they want, whereas with a singleton class, there can be only one instance of the class per process. A singleton object provides a global point of access to the resources of its class. Singletons are used in situations where this single point of control is desirable, such as with classes that offer some general service or resource.

### **Thread-Safe**

Thread safe code can be safely called from multiple threads or concurrent tasks without causing any problems (data corruption, crashing, etc). Code that is not thread safe must only be run in one context at a time. An example of thread safe code is NSArray. You can use it from multiple threads at the same time without issue. On the other hand, NSMutableArray is not thread safe and should only be accessed from one thread at a time.

### **Context Switch**

A context switch is the process of storing and restoring execution state (context) of a process or thread, when we switch between executing different threads on a single core (single process). So that the execution can be resumed from the same point at a later time. This enables multiple processes to share a single CPU and is an essential feature of a multitasking operating system. It’s like switching from one thread (process) to another.

## **Creating Thread-Safe Singletons**

Singletons, A very popular design pattern in every technology. One frequent concern with singletons is that often they’re not thread safe. This concern is well-justified given their use: singletons are often used from multiple controllers accessing the singleton instance at the same time.

Lets take an example, we have a class **DocumentManager**, which manages all our document read, write handling. And the **DocumentManager** class has been implemented as a singleton. To see how things can go wrong really quickly, we’ll create a controlled race condition on the singleton instance.

The current sharedManager function looks like the code below:

 + (instancetype)sharedManager   {
     static DocumentManager \* sharedDocumentManager = nil;
     if (!sharedDocumentManager) {
         sharedDocumentManager = \[\[DocumentManager alloc\] init\];
         NSLog(@"Singleton has memory address at: %@", sharedDocumentManager);
     }
     return sharedDocumentManager;
 }

The code is rather simple in its current state; we create a singleton. However, the if condition branch is not thread safe; if we invoke this method multiple times, there’s a possibility that one thread (call it Thread-A) could enter the if block and a context switch could occur before sharedDocumentManager is allocated. Then another thread (Thread-B) could enter the if, allocate an instance of the singleton, then exit.

When the system context switches back to Thread-A, we’ll then allocate another instance of the singleton, then exit. At that point we have two instances of a singleton — which is not what we want!

To force this condition to happen, replace **sharedManager** function in **DocumentManager.m** with the following implementation:

 + (instancetype)sharedManager   {
     static DocumentManager \* sharedDocumentManager = nil;
     if (!sharedDocumentManager) {
         \[NSThread sleepForTimeInterval:2\];
         sharedDocumentManager = \[\[DocumentManager alloc\] init\];
         NSLog(@"Singleton memory address: %@", sharedDocumentManager);
         \[NSThread sleepForTimeInterval:2\];
     }
     return sharedDocumentManager;
 }

In the code above we’re forcing a context switch to happen with **NSThread’s sleepForTimeInterval:** class method.

Now make a call to **sharedManager** function, for doing the same, Open **AppDelegate.m** and add the following code to the very beginning of **application:didFinishLaunchingWithOptions:**

dispatch\_async(dispatch\_get\_global\_queue(DISPATCH\_QUEUE\_PRIORITY\_HIGH, 0), ^{
         \[DocumentManager sharedManager\];
});
     
dispatch\_async(dispatch\_get\_global\_queue(DISPATCH\_QUEUE\_PRIORITY\_HIGH, 0), ^{
         \[DocumentManager sharedManager\];
});

This creates multiple asynchronous concurrent calls to instantiate the singleton and invoke the race condition as described above.

Build and run our project; check the console output and we’ll see multiple singletons instantiated, as shown below:

\[caption id="attachment\_165" align="aligncenter" width="772"\][![A console output demonstrating Thread-Unsafe Singleton object address.](images/threadsafesingleton1.png)](https://izeeshan.files.wordpress.com/2014/08/threadsafesingleton1.png) Console Output: Thread-Unsafe Singleton object address.\[/caption\]

Notice that there are several lines all showing different addresses of the singleton instance. That defeats the purpose of a singleton, doesn’t it? :)

This output shows we that the critical section executed several times when it should have only have executed once. Now, admittedly, we forced this situation to happen, but we can imagine how this condition could occur unintentionally as well.

##### **Note**: Based upon other system events beyond our control, a variable amount of **NSLogs** will show up on occasion. Threading issues can be extremely hard to debug since they tend to be hard to reproduce.

To correct this condition, the instantiation code should only execute once and block other instances from running while it is in the critical section of the **if** condition. This is exactly what **dispatch\_once** does.

Replace the conditional **if** check with **dispatch\_once** in the singleton initialisation method as shown below:

\+ (instancetype)sharedManager   {
     static DocumentManager \* sharedDocumentManager = nil;
     static dispatch\_once\_t onceToken;
     dispatch\_once(&onceToken, ^{
 
         \[NSThread sleepForTimeInterval:2\];
         sharedDocumentManager = \[\[DocumentManager alloc\] init\];
         NSLog(@"Singleton memory address: %@", sharedDocumentManager);
         \[NSThread sleepForTimeInterval:2\];
     });
     return sharedDocumentManager;
 }

Build and run our app; check the console output and we’ll now see one and only one instantiation of the singleton — which is what we’d expect for a singleton! :)

Now that we understand the importance of preventing race conditions, replace **DocumentManager’s** singleton initialisation with the following implementation:

\+ (instancetype)sharedManager   {
     static DocumentManager \* sharedDocumentManager = nil;
     static dispatch\_once\_t onceToken;
     dispatch\_once(&onceToken, ^{
         sharedDocumentManager = \[\[DocumentManager alloc\] init\];
         NSLog(@"Singleton memory address: %@", sharedDocumentManager);
     });
     return sharedDocumentManager;
}

**dispatch\_once()** executes a block once and only once in a thread safe manner. Different threads that try to access the critical section — the code passed to **dispatch\_once** — while a thread is already in this section are blocked until the critical section completes.

[![there-can-only-be-one](https://izeeshan.files.wordpress.com/2014/08/there-can-only-be-one.jpg?w=286)](https://izeeshan.files.wordpress.com/2014/08/there-can-only-be-one.jpg)

It should be noted that this just makes access to the shared instance thread safe. It does not make the class thread safe, necessarily. We could still have other critical sections in the class, for instance anything that manipulates internal data. Those would need to be made thread safe in other ways, such as synchronising access to the data.

##### Thanks for reading. :)

Some parts of this post is derived from [Ray Wenderlich Tutorials](http://www.raywenderlich.com/60749/grand-central-dispatch-in-depth-part-1 "Ray Wenderlich Tutorials").
