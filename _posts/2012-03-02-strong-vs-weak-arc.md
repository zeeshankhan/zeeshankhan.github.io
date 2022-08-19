---
title: "Strong Vs Weak - ARC"
date: "2012-03-02"
categories: 
  - "technical-posts"
tags: 
  - "arc"
  - "ios"
  - "ios-5-0"
  - "strong"
  - "weak"
---

The difference is that an object will be deallocated as soon as there are no **strong** pointers to it. Even if weak pointers point to it, once the last strong pointer is gone, the object will be deallocated, and all remaining weak pointers will be zeroed out. Perhaps an example is in order.

Imagine our object is a dog, and that the dog wants to run away (be deallocated).

_**Strong pointers**_ are like a leash on the dog. As long as you have the leash attached to the dog, the dog will not run away. If five people attach their leash to one dog, (five strong pointers to one object), then the dog will not run away until all five leashes are detached.

_**Weak pointers**_, on the other hand, are like little kids pointing at the dog and saying "Look! A dog!" As long as the dog is still on the leash, the little kids can still see the dog, and they'll still point to it. As soon as all the leashes are detached, though, the dog runs away no matter how many little kids are pointing to it. As soon as the last strong pointer (leash) no longer points to an object, the object will be deallocated, and all weak pointers will be zeroed out.

### **‘Weak' _notes:_**

In a property declaration, weak only applies to the synthesized setter method, if any, and synthesized instance variable, if any. If neither of those are synthesized then weak has no effect.

If the setter and instance variable are synthesized, the question then is: which property declaration is the compiler using to synthesize the setter and instance variable?

The compiler will never synthesize a property declared in a named category.

**Strong**: A strong reference is a reference to an object that stops it from being deallocated. In other words it creates a owner relationship.  A class instance takes an ownership interest a referenced object (i.e. so it cannot be deallocated until the owner is).

**Weak**: A weak reference is a reference to an object that does not stop it from being deallocated. In other words, it does not create an owner relationship. We do not “own” the object being referred to (e.g. typically a child object should not own its parent, so we would use a weak reference).

### Setup

To enable ARC simply set the Objective-C Automatic Reference Counting option in your Xcode project’s Build Settings to YES. Behind the scenes this sets the -fobjc-arc compiler flag that enables ARC.

Project File -> Build Settings -> Settings -> Objective-C Automatic Reference Counting : toggled button is there.

### _Notes:_

- Automatic Reference Counting (ARC) is a compiler-level feature.
- We can use strong in iOS 4 too but we can not use weak in iOS 4.
- We must not make any retain/release/autorelease/retainCount/\[super dealloc\] calls.
- This is not Garbage Collection, and reference counted memory has not disappeared, it has simply been automated.
- ARC can be disabled on a file by file basis.
- @autoreleasepool{}blocks instead of NSAutoReleasePool
- We cannot use NSAllocateObject or NSDeallocateObject.

### Other References:

- [Quora](http://www.quora.com/In-Objective-C-whats-the-difference-between-a-strong-and-weak-pointer), and
- [Another example](http://longweekendmobile.com/2011/09/07/objc-automatic-reference-counting-in-xcode-explained/ "Another Example")

##### Thanks for reading :)
