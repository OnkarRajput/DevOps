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
  
------
### JCMD 

The jcmd tool was introduced with Oracle’s Java 7. It’s useful in troubleshooting issues with JVM applications.
. It has various capabilities such as identifying java process Ids, acquiring heap dumps, acquiring thread dumps, acquiring garbage collection statistics,

```
jcmd <pid> Thread.print > <file-path>
```
“jcmd Thread.print” will do largely the same thing as jstack

### Windows (Ctrl + Break)
This option will work only in Windows Operating system.

 Now on the console window issue the “Ctrl + Break” command.
```
 java -classpath . SampleThreadProgram > C:workspacethreadDump.txt 2>&1  
```

-----
### Visual VM 
* Debug JVM using JVisual VM | Heap Dump | Thread Dump | Profiling :

JVisualVM: Java VisualVM is a graphical user interface tool that provides detailed information about the applications while they are running on a specified Java Virtual Machine (JVM). 

it's free comes with jvm no need to install extra external tools to moniter the resources. For remote hosts to moniter you have to expose jmx port to connect the remote host. to identify codes leak, memory leaks, or Code Smells.

* Overview: 
Monitor: heap/metaspace, classes, threads live and live peak

-----
### ThreadMXBean: 
write few lines of code to generate thread dumps programmatically.

```
 public void  dumpThreadDump() {
        ThreadMXBean threadMxBean = ManagementFactory.getThreadMXBean();
        for (ThreadInfo ti : threadMxBean.dumpAllThreads(true, true)) {
            System.out.print(ti.toString());
        }
    }   
```

-----------
### APM Tool – App Dynamics
1. Create an action, selecting Diagnostics->Take a thread dump in the Create Action window.

2. Enter a name for the action, the number of samples to take, and the interval between the thread dumps in milliseconds.

3. If you want to require approval before the thread dump action can be started, check the Require approval before this Action checkbox and enter the email address of the individual or group that is authorized to approve the action. See Actions Requiring Approval for more information.

4. Click OK.

Threads : how many threads pool running in

sampler : memory, cpu

thread dump: how many threads running or stuck. what's process line running

Heap dump : shows different hump  

profiler : shows thread pool like running jdbc connection, and some other blocking queue etc..

------
### JMC 
Java Mission Control (JMC) is a tool that collects and analyze data from Java applications running locally or deployed in production environments.
