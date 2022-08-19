---
title: "iOS Category"
date: "2012-09-06"
categories: 
  - "technical-posts"
tags: 
  - "ios"
---

### **Benefits**

- _A way of sectioning code_. Categories are used for _better code organisation_. Distribute the implementation of your own classes into separate source files—for example, you could group the methods of a large class into several categories and put each category in a different file.
- It can be used to add methods to classes for which you do not have the source code. You typically use a category to add methods to an existing class, such as one defined in the Cocoa frameworks.
- A category is a way to enhance an existing class, by adding additional functions to it. You use categories to define additional methods of an existing class—even one whose source code is unavailable to you—without subclassing.

### **Other Benefit and it’s issue**

Categories can also be used to override methods the class inherits, again, providing an alternative to sub classing. An alternative to sub classing. It provides a means to add methods to a class. You can always access the overridden method using super.

If the name of a method declared in a category is the same as a method in the original class, or a method in another category on the same class (or even a superclass), the behaviour is undefined as to which method implementation is used at runtime.

### **References**

- Apple Document: [Customizing existing classes](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html)
- [http://stackoverflow.com/a/9424016/559017](http://stackoverflow.com/a/9424016/559017)
- Properties in category: [http://nshipster.com/associated-objects/](http://nshipster.com/associated-objects/)

### **Extending a class vs Category**

The difference between this and extending a class is that when you extend a class, you can add additional functions, as well as variables. In a category, you can only add additional functions. The benefit of a category though is that you donít have to use the extended class to get the benefits of the added additional functions. All you have to do is include the header file of the category, and the additional functions of that class are available to you.

### **Background**

- Whenever we add some methods through category then it becomes a part of that class definition.
- The compiler will never synthesize a property declared in a named category.
- Categories are attached to the classes at runtime. (IMP)

### **_load_ and Category**

- +load, it is used to do category specific setup like initializing static variables. It’s like +initialize.
- Magical part of Objective C runtime: If multiple category implement +load, then all implementations will run. No guarantees on order, and we should never call +load method by hand.
- Every +load will be called only once. You should never called super load.

### **Declaration**

- Defining a category is identical to defining the interface for a class with one small exception:
- We add a category name inside a set of parenthesis after the interface declaration.
- Naming conventions like ClassName+NewMethodName.h and .m
- You cannot add instance variables to a class through a category.
- A category must import the interface file for the class it extends.

### **Naming Convention**

A common naming convention is that the base file name of the category is the name of the class the category extends followed by “+” followed by the name of the category.

#### **Example 1**

 @interface NSString (MyCatergory)
 - (void)doSomethingWithString;
 @end
 
 @implementation NSString (MyCatergory)
 - (void)doSomethingWithString{
      // do something here with string
 }
 @end

#### **Example** **2:** **Adding properties to Category**

 @interface UITableView (Animation)
 @property (nonatomic, retain) NSNumber \*duration;
 @end
 
 #import <objc/runtime.h>
 static char DURATION\_IDENTIFER;
 
 @implementation UITableView (Animation)
 @dynamic duration;
 
 - (void)setDuration:(NSNumber \*)duration {
     objc\_setAssociatedObject(self, &DURATION\_IDENTIFER, duration, OBJC\_ASSOCIATION\_RETAIN\_NONATOMIC);
 }
 
 - (NSNumber\*)duration {
     return objc\_getAssociatedObject(self, &DURATION\_IDENTIFER);
 }

 @end
