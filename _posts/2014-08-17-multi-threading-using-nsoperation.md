---
title: "Multi-Threading using NSOperation"
date: "2014-08-17"
categories: 
  - "technical-posts"
tags: 
  - "multithreading"
  - "nsoperation"
  - "nsthread"
  - "posix"
  - "threads"
---

Recently I had given couple of presentations on Multithreading using NSOperations and GCD. Here is the first draft of transcript.

## CPU (Central Processing Unit)

WHY I’m starting from here, because everything happens here only. But this is not the reason, The reason is to start from here is that how earlier days of computing works?Before going into the threads, lets go into initial days of computing, then we’ll try to solve some questions like:

- What do we understand by Cores like single core, dual core, octa core systems?
- How does these came into existences?
- What are the problem we faced before building this?
- How does it effect our system?

## Clock Speed

The Clock Speed (of CPU) is used to determine the maximum amount of wok a computer (CPU) can perform in a specific time (a time unit). So we can say, our system executes our programs with x clock speed.

**Problems:** Because the day after day technology is getting advance, So design of processor which takes cares of our task getting compact and complex. So many constraints started to come when we were working with ONE processor, and started to limit out the maximum clock speed of processors. So the designer looked for other ways to increase the total performance of their chips.

**Solution:** The solution is, they settled on increasing the number of processor cores on each chip. By increasing the number of cores, a single chip could execute more instructions per second without increasing the CPU speed (Clock Speed) or changing the chip size or other thermal characteristics.

**Another Problem:** So we have understood about the need of cores, now the next thing is How to use these cores? How to take advantage of the extra cores? We have n number of cores now… This means they can execute our instructions simultaneously. If we have two different task then they can executed parallel. So we need a software now, A software that can use these cores and perform task simultaneously or can do multiple things parallel.

## Thread

Here comes the Threads, We have multitasking operating systems like OS X or iOS which has got the thread APIs to work with these cores.

From a technical standpoint, a thread is a combination of the kernel-level and application-level coding (data structure), which is needed to manage the execution of code or a task. The kernel-level structures coordinate the dispatching of events to the thread and the preemptive scheduling of the thread on one of the available cores. The application-level structures include the call stack for storing function calls and the structures the application needs to manage and manipulate the thread’s attributes and state.

Having multiple threads in an application provides two very important potential advantages:

- Multiple threads can improve an application’s perceived responsiveness.
- Multiple threads can improve an application’s real-time performance on multicore systems.

With multicore computers common these days, threads provide a way to increase performance in some types of applications. Threads that perform different tasks can do so simultaneously on different processor cores, making it possible for an application to increase the amount of work it does in a given amount of time.Threads let us solve two tricky problems:

- **Parallel computation** — In this world of multicore CPUs, many tasks will run faster if you divide the work between threads that are scheduled on different cores. This approach can be useful even on a single core system. Specifically, if your computation thread spends some fraction of its time blocked (for example, waiting for disk I/O), you can use multiple threads to do useful computation on one thread while the other thread is blocked.
- **Asynchronous computation** — If you have some long-running computation to do and, as is the case on iOS, you must keep the main thread responsive, you can move that computation to a secondary thread. This allows your main thread to treat the computation as it would any other asynchronous operation.

A common approach is to use the NSThread API directly, or perhaps take advantage of Cocoa's- _performSelectorInBackground:withObject_: method.

### NSThread: (The traditional Way)

- Cocoa implements threads using the NSThread class, Foundation framework contains this class.
- Cocoa also provides methods on NSObject for spawning new threads and executing code on already-running threads.
- Based on POSIX ‘Portable Operating System Interface’ threading APIs (Pthreads), now a days every thread is POSIX
- Mac OS X (Mavericks) is 100% POSIX compliant.

### Hazards of threaded programming

However, threads are not always the right solution. Threads have a significant cost, in terms of both memory and, more importantly, code complexity.

