---
title: "Best practices for writing Objective-C code"
date: "2012-01-31"
tags: 
  - "cocoa-best-practices"
  - "objective-c"
---

I have found some best practices for writing Objective C code. [More on the same](http://stackoverflow.com/questions/155964/what-are-best-practices-that-you-use-when-writing-objective-c-and-cocoa)

1) With the advent of properties, I no longer use "\_" to prefix "private" class variables. After all, if a variable can be accessed by other classes shouldn't there be a property for it? I always disliked the "\_" prefix for making code uglier, and now I can leave it out.

2) Speaking of private things, I prefer to place private method definitions within the .m file in a class extension like so:

```
#import "MyClass.h" 
@interface MyClass () 
- (void) someMethod 
- (void) someOtherMethod 
@end 
@implementation MyClass 
```

Why clutter up the .h file with things outsiders should not care about? The empty () works for private categories in the .m file, and issues compile warnings if you do not implement the methods declared.

3) I have taken to putting dealloc at the top of the .m file, just below the @synthesize directives. Shouldn't what you dealloc be at the top of the list of things you want to think about in a class? That is especially true in an environment like the iPhone.

3.5) In table cells, make every element (including the cell itself) opaque for performance. That means setting the appropriate background color in everything.

3.6) When using an NSURLConnection, as a rule you may well want to implement the delegate method:

```

- (NSCachedURLResponse *)connection:(NSURLConnection *)connection                  
willCacheResponse:(NSCachedURLResponse *)cachedResponse 
{       
return nil; 
}
 
```

I find most web calls are very singular and it's more the exception than the rule you'll be wanting responses cached, especially for web service calls. Implementing the method as shown disables caching of responses.

Also of interest, are some good iPhone specific tips from Joseph Mattiello (received in an iPhone mailing list). There are more, but these were the most generally useful I thought (note that a few bits have now been slightly edited from the original to include details offered in responses):

4) Only use double precision if you have to, such as when working with CoreLocation. Make sure you end your constants in 'f' to make gcc store them as floats.

```

float val = someFloat * 2.2f; 
```

This is mostly important when `someFloat` may actually be a double, you don't need the mixed-mode math, since you're losing precision in 'val' on storage. While floating-point numbers are supported in hardware on iPhones, it may still take more time to do double-precision arithmetic as opposed to single precision. References:

- [Double vs float on the iPhone](http://stackoverflow.com/questions/1622729/double-vs-float-on-the-iphone)
- [iPhone/iPad double precision math](http://stackoverflow.com/questions/8854001/iphone-ipad-double-precision-math)

On the older phones supposedly calculations operate at the same speed but you can have more single precision components in registers than doubles, so for many calculations single precision will end up being faster.

5) Set your properties as `nonatomic`. They're `atomic` by default and upon synthesis, semaphore code will be created to prevent multi-threading problems. 99% of you probably don't need to worry about this and the code is much less bloated and memory efficient when set to nonatomic.

6) SQLite can be a very, very fast way to cache large data sets. A map application for instance can cache its tiles into SQLite files. The most expensive part is disk I/O. Avoid many small writes by sending `BEGIN;` and`COMMIT;` between large blocks. We use a 2 second timer for instance that resets on each new submit. When it expires, we send COMMIT; , which causes all your writes to go in one large chunk. SQLite stores transaction data to disk and doing this Begin/End wrapping avoids creation of many transaction files, grouping all of the transactions into one file.

Also, SQL will block your GUI if it's on your main thread. If you have a very long query, It's a good idea to store your queries as static objects, and run your SQL on a separate thread. Make sure to wrap anything that modifies the database for query strings in `@synchronize() {}` blocks. For short queries just leave things on the main thread for easier convenience.
