---
title: Queue in swift
date: 2016-03-05 09:41:10 +0400
categories: [Notes]
tags: [swift]
---

Here, we'll be creating a simple data structure **Queue**, to demonstrate some of the functionalities of swift language, which includes:

- Value-type generics,
- Secure memory pointers,
- Mutating functions on struct,
- Sequence Type and Generator Types protocols, and
- Copy-on-write feature on swift

**Queue** is an abstract data type or a linear data structure, in which the first element is inserted from one end called REAR (also called Tail), and the deletion of existing element takes place from the other end called as FRONT (also called Head). This makes queue as **FIFO** data structure, which means that element inserted first will also be removed first. Like Stack, Queue is also an ordered list of elements of similar data types.

 

![queue](images/queue.png)

The process to add an element into queue is called **Enqueue** and the process of removal of an element from queue is called **Dequeue**.

## Queue Protocol

It defines the functionality of Queue, having three functions \`enqueue:\`, \`dequeue\`, and \`peek\`. All functions are marked as \`mutating\` because they will be mutating the queue properties. Since it’ll be a homogenous queue we've associated the protocol with type \`Element\` which will be queued or dequeued.

protocol QueueType {
  typealias Element
  mutating func enqueue(element: Element)
  mutating func dequeue() -> Element?
  func peek() -> Element?
}

> ### Mutating
> 
> By default, the properties of a value type (like struct or enum) cannot be modified from within its instance methods. However, if you need to modify the properties of your structure or enumeration within a particular method, you can opt in to mutating behavior for that method. _**References**:_ [Apple Documentation](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Methods.html), A [blog post](https://www.natashatherobot.com/mutating-functions-swift-structs/) by [@NatashaTheRobot](https://twitter.com/NatashaTheRobot)

## Queue Storage:

final class Storage<Element> {

 private var pointer: UnsafeMutablePointer<Element>
 private let capacity: Int

 init(capacity: Int) {
   pointer = UnsafeMutablePointer<Element>.alloc(capacity)
   self.capacity = capacity
 }

 static func copy(storage: Storage) -> Storage<Element> {
   let storageNew = Storage<Element>(capacity: storage.capacity)
   storageNew.pointer.initializeFrom(storage.pointer, count: storage.capacity)
   return storageNew
 }

 func add(element: Element, at index: Int) {
   (pointer + index).initialize(element)
 }

 func removeAt(index: Int) {
   (pointer + index).destroy()
 }

 func itemAt(index: Int) -> Element {
   return (pointer + index).memory
 }

 deinit {
   pointer.destroy(capacity)
   pointer.dealloc(capacity)
 }
}

We'll create a generic class to store queue elements in it. This class will initialize a mutable pointer with given capacity and will have functions like \`add\` and \`remove\` at given index.

### Functions:

_**init(capacity: Int);**_ It will allocate memory with capacity for pointer, which will keep queue elements. Here is a good explanation about [Memory Pointers](http://stackoverflow.com/a/27721441/559017) in swift

_**func add(element: Element, at index: Int)**_ _**func removeAt(index: Int)**_ _**func itemAt(index: Int) -> Element**_ These functions will let us add, remove and return an element at a given position in the storage.

> Note: _**(ptr + position).memory**_ works because of **+** is overloaded in swift’s stdlib which gives the pointer incremented by the number provided.
> 
> \`public func +<Memory>(lhs: UnsafeMutablePointer<Memory>, rhs: Int) -> UnsafeMutablePointer<Memory>\`

_**deinit**_ Swift doesn’t provide any option to do a cleanup when a value type is removed from a memory. So we need to use class as a storage, it gives _deinit_ function where we can write the cleanup. _deinit_ is called when a reference type is removed (A reference type is removed when there are zero references to it), we have to get rid of all the memory we asked for while creating this storage when our queue goes out of scope.

_**static func copy(storage: Storage) -> Storage<Element>**_ We’ll need to copy the storage when our queue is passed around and is mutated.

## The Queue:

struct Queue<Element> : QueueType {

  private var storage: Storage<Element>
  private var rear: Int = 0
  private var front: Int = 0
  private var count: Int = 0
  private let capacity: Int

  init(capacity: Int) {
    self.capacity = capacity
    storage = Storage<Element>(capacity: capacity)
  }

  private mutating func makeUnique() {
    if !isUniquelyReferencedNonObjC(&storage) {
      storage = Storage.copy(storage)
    }
  }

  mutating func enqueue(element: Element) {
    guard count < capacity else {
      print("Queue is full.")
      return
    }
    makeUnique()
    storage.add(element, at: rear)
    rear = (rear + 1) % capacity
    count = count + 1
  }

  mutating func dequeue() -> Element? {
    guard count > 0 else {
      print("Queue is empty.")
      return nil
  }

   makeUnique()
   let item = storage.itemAt(front)
   storage.removeAt(front)
   front = (front + 1) % capacity
   count = count - 1
   return item
  }

  func peek() -> Element? {
   guard count > 0 else {
     print("Queue is empty.")
     return nil
   }
   return storage.itemAt(front)
  }
}

Queue is implemented as a struct containing five properties, _**storage**_ keeps the buffer, _**rear**_ and _**front**_ keeps the track of Tail and Head of the queue respectively, _**count**_ keeps the total number of elements and _**capacity**_ defines total size of queue buffer. The _**init**_ method will initialize _**storage**_ with the provided _**capacity**_.

### Functions:

_**private mutating func makeUnique()**_ It keeps the common functionality of struct. It calls _**isUniquelyReferencedNonObjC()**_ internally (a method defined in stdlib) which tells us if a _non-objc_ object ie pure swift object has reference count equal to one or not.

_**\`public func isUniquelyReferencedNonObjC<T : AnyObject>(inout object: T) -> Bool\`**_

if !isUniquelyReferencedNonObjC(&storage) {
  storage = Storage.copy(storage)
}

so when one queue instance is assigned into another variable it will share the storage instance until _**enqueue,**_ _**dequeue, or peek**_ is called on the new instance (or the old one) and it will detach the old storage and create a new copy for itself to use by calling the copy method on the storage. This is how _**copy-on-write**_ is achieved. Thanks **[@aciidb0mb3r](https://twitter.com/aciidb0mb3r)** for the clarification. Here is **[Doc](http://swiftdoc.org/v2.1/func/isUniquelyReferencedNonObjC/#func-isuniquelyreferencednonobjc-t_-anyobject-inout_-t)** and an **[example code](https://gist.github.com/zeeshankhan/8264de79175519551216)** which simplifies the same.

_**enqueue()**_ will insert an element at end of the queue. _**dequeue()**_ will remove first element from the queue. _**peek()**_ will return the first element of the queue.

In order for queue to support _**for..in**_ looping just like an array, It needs to implement _**SequenceType**_ protocol. Here is the key is _**generate()**_ function, which returns a _**GeneratorType**_. Thus we also need to implement the _**GeneratorType**_ protocol, which makes a call to _**dequeue()**_ function from _**next()**_ function implementation. Reference: [Swift Collection Protocols](http://nshipster.com/swift-collection-protocols/)

extension Queue: SequenceType {
  func generate() -> QueueGenerator<Element> {
    return QueueGenerator<Element>(queue: self)
  }
}

struct QueueGenerator<Element> : GeneratorType {
  var queue: Queue<Element>
    mutating func next() -> Element? {
    return queue.dequeue()
  }
}

## Examples

Let's try out queue with _**Int**_ and also calls _**SequenceType’s**_ functions to do operations on the queue.

var intQueue = Queue<Int>(capacity: 20)
intQueue.enqueue(11)
intQueue.enqueue(12)
intQueue.dequeue() // Remove from front ie 11
intQueue.enqueue(13)
print("Print elements in queue")
for i in intQueue {
 print(i)
}

let queueValuesMultipliedByTwo = intQueue.map { $0 \* 2 }
print(queueValuesMultipliedByTwo)

### Storing reference type in Queue

Now, let's try the queue with reference types, we'll create a simple class, which confirms to _**CustomStringConvertible**_ protocol to print class description. And add few instances into the queue.

class Foo : CustomStringConvertible {
 let tag: Int
 init(\_ tag: Int) {
  self.tag = tag
 }
 deinit {
  print("Removing...\\(tag)")
 }
 var description: String {
  return "#\\(tag)"
 }
}

var queueClass = Queue<Foo>(capacity: 20)
queueClass.enqueue(Foo(1))
queueClass.enqueue(Foo(2))
queueClass.dequeue()
print(queueClass)

The entire playground friendly code is **[available here](https://gist.github.com/zeeshankhan/8784ead07bd9033529df).** _Happy coding, cheers :)_
