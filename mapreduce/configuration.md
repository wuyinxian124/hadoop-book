job 表示一个mr 任务配置信息。  

对用户来说job是一个最主要的接口用来描述任务一个mr 任务。mr 框架就会忠实的执行job 所描述的任务，但是：
 * 有一些参数是final类型的，不能被修改。
 * 有一些参数是直接指定的(比如：Job.setNumReduceTasks(int)),还有一些参数比较复杂，会跟框架和job 配置相互影响（比如：Configuration.set(JobContext.NUM_MAPS, int)).）。

job 通常用来指定mapper，combiner,partitioner,reducer,InputFormat,OutputFormat的实现等。fileinputFormat 可以很多方法来指定input 文件，例如：
```
(FileInputFormat.setInputPaths(Job, Path…)
FileInputFormat.addInputPath(Job, Path))
(FileInputFormat.setInputPaths(Job, String…)
FileInputFormat.addInputPaths(Job, String))
```
但是指定输出路径，只能通过FileOutputFormat.setOutputPath(Path) 方法。  

job 也可以指定一些可选参数，比如是否使用comparator,文件是否放到DistributedCache，结果在传输中是否压缩，是否允许预防性（speculative）任务的执行 ，每个任务的最大尝试次数等等。  

当然你也可以使用Configuration的set和get 方法获取程序中任何参数。但是，DistributedCache的使用是面向大规模只读数据的。
