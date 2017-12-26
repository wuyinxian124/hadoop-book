[**英文原文**](https://hadoop.apache.org/docs/r2.7.2/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html#Reducer)

### Reducer
reducer 将原来同一个key的比较小value集合，聚合为一个更大的集合。    

可以通过Job.setNumReduceTasks(int).方法设置reducer 个数。  

总的来说：我们通过job.setReducerClass 方法来将reducer 现实提交一个job。mr 框架依赖每一个键值对去触发reuduce 方法。你还可以通过重写cleanup方法指定做一些清理工作。

reducer 包括三个阶段：shuffle，sort,reduce。  

#### shuffle
reducer获取的输入都是排序好的map 输出，mr框架通过http接口获取所有map输出中预制相关的那部分分块。    

#### Sort
这个阶段，框架将按照key的值对Reducer的输入进行分组 （因为不同mapper的输出中可能会有相同的key）。

Shuffle和Sort两个阶段是同时进行的；map的输出也是一边被取回一边被合并的。

#### Second sort
如果需要中间过程对key的分组规则和reduce前对key的分组规则不同，那么可以通过 JobConf.setSortComparatorClass(Class)来指定一个Comparator。再加上 JobConf.setGroupingComparatorClass(Class)可用于控制中间过程的key如何被分组，所以结合两者可以实现按值的二次排序。  

### Reduce
这个阶段，已经分组输入数据的每一个键值对都会触发reducer的 reduce(WritableComparable, Iterable<Writable>, Context)  方法。  

reducer 可以通过调用 Context.write(WritableComparable, Writable)方法将输出数据写入hdfs.  

程序可以通过Counter 对象上报统计数据。  

reducer 输出数据是没有排序的。  

### 多少reducer合适
最合适的reducer个数应该是 0.95-1.75 乘以节点个数 再乘以节点最大容器个数。  
The right number of reduces seems to be 0.95 or 1.75 multiplied by (<no. of nodes> * <no. of maximum containers per node>).

用0.95是因为，所有reduce可以在maps一完成时就立刻启动，开始传输map的输出结果。用1.75是因为，速度快的节点可以在完成第一轮reduce任务后，可以开始第二轮，这样可以得到比较好的负载均衡的效果。

增加reduce的数目会增加整个框架的开销，但可以改善负载均衡，降低由于执行失败带来的负面影响

上述比例因子比整体数目稍小一些是为了给框架中的推测性任务（speculative-tasks） 或失败的任务预留一些reduce的资源。

### 注意事项
如果没有归约要做，reducer数量可以设置为0.  

这种情况下，map任务的输出会直接写入由 FileOutputFormat.setOutputPath(Job, Path)指定的输出路径。框架在把它们写入FileSystem之前不会对结果进行排序。
