---
title: Swift logs
date: 2021-08-16 13:41:10 +0400
categories: [Swift]
tags: [writing, swift]
---

There are many ways we can log our data in standard output console and few of them also supports logging in Console.app. Let's have a look at them:
- `print()`
- `debugPrint()`
- `dump()`
- `NSLog`
- `OSLog`
- `Logger` and etc.

## `print`
- API reference: [https://developer.apple.com/documentation/swift/1541053-print]()
- It prints the given text to the standard output.
- It supports multiple arguments and converts those to String representable before printing.
- It terminates with a new line character as default which can be overridden.
- We can also pass separator to be printed between arguments.

```swift
func print(_ items: Any..., separator: String = " ", terminator: String = "\n")

print(1, 1.2, false, "") // 1 1.2 false 

print(1, 1.2, false, "", separator: " | ") // 1 | 1.2 | false | 

print("Apple", terminator: "") //Apple
```

## `debugPrint`
- API reference: [https://developer.apple.com/documentation/swift/1539920-debugprint]()
- It is similar to `print` except that it adds some additional information like type name etc.
- It prints things like `\n` `\t` as is, without formatting the text.
- if we implement both `CustomDebugStringConvertible` and `CustomStringConvertible` protocol, then `debugPrint` method default use `debugDescription` content, and `print` method default use `description` content.

```swift
func debugPrint(_ items: Any..., separator: String = " ", terminator: String = "\n")

print("") // 
debugPrint("") // ""

struct Foo {
    let name = "jump"
}

print(Foo()) // Foo(name: "jump")
debugPrint(Foo()) // __lldb_expr_1.Foo(name: "jump")
```
 
## `dump`
- API reference: [https://developer.apple.com/documentation/swift/1539127-dump]()
- It prints given object's content using its mirror.
- It prints almost same information as `debugPrint` except it starts with a dash character.
- It has more parameters to configure like
    - name = gives name to the value printed
    - indent = gives indentation before the dash character
    - maxDepth = specifies the maximum depth for descendants
    - maxItems = specifies the maximum number of elements to print full content

```swift
dump("") // - ""
dump(Foo())
/*
▿ __lldb_expr_15.Foo
  - name: "jump"
*/

dump("Apple", name: "Company", indent: 4, maxDepth: 5, maxItems: 5)
// output:     - Company: "Apple"
```

## `NSLog`
- It comes from Objective-C world. It is not a recommended solution for Swift.
- It is not part of Swift standard library, it requires `Foundation` framework to be imported.


## `OSLog`
- It prints log messages on unified logging system (Console.app).
- It requires `OSLog` framework to be imported.
- It does not work with Linux or Windows platform.
- It supports many log levels
    - default = is notice
    - info
    - debug
    - error
    - fault

### `os_log` parameters
- `%{public}@` = prints the given argument info as is.
- `%{private}@` = hides the given argument info `<private>` in Console.app but will be visible in Xcode console.

```swift
func os_log(_ type: OSLogType, dso: UnsafeRawPointer = #dsohandle, log: OSLog = .default, _ message: StaticString, _ args: CVarArg...)

os_log("message")

let log = OSLog(subsystem: "appName", category: "login")
os_log("apple", log: log, type: .info)

os_log("My id %{private}@ ", log: log, type: .info, "abc123")
// My id <private>
```


## `Logger`
- Came with iOS 14, improved version of `OSLog`
- Has similar initializer as `OSLog` with `subsystem` and `category`
- All the log levels are in form of functions here.
- Privacy level can be configured using enum unlike string in OSLog.
- It provide some nice APIs like setting `alignment` and formatting double value `.fixed(precision:)` etc.

```swift
let log = Logger(subsystem: "app", category: "viewcycle")
log.info("info message")
log.debug("My id \("abc123", privacy: .private)")

log.debug("Employee \("name", align: .left(columns: 10))")
```

**Persistent**
These log messages are stored in disks based on their type. Debug is not persisted, info is only persisted during log collect option, and the rest are persisted until storage limit.

## Open source

### Custom Dump from Point Free
[https://www.pointfree.co/blog/posts/62-open-sourcing-custom-dump]()

### CocoaLumberjack
[https://cocoalumberjack.github.io]()

## Further reading

### Apple Docs
There is an article from apple on [Generating Log Messages from Your Code](https://developer.apple.com/documentation/os/logging/generating_log_messages_from_your_code).
It tells about how to use the new [Logging Framework](https://developer.apple.com/documentation/os/logging) and its APIs in details.

### [https://steipete.com/posts/logging-in-swift/]()
[Peter Steinberger](https://twitter.com/steipete) has written an in-depth article on `os_log` and new logging APIs.

### WWDC videos
There are few videos from WWDC
- [Video - Measuring Performance Using Logging](https://developer.apple.com/videos/play/wwdc2018/405/)
- [Video - Explore logging in Swift](https://developer.apple.com/videos/play/wwdc2020/10168/)

<!-- 
How to read from Console App https://www.avanderlee.com/workflow/oslog-unified-logging/
-  -->
