---
title: Combine In-Practice!
date: 2020-05-21 13:41:10 +0400
categories: [Notes]
tags: [swift, combine]
---

## Key Points
- Apple introduced a new framework to deal with **asynchronous programming** called Combine in WWDC 2019.
- A framework which provides **stream of events** which can emits values and optionally end in a success or error.
- Or It is a **declarative** Swift API for processing values over time.
- Or It provides a **native** way of handling asynchronous events.
- The biggest caveat at the moment is that it is only available for iOS 13 and above.
- Combine lacks proper documentations and also does not have anything similar to RxCocoa where we could map our UI elements with Publishers and Subscribers.

## Key Concepts

- Publisher
- Subscriber
- Subscription
- Cancellable

### Publisher:-

- Publishers are the declarative part of Combine's API. They describe how values and errors are produced. But they don't produce, they publishes. we'll see how.
- Publishers are value types, so they are struct. 
- Publishers also allow for registrations of a subscriber. Something which receives values overtime.
- Publishes a stream of values, which becomes accessible only when subscribed to.

```swift
protocol Publisher {
 associatedtype Output
 associatedtype Failure: Error

 func subscribe<S: Subscriber>(_ subscriber: S) where S.Input == Output, S.Failure == Failure
}
```

- It is defined as a protocol with two associated type values **Output** and Failure. If it is not possible for a publisher to produce an error, then we can use the type Never for the associated type.
- Publisher has one key function called `subscribe()`

#### Built in Publishers

- NotificationCenter
- DataRequest
- Timer and etc

### Subscriber:-

- Subscribers are the counterpart to Publishers. They receive values including the completion.
- Usually Subscribers act and mutate state upon receipt of values, and for mutating state we use reference types in Swift. This means that they are Classes.

```swift
protocol Subscriber {
	associatedtype Input
 	associatedtype Failure: Error

	func receive(subscription: Subscription)
	func receive(_ input: Input) -> Subscribers.Demand
	func receive(completion: Subscribers.Completion<Failure>)
}
```

- It is defines as a protocol with two associated type values **Input** and Failure.
- Input type must match with its Publisher's Output.
- It has 3 key functions
- It can receive a subscription `receive(subscription: Subscription)`
- It can receive an Input
- It can receive completion

#### Built in Subscriber

- Sink
- Assign and etc

> So till now we've got the idea about publisher and subscriber. It’s obvious, that we have a publisher — that thing, which gives us values, and a subscriber, which requests this values for its own purpose. But the main part of this collaboration is usually hidden. I’m ? talking about subscription by itself, and that’s where all the magic happens. 
> Subscription is like Interactor, ViewModel or simply Brain of publisher-subscriber collaboration. The publisher creates a subscription with all required dependencies and gives it to the subscriber. After this, the subscription starts acting and processing values over time. Then subscriber consumes these values and we can receive them in the sink closure.

### Subscription

- A subscription is how a subscriber controls the flow of data from Publisher to a Subscriber.

```swift
protocol Subscription: Cancellable {

    func request(_ demand: Subscribers.Demand)
}
```

- It's a protocol which inherits `Cancellable`
- It has an only function which requests for the `Demand` a struct. Which defines the number of values a subscriber is subscribed to.
- There are three types of `Demand` `.none`, `.unlimited` and `.max(Int)`
- For example `sink` subscriber function is always subscribe to `.unlimited` values.

### Cancellable

- A confusing one, because there's not much documentation around it.
- Cancellation is also build into combine.
- Whenever a subscriber no longer needs to receive elements from a publisher, it may cancel its subscription. means set it to `nil`.
- Cancellation allows your to tear down the sequence of Publisher and subscribers early if we need to.

```swift
protocol Cancellable {

    func cancel()
}
```

- It's a protocol, which has a function to cancel the current subscription.
- It is documented that each subscriber should implement cancellable but how and flow are not mentioned. If we do not implement, I've not figured how it will affect.

> The subscriber types created by sink(receiveCompletion:receiveValue:) and assign(to:on:) both implement the Cancellable protocol, which provides a cancel() method. and they actually return `AnyCancellable`.

### The Flow/Pattern

![The Flow]({{ "/assets/img/post/CombineFlow.png" | relative_url }})

### Demo Time

- Existing publisher and subscriber
- Custom publisher
- Custom subscriber

## Other

- Operators
- Subjects
- Type Erasers
- RxSwift vs Combine etc

### Operators

- Operators are Publisher, and they are also declarative and therefore the value types.
- Changing behavior, adding values, removing values or etc
- They subscribe to another publisher (upstream)
- and They send result to subscriber (downstream)

### Subjects

- Subjects act both as a Subscriber and a Publisher.
- While a subject can receive values from an upstream publisher, it can also pass along these values to its downstream subscribers.

```swift
let currentSubject = CurrentValueSubject(1)
```

A CurrentValueSubject will hold an initial value.

```swift
let passthroughSubject = PassthroughSubject()
```

A passthrough subject will start emitting values only when it gets subscribed to.

### Type Erasers

- **AnySubscriber** wraps the Publisher protocol.
- **AnyPublisher** wraps the Subscriber protocol.
- **AnySubject** wraps a subject.
- **AnyCancellable** is a type-erased class that will automatically call `cancel()` on `deinit` using Swift’s memory management system.

### RxSwift vs. Combine

If we look into the APIs of RxSwift and ReactiveSwift, we could say that Combine is inspired by these framework API set. It is very similar when compared, and now there is a cheatsheet available as well which lists components and operators of Combine and RxSwift frameworks:
https://github.com/CombineCommunity/rxswift-to-combine-cheatsheet

In RxSwift..

- `Publishers` are Observables
- `Subscribers` are Observers
- `Cancellable` is Disposable _(There's no DisposeBag)_
- `CurrentValueSubject` is BehaviorSubject
- `PassthroughSubject` is PublishSubject
- `eraseToAnyPublisher` is asObservable
- `handleEvents()` is do()
- `Single` is Future

Thanks for the read, see you in next article :)
