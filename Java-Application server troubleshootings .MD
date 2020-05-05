# difference between heap dump and thread dump

* A thread dump is a snapshot of the state of all threads that are part of the process. The state of each thread is presented with a so called stack trace, which shows the contents of a thread's stack. Some of the threads belong to the Java application you are running, while others are JVM internal threads.

* A heap dump is a snapshot of the memory of a Java™ process. The snapshot contains information about the Java objects and classes in the heap at the moment the snapshot is triggered. ... Class loader, name, super class, and static fields. Garbage collection roots. Objects defined to be reachable by the JVM.


-------------------

## Thread dumps 

A thread dump will give you the information on the exact status of each thread.
Thread dumps are vital artifacts to diagnose CPU spikes, deadlocks, poor response times, memory problems, unresponsive applications, and other system problems.

------
### jstack
jstack is an effective command line tool to capture thread dumps. jstack tool is shipped in JDK_HOMEbin folder.

```
jstack -l  <pid> > <file-path>

 ``` 
----------  
### Kill -3

In major enterprises for security reasons only JREs are installed on production machines. such circumstances ‘kill -3’ option can be used.

```
kill -3 <pid>
```

pid: is the Process Id of the application, whose thread dump should be captured.
‘kill -3’ option is used thread dump is sent to the standard error stream. If you are running your application in tomcat, thread dump will be sent into <TOMCAT_HOME>/logs/catalina.out file.
  
