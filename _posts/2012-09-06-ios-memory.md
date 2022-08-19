---
title: "iOS Memory"
date: "2012-09-06"
---

Object Ownership:

1\. Object creation: alloc, init, new, copy, 2. Take Ownership: retain 3. must relinquish ownership of objects we own. 4. must not relinquish ownership of objects we do not own. Retain Count:

1\. when we create an object: retain count is 1. 2. when we retain an object: retain count is incremented by 1. 3. when w release an object: retain count is decremented by 1. 4. when we autorelease an object: retain count is decremented by 1 at some stage in the future. 5. when we assign nil to an object: retain count is 0. 6. when an object's retain count is reduced to 0, it is deallocated.

Remove from collection: whenever we remove any object from any collection class, it is sent a release (rather than autorelease) message. Parent removal:

Incorrect: NSString \*str = \[arr objectAtIndex:0\]; \[arr release\]; or \[arr removeObjectAtIndex:0\]; or arr = nil; //using str object here Correct: NSString \*str = \[\[arr objectAtIndex:0\] retain\]; \[arr release\]; or \[arr removeObjectAtIndex:0\]; or arr = nil; //using str object here \[str release\];

Deallocating an object: When any object's retain count drops to 0, that object's memory is reclaimed - in cocoa terminology it is freed or deallocated. when an object is deallocated, its dealloc method is invoked automatically. The role of the dealloc method is to free the object's own memory, and dispose of any resources it holds, including ownership of any object instance variables. If your class has object instance variables that it owns, you must implement a dealloc method that releases them, and then after releasing instance variables invokes super's implementation. - (void)dealloc { \[instanceVar release\]; \[super dealloc\]; }

Circular reference: Parent object retains the child reference and child reference retains the parent's object.

Weak Reference to Object

An object can not be deallocated until all of its strong references are released.

A weak reference is created by storing a pointer to an object without retaining the object.

why it is needed: Weak references are essential in cases where a circular reference would otherwise be set up. For example, if Object A and Object B communicate with each other, each needs a reference to the other. If each retains the other, neither object ever gets deallocated until the connection is broken, but the connection is not broken until one of the objects is deallocated. Catch-22. To break the circle, one object takes a subordinate role and obtains a weak reference to the other. As a concrete example, in a view hierarchy, a parent view owns, and hence retains, its child views, but a child view does not own its parent; the child still needs to know who its parent is, so it keeps a weak reference to its parent.

Example of weak reference: NSNotification. Manipulation of reference count (retain count) \[ take and relinquish ownership\]

1\. alloc or new

Allocates memory for an object, and returns it with retain count of 1. We own objects we create using any method that starts with the word alloc or with the word new.

2\. copy

Makes a copy of an object, and return it with retain count of 1. If we copy an object, we own the copy. This applies to any method that contains the word copy where copy refers to the object being returned.

3\. retain

Increase the retain count of an object by 1. Takes ownership of an object.

4\. release

Decrease the retain count of an object by 1. Relinquishes ownership of an object.

5\. autorelease

Decrease the reference count of an object by 1 at some stage in the future. Relinquishes ownership of an object at some stage in the future. The only places you shouldnít use accessor methods to set an instance variable are in init methods and dealloc. How does collection handles object's ownership:

When we add an object to a collection such as an array, dictionary, or set, the collection takes ownership of it. The collection will relinquish ownership when the object is removed from the collection or when the collection is itself released.
