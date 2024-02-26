# 异常

Java 异常相关类的的顶级类是 `Throwable`，之下分成两大类，一类是 **Exception**，一类是 **Error**。Exception 又分为 **Unchecked exceptions** 和 **Checked exceptions**，`RuntimeException` 是所有 Unchecked exceptions 的顶级父类。如下拓扑结构图所示：

![](theExceptionTopology.png)

+ **Error** ：一般是指那些 JVM 级别的非常严重的错误（如系统崩溃，虚拟机错误，内存空间不足，方法调用栈溢出等），仅靠程序本身无法恢复和预防的错误，所以不应该被捕获，发生 Error 程序一般终止运行。Error 也被视为 Unchecked exceptions；
+ **Exception** ：一般是指那些应用程序级可以处理或者属于预期范围的异常，所以可以被捕获，程序一般不会终止运行。
  + **Unchecked（RuntimeException and its subclasses）** ：一般是指那些应用程序级不可处理或恢复的异常（JDK: can be thrown during the normal operation of the Java Virtual Machine.）， 或完全没要必要捕获处理以及恢复的异常，因为这类异常本身就是 bug，应该被修复，所以不应该被捕获，因此在抛出之前，也不需要被声明；
  + **Checked（Exception and its other subclasses）** ：一般是指那些应用程序级可以处理或者属于预期范围的异常，所以应该被捕获并处理使程序恢复正常运行，因此在抛出之前，需要被显式声明。

对于 Checked exceptions （这也是编程中遇到的绝大多数情况），除非你明确知道怎么处理或者你明确需要对其进行包装，否则你不应该捕获任何受检异常，直至抛出到最外层，即最接近客户端的一层，然后统一处理。

## 断言

断言是用 `assert` 关键字来断言某个表达式恒为真（invariants），否则抛出错误（AssertionError）。可以为断言指定异常信息，如下所示：

```Java
assert param != null : "param can not be null";
```

断言，一般不在产品代码里使用， 因为如果断言失败则应该通过修复代码避免这种失败（意为代码本身的问题），而如果断言真的恒为真，则反而带来了额外的不必要的开销（因为本来就恒为真，没必要断言）。断言更多的是在单元测试框架下使用，用来断言被测试的程序正确与否，这时候你并不用对错误进行任何处理。另外，断言是很有用的注释工具，因为遵循标准语法，很容易阅读。要想使用断言，需要通过 JVM 参数 `-ea` 来启用断言，默认是关闭的。

## OOM

We talk about how to deal with **core dump** and **heap dump** for Java Application in this subject.

### Introduction

**Core dump**

A core dump, also known as a Java core, java core dump, Java thread dump, or a thread dump is a file that contains the following sections:

+ All of the threads that run on a Java Virtual Machine (JVM).
+ All of the monitors on a JVM.
+ Some useful information about the system that the JVM runs under.

It is used for viewing the thread activity inside the JVM at a given time and analyzing what an app is up to at some point in time, and if done at intervals handy in diagnosing some kinds of 'execution' problems (e.g. thread deadlock). IBM Thread and Monitor Dump Analyzer for Java should -- a lot of additional information besides just the threads and stacks -- used to determine hangs, deadlocks, and reasons for performance degradation System cores.

**Heap dump**

A heap dump is a snapshot of JVM memory/a dump of the state of the Java heap memory. Thus – it shows the live objects on the heap along with references between objects. It is used to determine memory usage patterns and memory leak suspects.

It is useful for analyzing what use of memory an application is making at some point in time so handy in diagnosing some memory issues, and if done at intervals handy in diagnosing memory leaks.

**A sample core dump file**

javacore*.txt

**A sample heap dump file**

java_pid1837.hprof

### Probable causes

**The probable cause of core dump**

Placeholder

**The probable cause of heap dump**

In general heap dump file generated with the following causes:

+ IO resources were not released
+ Database connections were not released
+ Collection classes were not cleaned
+ The resources hold by Thread were not released

### Tools to analyze dump file

**Tools for core dump**

+ **IBM Thread and Monitor Dump Analyzer for Java** - A tool that allows identification of hangs, deadlocks, resource contention, and bottlenecks in Java threads.

**Tools for heap dump**

+ **Memory Analyzer (MAT)** - The Eclipse Memory Analyzer is a fast and feature-rich Java heap analyzer that helps you find memory leaks and reduce memory consumption.

    Usage: Just double click `MemoryAnalyzer.exe`

