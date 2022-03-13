---
title: Combine Introduction!
date: 2020-05-20 13:41:10 +0400
categories: [Notes]
tags: [swift, combine]
---

- Apple introduced a new framework to deal with asynchronous programming called Combine in WWDC 2019.
- A framework which provides stream of data which can emits values and optionally end in a success or error.
- It is a declarative Swift API for processing values over time.

### Caveat

- The biggest caveat at the moment is that it is only available for iOS 13 and above.
- Combine also does not have anything similar to RxCocoa where we could map our UI elements with Publishers and Subscribers.

## What the benefits of using it

- Since it is a way to write reactive programming, which means it executes its operations asynchronously.
- Using combine won't change the way we write our code. we could still follow our all the design patterns we are using and keep our architecture intact.
- The only thing we need to make sure that we understand the APIs clearly as If we've an experience with RxSwift/Reactive Programming then we might messed-up with API names and outcomes.

## How it stands against Rx

- If we look into the APIs of RxSwift and ReactiveSwift, we could say that Combine is inspired by these framework API set. It is very similar when compared, and now there is a cheat-sheet available as well which lists components and operators of Combine and RxSwift frameworks:
[CheatSheet](https://github.com/CombineCommunity/rxswift-to-combine-cheatsheet)

### Basics

|                       | RxSwift                          | Combine                                    |
|-----------------------|----------------------------------|--------------------------------------------|
| Deployment Target     | iOS 8.0+                         | iOS 13.0+                                  |
| Platforms supported   | iOS, macOS, tvOS, watchOS, Linux | iOS, macOS, tvOS, watchOS, UIKit for Mac ¹ |
| UI Bindings           | RxCocoa                          | SwiftUI ²                                  |

### Core Components

| RxSwift                   | Combine                         | Notes                                                                                                                                                           |
|---------------------------|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Observable                | Publisher                       |                                                                                                                                                                 |
| Observer                  | Subscriber                      |                                                                                                                                                                 |
| AnyObserver               | AnySubscriber                   |                                                                                                                                                                 |
| Disposable                | Cancellable                     |                                                                                                                                                                 |
| DisposeBag                | A collection of AnyCancellables | Call anyCancellable.store(in: collection), where collection can be an array, a set, or any other RangeReplaceableCollection                                     |
| Driver                    | `BindableObject` (SwiftUI)        | Both guarantee no failure, but Driver guarantees delivery on Main Thread. In Combine, SwiftUI recreates the entire view hierarchy on the Main Thread, instead. |
| PublishSubject            | PassthroughSubject              |                                                                                                                                                                 |
| BehaviorSubject           | CurrentValueSubject             | This seems to be the type that holds @State under the hood                                                                                                      |
| SchedulerType             | Scheduler                       |                                                                                                                                                                 |

## How big is learning curve

It is not difficult for anyone who knows any reactive programming whether it is RxSwift or ReactiveSwift.
It is similar in core concept only API differs.

## Examples

```swift
let publisher = Just(23)
_ = publisher.sink {
    print($0) // It will emit 23 here
}

"23".publisher.sink { print($0) }

[1,2,3,4,5].publisher.sink { print($0) }
```

Thanks for the read, see you in next article :)