Problem will arise when you increase number of cores in your system. Why, because our threaded code does not scale vary well, to arbitrary numbers of cores. We cannot create as many threads as there are cores and expect a program to run well. Its very challenging for an application to compute your task that can use available number of cores in your system efficiently. Even if you manage to do so, to get the numbers correct, there is still a challenge to run these many thread efficiently and keeping them from interfering with one another.

Although threads have been around for many years and continue to have their uses, they do not solve the general problem of executing multiple tasks in a scalable way. With threads, the actual burden of creating a scalable solution rests squarely on the shoulders of you, the developer.

- **UIKit Access:** Code ends up calling UIKit on a secondary thread, which is not allowed.
- **DB Access:** Not calling database function on same thread (Creating DB on main, then executing on another may cause you crash)
- **Stale Result:** Accessing a property with multiple threads leaves the user interface showing stale results.
- **Cancellation:** It would be nice if we could stop a thread, which needs to be discarded, so that it doesn't waste CPU cycles, battery life, and memory.
- **Thread-safe De-allocation:** If you run any threaded code within UIKit objects (such as a view controller, where the -dealloc method typically releases UIView objects), there's a chance that the object's –dealloc method will be called on a secondary thread.

**Solutions** \- Lets summarize the problem: There must be a way for an app to take advantage of multiple cores. And Solution has to be simple:  provides the solution of all these problems.

- Operations _(NSOperationQueue)_
- GCD _(Grand Central Dispatch)_
- Notification queue  (_addObserverForName:object:queue:usingBlock:)_
- Asynchronous (_NSURLConnections, NSURLSessions)_
- Timers (_NSTimer)_
- Separate Processes

In many cases it's a good idea to use the asynchronous APIs available in iOS instead.

- **Periodic execution** — Starting a thread just to execute code periodically is never a good idea. You should use NSTimer instead.
- **Networking** — On a typical iOS device, the CPU is much faster than the network so, if you start a thread to do your networking synchronously, that thread will spend most of its time blocked. A better approach is to use the asynchronous networking APIs provided by iOS.

## NSOperations

An abstract class comes to the rescue! NSOperation and NSOperationQueue are higher level classes that have greatly simplified the process of dealing with multiple threads.

- Introduced in OS X v10.5, an operation object is a wrapper for a task that would normally be executed on a secondary thread.
- This wrapper hides the thread management aspects of performing the task, leaving you free to focus on the task itself.
- We typically use these objects in conjunction with an operation queue object, which actually manages the execution of the operation objects on one or more threads.
- We only need to focus on business logic, rest NSOperation does it. It make sure our business logic works correctly with other system objects.
- It encapsulates (executes) the code and data associated with a single task.
- Because it is abstract, we do not use this class directly but instead subclass or use one of the system-defined subclasses (NSInvocationOperation or NSBlockOperation) to perform the actual task.
- An operation object is a single-shot object—that is, it executes its task once and cannot be used to execute it again.
- And It’s a Thread Safe class.

### NSOperations Features

#### **Manage Thread Lifecycle:**

- NSOperation is the one way to manage your threaded code, it takes care of the whole lifecycle of a thread from thread creation to destruction.
- It provides the infrastructure for solving all of the problems discussed above.

#### **Asynchronous:**

- NSOperation is a class that allows you to **model asynchronous operations** in a structured fashion. It Encapsulate a unit of work in an object asynchronously.
- It provides a way for the high-level parts of your application to start asynchronous operations without worrying about the details of how they are executed.
- Operation objects are non-concurrent by default.

#### **KVO Compliant Properties**:

The NSOperation class is key-value coding (KVC) and key-value observing (KVO) compliant for several of its properties. As needed, you can observe these properties to control other parts of your application. The properties you can observe include the following:

