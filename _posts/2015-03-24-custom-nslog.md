---
title: "Custom NSLog"
date: "2015-03-24"
categories: 
  - "technical-posts"
tags: 
  - "c"
  - "fprintf"
  - "ios"
  - "nslog"
  - "objective-c"
  - "variable-length"
---

## **ZKLog**

Writing our own NSLog using c-function with variable length arguments.

I've used _fprintf()_ function to write on console, because it sends the formatted output to the stream, where _printf()_ internally invokes _fprintf()_ with stdout.

_stderr_ stands for standard error device. The benefit of doing this is that anything written to standard error is not buffered so it is immediately written to the screen and is useful for debugging.

void ZKLog(NSString\*msg,...) {   va\_list arguments;
  va\_start(arguments, msg);
  NSString\* message = \[\[NSString alloc\] initWithFormat:msg arguments:arguments\];
  va\_end(arguments);
 fprintf(stderr, "\\n%s\\n", \[message UTF8String\]);  \[message release\]; message = nil; }
