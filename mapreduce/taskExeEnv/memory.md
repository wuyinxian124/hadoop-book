用户和管理者能够通过mapreduce.{map|reduce}.memory.mb 参数指定子任务和子进程的最大虚拟内存。注意这个值是对进程的限制。mapreduce.{map|reduce}.memory.mb参数单位是MB。这个值也必须大于或者等于父进程带入的 -xmx 值，否则子进程起不来。


Note: mapreduce.{map|reduce}.java.opts are used only for configuring the launched child tasks from MRAppMaster. Configuring the memory options for daemons is documented in Configuring the Environment of the Hadoop Daemons.

The memory available to some parts of the framework is also configurable. In map and reduce tasks, performance may be influenced by adjusting parameters influencing the concurrency of operations and the frequency with which data will hit disk. Monitoring the filesystem counters for a job- particularly relative to byte counts from the map and into the reduce- is invaluable to the tuning of these parameters.
