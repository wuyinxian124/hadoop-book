### wordcount demo

前言：
这个例子只是让我了解一个mr 任务是怎么运行的。
这个例子实现的功能就是统计输入数据中各个单词的个数。

```java
import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

  public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      }
    }
  }

  public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
    }
  }

  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    Job job = Job.getInstance(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}
```

#### 打包
```
$ bin/hadoop com.sun.tools.javac.Main WordCount.java
$ jar cf wc.jar WordCount*.class
```
#### 执行
```
bin/hadoop jar wc.jar WordCount /user/joe/wordcount/input /user/joe/wordcount/output
```

##### 代码说明
1. map TokenizerMapper  
实现Mapper  方法，重写了map方法。
在该方法中默认提供的TextInputFormat一次处理一条记录，通过StringTokenizer将记录通过空格切分，然后emit 一个格式如k-v 的记录（ < <word>, 1>） 。  

2. 聚合  
 这个例子中指定了一个聚合类combiner，因此map的输出会直接给本地的聚合类，并将结果按照key 进行排序。聚合类combiner类似reduce。  
 WordCount also specifies a combiner. Hence, the output of each map is passed through the local combiner (which is same as the Reducer as per the job configuration) for local aggregation, after being sorted on the *key*s.  
 我们假设 map的输出是
 ```
 < Hello, 1>
< World, 1>
< Bye, 1>
< World, 1>
 ```
 那么被聚合函数处理之后，得到的结果就是
 ```
 < Bye, 1>
< Hello, 1>
< World, 2>`
 ```  
3. reduce IntSumReducer  
 实现 Reducer 接口，重写了  reduce 方法，实现的功能就是对每一个key 做sum.  

4. main 方法  
  main 方法中指定了一个job 需要的各种参数和配置，比如输入输出路径，输入和输出 format类，最后调用 waitForCompletion 方法等待mr 完成。  
  The main method specifies various facets of the job, such as the input/output paths (passed via the command line), key/value types, input/output formats etc., in the Job. It then calls the job.waitForCompletion to submit the job and monitor its progress.
