---
title: Operating System
date: 2016-06-04 09:41:10 +0400
categories: [Notes]
tags: [os]
---

### Sharing my very old notes on operating system.

**Reliability:** can be defined as the probability that a system will produce correct outputs **up to** some given time _t._ Reliability is enhanced by features that help to avoid, detect and repair hardware faults. A reliable system does not silently continue and deliver results that include uncorrected corrupted data. Instead, it detects and, if possible, corrects the corruption.

**Availability:** means the probability that a system is operational at a given time, i.e. the amount of time a device is actually operating as the percentage of total time it should be operating. High-availability systems may report availability in terms of minutes or hours of downtime per year. Availability features allow the system to stay operational even when faults do occur.

**Scalability:** The ability to retain performance levels when adding additional processors.

**Clock Speed**: The Clock Speed (of CPU) is used to determine the maximum amount of wok a computer (CPU) can perform in a specific time (a time unit). So we can say, our system executes our programs with x clock speed.

**Thread**: From a technical standpoint, a thread is a combination of the kernel-level and application-level coding (data structure), which is needed to manage the execution of code or a task. The kernel-level structures coordinate the dispatching of events to the thread and the preemptive scheduling of the thread on one of the available cores. The application-level structures include the call stack for storing function calls and the structures the application needs to manage and manipulate the thread’s attributes and state.

**_Difference between_ Task_,_ Process _and_ Threads**

**Threads** are used for small tasks, whereas processes are used for more 'heavyweight' tasks – basically the execution of applications. Another difference between a thread and a process is that threads within the same process share the same address space, whereas different processes do not.

**Process** Each process provides the resources needed to execute a program. A process has a virtual address space, executable code, open handles to system objects, a security context, a unique process identifier, environment variables, a priority class, minimum and maximum working set sizes, and at least one thread of execution. Each process is started with a single thread, often called the primary thread, but can create additional threads from any of its threads.

**Thread** A thread is the entity within a process that can be scheduled for execution. All threads of a process share its virtual address space and system resources. In addition, each thread maintains exception handlers, a scheduling priority, thread local storage, a unique thread identifier, and a set of structures the system will use to save the thread context until it is scheduled. The thread context includes the thread's set of machine registers, the kernel stack, a thread environment block, and a user stack in the address space of the thread's process. Threads can also have their own security context, which can be used for impersonating clients.

**Cloud computing**, also on-demand computing, is a kind of Internet-based computingthat provides shared processing resources and data to computers and other devices on demand.

**Virtual memory** is a feature of an operating system (OS) that allows a computer to compensate for shortages of physical **memory** by temporarily transferring pages of data from random access **memory**(RAM) to disk storage.

**Critical Section:** In concurrent programming, a **critical section** is a part of a multi-process program that may not be concurrently executed by more than one of the program's processes.

- In other words, It is a piece of a program that requires mutual exclusion of access.
- Typically, the critical section accesses a shared resource, such as a data structure, a peripheral device, or a network connection, that does not allow multiple concurrent accesses.
- A critical section may consist of multiple discontiguous parts of the program's code. For example, one part of a program might read from a file that another part wishes to modify. These parts together form a single critical section, since simultaneous readings and modifications may interfere with each other.
- Since critical sections may execute only on the processor on which they are entered, **synchronization** is only required within the executing processor.

### **Concurrency issues**

- **Resource Starvation**: In computer science, starvation is a problem encountered in concurrent computing where a process is perpetually denied necessary resources to process its work. Starvation may be caused by errors in a scheduling or mutual exclusion algorithm, but can also be caused by resource leaks
- **Mutual Exclusion**: Mutual exclusion is in many ways the fundamental issue in concurrency. It is the requirement that when a process P is accessing a shared resource R, no other process should be able to access R until P has finished with R. Examples of such resources include files, I/O devices such as printers, and shared data structures.

