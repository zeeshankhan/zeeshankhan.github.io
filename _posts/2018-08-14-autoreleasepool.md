---
title: autoreleasepool
date: 2018-08-14 09:41:10 +0400
categories: [Notes]
tags: [objective-c]
---

**High-Level Overview** The ball gets rolling when the `autorelease` message is sent to an object. `autorelease` is implemented on NSObject, and just calls through to `[NSAutoreleasePool addObject: self]`. This is a class method, which then needs to track down the right instance to talk to.

`NSAutoreleasePool` instances are stored in a per-thread stack. When a new pool is created, it gets pushed onto the top of the stack. When a pool is destroyed, it's popped off the stack. When the `NSAutoreleasePool`class method needs to look up the current pool, it grabs the stack for the current thread and grabs the pool at the top.

Once the right pool is found, the `addObject:` instance method is used to add the object to the pool. When an object is added to the pool, it's just added to a list of objects kept by the pool.

When a pool is destroyed, it goes through this list of objects and sends `release` to each one. This is just about all there is to it. There is one additional small complication: if a pool is destroyed which is not at the top of the stack of pools, it also destroys the other pools which sit above it. In short, `NSAutoreleasePool` instances nest, and if you fail to destroy an inner one, the outer one will take care of it when it gets destroyed.

**Garbage Collection** `NSAutoreleasePool` exists under garbage collection and is even slightly functional. If you use the `drain`message rather than the `release` message, destroying a pool under garbage collection signals to the collector that this might be a good time to run a collection cycle. Aside from this, however, `NSAutoreleasePool` does nothing under garbage collection and isn't very interesting to consider there.

- There's no way to tell if an object has been autoreleased. The pool is a fairly dumb container with only the barest idea of what it contains. It really just keeps a list for the purposes of sending `release` to those objects later. This is perfectly fine, because your code should never care whether an object has already been autoreleased.
- Objects that are autoreleased twice just get added to the pool twice, and then when the pool is destroyed they get released twice.
- Autoreleased objects get released when the current autorelease pool is destroyed. Pools are destroyed when the code that created them explicitly destroys them. If you aren't managing your own pools, then autoreleased objects will survive at least until you return to the code you don't own (like Cocoa).
- If you autorelease an object on one thread and then pass it to another thread, nothing special happens. The object is still released when the first thread's pool gets destroyed, regardless of what's happening on the new thread. If you need an object to survive the passage, it needs to be retained before sending and then released after receiving. (Fortunately, the cross-thread messaging mechanisms you're likely to use with objects, like GCD/blocks and Cocoa's `perform...` methods do this for you.)

The `@autorelease` keyword generates calls to `objc_autoreleasePoolPush()` and`objc_autoreleasePoolPop()`, which use pool tokens directly. I presume an `NSAutoreleasePool` is now a simple wrapper for a pool token.

**Core Foundation `@autorelease` in ARC** Without ARC to `autorelease` a CoreFoundation object:

CFDictionaryRef MakeDictionary(void) {
        CFMutableDictionaryRef dict = CFDictionaryCreateMutable(NULL, 0, NULL, NULL);
        _// Put some stuff in the dictionary here perhaps_

        \[(id)dict autorelease\];
        return dict;
    }

This above code no longer works with ARC, because the call to `autorelease` is not permitted. To solve this, Apple helpfully provided us with a `CFAutorelease` function which does the same thing and can be used with ARC. Unfortunately, it's only available as of iOS 7 and Mac OS X 10.9.

iOS 6 support for autorelease with ARC.

   CFDictionaryRef MakeDictionary(void) {
        CFMutableDictionaryRef dict = CFDictionaryCreateMutable(NULL, 0, NULL, NULL);
        _// Put some stuff in the dictionary here perhaps_

        SEL autorelease = sel\_getUid("autorelease");
        IMP imp = class\_getMethodImplementation(object\_getClass((\_\_bridge id)dict), autorelease);
        ((CFTypeRef (\*)(CFTypeRef, SEL))imp)(dict, autorelease);

        return dict;
    }