- _isCancelled - read-only property_
- _isConcurrent - read-only property_
- _isExecuting - read-only property_
- _isFinished - read-only property_
- _isReady - read-only property_
- _dependencies - read-only property_
- _queuePriority - readable and writable property_
- _completionBlock - readable and writable property_

#### **Operation Dependencies:**

Dependencies are a convenient way to execute operations in a specific order.

- You can make operations dependent on other operations, and thereby establish chains of operation execution and operation fan in.
- You can add and remove dependencies for an operation using the _addDependency_: and _removeDependency_: methods.
- By default, an operation object that has dependencies is not considered ready until all of its dependent operation objects have finished executing. Once the last dependent operation finishes, however, the operation object becomes ready and able to execute.
- The dependencies supported by NSOperation make no distinction about whether a dependent operation finished successfully or unsuccessfully. (In other words, canceling an operation similarly marks it as finished.) It is up to you to determine whether an operation with dependencies should proceed in cases where its dependent operations were cancelled or did not complete their task successfully. This may require you to incorporate some additional error tracking capabilities into your operation objects.
- Any operation can be dependent on any number of operations. When you make operation A dependent on operation B, even though you call “start” on operation A, it will not start unless operation B isFinished is true. For example:

_DownloadOperation \*downloadOp = \[\[DownloadOperation alloc\] init\]; 
// DownloadOperation is a subclass of NSOperation_
_FilterOperation \*filterOp = \[\[MyFilterOperation alloc\] init\]; 
// FilterOperation is a subclass of NSOperation_ _\[filterOp addDependency:downloadOp\];_ _// To remove dependencies:_ _\[filterOp removeDependency:downloadOp\];_ 

#### **Priority:** 

Sometimes the operation you wish to run in the background is not crucial and can be performed at a lower priority. You set the priority of an operation by using “_setQueuePriority_:”.

\[filterOp setQueuePriority:NSOperationQueuePriorityVeryLow\];

Other options for thread priority are:

- _NSOperationQueuePriorityLow,_ 
- _NSOperationQueuePriorityNormal,_ 
- _NSOperationQueuePriorityHigh, and_ 
- _NSOperationQueuePriorityVeryHigh._ 

When you add operations to a queue, the NSOperationQueue looks through all of the operations, before calling “start” on them. Those that have higher priorities will be executed first. Operations with the same priority will be executed in order of submission to the queue (FIFO).

#### **Completion block:** 

Another useful method in NSOperation class is _setCompletionBlock_:. If there is something that you want to do once the operation has been completed, you can put it in a block and pass it into this method. Note that there is no guarantee the block will be executed on the main thread.

\[filterOp setCompletionBlock: ^{
    NSLog(@"Finished filtering an image.");
}\]; 

#### **Thread Confinement:** 

NSOperation encourages a programming model called **Thread Confinement**. In this model the resources used by the thread are owned by that thread, not shared between threads, where you can still specify priorities and manage dependencies between operations.

#### **Cancellation:**

An operation can also be canceled. Canceling an operation object leaves the object in the queue but notifies the object that it should abort its task as quickly as possible. For currently executing operations, this means that the operation object’s work code must check the cancellation state, stop what it is doing, and mark itself as finished. In OS X v10.6 and later, canceling an operation causes the operation to ignore any dependencies it may have. This behavior makes it possible for the queue to execute the operation’s start method as soon as possible. The start method, in turn, moves the operation to the finished state so that it can be removed from the queue.

#### **Concurrent vs Non-Concurrent Operations:**

NSOperation objects are non-concurrent by default. But If you plan on executing an operation object manually, instead of adding it to a queue, you can design your operation to execute in a concurrent or non-concurrent manner.

In a **non-concurrent operation**, the operation’s task is performed synchronously—that is, the operation object does not create a separate thread on which to run the task. Thus, when you call the start method of a non-concurrent operation directly from your code, the operation executes immediately in the current thread. By the time the start method of such an object returns control to the caller, the task itself is complete.

