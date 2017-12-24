[**该文档对应的英文原文**](https://hadoop.apache.org/docs/r2.7.2/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html#Mapper)

### Mapper接口
mapper 的工作就是将输入的键值对转给一系列的中间结果键值对。  

多个map 就是多个任务，每一个map任务都会将输入记录转成临时记录。中间结果和输入结果类型可以不一样，一个输入结果可能会转换成零个或者是多个中间结果。  

hadoop 框架会为每一个由InputFormat 对象生成的InputSplit  生成一个对应的map 任务。  

map 实现通过job.setMapperClass 方法传输到job。 mr 框架调用map 实现类重新的map 方法。mr 框架调用inputSplit 获得的每一个kv 键值对都会触发map 方法。当然你可以重新clean方法 ，指定清理相应的资源。  

输入和输出键值对不一定要同类型。一个输入键值对可以转为0或者多个输出键值对。通过调用context.write方法可以将输出键值对聚合。  

程序中也可以使用计数器统计输入和输出键值对数量。  

mr 框架会将map输出按照key 进行分组，然后发送给reduceer 已确定最后的输出结果。用户也可以通过job.setGroupingComparatorClass方法指定Comparator 类，来控制分组。

map 输出会被排序，分组传输给reducer。分组的个数和reducer个数一致。你可以定义一个分组实现，来确定哪些keys将会分配给哪个reducer.   

你也可以通过job.setCombinerClass方法指定一个combiner，可以将map 的输出结果现在本地聚合，这样就可以减少mapper到reducer的数据量。  

中间结果和输出通常都是键值对的形式，但是你也可以通过配置文件指定解压类对中间结果和最终结果进行解压缩。   

### 多少map 合适

map的个数通常是由输入数据量决定的，也就是输入分区的块数。  

通常在每一个节点合适的map并行数在10 到100之间。如果cpu 性能比较好，也可以设置为300.任务的设置也需要一些时间，所以最好让map执行超过一分钟。      

综上所述 如果你有10TB的数据量(每块大小128MB)，你最好设置82000个map,除非程序中 Configuration.set(MRJobConfig.NUM_MAPS, int)指定了一个更高的值。  
