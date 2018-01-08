MRAppMaster 将在一个独立的jvm上以子进程的方式执行 mapper或者reducer任务。  

任务进程继承了父进程(MRAppMaster)环境变量。

任务子进程会继承MRAppMaster父进程的环境变量。用户可以通过job 提供的可选参数mapreduce.{map|reduce}.java.opts（或者configuration 参数）来指定子jvm进程环境变量。比如说通过-Djava.library.path=<> 来将一个非标准路径设为运行时的链接用以搜索共享库.如果mapreduce.{map|reduce}.java.opts 参数中包含@taskid@，那么其将会被MapReduce 任务的任务id替换。

下面是一个多个参数和替换的例子，其中有设置jvm gc 日志的，有启动支持无密登录的jvm jmx agent 的（其可以用来连接jconsoel 可以用来监控子进程内存，线程和线程dump）,有设置map和reduce 子进程最大heap 大小分别是512M和1024M。并为子jvm的java.library.path添加了一个附加路径。


```
<property>
  <name>mapreduce.map.java.opts</name>
  <value>
  -Xmx512M -Djava.library.path=/home/mycompany/lib -verbose:gc -Xloggc:/tmp/@taskid@.gc
  -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false
  </value>
</property>

<property>
  <name>mapreduce.reduce.java.opts</name>
  <value>
  -Xmx1024M -Djava.library.path=/home/mycompany/lib -verbose:gc -Xloggc:/tmp/@taskid@.gc
  -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false
  </value>
</property>
```