A **concurrent operation** runs asynchronously. In other words, when you call the start method of a concurrent operation, that method could return before the corresponding task is completed. This might happen because the operation object created a new thread to execute the task or because the operation called an asynchronous function.

**Note:** In OS X v10.6, operation queues ignore the value returned by isConcurrent and always call the start method of your operation from a separate thread. In OS X v10.5, however, operation queues create a thread only if isConcurrent returns NO. In general, if you are always using operations with an operation queue, there is no reason to make them concurrent.

A good example of a concurrent operation is one that executes an HTTP request using the NSURLConnection API.

#### **Notes on NSOperation (_Caveats_)**

- A finished operation cannot be restarted.
- You cannot reuse an operation. Once it is added to a queue, you give up ownership. If you want to use the same operation class again, you must create a new instance.
- If you cancel an operation, it will not happen instantly. It will happen at some point in the future when someone explicitly checks for _isCancelled == YES_ in “main”; otherwise, the operation will run until it is done.
- Always check for the isCancelled property frequently. You don’t want to run a operation in the background if it is no longer required!

## NSOperationQueue

NSOperationQueue also has a fairly simple interface. It is even simpler than NSOperation, because you don’t need to subclass it, or override any method — you simply create one. It is a good practice to give your queue a name; this way you can identify your operation queues at run time and make it debugging easier.

### NSOperationsQueue Features

#### **ADD Operations:**

The NSOperationQueue class regulates the execution of a set of NSOperation objects. Each operation is executed by an operation queue (NSOperationQueue). As soon as an operation is added to a queue, you should relinquish ownership by sending a release message to the operation object (if using manual reference counting, no ARC), and the queue will then assume responsibility to start the operation. At this point, it is up to the queue as to when it will call “start”.

_DownloadOperation \*downloadOp = \[\[DownloadOperation alloc\] init\];_ \[myQueue addOperation:downloadOp\];
\[downloadOp release\]; // Manual Reference Counting

#### **READY** & **EXECUTE Operations:**

- An operation object is not considered ready to execute until all of its dependent operations have finished executing.
- Operations within the queue (but not yet executing) are themselves organized according to priority levels and inter-operation object dependencies and are executed accordingly.
- An operation queue executes its queued operation objects based on their priority and readiness. If all of the queued operation objects have the same priority and are ready to execute when they are put in the queue—that is, their _isReady_ method returns _YES_—they are executed in the order in which they were submitted to the queue (**FIFO**).

#### **PENDING Operations:** 

At any time you can ask a queue which operations are in the queue, and how many operations there are in total. Remember that only those operations that are waiting to be executed, and those that are running, are kept in the queue. As soon as an operation is done, it is gone from the queue.

NSArray \*active\_and\_pending\_operations = myQueue.operations;
NSInteger count\_of\_operations = myQueue.operationCount;

#### **MULTIPLE Queues:**

An application may create multiple operation queues and submit operations to any of them. You can create operation queues to model your problem space. For example, a file copying program could use one operation queue per disk to avoid thrashing the disk head.

#### **THREADS & QUEUES**

A queue is not the same thing as thread. A queue can have multiple threads. Each operation within a queue is running on its own thread. Take the example where you create one queue, and add three operations to it. The queue will launch three separate threads, and run all operations concurrently on their own threads.

**How many threads** will be created? That’s a good question! :) It depends on the hardware. By default, NSOperationQueue class will do some magic behind the scenes, decide what is best for the particular platform the code is running on, and will launch the maximum possible number of threads.

Consider the following example. Assume the system is idle, and there are lots of resources available, so NSOperationQueue could launch something like eight simultaneous threads. Next time you run the program, the system could be busy with other unrelated operations which are consuming resources, and NSOperationQueue will only launch two simultaneous threads.

#### **Queue Width:**