+ **IBM HeapAnalyzer** - A graphical tool for discovering possible Java heap leaks, HeapAnalyzer allows the finding of a possible Java™ heap leak area through its heuristic search engine and analysis of the Java heap dump in Java applications.

    Usage: `\>java -Xmx1g -jar haxxx.jar.`

+ [Deprecated] IBM Memory Dump Diagnostic for Java (MDD4J) - The Memory Dump Diagnostic for Java tool analyzes common formats of memory dumps (heap dumps) from a virtual machine (JVM) that is running any stand-alone Java application including WebSphere Application Server. The memory dump analysis is aimed at identifying data structures within the Java heap that might be root causes of memory leaks. The analysis also identifies major contributors to the Java heap footprint of the application and their ownership relationship. The tool is capable of analyzing very large memory dumps obtained from production environment application servers encountering OutOfMemoryError issues. (Analysis of very large memory dumps requires at least 2GB of RAM.)
+ IBM Memory Analyzer - The IBM Monitoring and Diagnostic Tools for Java - Memory Analyzer is a Java heap analysis tool based on the Eclipse Memory Analyzer. The IBM Monitoring and Diagnostic Tools for Java - Memory Analyzer (Memory Analyzer) brings the diagnostic capabilities of the Eclipse Memory Analyzer Tool (MAT) to the IBM Virtual Machines for Java.

### Tools to Profile

**Tools for diagnose CPU/Memory**

+ **JConsole** - The Java 2 Platform, Standard Edition (J2SE) 5.0 release provides comprehensive monitoring and management support. It not only defines the management interfaces for the Java virtual machine, but also provides out-of-the-box remote monitoring and management on the Java platform and of applications that run on it. In addition, JDK 5.0 includes the Java Monitoring and Management Console (JConsole) tool. It uses the extensive instrumentation of the Java virtual machine to provide information on performance and resource consumption of applications running on the Java platform using Java Management Extension (JMX) technology.

    JConsle can recognize Java app automaticly.

    Usage: `\>jconsole`

+ **Java VisualVM** - VisualVM is a visual tool integrating several commandline JDK tools and lightweight profiling capabilities. Designed for both production and development time use, it further enhances the capability of monitoring and performance analysis for the Java SE platform.

    Java VisualVM can recognize Java app automaticly.

    Usage: `\>jvisualvm`

+ **JProfiler** - JProfiler is an award-winning all-in-one Java profiler. JProfiler's intuitive GUI helps you find performance bottlenecks, pin down memory leaks and resolve threading issues.

    Usage(*Maybe not up to date*):
    1. Append below properties to Java app launch file: `-agentlib:jprofilerti=port=8849 -Xbootclasspath/a:{JProfiler_HOME}/bin/agent.jar`
    2. Restarts the Java app.
    3. New Remote Integration in JProfiler. Please see JProfiler guide.
    4. Start JProfiler. Please see JProfiler guide.

