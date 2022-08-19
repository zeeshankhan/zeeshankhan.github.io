---
title: "Interview Stuff"
date: "2015-03-31"
categories: 
  - "technical-posts"
tags: 
  - "gcd"
  - "interview-questions"
  - "ios"
  - "ios-qa"
---

A list of interview topics for the developers, who are looking to hire or get hired for iOS work. These stuff are based on my experience while giving interviews or taking some for an iOS developer job. It also includes some self-test questions which have not been asked.

**\_\_kindOf** (Avoid compiler warnings for subclass access or to avoid casting) [http://stackoverflow.com/a/31399395/559017](http://stackoverflow.com/a/31399395/559017)

**Garbage Collector in Objective-C** No, Objective C does not have GC, It does not scan heap for unused objects, no whole app pause, no deterministic release of any NS Object. Objective C used Manual Reference Counting until iOS 5 in terms of 'retain', 'release', and Automatic Reference Count in terms of 'strong', 'weak'. [https://mikeash.com/pyblog/friday-qa-2010-07-16-zeroing-weak-references-in-objective-c.html](https://mikeash.com/pyblog/friday-qa-2010-07-16-zeroing-weak-references-in-objective-c.html)

**weak v/s assign** The only difference between `weak` and `assign` is that if the object a `weak` property points to is deallocated, then the value of the `weak` pointer will be set to `nil`, so that you never run the risk of accessing garbage. If you use `assign`, that won't happen, so if the object gets deallocated from under you and you try to access it, you will access garbage. weak does not work with primitive types like int, double. weak only works with ObjectiveC objects.

**Compiler directive?** [http://nshipster.com/at-compiler-directives/](http://nshipster.com/at-compiler-directives/)

**instancetype?** [http://nshipster.com/instancetype/](http://nshipster.com/instancetype/) and [http://stackoverflow.com/a/8976920](http://stackoverflow.com/a/8976920)

**NULL (void\* 0), Nil (class 0), nil (id 0) and NSNull \[NSNull null\]** [http://nshipster.com/nil/](http://nshipster.com/nil/)

**Differentiate** **#import, #include and @class?** **#**import**:** Improved version of include, brings entire header file into current file **#**include**:** With objective C, there is performance hit with #include, compiler must open up each header file to notice the include guard. **@**class**:** A forward declaration compiler directive, It tells the compiler that class exists, does not know anything about class. It minimizes the amount of code seen by the compiler or linker.

**Objective-C Variable Length Argument (var args).** [https://izeeshan.wordpress.com/2015/03/24/217/](https://izeeshan.wordpress.com/2015/03/24/217/)

**Method swizzling / Aspect Oriented Programming** [http://nshipster.com/method-swizzling/](http://nshipster.com/method-swizzling/) [https://github.com/steipete/Aspects](https://github.com/steipete/Aspects)

**Objective-C Collection Types and An array of weak objects.** [https://mikeash.com/pyblog/friday-qa-2010-05-28-leopard-collection-classes.html](https://mikeash.com/pyblog/friday-qa-2010-05-28-leopard-collection-classes.html) [https://mikeash.com/pyblog/friday-qa-2012-03-09-lets-build-nsmutablearray.html](https://mikeash.com/pyblog/friday-qa-2012-03-09-lets-build-nsmutablearray.html)

**Design patterns.**

1. **Thread Safe Singleton**: [https://izeeshan.wordpress.com/2014/08/31/threadsafe-singleton/](https://izeeshan.wordpress.com/2014/08/31/threadsafe-singleton/)
2. **Facade** - Wrapper on a complex system or apis.
3. **Decorator** - Dynamically adds behaviors and responsibilities to an object without modifying its code, _Ex. Category and Delegation_ (Modifies the behavior of an object instance.)
4. **Adapter** - An Adapter allows classes with incompatible interfaces to work together. It wraps itself around an object and exposes a standard interface to interact with that object. **_Ex. NSCopying protocol_** implemented by so many, to provide standard copy method. _Delegates and Datasources are examples of Adapter design pattern_.
5. **Observer / Broadcasting Listeners** - One object notifies other objects of any state changes. The objects involved don’t need to know about one another – thus encouraging a decoupled design. This pattern’s most often used to notify interested objects when a property has changed. **Ex. NSNotification** (Asynchronous message passing between two different objects) **and KVO**
6. **Memento** - It captures and externalizes an object’s internal state. In other words, it saves your stuff somewhere. Later on, this externalized state can be restored without violating encapsulation; that is, private data remains private. **_Ex. NSUserDefaults and Archiving, unarchiving._**
7. **Command** - (**_Ex. Target-Action of a button_**) It encapsulates a request or action as an object. The encapsulated request is much more flexible than a raw request and can be passed between objects, stored for later, modified dynamically, or placed into a queue.
8. **Dependency Injection:** A software design pattern that implements inversion of control for software libraries. Caller delegates to an external framework the control flow of discovering and importing a service or software module specified or "injected" by the caller.

**NSObject: the Class and the Protocol** [https://mikeash.com/pyblog/friday-qa-2013-10-25-nsobject-the-class-and-the-protocol.html](https://mikeash.com/pyblog/friday-qa-2013-10-25-nsobject-the-class-and-the-protocol.html)

1. Multiple root classes in objective c, need common rules.
2. Protocol inheritance (can access all NSObject protocol functions if inherited).

**Explain**: \[self self\], \[self class\], \[self release\]

**Objective-C Category,** -**dealloc and +load methods in category, category with library and frameworks.** [https://izeeshan.wordpress.com/2012/09/06/ios-category/](https://izeeshan.wordpress.com/2012/09/06/ios-category/) Properties**:** [http://nshipster.com/associated-objects/](http://nshipster.com/associated-objects/) Dealloc**:** [http://stackoverflow.com/questions/14708905/how-do-i-access-the-dealloc-method-in-a-class-category](http://stackoverflow.com/questions/14708905/how-do-i-access-the-dealloc-method-in-a-class-category) Linker Flags**:** [http://stackoverflow.com/questions/2567498/objective-c-categories-in-static-library](http://stackoverflow.com/questions/2567498/objective-c-categories-in-static-library)

**+load / +initialize** [http://stackoverflow.com/a/13326633/559017](http://stackoverflow.com/a/13326633/559017) [https://www.mikeash.com/pyblog/friday-qa-2009-05-22-objective-c-class-loading-and-initialization.html](https://www.mikeash.com/pyblog/friday-qa-2009-05-22-objective-c-class-loading-and-initialization.html)

**IPA size**: [https://developer.apple.com/news/?id=02122015a](https://developer.apple.com/news/?id=02122015a)

**NSAutoreleasePool**: [https://mikeash.com/pyblog/friday-qa-2011-09-02-lets-build-nsautoreleasepool.html](https://mikeash.com/pyblog/friday-qa-2011-09-02-lets-build-nsautoreleasepool.html)

- If a pool is destroyed which is not at the top of the stack of pools, it also destroys the other pools which sit above it. In short, `NSAutoreleasePool` instances nest.
- **drain vs release**, NSAutoreleasePool: drain is same as release except it signals to the collector that this might be a good time to run a collection cycle.

**NSZombie:** It's a memory debugging aid. Specifically, when you set `NSZombieEnabled` then whenever an object reaches retain count 0, rather than begin deallocated it morphs itself into an `NSZombie` instance. Whenever such a zombie receives a message, it logs a warning rather than crashing or behaving in an unpredictable way.

**Why `retainCount` should never be used in shipping code.** [http://stackoverflow.com/a/4636477](http://stackoverflow.com/a/4636477)

- Invoking a method on nil returns a zero 0 value.
- Best practices are implicit instructions to compiler.

**Core Data & Migration:** [https://izeeshan.wordpress.com/2014/11/10/core-data-migration/](https://izeeshan.wordpress.com/2014/11/10/core-data-migration/)

- **CoreData**\- Transient property: [http://davemeehan.com/technology/objective-c/core-data-transient-properties-on-nsmanagedobject](http://davemeehan.com/technology/objective-c/core-data-transient-properties-on-nsmanagedobject)
- **\_\_attribute\_\_(())**: [http://stackoverflow.com/questions/2053029/how-exactly-does-attribute-constructor-work](http://stackoverflow.com/questions/2053029/how-exactly-does-attribute-constructor-work)

**XML Parsers**

- **SAX**: _Simple API for XML_; Its one where our code gets notified as the parser reads the xml tree (data). We construct the objects and keep the track of states.
- **DOM**: _Document Object Model_; It reads the entire document and builds the object in which we can query for different elements.
- **NSXMLParser**: Its a SAX parser written in Objective-C and is quite straightforward to use.
- **libxml2**: A C based API and an Open source library, which supports both SAX and DOM processing. One cooler feature of this library, as it can parse the data as it's being read from network.

**_Differences_**

- In SAX we get notified but in DOM we have to query for object.
- In SAX we construct the objects and keep the track of states but in DOM we get build objects and keys as elements.
- Memory: DOM usually require more memory than SAX, because it reads all the data at once and kept that in memory; this is something to consider when we deal with large documents.

**Web Services (WSDL / SOAP / AJAX)** Web Service is like program-to-program communication over the Internet through XML. It provides a service, based on Operations, defined in its interface. It communicated between different language, different platforms.

- **WSDL**: Web Service provides a way to describe their interface in enough details, and that description is usually provided in an XML document called _Web Service Description Language (WSDL)._ In web service we send XML and we receive XML as response.
- **SOAP:** Web Service exposes useful functionality to Web users through a standard web protocols. In most cases, the protocol used is SOAP. It stands for _Simple Object Access Protocol_.
- **JWS:** _Java Web Service_ - JAX-WS stands for Java API for XML Web Service. JAX-WS is a technology for building web services and clients that communicate using XML.
- **AJAX:** _AJAX stands for Asynchronous JavaScript and XML_. It allows asynchronous communication between a client and server. It does not mandate that the end user must wait for processing a request.

**REST APIs**? **How is it differ from SOAP?**

1. Representational State Transfer v Simple Object Access Protocol.
2. REST permits many different data formats where as SOAP only permits XML.
3. REST better browser support (coz of JSON), better performance, scalability.
4. REST reads can be cached, SOAP can not be.
5. SOAP used for WS-Security, WS-Atomic Transaction, WS-Reliable Messaging, Supports ACID transactions.

**Opaque v Alpha:** [http://stackoverflow.com/a/8520656](http://stackoverflow.com/a/8520656) **A struct** is a special C data type that encapsulates other pieces of data into a single cohesive unit. Like an object, but built into C.

**Coordinates v/s Drawing** The coordinates of Core Graphics drawings start from the bottom-left corner, while UIKit global coordinates start from the top-left.

**NSPredicate v NSScanner:** [http://nshipster.com/nsscanner/](http://nshipster.com/nsscanner/)

**Frame v Bound v Center** Frame: A view's frame (CGRect) is the position of its rectangle in the superview's coordinate system. By default it starts at the top left. Bounds: A view's bounds (CGRect) expresses a view rectangle in its own coordinate system. Center: A center is a CGPoint expressed in terms of the superview's coordinate system and it determines the position of the exact center point of the view.

**Application States**:

- **Not Running**: The app has not been launched or it has been terminated.
- **Inactive:** The app is in the foreground but not receiving events, Ex. User has locked the device with the app active.
- **Active:** The normal state of “In Use” for an app.
- **Background:** The app is no longer on-screen, but still executing the code.
- **Suspended:** The app is still resident in memory but is not executing code.

**BLOCK**:

- Blocks are created on the stack
- Blocks can be copied to the heap
- Blocks have their own private const copies of stack variables (and pointers)
- Mutable stack variables and pointers must be declared with the \_\_block keyword
- Blocks, Operations and Retain Cycles - [http://conradstoll.com/blog/2013/1/19/blocks-operations-and-retain-cycles.html](http://conradstoll.com/blog/2013/1/19/blocks-operations-and-retain-cycles.html)
- Docs:
    - [http://albertodebortoli.github.io/blog/2013/04/21/objective-c-blocks-under-the-hood/](http://albertodebortoli.github.io/blog/2013/04/21/objective-c-blocks-under-the-hood/)
    - docs: [http://developer.apple.com/library/ios/#documentation/cocoa/Conceptual/Blocks/Articles/00\_Introduction.html](http://developer.apple.com/library/ios/#documentation/cocoa/Conceptual/Blocks/Articles/00_Introduction.html)
    - [http://ios-blog.co.uk/tutorials/programming-with-blocks-an-overview/](http://ios-blog.co.uk/tutorials/programming-with-blocks-an-overview/)

**dispatch barrier**: [https://izeeshan.wordpress.com/2014/09/03/dispatch-barriers/](https://izeeshan.wordpress.com/2014/09/03/dispatch-barriers/) ****dispatch\_group**:** [http://amro.co/gcd-using-dispatch-groups-for-fun-and-profit](http://amro.co/gcd-using-dispatch-groups-for-fun-and-profit) [https://www.mikeash.com/pyblog/friday-qa-2009-09-04-intro-to-grand-central-dispatch-part-ii-multi-core-performance.html](https://www.mikeash.com/pyblog/friday-qa-2009-09-04-intro-to-grand-central-dispatch-part-ii-multi-core-performance.html) [http://www.raywenderlich.com/63338/grand-central-dispatch-in-depth-part-2](http://www.raywenderlich.com/63338/grand-central-dispatch-in-depth-part-2)

**Multithreading:** **Sync v Async, Concurrency v Non-Concurrency, Parallel v Concurrency.** [https://izeeshan.wordpress.com/2014/08/17/multi-threading-using-nsoperation/](https://izeeshan.wordpress.com/2014/08/17/multi-threading-using-nsoperation/)

**Operating System:** [https://izeeshan.wordpress.com/2016/06/04/operating-system/](https://izeeshan.wordpress.com/2016/06/04/operating-system/)

Difference between `+alloc` and `+allocWithZone` : When one object creates another, it’s sometimes a good idea to make sure they’re both allocated from the same region of memory. The zone method (declared in the NSObject protocol) can be used for this purpose; it returns the zone where the receiver is located. (2014) Apple documentation says that the `zone` parameter is ignored, and "This method exists for historical reasons; memory zones are no longer used by Objective-C."

#TODO **Network layers, SSL pinning** **Cryptography** **Public key v Private key**:

##### **Other References**

1. NEVER MISS **Whats New**: [https://developer.apple.com/library/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html](https://developer.apple.com/library/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
2. [Cameron Banga](https://github.com/CameronBanga/iOS-Developer-and-Designer-Interview-Questions#contents "Cameron Banga")
3. [Raywenderlich Blog](http://www.raywenderlich.com/53962/ios-interview-questions "Raywenderlich Blog")
4. [NSHipster Quizzes](http://nshipster.com/nshipster-quiz-1/ "NSHipster Quiz")
5. http://bizzydevapps.weebly.com/
6. http://huntmyideas.weebly.com/blog/category/ios-interview-questions-and-answers
7. https://horseshoe7.wordpress.com/2012/07/25/what-to-ask-an-ios-developer-at-their-interview/

##### Thanks for reading.

##### Feel free to add other links or questions in comments, Cheers!!