Each queue has a maximum number of operations that it will execute in parallel / concurrently (_maxConcurrentOperationCount_), commonly known as the **Queue Width**. This width defaults to a value that's appropriate for the device on which you're running, but you can set it to a value that's appropriate for the task at hand. For example, you can guarantee that operations are serialized (executed one at a time) by setting the queue width to 1. Or, if your operations hit the network, you can use the queue width to limit the number of concurrent network operations.

NSOperationQueue may choose to run any number of concurrent operations, but it won’t be more than the maximum.

myQueue.MaxConcurrentOperationCount = 3;

If you change your mind, and want to set MaxConcurrentOperationCount back to its default, you would perform the following changes:

myQueue.MaxConcurrentOperationCount = NSOperationQueueDefaultMaxConcurrentOperationCount;

**Serial Queue:** For a queue whose maximum number of concurrent operations is set to 1, this equates to a **Serial Queue**. However, you should never rely on the serial execution of operation objects. Changes in the readiness of an operation can change the resulting execution order.

#### **Pause (Suspend) Queue:**

We can pause a queue by setting _setSuspended:YES._ This will suspend all operations in a queue — you can’t suspend operations individually. To resume the queue, simply _setSuspended:NO_.

// Suspend a queue 
\[myQueue setSuspended:YES\]; 
// Resume a queue
\[myQueue setSuspended: NO\];

#### **CANCEL Operations:**

After being added to a queue, an operation remains in that queue until it is explicitly canceled or finishes executing its task. To cancel all operations in a queue, you simply call “cancelAllOperations”. The reason is that cancelAllOperations doesn’t do much, except that it calls “cancel” on every operation in the queue — it doesn’t do anything magical! :)

If an operation has not yet started, and you call “cancel” on it, the operation will be cancelled and removed from the queue. However, if an operation is already executing, it is up to that individual operation to recognize the cancellation (by checking the isCancelled property) and stop what it is doing.

\[myQueue cancelAllOperations\];

#### **KVO-Compliant Properties**

The NSOperationQueue class is key-value coding (KVC) and key-value observing (KVO) compliant. You can observe these properties as desired to control other parts of your application. The properties you can observe include the following:

- _operations - read-only property_
- _operationCount - read-only property_
- _maxConcurrentOperationCount - readable and writable property_
- _suspended - readable and writable property_
- _name - readable and writable property_

Although you can attach observers to these properties, you should not use Cocoa bindings to bind them to elements of your application’s user interface. Code associated with your user interface typically must execute only in your application’s main thread. However, KVO notifications associated with an operation queue may occur in any thread.

#### **Add Operation With Block:**

If you have a simple operation that does not need to be subclassed, you can simply pass it into a queue by way of a block. If you want to send any data back from the block, remember that you should not pass into the block any strong reference pointers; instead, you must use a weak reference. Also, if you want to do something that is related to the UI in the block, you must do it on the main thread:

UIImage \*myImage = nil;
// Create a weak reference
\_\_weak UIImage \*myImage\_weak = myImage;
// Add an operation as a block to a queue
\[myQueue addOperationWithBlock: ^ {
    // a block of operation
    NSURL \*aURL = \[NSURL 
       URLWithString:@"http://www.somewhere.com/image.png"\];
    NSError \*error = nil;
    NSData \*data = \[NSData dataWithContentsOfURL:aURL 
                     options:nil error:&error\];
    If (!error)
      \[myImage\_weak imageWithData:data\];
   // Get hold of main queue (main thread)
    \[\[NSOperationQueue mainQueue\] addOperationWithBlock: ^ { 
        myImageView.imag   e = myImage\_weak; // updating UI 
    }\]; 
  }\];

##### [Presentation PPT](https://speakerdeck.com/izeeshan/multi-threading-using-nsoperation "Presentation PPT") and [Sample Code](https://github.com/zeeshankhan/ZKNSOperationUse "Use of NSOperation")

##### Thanks for reading :)

##### Second draft of presentation is on its way... stay tuned!!