+  **HPROF** - Heap and CPU Profiling Agent (JVMTI Demonstration Code).

    Usage:
    Please refer to [HPROF: A Heap/CPU Profiling Tool](http://docs.oracle.com/javase/7/docs/technotes/samples/hprof.html).

### 附录

摘自  [HPROF: A Heap/CPU Profiling Tool](http://docs.oracle.com/javase/7/docs/technotes/samples/hprof.html)

```
HPROF: A Heap/CPU Profiling Tool

The Java 2 Platform Standard Edition (J2SE) has always provided a simple command line profiling tool called HPROF for heap and cpu profiling. HPROF is actually a JVM native agent library which is dynamically loaded through a command line option, at JVM startup, and becomes part of the JVM process. By supplying HPROF options at startup, users can request various types of heap and/or cpu profiling features from HPROF. The data generated can be in textual or binary format, and can be used to track down and isolate performance problems involving memory usage and inefficient code. The binary format file from HPROF can be used with tools such as HAT to browse the allocated objects in the heap.

In J2SE Version 5.0, HPROF has been implemented on the new Java Virtual Machine Tool Interface (JVM TI).
HPROF Startup

HPROF is capable of presenting CPU usage, heap allocation statistics, and monitor contention profiles. In addition, it can also report complete heap dumps and states of all the monitors and threads in the Java virtual machine. HPROF can be invoked by:

java -agentlib:hprof[=options] ToBeProfiledClass

-OR-

java -Xrunhprof[:options] ToBeProfiledClass

Depending on the type of profiling requested, HPROF instructs the virtual machine to send it the relevant JVM TI events and processes the event data into profiling information. For example, the following command obtains the heap allocation profile:

java -agentlib:hprof=heap=sites ToBeProfiledClass

Following is the complete list of options that can be passed to HPROF:

java -agentlib:hprof=help

     HPROF: Heap and CPU Profiling Agent (JVM TI Demonstration Code)

hprof usage: java -agentlib:hprof=[help]|[<option>=<value>, ...]

Option Name and Value  Description                    Default
---------------------  -----------                    -------
heap=dump|sites|all    heap profiling                 all
cpu=samples|times|old  CPU usage                      off
monitor=y|n            monitor contention             n
format=a|b             text(txt) or binary output     a
file=<file>            write data to file             java.hprof[.txt]
net=<host>:<port>      send data over a socket        off
depth=<size>           stack trace depth              4
interval=<ms>          sample interval in ms          10
cutoff=<value>         output cutoff point            0.0001
lineno=y|n             line number in traces?         y
thread=y|n             thread in traces?              n
doe=y|n                dump on exit?                  y
msa=y|n                Solaris micro state accounting n
force=y|n              force output to <file>         y
verbose=y|n            print messages about dumps     y

Obsolete Options
----------------
gc_okay=y|n

Examples
--------
  - Get sample cpu information every 20 millisec, with a stack depth of 3:
      java -agentlib:hprof=cpu=samples,interval=20,depth=3 classname
  - Get heap usage information based on the allocation sites:
      java -agentlib:hprof=heap=sites classname

Notes
-----
  - The option format=b cannot be used with monitor=y.
  - The option format=b cannot be used with cpu=old|times.
  - Use of the -Xrunhprof interface can still be used, e.g.
       java -Xrunhprof:[help]|[<option>=<value>, ...]
    will behave exactly the same as:
       java -agentlib:hprof=[help]|[<option>=<value>, ...]

Warnings
--------
  - This is demonstration code for the JVM TI interface and use of BCI,
    it is not an official product or formal part of the J2SE.
  - The -Xrunhprof interface will be removed in a future release.
  - The option format=b is considered experimental, this format may change
    in a future release.

By default, heap profiling information (sites and dump) is written out to java.hprof.txt (ascii). The output in most cases will contain ID's for traces, threads,and objects. Each type of ID will typically start with a different number than the other ID's. For example, traces might start with 300000.

Normally the default (force=y) will clobber any existing information in the output file, so if you have multiple VMs running with HPROF enabled, you should use force=n, which will append additional characters to the output filename as needed.

The interval option only applies to cpu=samples and controls the time that the sampling thread sleeps between samples of the thread stacks.

The msa option only applies to Solaris and causes the Solaris Micro State Accounting to be used.

The interval, msa, and force options are new HPROF options in J2SE 1.5.
Example Usage

We could create an application, but let's instead pick on an existing Java application in the J2SE, javac. With javac I need to pass Java options in with -J. If you were running Java directly, you would not use the -J prefix.

There is also a way to pass in J2SE 5.0 Java options with an environment variable JAVA_TOOL_OPTIONS, but with all environment variables you need to be careful that you don't impact more VMs than you intend.
Heap Allocation Profiles (heap=sites)

Following is the heap allocation profile generated by running the Java compiler (javac) on a set of input files. Only parts of the profiler output file (java.hprof.txt) are shown here.

Command used: javac -J-agentlib:hprof=heap=sites Hello.java

SITES BEGIN (ordered by live bytes) Fri Oct 22 11:52:24 2004
          percent          live          alloc'ed  stack class
 rank   self  accum     bytes objs     bytes  objs trace name
    1 44.73% 44.73%   1161280 14516  1161280 14516 302032 java.util.zip.ZipEntry
    2  8.95% 53.67%    232256 14516   232256 14516 302033 com.sun.tools.javac.util.List
    3  5.06% 58.74%    131504    2    131504     2 301029 com.sun.tools.javac.util.Name[]
    4  5.05% 63.79%    131088    1    131088     1 301030 byte[]
    5  5.05% 68.84%    131072    1    131072     1 301710 byte[]

A crucial piece of information in heap profile is the amount of allocation that occurs in various parts of the program. The SITES record above tells us that 44.73% of the total space was allocated for java.util.zip.ZipEntry objects at a particular SITE (a unique stack trace of a fixed depth). Note that the amount of live data matches the total allocated numbers, which means that a garbage collection may have happened shortly before HPROF iterated over the heap. Normally the amount of live data will be less than or equal to the total allocation that has occurred at a given site.

A good way to relate allocation sites to the source code is to record the dynamic stack traces that led to the heap allocation. Following is another part of the java.hprof.txt file that illustrates the stack traces referred to by the top two allocation sites in the output shown above.

TRACE 302032:
        java.util.zip.ZipEntry.<init>(ZipEntry.java:101)
        java.util.zip.ZipFile$3.nextElement(ZipFile.java:435)
        java.util.zip.ZipFile$3.nextElement(ZipFile.java:413)
        com.sun.tools.javac.jvm.ClassReader.openArchive(ClassReader.java:1442)
TRACE 302033:
        com.sun.tools.javac.util.List.<init>(List.java:43)
        com.sun.tools.javac.util.List.<init>(List.java:51)
        com.sun.tools.javac.util.ListBuffer.append(ListBuffer.java:98)
        com.sun.tools.javac.jvm.ClassReader.openArchive(ClassReader.java:1442)

Each frame in the stack trace contains class name, method name, source file name, and the line number. The user can set the maximum number of frames collected by the HPROF agent (depth option). The default depth is 4. Stack traces reveal not only which methods performed heap allocation, but also which methods were ultimately responsible for making calls that resulted in memory allocation.

The above stack traces are shared with all the running threads, if it was necessary to separate the stack traces of different threads the thread option would need to be used. This will increase the space used and the number of stack traces in the output file.

If the depth of the stacks wasn't deep enough, the depth option could be used to increase the stack depth. Currently, recursion is not treated specially, so getting the caller information on very deep recursion stacks can be difficult. The larger the depth, the more space is needed to save the stack traces.

So what does the above information tell us? For javac, having lots of ZipEntry and List class instances makes sense, so other than the fact that javac relies heavily on these classes there isn't much else to say. Normally you want to watch out for large accumulations of objects, allocated at the same location, that seem excessive.

Don't expect the above information to reproduce on identical runs with applications that are highly multi-threaded.

This option can impact the application performance due to the data gathering (stack traces) on object allocation and garbage collection.
Heap Dump (heap=dump)

A complete dump of the current live objects in the heap can be obtained with:

Command used: javac -J-agentlib:hprof=heap=dump Hello.java

This is a very large output file, but can be viewed and searched in any editor. But a better way to look at this kind of detail is with HAT. All the information of the above heap=sites option is included, plus the specific details on every object allocated and the references to all objects.

This option causes the greatest amount of memory to be used because it stores details on every object allocated, it can also impact the application performance due to the data gathering (stack traces) on object allocation and garbage collection.
CPU Usage Sampling Profiles (cpu=samples)
h2>

HPROF can collect CPU usage information by sampling threads. Following is part of the output collected from a run of the javac compiler.

Command used: javac -J-agentlib:hprof=cpu=samples Hello.java

CPU SAMPLES BEGIN (total = 126) Fri Oct 22 12:12:14 2004
rank   self  accum   count trace method
   1 53.17% 53.17%      67 300027 java.util.zip.ZipFile.getEntry
   2 17.46% 70.63%      22 300135 java.util.zip.ZipFile.getNextEntry
   3  5.56% 76.19%       7 300111 java.lang.ClassLoader.defineClass2
   4  3.97% 80.16%       5 300140 java.io.UnixFileSystem.list
   5  2.38% 82.54%       3 300149 java.lang.Shutdown.halt0
   6  1.59% 84.13%       2 300136 java.util.zip.ZipEntry.initFields
   7  1.59% 85.71%       2 300138 java.lang.String.substring
   8  1.59% 87.30%       2 300026 java.util.zip.ZipFile.open
   9  0.79% 88.10%       1 300118 com.sun.tools.javac.code.Type$ErrorType.<init>
  10  0.79% 88.89%       1 300134 java.util.zip.ZipFile.ensureOpen

The HPROF agent periodically samples the stack of all running threads to record the most frequently active stack traces. The count field above indicates how many times a particular stack trace was found to be active (not how many times a method was called). These stack traces correspond to the CPU usage hot spots in the application. This option does not require BCI or modifications of the classes loaded and of all the options causes the least disturbance of the application being profiled.

The interval option can be used to adjust the sampling time or the time that the sampling thread sleeps between samples.

So what does the above information tell us? First, statistically it's a pretty poor sample, only 126 samples, compiling a larger Java source file would probably yield better information, or better yet a large batch of Java sources. Second, this data pretty much matches the heap=sites data in that we know that javac relies heavily on the ZipFile class, which makes sense. It appears that any performance improvements in ZipFile will probably improve the performance of javac. The stack traces of interest here are:

TRACE 300027:

java.util.zip.ZipFile.getEntry(ZipFile.java:Unknown line)

java.util.zip.ZipFile.getEntry(ZipFile.java:253)

java.util.jar.JarFile.getEntry(JarFile.java:197)

java.util.jar.JarFile.getJarEntry(JarFile.java:180)

TRACE 300135:

java.util.zip.ZipFile.getNextEntry(ZipFile.java:Unknown line)

java.util.zip.ZipFile.access$700(ZipFile.java:35)

java.util.zip.ZipFile$3.nextElement(ZipFile.java:419)

java.util.zip.ZipFile$3.nextElement(ZipFile.java:413)

Don't expect the above information to reproduce on identical runs with highly multi-threaded applications, especially when the sample count is low.
CPU Usage Times Profile (cpu=times)

HPROF can collect CPU usage information by injecting code into every method entry and exit, keeping track of exact method call counts and the time spent in each method. This uses Byte Code Injection (BCI) and runs considerably slower than cpu=samples. Following is part of the output collected from a run of the javac compiler.

Command used: javac -J-agentlib:hprof=cpu=times Hello.java

CPU TIME (ms) BEGIN (total = 103099259) Fri Oct 22 12:21:23 2004
rank   self  accum   count trace method
   1  5.28%  5.28%       1 308128 com.sun.tools.javac.Main.compile
   2  5.16% 10.43%       1 308127 com.sun.tools.javac.main.Main.compile
   3  5.15% 15.58%       1 308126 com.sun.tools.javac.main.Main.compile
   4  4.07% 19.66%       1 308060 com.sun.tools.javac.main.JavaCompiler.compile
   5  3.90% 23.56%       1 306652 com.sun.tools.javac.comp.Enter.main
   6  3.90% 27.46%       1 306651 com.sun.tools.javac.comp.Enter.complete
   7  3.74% 31.21%       4 305626 com.sun.tools.javac.jvm.ClassReader.listAll
   8  3.74% 34.95%      18 305625 com.sun.tools.javac.jvm.ClassReader.list
   9  3.24% 38.18%       1 305831 com.sun.tools.javac.comp.Enter.classEnter
  10  3.24% 41.42%       1 305827 com.sun.tools.javac.comp.Enter.classEnter
  11  3.24% 44.65%       1 305826 com.sun.tools.javac.tree.Tree$TopLevel.accept
  12  3.24% 47.89%       1 305825 com.sun.tools.javac.comp.Enter.visitTopLevel
  13  3.23% 51.12%       1 305725 com.sun.tools.javac.code.Symbol.complete
  14  3.23% 54.34%       1 305724 com.sun.tools.javac.jvm.ClassReader.complete
  15  3.23% 57.57%       1 305722 com.sun.tools.javac.jvm.ClassReader.fillIn
  16  1.91% 59.47%      16 305611 com.sun.tools.javac.jvm.ClassReader.openArchive
  17  1.30% 60.78%     330 300051 java.lang.ClassLoader.loadClass
  18  1.28% 62.06%     330 300050 java.lang.ClassLoader.loadClass
  19  1.22% 63.28%     512 300695 java.net.URLClassLoader.findClass
  20  1.21% 64.48%     512 300693 java.net.URLClassLoader$1.run
  21  1.09% 65.57%   14516 305575 java.util.zip.ZipFile$3.nextElement
  22  0.98% 66.55%   14516 305574 java.util.zip.ZipFile$3.nextElement
  23  0.96% 67.52%       1 304560 com.sun.tools.javac.main.JavaCompiler.instance
  24  0.96% 68.48%       1 304559 com.sun.tools.javac.main.JavaCompiler.<init>
  25  0.71% 69.19%     256 302078 java.net.URLClassLoader.access$100

Here the count represents the true count of the times this method was entered, and the percentages represent a measure of thread time spent in those method.

The traces normally don't include line numbers but can be added with the lineno option.

Looking at the above data, it appears that even though some of the ZipFile$3 class methods are called over 14,000 times, they don't seem to be consuming vast amounts of CPU time. Again, this is probably represents a poor sample and I would not put much time into analyzing the above information.
Using HAT with HPROF

HAT (Heap Analysis Tool) is a browser based tool that uses the HPROF binary format to construct web pages so you can browse all the objects in the heap, and see all the references to and from objects.
A Bit of HPROF History

Previous releases of J2SE (1.2 through 1.4) contained an HPROF agent built on the experimental JVMPI. JVMPI worked fairly reliably in the old 1.2 Classic VM, but it was unreliable and difficult to maintain with the newer HotSpot VM and the different garbage collectors in the 1.3 and 1.4 releases. The newer JVM TI in J2SE 5.0 replaces both JVMDI and JVMPI. In the 5.0 release, JVMPI is still available, and the older HPROF JVMPI based agent from 1.4.2 can actually be used with 5.0, but it isn't recommended. The new HPROF, in J2SE 5.0, is (with minor exceptions) a fully functional replacement for the old HPROF but source wise was close to being a complete rewrite. All the old options are available, and the output format is basically the same. So if you are used to the old HPROF output, or you have tools that read any HPROF format, you won't see many differences and hopefully you will see fewer problems that you have seen in the past with the JVMPI-based HPROF. The source to HPROF is available with the full JDK download in the demo/jvmti/hprof installation directory.
How Does HPROF Work?

HPROF is a dynamically-linked native library that uses JVM TI and writes out profiling information either to a file descriptor or to a socket in ascii or binary format. This information can be further processed by a profiler front-end tool or dumped to a file. It generates this information through calls to JVM TI, event callbacks from JVM TI, and through Byte Code Insertion (BCI) on all class file images loaded into the VM. JVM TI has an event called JVMTI_EVENT_CLASS_FILE_LOAD_HOOK which gives HPROF access to the class file image and an opportunity to modify that class file image before the VM actually loads it. Sound scary? It is, don't take BCI lightly. In the case of HPROF the BCI operations only instrument and don't change the behavior of the bytecodes. Use of JVM TI was pretty critical here for HPROF to do BCI because we needed to do BCI on ALL the classes, including early classes like java.lang.Object. Of course, the instrumentation code needs to be made inoperable until the VM has reached a stage where this inserted code can be executed, normally the event JVMTI_EVENT_VM_INIT.

The amount of BCI that HPROF does depends on the options supplied, cpu=times triggers insertions into all method entries and exits, and the heap options trigger BCI on the <init> method of java.lang.object and any 'newarray' opcodes seen in any method. This BCI work is actually done through the shared library java_crw_demo, which accepts a set of options, a class file image, and returns a new class file image. The java_crw_demo library is part of the sources delivered with the J2SE 5.0 in the demo/jvmti directory.

Currently HPROF injects calls to static Java methods which in turn call a native method that is in the HPROF agent library itself. This was an early design choice to limit the extra Java code introduced during profiling. So the combination of the requested JVM TI events, and the created BCI events, provides the basics for HPROF to work.

The cpu=samples option doesn't use BCI, HPROF just spawns a separate thread that sleeps for a fixed number of micro seconds, and wakes up and samples all the running thread stacks using JVM TI.

The cpu=times option attempts to track the running stack of all threads, and keep accurate CPU time usage on all methods. This option probably places the greatest strain on the VM, where every method entry and method exit is tracked. Applications that make many method calls will be impacted more than others.

The heap=sites and heap=dump options are the ones that need to track object allocations. These options can be memory intensive (less so with hprof=sites) and applications that allocate many objects or allocate and free many objects will be impacted more with these options. On each object allocation, the stack must be sampled so we know where the object was allocated, and that stack information must be saved. HPROF has a series of tables allocated in the C or malloc() heap that track all it's information. HPROF currently does not allocate any Java objects.
Summary

As you can see, the HPROF agent can be used to generate a wide variety of profiles. But as the above examples using javac demonstrate, make sure you have a large enough sampling to know that your data makes sense.

Brave C/JNI programmers could even take the source to HPROF (it's available in the J2SE SDK download in the demo/jvmti/hprof directory) and customize it or create their own special profiling tool.
```
