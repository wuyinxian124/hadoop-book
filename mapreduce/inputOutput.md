### job的input和output

为什么要把input和output 单独拿出来说呢，因为job 的输入和输出都是k-v的格式。  
mr框架操作的是一系列键值对，所以框架中能够处理的输入是键值对，最终得到的输出结果也是键值对（或者其他可以相似的类型）。  

The MapReduce framework operates exclusively on <key, value> pairs, that is, the framework views the input to the job as a set of <key, value> pairs and produces a set of <key, value> pairs as the output of the job, conceivably of different types.

key和value类型要能够被mr 序列化，因此必须实现writeable 接口。key 类型因为要被mr 排序所以必须实现WritableComparable接口。  
The key and value classes have to be serializable by the framework and hence need to implement the Writable interface. Additionally, the key classes have to implement the WritableComparable interface to facilitate sorting by the framework.

所以如果从输入输出格式来看一个mr 任务 大概如下图所示：  
(input) <k1, v1> -> map -> <k2, v2> -> combine -> <k2, v2> -> reduce -> <k3, v3> (output)

Input and Output types of a MapReduce job:

(input) <k1, v1> -> map -> <k2, v2> -> combine -> <k2, v2> -> reduce -> <k3, v3> (output)
