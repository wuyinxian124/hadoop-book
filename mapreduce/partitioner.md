分区是指按键值分区。  

分区控制map中间结果输出中间结果键值对的键分区。key值用来产生分区，通常是用hash分区。分区数量跟job 任务中reducer个数一样。因此也控制着哪个键值对会发送到哪个reducer。  

默认分区是hash分区。  
