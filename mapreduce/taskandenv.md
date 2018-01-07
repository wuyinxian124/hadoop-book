MRAppMaster 将在一个独立的jvm上以子进程的方式执行 mapper或者reducer任务。  

任务进程继承了父进程(MRAppMaster)环境变量。

The child-task inherits the environment of the parent MRAppMaster. The user can specify additional options to the child-jvm via the mapreduce.{map|reduce}.java.opts and configuration parameter in the Job such as non-standard paths for the run-time linker to search shared libraries via -Djava.library.path=<> etc. If the mapreduce.{map|reduce}.java.opts parameters contains the symbol @taskid@ it is interpolated with value of taskid of the MapReduce task.

Here is an example with multiple arguments and substitutions, showing jvm GC logging, and start of a passwordless JVM JMX agent so that it can connect with jconsole and the likes to watch child memory, threads and get thread dumps. It also sets the maximum heap-size of the map and reduce child jvm to 512MB & 1024MB respectively. It also adds an additional path to the java.library.path of the child-jvm.
