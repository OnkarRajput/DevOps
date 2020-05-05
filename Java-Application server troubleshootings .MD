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
 
 ### threaddump_linux_jstack-continuous.sh
 
 ```
 vim threaddump_linux_jstack-continuous.sh
 ```
 
 ```
 #!/bin/sh
#
# Takes the JBoss PID as an argument. 
#
# Make sure you set JAVA_HOME
#
# Create thread dumps a specified number of times (i.e. LOOP) and INTERVAL. 
#
# Thread dumps will be collected in the file "jstack_threaddump.out", in the same directory from where this script is been executed.
#
# Usage: sh ./threaddump_linux_jstack-continuous.sh <JBOSS_PID>
#

# Number of times to collect data.
LOOP=6
# Interval in seconds between data points.
INTERVAL=20

# Setting the Java Home, by giving the path where your JDK is kept
JAVA_HOME=/home/jdk1.6.0_21

for ((i=1; i <= $LOOP; i++))
do
   $JAVA_HOME/bin/jstack -l $1 >> jstack_threaddump.out
   echo "thread dump #" $i
   if [ $i -lt $LOOP ]; then
    echo "sleeping..."
    sleep $INTERVAL
  fi
done

 
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


-------------
## How do I fix Java heap space error?
1. Calculate memory needed.
2. Check that the JVMs have enough memory for the TaskTracker tasks.
3. Check that the JVMs settings are suitable for your tasks.
4. Limit your nodes use of swap space and paged memory.
5. Set the task attempt slots to a number that’s lower than the number calculated by the JobTracker web GUI
https://mapr.com/blog/how-to-avoid-java-heap-space-errors-understanding-and-managing-task-attempt-memory/

## What is the Java heap?
The heap is the runtime data area from which memory for all class instances and arrays is allocated. The heap is created on virtual machine start-up. Heap storage for objects is reclaimed by an automatic storage management system (known as a garbage collector); objects are never explicitly deallocated.

## How do I increase heap size?
You can increase or change size of Java Heap space by using JVM command line option -Xms, -Xmx and -Xmn. don't forget to add word "M" or "G" after specifying size to indicate Mega or Gig. for example you can set java heap size to 258MB by executing following command java -Xmx256m HelloWord.

## What is a heap error?
Heap errors. Heap errors can occur when your code inadvertently overwrites control information that the memory management functions use to control heap usage. The application that you are debugging must have been built with the heap check capability.


## Difference between Java Heap Space and Stack Memory:
Heap memory is used by all the parts of the application whereas stack memory is used only by one thread of execution. Whenever an object is created, it's always stored in the Heap space and stack memory contains the reference to it.


## What is thread dump and heap dump in Weblogic?
A thread dump is a dump of the stacks of all live threads. Thus useful for analyzing what an app is up to at some point in time, and if done at intervals handy in diagnosing some kinds of 'execution' problems (e.g. thread deadlock). A heap dump is a dump of the state of the Java heap memory.

## When should I take thread dump?
A good practice is to take 10 thread dumps at a regular interval (for example, one thread dump every ten seconds).
Step 1: Get the PID of your Java process. The first piece of information you will need to be able to obtain a thread dump is your Java process's PID. ...
Step 2: Request a thread dump from the JVM. jstack.

## How do I get a memory dump?
* Enable memory dump setting

* In Control Panel, select System and Security > System.
Select Advanced system settings, and then select the Advanced tab.

*In the Startup and Recovery area, select Settings.

* Make sure that Kernel memory dump or Complete memory dump is selected under Writing Debugging Information.

## How do I check my heap dump?
Open the heap dump in Eclipse Memory Analyzer using the option File --> Open Heap Dump. First, it will prompt you to create a leak suspect report. The user can create it or skip it. The "overview" tab of the memory analyzer will show the total size of the heap and a pie chart of object size.Au

## How do I collect a heap dump?
Heap dump = memory contents for the JVM process output to a binary file. To take a thread dump on Windows, CTRL + BREAK if your JVM is the foreground process is the simplest way. If you have a unix-like shell on Windows like Cygwin or MobaXterm, you can use kill -3 {pid} like you can in Unix.

## Why do we take thread dump?
Thread dumps are vital artifacts to diagnose CPU spikes, deadlocks, memory problems, unresponsive applications, poor response times, and other system problems. Thread dumps are vital artifacts to diagnose CPU spikes, deadlocks, memory problems, unresponsive applications, poor response times, and other system problems

## What thread dump contains?
A thread dump is a snapshot of the state of all threads that are part of the process. The state of each thread is presented with a so called stack trace, which shows the contents of a thread's stack. Some of the threads belong to the Java application you are running, while others are JVM internal threads.

# How do you take a thread dump?
Ctrl + Break (Windows) In Windows operating systems, we can capture a thread dump using the CTRL and Break key combination. To take a thread dump, navigate to the console used to launch the Java application and press CTRL and Break keys together.

# How do you take a thread dump in Unix?
On UNIX, Mac OS X, and Linux, you can send a QUIT signal to the Java process to tell it to output a thread dump to standard output. You may need to run this command as sudo -u user kill -QUIT <pid> where "user" is the user that the Java process is running as
