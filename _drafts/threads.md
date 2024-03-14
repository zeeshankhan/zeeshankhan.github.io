# Thread

Register variable
A processor has things called "registers", which are essentially its local variables. Anything it wants to operate on, it loads from memory (RAM) into registers, does the operations, and then stores them back to memory. One of those registers is the "program counter", which holds the location of the next instruction to run. 

Stack Variable
Now, there's a fixed quantity of registers, while you're allowed to have unlimited local variables, so there's also a dedicated region of memory for storing local variables that aren't currently in registers, which is called "the stack" (it's a stack because each time you call a function its variables get added to the top, and each time you return from a function they get taken off the top).

A thread is:
* All registers states
* The stack
* A pointer for the operating system (the kernel scheduler specifically) to keep track of it
* A few odds and ends of metadata and extra state like priority and such

If you have more than one thread (a "multithreaded program"), then you have more than one program counter, more than one set of registers, and more than one stack, i.e. everything you need for more than one processor (or more than one core of one processor, almost the same thing) to run simultaneously. Every program has at least one thread, so running two programs also lets you use two processors at once, just like a single program with two threads would.

A multi-tasking operating system, which all modern consumer-focused ones are, will switch which thread is running at frequent intervals. This allows for arbitrarily many programs (or threads!) to run on arbitrarily few processors/cores. There are two ways to decide when to switch: a thread can voluntarily tell the system "I'm done for now, let something else run", or the system can just say "time's up, it's someone else's turn". These are called cooperative multitasking, and preemptive multitasking, respectively. All modern consumer-focused operating systems are primarily preemptively multitasking, because otherwise a badly behaved program can prevent other stuff from running by just not saying it's done.
But.
Preemptive multitasking with threads has some significant costs. In particular:
* Switching threads requires saving all the registers from the current thread into memory, and then loading all the registers from the new thread from memory, which takes time
* Storing all the information for a thread takes up memory
* Having the operating system (kernel) keep track of the threads requires more memory
So Swift Concurrency uses a hybrid model: it has light weight "thread-like" Tasks that the kernel knows nothing about, and the Concurrency library does its own cooperative multitasking to decide which of those get to run on a small handful of "real" kernel-aware threads. Every time you await something, you're giving the cooperative multitasking system a chance to say "ok it's someone else's turn", i.e. you're giving up your actual thread. Sometimes in the literature this is called an "M:N" threading model: M program threads are mapped onto N kernel threads, where M > N.
In the best case scenario this means that what previously would have required many many (expensive) real threads, each running for a short period of time, now requires only a tiny number of real threads, and they all run for as long as the kernel will let them, minimizing both memory overhead and switching costs.


What’s the difference between a Thread and a Task? : swift
https://www.reddit.com/r/swift/comments/wm3get/comment/ijx3uay/