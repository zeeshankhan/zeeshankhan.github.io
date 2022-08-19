---
title: "Handling Shared Resources using Dispatch Barriers"
date: "2014-09-03"
categories: 
  - "technical-posts"
tags: 
  - "blocks"
  - "gcd"
  - "ios"
  - "max-os-x"
  - "multiple-threads"
  - "multithreading"
  - "objective-c"
  - "threads"
---

### Handling Thread-Unsafe Shared Resource

In the [last post](http://izeeshan.wordpress.com/2014/08/31/threadsafe-singleton/ "Is your Singleton Thread-Safe?"), we have discussed about creating thread-safe singleton objects. But creating a thread-safe singleton does not solve all the issues. If your singleton object uses any mutable object like _NSMutableArray_, then you need to consider whether that object is itself thread-safe or not. There is a misconception that the Foundation framework is thread-safe and the Application / UI Kit framework is not. Unfortunately, this is somewhat misleading. Each framework has areas that are thread-safe and areas that are not thread-safe. Apple maintains a [helpful list](https://developer.apple.com/library/mac/documentation/cocoa/conceptual/multithreading/ThreadSafetySummary/ThreadSafetySummary.html "Thread-Safe Unsafe classes") of the numerous Foundation framework classes which are not thread-safe.

### The Problem

Lets take an example, we have a class **DocumentManager**, which manages all our document read, write handling. And the **DocumentManager** class has been implemented as a singleton. This singleton object uses a _NSMutableArray_ property for keeping all the document names, which is a Thread-Unsafe class. In **DocumentManager**, we have two functions _addDocumentName_ and _allDocs_. Although many threads can read an instance of _NSMutableArray_ simultaneously without issue, It's not safe to let one thread modify the array while another is reading it. Our singleton doesn't prevent this condition from happening in its current state. To see the problem, have a look at code, which has been reproduced below:

\- (void)addDocumentName:(NSString\*)docName {
    if (docName)
       \[\_arrDocs addObject:docName\];
}

This is a write method, which modifies the mutable array object by adding a document name into it. Now take a look at getter method:

\- (NSArray\*)allDocs { 
    return \[NSArray arrayWithArray:\_arrDocs\];
}

This is a read method as it's reading the mutable array property. It makes an immutable copy for the caller in order to defend against the caller mutating the array inappropriately, but none of this provides any protection against one thread calling the the write method addDocumentName: while simultaneously another thread calls the read method allDocs. This is the classic software development [Readers-Writers Problem](http://wikipedia.moesalih.com/Readers%E2%80%93writers_problem "Readers Writers Problem"). GCD provides an elegant solution of creating a [Readers-writer lock](http://wikipedia.moesalih.com/Readers%E2%80%93writer_lock "Readers Writer Lock") using dispatch barriers.

## **Dispatch Barriers**

It allows you to make thread-unsafe object to thread-safe. It creates a synchronisation point for a code block executing in a concurrent dispatch queue. Dispatch barriers are a group of functions acting as a serial queue style objects when working with concurrent queues. Using GCD's barrier API ensures that the submitted block is the only item executed on the specified queue for that particular time. This means that all items submitted to the queue prior to the dispatch barrier must complete before the block will execute. When the block's turn arrives, the barrier executes the block and ensures that the queue does not execute any other blocks during that time. Once finished, the queue returns to its default implementation. GCD provides both synchronous and asynchronous barrier functions. The diagram below illustrates the effect of barrier functions on various asynchronous blocks:

\[caption id="attachment\_178" align="aligncenter" width="660"\][![Dispatch Barrier](https://izeeshan.files.wordpress.com/2014/09/dispatch-barrier1.png?w=660)](https://izeeshan.files.wordpress.com/2014/09/dispatch-barrier1.png) Execution of Dispatch Barrier\[/caption\]

Notice how in normal operation the queue acts just like a normal concurrent queue. But when the barrier is executing, it essentially acts like a serial queue. That is, the barrier is the only thing executing. After the barrier finishes, the queue goes back to being a normal concurrent queue. Here's when you would - and wouldn't - use barrier functions:

- **Custom Serial Queue**: A bad choice here; barriers won't do anything helpful since a serial queue executes one operation at a time anyway.
- **Global Concurrent Queue**: Use caution here; this probably isn't the best idea since other systems might be using the queues and you don't want to monopolise them for your own purposes.
- **Custom Concurrent Queue**: This is a great choice for atomic or critical areas of code. Anything you're setting or instantiating that needs to be thread safe is a great candidate for a barrier.

Since the only decent choice above is the custom concurrent queue, you'll need to create one of your own to handle your barrier function and separate the read and write functions. The concurrent queue will allow multiple read operations simultaneously. Creating a custom concurrent queue is easy: just pass **DISPATCH\_QUEUE\_CONCURRENT** to the **dispatch\_queue\_create** function. Open your singleton class, and add the following private property to the class extension category:

@property (nonatomic, strong) dispatch\_queue\_t concurrentDocumentQueue;

Now replace _addDocumentName_ function with below implementation:

\- (void)addDocumentName:(NSString\*)docName { 
    if (docName) { // 1 
        dispatch\_barrier\_async(self. concurrentDocumentQueue, ^{ // 2 
            \[\_arrDocs addObject:docName\]; // 3 
        }); 
    } 
}

Here's how your new write function works:

1. Check that there's a valid document name before performing all the following work.
2. Add the write operation using your custom queue. When the critical section executes at a later time this will be the only item in your queue to execute.
3. This is the actual code which adds the object to the array. Since it's a barrier block, this block will never run simultaneously with any other block in _concurrentDocumentQueue_.

This takes care of the write method, but we also need to implement the _allDocs_ read method and instantiate _concurrentDocumentQueue_. To ensure thread safety with the writer side of matters, you need to perform the read on the _concurrentDocumentQueue_ queue. You need to return from the function though, so you can't dispatch asynchronously to the queue because that wouldn't necessarily run before the reader function returns. In this case, _dispatch\_sync_ would be an excellent candidate. _dispatch\_sync()_ synchronously submits work and waits for it to be completed before returning. Use _dispatch\_sync_ to track of your work with dispatch barriers, or when you need to wait for the operation to finish before you can use the data processed by the block. If you're working with the second case, you'll sometimes see a _\_\_block_ variable written outside of the _dispatch\_sync_ scope in order to use the processed object returned outside the _dispatch\_sync_ function. You need to be careful though. Imagine if you call _dispatch\_sync_ and target the current queue you're already running on. This will result in a deadlock because the call will wait to until the block finishes, but the block can't finish (it can't even start!) until the currently executing task is finished, which can't! This should force you to be conscious of which queue you're calling from” as well as which queue you're passing in. Here's a quick overview of when and where to use _dispatch\_sync_:

- **Custom Serial Queue**: Be VERY careful in this situation; if you're running in a queue and call _dispatch\_sync_ targeting the same queue, you will definitely create a deadlock.
- **Main Queue (Serial)**: Be VERY careful for the same reasons as above; this situation also has potential for a deadlock condition.
- **Concurrent Queue**: This is a good candidate to sync work through dispatch barriers or when waiting for a task to complete so you can perform further processing.

Now replace _allDocs_ method with the following implementation:

\- (NSArray\*)allDocs {
    \_\_block NSArray \*array; // 1
    dispatch\_sync(self.concurrentDocumentQueue, ^{ // 2
        array = \[NSArray arrayWithArray:\_arrDocs\]; // 3
    });
    return array;
}

Here's your read function. Taking each numbered comment in turn, you'll find the following:

1. The _\_\_block_ keyword allows objects to be mutable inside a block. Without this, array would be read-only inside the block and your code wouldn't even compile.
2. Dispatch synchronously onto the _concurrentDocumentQueue_ to perform the read.
3. Store the document array in array and return it.

Finally, you need to instantiate your _concurrentDocumentQueue_ property. Since we are working on a singleton class, so we will make the changes in the _sharedManager_ function so that it instantiate only once. Change _sharedManager_ method to instantiate the custom concurrent queue like so:

\+ (instancetype)sharedManager {
    static DocumentManager \*sharedDocumentManager = nil;
    static dispatch\_once\_t onceToken;
    dispatch\_once(&onceToken, ^{
      sharedDocumentManager = \[\[DocumentManager alloc\] init\];
      sharedDocumentManager->\_arrDocs = \[NSMutableArray array\];
      // ADD THIS:
      sharedDocumentManager->\_concurrentDocumentQueue = dispatch\_queue\_create("com.mycompany.ThreadySafety.documentQueue",DISPATCH\_QUEUE\_CONCURRENT); 
    });
    return sharedDocumentManager;
}

This initialises _concurrentDocumentQueue_ as a concurrent queue using _dispatch\_queue\_create_. The first parameter is a reversed DNS style naming convention; make sure it's descriptive since this can be helpful when debugging. The second parameter specifies whether you want your queue to be serial or concurrent. Note: When searching for examples on the web, you'll often see people pass 0 or NULL as the second parameter of _dispatch\_queue\_create_. This is a dated way of creating a serial dispatch queue; it's always better to be specific with your parameters. Your mutable property now thread safe. No matter where or how you read or write this property, you can be confident that it will be done in a safe manner with no amusing surprises.

##### You can download the [source code](https://github.com/zeeshankhan/GCDExercise "GCD Practice") which contains all the code snippet used in this post. Some parts of this post is derived from [Ray Wenderlich Tutorials](http://www.raywenderlich.com/60749/grand-central-dispatch-in-depth-part-1 "Ray Wenderlich Tutorials"). Thanks for reading. :)
