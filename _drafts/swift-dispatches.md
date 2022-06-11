# Swift Dispatch

Method Dispatch refers to the operation a program uses to determine which method implementation should be executed when a method or function is called. 
Method Dispatch is how a program selects which instructions to execute when invoking a method.
The goal of dispatch is for the program to tell the CPU where in memory it can find the executable code for a particular method call. 

Compiled programming languages have three primary methods of dispatch at their disposal: 
- direct dispatch, 
- table dispatch, and 
- message dispatch,

Java uses table dispatch by default, but you can opt into direct dispatch by using the `final` keyword. 
C++ uses direct dispatch by default, but you can opt into table dispatch by adding the `virtual` keyword. 
Objective-C always uses message dispatch, but allows developers to fall back to C in order to get the performance gains of direct dispatch. 
Swift has taken on the noble goal of supporting all three types of dispatch.

The two most well-known dispatch types we have in Swift are the Static and the Dynamic dispatch.

## Static dispatch (aka compile time dispatch aka direct dispatch)
- Very effectively optimized direct call where the compiler at compile time decides which method should be called.
- Static dispatch is used in situations where inheritance isn’t allowed and the compiler can only call one method.
- Value types use static dispatch because they don’t need inheritance. Global functions, outside classes or structs or enums, use static dispatch too.
- Static dispatch results in better performance compromising dynamism behavior/semantics in programming.

Swift uses static dispatch in a few key developer facing scenarios.
- All methods introduced by structs are statically dispatched.
- Methods in classes marked with the final keyword are statically dispatched.
- Static methods are statically dispatched (go figure).
- Any methods introduced in a protocol extension are also statically dispatched.

What do all of these scenarios have in common? No inheritance.

For a method to be considered for static dispatch there can’t be another potential callable method present on the same type with the same signature. Structs don’t allow inheritance, the methods in a class marked as final can’t be overridden, unlike class methods static methods can’t be overridden, and extension methods disallow for inheritance.

## Dynamic dispatch (aka Runtime dispatch aka Table dispatch)
- The system looks for specific method implementation in compiler created dispatch table (called witness table in Swift) in runtime, not in compilation time.
- In OOPs, it is polymorphism. When we send a message to a class instance, dynamic dispatch chooses the implementation from the hierarchy. 
- In protocol’s world it is just protocol requirements.

Dynamic dispatch is used in the following areas.
- Methods not marked as final in classes.
- Class methods
- Required methods in protocols

> Each type that states its conformance to a protocol gets its own protocol witness table.

## Existential Container or Type
When we manually cast our variable to a protocol type then Swift compiler moves this variable to an existential container. Or that variable becomes an Existential type.

In other words: A plain protocol name in type context means an existential type. In below example variable `f` is an existential type.
```Swift
protocol Foo {}
let f: Foo = ...
```
Existential types are significantly more expensive to use than using concrete types because the compiler and runtime can’t pre-determine how much memory should be allocated for the concrete object that will fill in the existential. In addition to heap allocation and reference counting, code using existential types incurs pointer indirection and dynamic method dispatch that cannot be optimized away.

When we create a variable without a type definition, and it only conforms to a protocol, it is also created in the existential container which uses static dispatch. 

Also, when we add some methods to our `struct`/`enum`/`class` in extension, they will also be statically dispatched.

> How can we avoid static dispatch of extension? Very easy, just need to add a method from protocol extension to required protocol methods.

## Protocol extensions
Protocol extensions have 2 types of methods. 
    - Requirement methods and 
        - Requirement methods use dynamic dispatch
    - Static methods ("Not Backed by Requirement")
        - Static methods use static dispatch.


## Reference types dynamic dispatch:
Swift allows a class to override methods and properties declared in its superclasses. This means that the program has to determine at runtime which method or property is being referred to and then perform an indirect call or indirect access. This technique, called dynamic dispatch, increases language expressivity at the cost of a constant amount of runtime overhead for each indirect usage.

In Swift, dynamic dispatch calls are implemented by looking up a function from a method table and then performing an indirect call. This is slower than performing a direct call. Additionally, indirect calls also prevent many compiler optimizations, making the indirect call even more expensive. 

### Use final when you know that a declaration does not need to be overridden.
The final keyword is a restriction on a class, method, or property that indicates that the declaration cannot be overridden. This allows the compiler to safely elide dynamic dispatch indirection.

### Infer final on declarations referenced in one file by applying the private keyword.
Applying the private keyword to a declaration restricts the visibility of the declaration to the current file. This allows the compiler to find all potentially overriding declarations. The absence of any such overriding declarations enables the compiler to infer the final keyword automatically and remove indirect calls for methods and property accesses.

### Use Whole Module Optimization to infer final on internal declarations.
if Whole Module Optimization is enabled, all of the module is compiled together at the same time. This allows the compiler to make inferences about the entire module together and infer final on declarations with internal if there are no visible overrides.



## Resources
- https://medium.com/@PavloShadov/https-medium-com-pavloshadov-swift-protocols-magic-of-dynamic-static-methods-dispatches-dfe0e0c85509
- https://medium.com/@leandromperez/protocol-extensions-gotcha-9ef1a42c83b6
- https://developer.apple.com/swift/blog/?id=27
- https://web.archive.org/web/20180718214455/https://www.raizlabs.com/dev/2016/12/swift-method-dispatch/
- https://thedarwinguy.com/2020/04/07/swift-dispatch-explained-simply/
- https://towardsdev.com/method-dispatch-in-swift-protocols-7b1323338468 


> Protocol with associated types are not Type so it can not be added into a collection as an example. Protocol without associated type are Type so it can be added into collection types.



# Swift Tip: static vs class
Static

The static keyword can be used with classes, structs, and enums. Static members are scoped to a type and shared across all instances.

Members marked as static are inherited by subclasses but cannot be overridden. The below code won’t compile.
```
class Base {
 
    static var staticProperty: String {
        return "staticPropery from Base."
    }
    init() {}
}
 
class Child: Base {
     
    override static var staticProperty: String { // Error cannot override static member. 
        return "staticPropery from Child."
    }
    override init() {}
}
```

Class

The class keyword is similar to the static keyword but offers additional inheritance based functionality and one major restriction. Class can only be used with class types.

The class keyword in many regards works just like the static keyword with the added benefit of inheritance. Subclasses are able to override non-final class members. Since the benefits the class keyword provides only apply to inheritance it makes sense that only class types can use the keyword.
```
class Base {
     
    class var classProperty: String {
        return "classPropery from Base."
    }
     
    init() {
         
    }
}
 
class Child: Base {
    override init() {}
 
     
    override class var classProperty: String {
        return "classPropery from Child."
    }
 
}
``` 
If your working with structs or enums the static keyword is the only keyword available for associating methods or properties with a type. When working with classes you can use either the static or class keyword. Choose the class keyword when it makes sense to allow subclasses to override a type scoped member.