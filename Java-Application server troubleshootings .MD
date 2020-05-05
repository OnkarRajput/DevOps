# difference between heap dump and thread dump

* A thread dump is a snapshot of the state of all threads that are part of the process. The state of each thread is presented with a so called stack trace, which shows the contents of a thread's stack. Some of the threads belong to the Java application you are running, while others are JVM internal threads.

* A heap dump is a snapshot of the memory of a Java™ process. The snapshot contains information about the Java objects and classes in the heap at the moment the snapshot is triggered. ... Class loader, name, super class, and static fields. Garbage collection roots. Objects defined to be reachable by the JVM.
