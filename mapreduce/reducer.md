[**英文原文**](https://hadoop.apache.org/docs/r2.7.2/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html#Reducer)

### Reducer
reducer 将原来同一个key的value集合比较小，聚合为一个更大的集合。    

可以通过Job.setNumReduceTasks(int).方法设置reducer 个数。  

通常都是基于job.setReducerClass 方法来将reducer 对象提交一个job。mr 框架依赖每一个键值对去触发reuduce 方法。你还可以通过重写cleanup方法指定做一些清理工作。

reducer的核心包括：shuffle，sort,reduce。  


#### shuffle

Input to the Reducer is the sorted output of the mappers. In this phase the framework fetches the relevant partition of the output of all the mappers, via HTTP.

#### Sort

The framework groups Reducer inputs by keys (since different mappers may have output the same key) in this stage.

The shuffle and sort phases occur simultaneously; while map-outputs are being fetched they are merged.

Second
