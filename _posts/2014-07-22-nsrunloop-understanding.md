---
title: "NSRunLoop understanding"
date: "2014-07-22"
categories: 
  - "technical-posts"
tags: 
  - "nsrunloop"
  - "nsthread"
  - "runloops"
---

## **NSRunLoop** (A wrapper of _CFRunLoop_)

Nothing but **_a loop_** or **_a event processing loop_**, where your thread enters, where it runs event handlers for incoming events.

- They handle (manage/monitor) input sources (input devices) like keyboard, mouse events from window system.
- They also observe objects like NSPort, NSConnection (NSTimer events).

### **_How runloop works:_**

- It takes all input events,
- Keeps them in runloop stack,
- Fires one at a time,
- Gives events to handlers to process (input data as events, callback methods as handlers for Connection object),
- Waits till that event gets completed,
- Once complete return back to runloop stack,
- Check for another event to process,
- If none is there goes to sleep.

\- Basically, It sends asynchronous callbacks to handlers, if we don’t want remove / invalidate object from runloop. - They must be associated at least one mode, because modes determines what event should processed. - Scheduling a run loop source doesn't allow the source's callbacks to run concurrently with other source's callbacks.

### **Thread and NSRunLoop**

- Our application can not create or explicitly manage NSRunLoop objects.
- Each thread (NSThread) object has its own NSRunLoop object (automatically created).
- Even main thread has its own NSRunLoop object (automatically created).
- There is exactly one run loop per thread. You neither create nor destroy a thread’s run loop.

### **Thread Safety**

1. NSRunLoop is **Not Thread Safe** means will not return to loop until one process gets completed.
2. NSRunLoop’s methods should only be called within the context of the current thread.
3. We should NEVER call the methods of NSRunLoop object running in a different thread (might cause unexpected results).

### **Some book notes:**

- A run loop is a piece of infrastructure used to manage events arriving asynchronously on a thread.
- A run loop works by monitoring one or more event sources for the thread. As events arrive, the system wakes up the thread and dispatches the events to the run loop, which then dispatches them to the handlers you specify. If no events are present and ready to be handled, the run loop puts the thread to sleep.
- Run loops make it possible to create long-lived threads that use a minimal amount of resources. Because a run loop puts its thread to sleep when there is nothing to do, it eliminates the need for polling, which wastes CPU cycles and prevents the processor itself from sleeping and saving power.
- To configure a run loop, all you have to do is launch your thread, get a reference to the run loop object, install your event handlers, and tell the run loop to run.
- Every thread has one and only one run loop. Each run loop, and hence each thread, however, has its own set of input modes that determine which input sources are listened to when the run loop is run. The input modes defined in one run loop do not affect the input modes defined in another run loop, even though they may have the same name.
- Despite some outward appearances, the NSRunLoop class is not thread safe. You should call the instance methods of this class only from the thread that owns it.
- **Run loop mode** A collection of input sources, timer sources, and run loop observers associated with a particular name. When run in a specific “mode,” a run loop monitors only the sources and observers associated with that mode.
- If you have some kind of user interface, or other code that needs to listen to events like network ports, you need a run loop.
- In general, your application does not need to either create or explicitly manage NSRunLoop objects. Each NSThread object, including the application’s main thread, has an NSRunLoop object automatically created for it as needed. If you need to access the current thread’s run loop, you do so with the class method currentRunLoop.

## Key Terms

#### **Synchronization Tool**

- One of the hazards of threaded programming is resource contention among multiple threads. If multiple threads try to use or modify the same resource at the same time, problems can occur.
- One way to alleviate the problem is to eliminate the shared resource altogether and make sure each thread has its own distinct set of resources on which to operate.
- Another way is to synchronize access to the resource using locks, conditions, atomic operations, and other techniques. Best way is to use @synchronized(object) {Everything will be protected here.} block. It’s a way to acquire lock for given object, no other thread can acquire if one is already have it.

#### **Mutex / Locks**

- Locks provide a brute force form of protection for code that can be executed by only one thread at a time. The most common type of lock is mutual exclusion lock, also known as a **mutex**.
- A lock that provides mutually exclusive access to a shared resource.
- A mutex lock can be held by only one thread at a time. Attempting to acquire a mutex held by a different thread puts the current thread to sleep until the lock is finally acquired.
- When a thread tries to acquire a mutex that is currently held by another thread, it blocks until the lock is released by the other thread.

#### **Semaphore**

- A protected variable that restricts access to a shared resource.
- Mutex and Conditions are both different types of semaphore.

##### **Thread**: A separate path of execution for code. **Process**: A running executable, which can encompass multiple threads. **Task**: The abstract concept of work that need to be performed.

### References:

##### [Apple Guide](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW1 "Multithreading Programming Guide"), [Class Reference](https://developer.apple.com/library/mac/documentation/cocoa/reference/foundation/classes/nsrunloop_class/reference/reference.html "Class Reference"), [Concurrent Programming Guide](https://developer.apple.com/library/ios/documentation/general/conceptual/concurrencyprogrammingguide/Introduction/Introduction.html "Concurrent Programming Guide")
