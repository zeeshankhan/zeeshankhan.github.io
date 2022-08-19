---
title: "NSAssert in iOS"
date: "2012-09-13"
categories: 
  - "technical-posts"
tags: 
  - "assertion-handler"
  - "ios"
  - "nsassert"
---

Assertion is a defence against programming errors. It tests a condition, and if the condition fails, it raises an exception. This is processed by the current exception handler, which by default call abort and crashes the program. iOS calls abort by default, which terminates the program no matter what thread it runs on.

##### _NSAssert_ is a macro, which evaluates the conditions and serves as a front end to the assertion handler.

Each thread has its own assertion handler, which is an object of class _NSAssertionHandler_. When invoked, an assertion handler prints an error message that includes the method and class names. It then raises an _NSInternalInconsistencyException_ exception. If condition evaluates to NO, the macro invokes _handleFailureInMethod:object:file:lineNumber:description:_ on the assertion handler for the current thread, passing desc as the description string.

- This macro should be used only within Objective-C methods.
- We should use **Exceptions** for error condition which come from the outside (outside the method or outside the program) like parameter checking or missing/defective external resources like files or connections or user input.
- We should use **Assertions** to indicate an internal defects like programming errors, conditions that shouldn't occur, e.g. class/method invariants and invalid program state.

```
NSAssert(someCondition, @"Print this statement."); 
```

Assert is to make sure a value is what its suppose to be. If assertion fails that means something went wrong and so the app quits. One reason to use assert would be if you have some function that will not behave or will create very bad side effects if one of the parameters passed to it is not exactly some value (or a range of values) you can put an assert to make sure that value is what you expect it to be, and if its not then something is really wrong, and so the app quits. Assert can be very useful for debugging/unit testing, and also when you provide frameworks to stop the users from doing "evil" things

You should take out NSAssert for release. This is debatable. I always release my applications with assertions enabled, and this is standard practice for a lot of software, Apple for example does this. As soon as your program has detected an abnormal state you should crash. You can get a stack trace of where the error occurred, whereas if you disable asserts you could end up corrupting memory and/or user data and the problem will be very hard to debug.

Note that XCode 4 has NS\_BLOCK\_ASSERTIONS defined by default in release configurations. I guess if you don't change that your released code will not contain NSAssert

##### _**References:**_

- [NSAssertionHandler Class Reference](http://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSAssertionHandler_Class/Reference/Reference.html "NSAssertionHandler Class Reference")
- [http://stackoverflow.com/questions/1375786/whats-the-point-of-nsassert-actually](http://stackoverflow.com/questions/1375786/whats-the-point-of-nsassert-actually "NSAssert")
