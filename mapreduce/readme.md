### mapreduce 编程

hadoop mr是一个能在大规模集群中通过可靠和容错方式处理和计算极大规模的数据。  
Hadoop MapReduce is a software framework for easily writing applications which process vast amounts of data (multi-terabyte data-sets) in-parallel on large clusters (thousands of nodes) of commodity hardware in a reliable, fault-tolerant manner.

job 的task 任务通过完全并行的方式将输入的数据集切分成完全独立的块。然后将map 的输出排序输入到reducer任务中。通常map的输出和reduce的输入都是在同一个文件系统（或者说尽可能会在同一个节点）。mr的框架处理的是任务调度，监控以及重跑失败任务。  

A MapReduce job usually splits the input data-set into independent chunks which are processed by the map tasks in a completely parallel manner. The framework sorts the outputs of the maps, which are then input to the reduce tasks. Typically both the input and the output of the job are stored in a file-system. The framework takes care of scheduling tasks, monitoring them and re-executes the failed tasks.

通常计算和存储在同一个节点，也就是说mr和hdfs会运行在同一个节点。这个配置使得任务调度非常高效（因为数据就在本地），节约了非常宝贵的宽带。

Typically the compute nodes and the storage nodes are the same, that is, the MapReduce framework and the Hadoop Distributed File System (see HDFS Architecture Guide) are running on the same set of nodes. This configuration allows the framework to effectively schedule tasks on the nodes where data is already present, resulting in very high aggregate bandwidth across the cluster.

一个mr最小单位的mr 集群包括：一个resourcemanager节点，一个node manager 节点和一个mrapp master.  

The MapReduce framework consists of a single master ResourceManager, one slave NodeManager per cluster-node, and MRAppMaster per application (see YARN Architecture Guide).

一个最简单的mr 程序包括：输入和输出路径，通过继承抽象类或实现接口具体化的map或reduce，以及其他的一些job 的参数和配置。  

Minimally, applications specify the input/output locations and supply map and reduce functions via implementations of appropriate interfaces and/or abstract-classes. These, and other job parameters, comprise the job configuration.

hadoop job client 提交job(jar)和配置给resourceManager 。resourceManager承担了将jar和配置文件下发到slave节点，并调度，监控任务，同时提供状态和诊断信息给job client .  

The Hadoop job client then submits the job (jar/executable etc.) and configuration to the ResourceManager which then assumes the responsibility of distributing the software/configuration to the slaves, scheduling tasks and monitoring them, providing status and diagnostic information to the job-client.
