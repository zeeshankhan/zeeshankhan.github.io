---
title: "Thread Safe vs Thread Un-Safe"
date: "2014-08-18"
categories: 
  - "technical-posts"
tags: 
  - "multithreading"
  - "nsoperation"
  - "nsthread"
  - "posix"
  - "threads"
---

## Thread safe and Thread un-safe objects:

- Thread safe objects are safe to pass from one thread to another. On the other hand you may get stale value if you try to access a thread unsafe object with multiple threads.
- A non thread safe (thread unsafe) API is one where you can not interact with multiple threads simultaneously.
- If something is described as "not thread safe", it means that no special precautions have been taken to ensure that it won't crash when two separate threads try to use it simultaneously.
- In general, code that is to be used by more than one thread requires explicit locks (or @synchronize blocks) wrapping around the code.
- In particular, If two threads try to write any object or variable at the same time can cause a crash (since they’d be writing to the same memory address.)
- Similarly, if one thread was reading a variable while another was writing to it, garbage would be returned and the program would likely crash.
- Using @synchronized, or NSLock or a POSIX mutex etc, ensures that only one thread can execute a particular block of code at any given time. The other threads get blocked and have to wait until the lock is released. There is a slight performance hit with using locks.
- An API is only thread safe if the documentation explicitly claims thread safety. If there is no mention of thread safety, you must assume that the API is not thread safe.

An _**atomic**_ property in Objective C guarantees that you will never see partial writes. When a @property has the attribute atomic it is impossible to only partially write the value. The setter/getter would be like:

\- (void)setMyName:(NSString\*)name {
   @synchronized(self){
     \_myName = name; 
   }
}
- (NSString\*)myName {
   @synchronized(self){
     return \_myName;
   }
}

[Threading Programming Guide - Apple Docs](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Multithreading/Introduction/Introduction.html#//apple_ref/doc/uid/10000057i-CH1-SW1 "Threading Programming Guide - Apple Docs")

[Programming with Objective C - Apple Docs](https://developer.apple.com/library/ios/documentation/cocoa/conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html "Programming with Objective C - Apple Docs")