**A Mutex (Lock)** is locking mechanism used to synchronize access to a resource. Only one task (can be a thread or process based on OS abstraction) can acquire the mutex. It means there will be ownership associated with mutex, and only the owner can release the lock (mutex). _\- very good answer_ [http://stackoverflow.com/a/346678](http://stackoverflow.com/a/346678) **Semaphore** is signaling mechanism (“I am done, you can carry on” kind of signal). For example, if you are listening songs (assume it as one task) on your mobile and at the same time your friend called you, an interrupt will be triggered upon which an interrupt service routine (ISR) will signal the call processing task to wakeup.

**Deadlock / Livelock** (how to avoid?) [http://wikipedia.moesalih.com/Deadlock#Livelock](http://wikipedia.moesalih.com/Deadlock#Livelock) [http://stackoverflow.com/questions/6155951/whats-the-difference-between-deadlock-and-livelock](http://stackoverflow.com/questions/6155951/whats-the-difference-between-deadlock-and-livelock)

**DEADLOCK** Deadlock is a condition in which a task waits indefinitely for conditions that can never be satisfied - task claims exclusive control over shared resources - task holds resources while waiting for other resources to be released - tasks cannot be forced to relinguish resources - **a circular waiting condition exists**

- **Deadlock**: A situation in which two or more processes are unable to proceed because each is waiting for one the others to do something.
- **For example**, consider two processes, P1 and P2, and two resources, R1 and R2. Suppose that each process needs access to both resources to perform part of its function. Then it is possible to have the following situation: the OS assigns R1 to P2, and R2 to P1. Each process is waiting for one of the two resources. Neither will release the resource that it already owns until it has acquired the other resource and performed the function requiring both resources. The two processes are deadlocked

**Live Locked** threads are unable to make further progress. However, the threads are not blocked — they are simply too busy responding to each other to resume work.

- **Livelock**: A situation in which two or more processes continuously change their states in response to changes in the other process(es) without doing any useful work:
- **Starvation**: A situation in which a runnable process is overlooked indefinitely by the scheduler; although it is able to proceed, it is never chosen.
- **Example:** Suppose that three processes (P1, P2, P3) each require periodic access to resource R. Consider the situation in which P1 is in possession of the resource, and both P2 and P3 are delayed, waiting for that resource. When P1 exits its critical section, either P2 or P3 should be allowed access to R. Assume that the OS grants access to P3 and that P1 again requires access before P3 completes its critical section. If the OS grants access to P1 after P3 has finished, and subsequently alternately grants access to P1 and P3, then P2 may indefinitely be denied access to the resource, even though there is no deadlock situation.
- A live lock is similar to a deadlock, except that the states of the processes involved in the livelock constantly change with regard to one another, none progressing. **Livelock is a special case of resource starvation**; the general definition only states that a specific process is not progressing.
- A real-world example of livelock occurs when two people meet in a narrow corridor, and each tries to be polite by moving aside to let the other pass, but they end up swaying from side to side without making any progress because they both repeatedly move the same way at the same time.
- Livelock is a risk with some algorithms that detect and recover from deadlock. If more than one process takes action, the deadlock detection algorithm can be repeatedly triggered. This can be avoided by ensuring that only one process (chosen randomly or by priority) takes action.

**Context switch**: [http://wikipedia.moesalih.com/Context\_switch](http://wikipedia.moesalih.com/Context_switch) In computing, a **context switch** is the process of storing and restoring the [state](http://wikipedia.moesalih.com/wiki/State_(computer_science)) (more specifically, the execution [context](http://wikipedia.moesalih.com/wiki/Context_(computing))) of a [process](http://wikipedia.moesalih.com/wiki/Process_(computing)) or [thread](http://wikipedia.moesalih.com/wiki/Thread_(computing)) so that execution can be resumed from the same point at a later time. This enables multiple processes to share a sing CPU and **is an essential feature of a [multitasking](http://wikipedia.moesalih.com/wiki/Computer_multitasking) [operating system](http://wikipedia.moesalih.com/wiki/Operating_system).**

**Process Scheduling**: Job **scheduler** selects processes from the queue and loads them into memory for execution. Process loads into the memory for CPU **scheduling**. The primary objective of the job **scheduler** is to provide a balanced mix of jobs, such as I/O bound and processor bound. [http://www.tutorialspoint.com/operating\_system/os\_process\_scheduling.htm](http://www.tutorialspoint.com/operating_system/os_process_scheduling.htm)

**Race Condition**: A race condition is an undesirable situation that occurs when a device or system attempts to perform two or more operations at the same time, but because of the nature of the device or system, the operations must be done in the proper sequence in order to be done correctly.

**Green Thread**: Green threads are threads that are scheduled by a runtime library or virtual machine (VM) instead of natively by the underlying operating system. Green threads emulate multithreaded environments without relying on any native OS capabilities, and they are managed in user space instead of kernel space, enabling them to work in environments that do not have native thread support

**Multiprocessing** — multiple CPUs executing concurrently **Multitasking** — the operating systems simulates concurrency on a single CPU by interleaving the execution of different tasks.

- _Awesome Answer about Concurrency and Parallelism:_ [http://stackoverflow.com/a/24684037](http://stackoverflow.com/a/24684037)
- _Detailed here:_ [http://sce.uhcl.edu/helm/rationalunifiedprocess/process/workflow/ana\_desi/co\_cncry.htm](http://sce.uhcl.edu/helm/rationalunifiedprocess/process/workflow/ana_desi/co_cncry.htm)
- _Objective C_: [https://www.objc.io/issues/2-concurrency/concurrency-apis-and-pitfalls/](https://www.objc.io/issues/2-concurrency/concurrency-apis-and-pitfalls/)

**Interrupt-ability - Concurrency** is when two or more tasks can start, run, and complete in overlapping time periods. It doesn't necessarily mean they'll ever both be running at the same instant. Eg. multitasking on a single-core machine.

- A condition that exists when at least two threads are making progress. A more generalized form of parallelism that can include time-slicing as a form of virtual parallelism.

**Independent-ability Parallelism** is when tasks literally run at the same time, eg. on a multicore processor.

- A condition that arises when at least two threads are executing simultaneously.

**Conditions for Deadlock**: Three policy conditions are necessary for deadlock to be possible.

- **Mutual exclusion** Only one process may use a resource at one time.
- **Hold and wait** A process may hold some resources while waiting for others.
- **No preemption** No process can be forced to release a resource. A fourth condition is required for deadlock to actually occur.
- **Circular wait** A closed chain of processes exists, such that each process is blocked waiting for a resource held by another process in the set. Three approaches exist for dealing with deadlock.
- **Prevention** involves adopting a static policy that disallows one of the four conditions above.
- **Avoidance** involves making dynamic choices that guarantee prevention.
- **Detection and recovery** involves recognising when deadlock has occurred, and trying to recover.

**Deadlock Prevention:** We can prevent deadlock from occurring by adopting either **an indirect policy** that disallows one of the three necessary conditions, or **a direct policy** that disallows sets of processes that can exhibit a circular wait.

### Scalability Issues with synchronization

**Scalability:** The ability to retain performance levels when adding additional processors. In context of mutual exclusion ME lock.

1. **Latency**: If a thread wants to acquire a lock, it has to do some operations, it has to go to memory, get this lock and make sure no one else is competing with it. So **_the time that is spent by a thread in acquiring the lock_ is called Latency**. Latency is saying, lock is currently not being used. How long does it take for me to go and get it. That really the key question that latency is.
2. **Waiting Time**: Scalability with synchronization, is the waiting time. That is, If i want to go and get the lock, how long i wait in the order to get the lock?
3. **Contention**: **_Un-scalability of lock is Contention_**. When so many thread waits to get a particular, and only one of them wins. Thats contention part of implementing synchronization primitive.

### Distributed System / Cloud Computing

**Definition:** Distributed processing involves multiple processes on multiple systems. A distributed system is a collection of Nodes which are interconnected by a Local Area Network (LAN) or a Wide Area Network (WAN) and the LAN may be implemented using a twisted pair, coaxial cable and optical fiber, and if it is a WAN, it could be implemented using a satellite communication, microwave links and so on. That's sort of the picture of What a distributed system is, number one.

**Memory** Number two, there's no physical memory that is shared between nodes of the distributed system. So the only way nodes can communicate with one another is by sending messages on the local area network to one another. So there is no shared memory (or physical memory) for communication between the nodes of the distributed system.

**Time** Event Computation Time is the time it takes on a single node to do some meaningful processing, that computation time is what we are calling as the **event computation time**. And a node may also communicate With other nodes in the system and that's what we're calling as **communication time** or the **messaging time**. And the third property, is the fact that the message communication time is significantly larger than event computation time that happens on a single node.

**Reason** A system is distributed if a message transmission time, is not negligible compared to the time between events in a single process.

**Example** What is the implication of this definition? Interestingly, even a cluster is a distributed system by this definition. Because processors have become blazingly fast, so the event computation has shrunk quite a bit. On the other hand the message communication time is also becoming better but not as fast as the computation time that happens on a single processor and therefore even on a cluster which is all contained in a single rack in a data center, the message transmission time is significantly more than The event time. And so even a cluster is a distributed system by this definition.

**Benefits of Cloud Computing**

- Reduced Cost
- Automatic Updates
- Green Benefits of Cloud computing
- Remote Access
- Disaster Relief
- Self-service provisioning
- Scalability
- Reliability and fault-tolerance
- Ease of Use
- Skills and Proficiency

_Happy Reading :)_
