# MapReduce

## 概述

目标文件

hello.txt

```text
atguigu atguigu
ss ss
cls cls
jiao
banzhang
xue
hadoop
```

输出结果

```text
atguigu 2
ss 			2
cls 		2	
jiao		1
banzhang1
xue			1
hadoop	1
```

- Mapper
  - 将MapTask传给我们的文本内容先转成String
  - 根据空格将这一行切分成单词
  - map阶段将单词输出为<单词，1>   是负责分，key单词，value单词出现的次数
- Reducer
  - 汇总各个key的次数
  - 输出该key的总次数
- Driver
  - 获取配置信息，获取job对象实例
  - 指定本程序的jar包所在的本地路径
  - 关联Mapper和Reducer业务类
  - 指定Mapper输出的数据是kv类型
  - 指定最终输出的数据是kv类型
  - 指定job输入原始文件所在目录(hadoop必须有输入路径和输出路径，而且输出路径必须不能存在)
  - 指定job的输出结果所在目录
  - 提交作业

## 创建工程

### 1、创建maven工程

pom

```xml
<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.apache.logging.log4j</groupId>
			<artifactId>log4j-core</artifactId>
			<version>2.8.2</version>
		</dependency>
		<dependency>
			<groupId>org.apache.hadoop</groupId>
			<artifactId>hadoop-common</artifactId>
			<version>2.7.2</version>
		</dependency>
		<dependency>
			<groupId>org.apache.hadoop</groupId>
			<artifactId>hadoop-client</artifactId>
			<version>2.7.2</version>
		</dependency>
		<dependency>
			<groupId>org.apache.hadoop</groupId>
			<artifactId>hadoop-hdfs</artifactId>
			<version>2.7.2</version>
		</dependency>
</dependencies>
```

log4j.properties

```properties
log4j.rootLogger=INFO, stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m%n
log4j.appender.logfile=org.apache.log4j.FileAppender
log4j.appender.logfile.File=target/spring.log
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=%d %p [%c] - %m%n
```

### 2、第一个map阶段

##### WordcountMapper 

```java
package com.aishang.mr.wordcount;

import java.io.IOException;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

/**
 * KEYIN 输入数据的key VALUEIN 输入数据的value KEYOUT 输出数据的类型 <ss,1> ss是字符串 VALUEOUT
 * 输出数据的类型 1 是long类型
 * 
 * @author liujiang
 *
 */
public class WordcountMapper extends Mapper<LongWritable, Text, Text, IntWritable> {
	Text k = new Text();
	IntWritable v = new IntWritable(1);
	
	@Override
	protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
		// 1 获取一行
		String line = value.toString();

		// 2 切割
		String[] words = line.split(" ");

		// 3 输出
		for (String word : words) {

			k.set(word);
			context.write(k, v);
		}
	}
}

```

##### WordcountReducer 

```java
package com.aishang.mr.wordcount;

import java.io.IOException;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;
//keyin valuein map阶段的输出的kv 的类型
public class WordcountReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
	int sum;
	IntWritable v = new IntWritable();

	@Override
	protected void reduce(Text key, Iterable<IntWritable> , Context context)
			throws IOException, InterruptedException {

		// 1 累加求和
		sum = 0;
		for (IntWritable count : values) {
			sum += count.get();
		}

		// 2 输出  atguigu 2
		v.set(sum);
		context.write(key, v);
	}
}

```

##### WordcountDriver

```java
package com.aishang.mr.wordcount;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordcountDriver{
	public static void main(String[] args) throws IllegalArgumentException, IOException, ClassNotFoundException, InterruptedException {
		// 1 获取配置信息以及封装任务
				Configuration configuration = new Configuration();
				Job job = Job.getInstance(configuration);

				// 2 设置jar加载路径
				job.setJarByClass(WordcountDriver.class);

				// 3 设置map和reduce类
				job.setMapperClass(WordcountMapper.class);
				job.setReducerClass(WordcountReducer.class);

				// 4 设置map输出
				job.setMapOutputKeyClass(Text.class);
				job.setMapOutputValueClass(IntWritable.class);

				// 5 设置最终输出kv类型
				job.setOutputKeyClass(Text.class);
				job.setOutputValueClass(IntWritable.class);
				
				// 6 设置输入和输出路径
				FileInputFormat.setInputPaths(job, new Path(args[0]));
				FileOutputFormat.setOutputPath(job, new Path(args[1]));

				// 7 提交
				boolean result = job.waitForCompletion(true);

				System.exit(result ? 0 : 1);
	}

}

```

测试运行

![Run_Configurations_和_eclipse-workspace_-_mr0527_src_main_java_com_aishang_mr_wordcount_WordcountDriver_java_-_Eclipse_IDE](/Users/liujiang/Documents/Typora/imgs/Run_Configurations_和_eclipse-workspace_-_mr0527_src_main_java_com_aishang_mr_wordcount_WordcountDriver_java_-_Eclipse_IDE.png)

对应代码的args[0]，args[1]

此时我的文件在桌面hello.txt，输出到桌面的demo文件夹，注意该文件夹不能存在

```sh
/Users/liujiang/Desktop/hello.txt /Users/liujiang/Desktop/demo
```

![eclipse-workspace_-_mr0527_src_main_java_com_aishang_mr_wordcount_WordcountDriver_java_-_Eclipse_IDE](http://ww3.sinaimg.cn/large/006tNc79ly1g3gvh4u0v7j30zk0akjsl.jpg)

访问demo中的part-r-00000文件

```sh
atguigu	2
banzhang	1
cls	2
hadoop	1
jiao	1
ss	2
xue	1

```

### 3、集群中运行

- 打包项目

  pom，添加

  ```xml
  <build>
  		<plugins>
  			<plugin>
  				<artifactId>maven-compiler-plugin</artifactId>
  				<version>2.3.2</version>
  				<configuration>
  					<source>1.8</source>
  					<target>1.8</target>
  				</configuration>
  			</plugin>
  			<plugin>
  				<artifactId>maven-assembly-plugin </artifactId>
  				<configuration>
  					<descriptorRefs>
  						<descriptorRef>jar-with-dependencies</descriptorRef>
  					</descriptorRefs>
  					<archive>
  						<manifest>
  							<mainClass>com.aishang.mr.WordcountDriver</mainClass>
                <!--需要替换为自己工程主类-->
  						</manifest>
  					</archive>
  				</configuration>
  				<executions>
  					<execution>
  						<id>make-assembly</id>
  						<phase>package</phase>
  						<goals>
  							<goal>single</goal>
  						</goals>
  					</execution>
  				</executions>
  			</plugin>
  		</plugins>
  	</build>
  ```

  如果工程上显示红叉。在项目上右键->maven->update project即可

- 右键项目run as  -> maven install

  ![eclipse-workspace_-_mr0527_pom_xml_-_Eclipse_IDE](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3gz2nol9hj30gc0gamzh.jpg)

- mr0527-0.0.1-SNAPSHOT.jar复制到桌面，改名wc.jar上传Hadoop102中

  

  ```sh
  cd /opt/module/hadoop-2.7.2
  上传文件
  
  执行WordCount程序
  
  hadoop jar wc.jar com.aishang.mr.wordcount.WordcountDriver /ab.txt /mr
  
  
  
  
  
  结果
  19/05/28 13:56:41 INFO client.RMProxy: Connecting to ResourceManager at hadoop103/192.168.83.103:8032
  19/05/28 13:56:42 WARN mapreduce.JobResourceUploader: Hadoop command-line option parsing not performed. Implement the Tool interface and execute your application with ToolRunner to remedy this.
  19/05/28 13:56:43 INFO input.FileInputFormat: Total input paths to process : 1
  19/05/28 13:56:43 INFO mapreduce.JobSubmitter: number of splits:1
  19/05/28 13:56:43 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1558579191206_0001
  19/05/28 13:56:44 INFO impl.YarnClientImpl: Submitted application application_1558579191206_0001
  19/05/28 13:56:44 INFO mapreduce.Job: The url to track the job: http://hadoop103:8088/proxy/application_1558579191206_0001/
  19/05/28 13:56:44 INFO mapreduce.Job: Running job: job_1558579191206_0001
  19/05/28 13:56:56 INFO mapreduce.Job: Job job_1558579191206_0001 running in uber mode : false
  19/05/28 13:56:56 INFO mapreduce.Job:  map 0% reduce 0%
  19/05/28 13:57:05 INFO mapreduce.Job:  map 100% reduce 0%
  19/05/28 13:57:12 INFO mapreduce.Job:  map 100% reduce 100%
  19/05/28 13:57:13 INFO mapreduce.Job: Job job_1558579191206_0001 completed successfully
  19/05/28 13:57:13 INFO mapreduce.Job: Counters: 49
          File System Counters
  
  
  如果是多个文件处理
  hadoop jar wc.jar com.aishang.mr.wordcount.WordcountDriver /411 /mr
  
  输入路径接到文件夹即可
  ```

  运行jar后面是跟 全限定类名+集群输入路径+输出路径

  查看

  ![Browsing_HDFS](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3gzr78w4sj31wh0u0wjm.jpg)

## hadoop序列化

### 什么是序列化

- 序列化就是把内存中的对象，转换成字节序列（或其他数据传输协议）以便于存储到磁盘（持久化）和网络传输。
- 反序列化就是将收到字节序列（或其他数据传输协议）或者是磁盘的持久化数据，转换成内存中的对象。

### 为什么要序列化

- 一般来说，“活的”对象只生存在内存里，关机断电就没有了。而且“活的”对象只能由本地的进程使用，不能被发送到网络上的另外一台计算机。 然而序列化可以存储“活的”对象，可以将“活的”对象发送到远程计算机。

### 为什么不用Java的序列化

- Java的序列化是一个重量级序列化框架（Serializable），一个对象被序列化后，会附带很多额外的信息（各种校验信息，Header，继承体系等），不便于在网络中高效传输。所以，Hadoop自己开发了一套序列化机制（Writable）。

### Hadoop序列化特点：

- （1）紧凑 ：高效使用存储空间。
- （2）快速：读写数据的额外开销小。
- （3）可扩展：随着通信协议的升级而可升级
- （4）互操作：支持多语言的交互

### 自定义bean对象实现序列化接口（Writable）

- 必须实现Writable接口
- 反序列化时，需要反射调用空参构造函数，所以必须有空参构造
- 重写序列化方法
- 重写反序列化方法
- 注意反序列化的顺序和序列化的顺序完全一致
- 要想把结果显示在文件中，需要重写toString()，可用”\t”分开，方便后续用。
- 如果需要将自定义的bean放在key中传输，则还需要实现Comparable接口，因为MapReduce框中的Shuffle过程要求对key必须能排序。详见后面排序案例。

```sh

在企业开发中往往常用的基本序列化类型不能满足所有需求，比如在Hadoop框架内部传递一个bean对象，那么该对象就需要实现序列化接口。
具体实现bean对象序列化步骤如下7步。
（1）必须实现Writable接口
（2）反序列化时，需要反射调用空参构造函数，所以必须有空参构造
public FlowBean() {
	super();
}
（3）重写序列化方法
@Override
public void write(DataOutput out) throws IOException {
	out.writeLong(upFlow);
	out.writeLong(downFlow);
	out.writeLong(sumFlow);
}
（4）重写反序列化方法
@Override
public void readFields(DataInput in) throws IOException {
	upFlow = in.readLong();
	downFlow = in.readLong();
	sumFlow = in.readLong();
}
（5）注意反序列化的顺序和序列化的顺序完全一致
（6）要想把结果显示在文件中，需要重写toString()，可用”\t”分开，方便后续用。
（7）如果需要将自定义的bean放在key中传输，则还需要实现Comparable接口，因为MapReduce框中的Shuffle过程要求对key必须能排序。详见后面排序案例。
@Override
public int compareTo(FlowBean o) {
	// 倒序排列，从大到小
	return this.sumFlow > o.getSumFlow() ? -1 : 1;
}
```

### 序列化案例实操

需求

统计每一个手机号耗费的总上行流量、下行流量、总流量

```text
1	13736230513	192.196.100.1	www.atguigu.com	2481	24681	200
2	13846544121	192.196.100.2			264	0	200
3 	13956435636	192.196.100.3			132	1512	200
4 	13966251146	192.168.100.1			240	0	404
5 	18271575951	192.168.100.2	www.atguigu.com	1527	2106	200
6 	84188413	192.168.100.3	www.atguigu.com	4116	1432	200
7 	13590439668	192.168.100.4			1116	954	200
8 	15910133277	192.168.100.5	www.hao123.com	3156	2936	200
9 	13729199489	192.168.100.6			240	0	200
10 	13630577991	192.168.100.7	www.shouhu.com	6960	690	200
11 	15043685818	192.168.100.8	www.baidu.com	3659	3538	200
12 	15959002129	192.168.100.9	www.atguigu.com	1938	180	500
13 	13560439638	192.168.100.10			918	4938	200
14 	13470253144	192.168.100.11			180	180	200
15 	13682846555	192.168.100.12	www.qq.com	1938	2910	200
16 	13992314666	192.168.100.13	www.gaga.com	3008	3720	200
17 	13509468723	192.168.100.14	www.qinghua.com	7335	110349	404
18 	18390173782	192.168.100.15	www.sogou.com	9531	2412	200
19 	13975057813	192.168.100.16	www.baidu.com	11058	48243	200
20 	13768778790	192.168.100.17			120	120	200
21 	13568436656	192.168.100.18	www.alibaba.com	2481	24681	200
22 	13568436656	192.168.100.19			1116	954	200
```

#### FlowBean

```java
package com.aishang.mr.flowsum;

import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;

import org.apache.hadoop.io.Writable;
//1 实现writable接口
public class FlowBean implements Writable {
	private long upFlow;
	private long downFlow;
	private long sumFlow;

	FlowBean(long upFlow, long downFlow) {
		this.upFlow = upFlow;
		this.downFlow = downFlow;
		this.sumFlow = upFlow + downFlow;
	}
	//2  反序列化时，需要反射调用空参构造函数，所以必须有
	FlowBean() {

	}

	// 3 写序列化方法
	@Override
	public void write(DataOutput out) throws IOException {
		// TODO Auto-generated method stub
		out.writeLong(upFlow);
		out.writeLong(downFlow);
		out.writeLong(sumFlow);
	}

	// 4 反序列化方法
	// 5 反序列化方法读顺序必须和写序列化方法的写顺序必须一致
	@Override
	public void readFields(DataInput in) throws IOException {
		// TODO Auto-generated method stub
		this.upFlow = in.readLong();
		this.downFlow = in.readLong();
		this.sumFlow = in.readLong();
	}
	@Override
	public String toString() {
		return upFlow + "\t" + downFlow + "\t" + sumFlow;
	}
	public long getUpFlow() {
		return upFlow;
	}
	public void setUpFlow(long upFlow) {
		this.upFlow = upFlow;
	}
	public long getDownFlow() {
		return downFlow;
	}
	public void setDownFlow(long downFlow) {
		this.downFlow = downFlow;
	}
	public long getSumFlow() {
		return sumFlow;
	}
	public void setSumFlow(long sumFlow) {
		this.sumFlow = sumFlow;
	}

}

```

#### FlowCountMapper

```java
package com.aishang.mr.flowsum;

import java.io.IOException;

import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

/**
 * 1 13736230513 192.196.100.1 www.atguigu.com 2481 24681 200 KEYIN输入数据的类型key
 * 手机号 VALUEIN输入数据的类型value text KEYOUT输出数据类型的key text VALUEOUT flowbean
 * 
 * @author liujiang
 *
 */
public class FlowCountMapper extends Mapper<LongWritable, Text, Text, FlowBean> {
	FlowBean v = new FlowBean();
	Text k = new Text();

	@Override
	protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
		// 1 13736230513 192.196.100.1 www.atguigu.com 2481 24681 200
		// 获取一行
		String line = value.toString();
		// 2 切割字段
		String[] fields = line.split("\t");
		// 3 封装对象
		// 取出手机号码
		String phoneNum = fields[1];
		// 取出上行流量和下行流量,从后往前读，因为中间有空的字段
		long upFlow = Long.parseLong(fields[fields.length - 3]);
		long downFlow = Long.parseLong(fields[fields.length - 2]);
		
		k.set(phoneNum);
		v.setUpFlow(upFlow);
		v.setDownFlow(downFlow);
		
		//写出
		context.write(k, v);
	}
}

```

##### FlowCountReducer

```java
package com.aishang.mr.flowsum;

import java.io.IOException;

import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class FlowCountReducer extends Reducer<Text, FlowBean, Text, FlowBean> {
	@Override
	protected void reduce(Text key, Iterable<FlowBean> values, Context context)
			throws IOException, InterruptedException {
		long sum_upFlow = 0;
		long sum_downFlow = 0;

		// 1 遍历所用bean，将其中的上行流量，下行流量分别累加
		for (FlowBean flowBean : values) {
			sum_upFlow += flowBean.getUpFlow();
			sum_downFlow += flowBean.getDownFlow();
		}

		// 2 封装对象
		FlowBean resultBean = new FlowBean(sum_upFlow, sum_downFlow);
		
		// 3 写出
		context.write(key, resultBean);
	}
}

```



##### FlowsumDriver 

```java
package com.aishang.mr.flowsum;

import java.io.IOException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class FlowsumDriver {
	public static void main(String[] args) throws Exception {
		// 输入输出路径需要根据自己电脑上实际的输入输出路径设置
		args = new String[] { "/Users/liujiang/Documents/hadoop-test-txt/two", "/Users/liujiang/Documents/hadoop-test-txt/t1" };

		// 1 获取配置信息，或者job对象实例
		Configuration configuration = new Configuration();
		Job job = Job.getInstance(configuration);

		// 6 指定本程序的jar包所在的本地路径
		job.setJarByClass(FlowsumDriver.class);

		// 2 指定本业务job要使用的mapper/Reducer业务类
		job.setMapperClass(FlowCountMapper.class);
		job.setReducerClass(FlowCountReducer.class);

		// 3 指定mapper输出数据的kv类型
		job.setMapOutputKeyClass(Text.class);
		job.setMapOutputValueClass(FlowBean.class);

		// 4 指定最终输出的数据的kv类型
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(FlowBean.class);

		// 5 指定job的输入原始文件所在目录
		FileInputFormat.setInputPaths(job, new Path(args[0]));
		FileOutputFormat.setOutputPath(job, new Path(args[1]));

		// 7 将job中配置的相关参数，以及job所用的java类所在的jar包， 提交给yarn去运行
		boolean result = job.waitForCompletion(true);
		System.exit(result ? 0 : 1);
	}

}

```

结果

- 注意，原始数据间的间距，因为按\t切割，多写一个空格，就报错
- 注意隐藏文件，原始文件的文件夹内不要有隐藏文件，导致结果不一致

```java
13470253144	180	180	360
13509468723	7335	110349	117684
13560439638	918	4938	5856
13568436656	3597	25635	29232
13590439668	1116	954	2070
13630577991	6960	690	7650
13682846555	1938	2910	4848
13729199489	240	0	240
13736230513	2481	24681	27162
13768778790	120	120	240
13846544121	264	0	264
13956435636	132	1512	1644
13966251146	240	0	240
13975057813	11058	48243	59301
13992314666	3008	3720	6728
15043685818	3659	3538	7197
15910133277	3156	2936	6092
15959002129	1938	180	2118
18271575951	1527	2106	3633
18390173782	9531	2412	11943
84188413	4116	1432	5548

```

## MapReduce框架原理

### 切片与MapTask并行度决定机制

1．问题引出

MapTask的并行度决定Map阶段的任务处理并发度，进而影响到整个Job的处理速度。

思考：1G的数据，启动8个MapTask，可以提高集群的并发处理能力。那么1K的数据，也启动8个MapTask，会提高集群性能吗？MapTask并行任务是否越多越好呢？哪些因素影响了MapTask并行度？![img](http://ww1.sinaimg.cn/large/006tNc79ly1g3h3tfa2q2j30100103ya.jpg)

2．MapTask并行度决定机制

数据块：Block是HDFS物理上把数据分成一块一块。

数据切片：数据切片只是在逻辑上对输入进行分片，并不会在磁盘上将其切分成片进行存储。

## KeyValueTextInputFormat使用案例

需求

统计输入文件中每一行的第一个单词相同的行数。

（1）输入数据

```text
banzhang ni hao

xihuan hadoop banzhang

banzhang ni hao

xihuan hadoop banzhang
```



（2）期望结果数据

```text
banzhang	2

xihuan	2
```

- driver里需要设置切割符和输入格式

Mapper

```java
package com.aishang.mr.kevalue;

import java.io.IOException;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

public class KeyValueMapper extends Mapper<Text, Text, Text, IntWritable> {
	IntWritable v = new IntWritable(1);

	@Override
	protected void map(Text key, Text value, Mapper<Text, Text, Text, IntWritable>.Context context)
			throws IOException, InterruptedException {
		context.write(key, v);
	}
}

```

Reducer

```java
package com.aishang.mr.kevalue;

import java.io.IOException;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class KeyValueReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
	IntWritable v = new IntWritable();
	@Override
	protected void reduce(Text key, Iterable<IntWritable> values, Context context)
			throws IOException, InterruptedException {
		int sum = 0;
		//<banzhang 1>
		//<banzhang 1>
		// 1 汇总统计,values  就是  1
		for (IntWritable value : values) {
			sum += value.get();
		}

		v.set(sum);
		// 2 输出
		context.write(key, v);
	}
}

```

Driver

```java
package com.aishang.mr.kevalue;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.KeyValueLineRecordReader;
import org.apache.hadoop.mapreduce.lib.input.KeyValueTextInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class KeyValueDriver {
	public static void main(String[] args) throws IllegalArgumentException, IOException, ClassNotFoundException, InterruptedException {
		args = new String[]{"/Users/liujiang/Documents/hadoop-test-txt/keyvalue","/Users/liujiang/Documents/hadoop-test-txt/t2"};
		Configuration conf = new Configuration();
		// 设置切割符
		conf.set(KeyValueLineRecordReader.KEY_VALUE_SEPERATOR, " ");
		// 1 获取job对象
		Job job = Job.getInstance(conf);

		// 2 设置jar包位置，关联mapper和reducer
		job.setJarByClass(KeyValueDriver.class);
		job.setMapperClass(KeyValueMapper.class);
		job.setReducerClass(KeyValueReducer.class);

		// 3 设置map输出kv类型
		job.setMapOutputKeyClass(Text.class);
		job.setMapOutputValueClass(IntWritable.class);

		// 4 设置最终输出kv类型
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(IntWritable.class);

		// 5 设置输入输出数据路径
		FileInputFormat.setInputPaths(job, new Path(args[0]));

		// 设置输入格式
		job.setInputFormatClass(KeyValueTextInputFormat.class);

		// 6 设置输出数据路径
		FileOutputFormat.setOutputPath(job, new Path(args[1]));

		// 7 提交job
		job.waitForCompletion(true);
	}
}

```

## NLineInputFormat使用案例

- 每个map进程处理的Inputsplit不在按block划分，而是按NLineInputFormat指定的行数N来划分
- 即输入文件的总行数/N=切片数，如果不是整数，切片数=商+1

## 自定义InputFormat

无论HDFS还是MapReduce，在处理小文件时效率都非常低，但又难免面临处理大量小文件的场景，此时，就需要有相应解决方案。可以自定义InputFormat实现小文件的合并

1．需求

将多个小文件合并成一个SequenceFile文件（SequenceFile文件是Hadoop用来存储二进制形式的key-value对的文件格式），SequenceFile里面存储着多个文件，存储的形式为文件路径+名称为key，文件内容为value。

one.txt

```txt
yongpeng weidong weinan
sanfeng luozong xiaoming
```

two.txt

```text
longlong fanfan
mazong kailun yuhang yixin
longlong fanfan
mazong kailun yuhang yixin
```

three.txt

```text
shuaige changmo zhenqiang 
dongli lingu xuanxuan
```

### WholeRecordReader

```java
package com.aishang.inpuformat;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FSDataInputStream;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.BytesWritable;
import org.apache.hadoop.io.IOUtils;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.InputSplit;
import org.apache.hadoop.mapreduce.RecordReader;
import org.apache.hadoop.mapreduce.TaskAttemptContext;
import org.apache.hadoop.mapreduce.lib.input.FileSplit;

public class WholeRecordReader extends RecordReader<Text, BytesWritable> {
	FileSplit split;
	Configuration configuration;
	Text k = new Text();
	BytesWritable v = new BytesWritable();
	boolean isProgress = true;//默认一直读
	@Override
	public void initialize(InputSplit split, TaskAttemptContext context) throws IOException, InterruptedException {
		// 初始化
		this.split = (FileSplit) split;
		// 获取配置信息
		configuration = context.getConfiguration();

	}

	@Override
	public boolean nextKeyValue() throws IOException, InterruptedException {
		// 核心业务逻辑
		
		if (isProgress) {//如果有文件就读，读一个文件创建一个对象，三个文件三次
			//1获取fs对象
			Path path = split.getPath();
			FileSystem fs = path.getFileSystem(configuration);
			
			//2获取输入流
			FSDataInputStream fis = fs.open(path);
			//3拷贝
			byte[] buf = new byte[(int)split.getLength()];
			IOUtils.readFully(fis, buf, 0, buf.length);
			
			//4封装v
			v.set(buf, 0, buf.length);
			
			//5封装k  路径+名称
			k.set(path.toString());
			
			//关闭资源
			IOUtils.closeStream(fis);
			
		
			isProgress = false;
			return true;
		}
		return false;
	}

	@Override
	public Text getCurrentKey() throws IOException, InterruptedException {
		// 获取当前的key
		
		return k;
	}

	@Override
	public BytesWritable getCurrentValue() throws IOException, InterruptedException {

		return v;
	}

	@Override
	public float getProgress() throws IOException, InterruptedException {
		// 获取正在处理的进度，就是进度条
		return 0;
	}

	@Override
	public void close() throws IOException {
		// 关闭

	}

}

```



### WholeFileInputformat

```java
package com.aishang.inpuformat;

import java.io.IOException;

import org.apache.hadoop.io.BytesWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.InputSplit;
import org.apache.hadoop.mapreduce.RecordReader;
import org.apache.hadoop.mapreduce.TaskAttemptContext;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;

public class WholeFileInputformat extends FileInputFormat<Text, BytesWritable>{

	@Override
	public RecordReader<Text, BytesWritable> createRecordReader(InputSplit split, TaskAttemptContext context)
			throws IOException, InterruptedException {
		// TODO Auto-generated method stub
		WholeRecordReader recordReader = new WholeRecordReader();
		recordReader.initialize(split, context);
		return recordReader;
	}

}

```



### SequenceFileMapper

```java
package com.aishang.inpuformat;

import java.io.IOException;

import org.apache.hadoop.io.BytesWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

public class SequenceFileMapper extends Mapper<Text, BytesWritable, Text, BytesWritable> {
	@Override
	protected void map(Text key, BytesWritable value, Mapper<Text, BytesWritable, Text, BytesWritable>.Context context)
			throws IOException, InterruptedException {
		context.write(key, value);
	}
}

```



### SequenceFileReducer

```java
package com.aishang.inpuformat;

import java.io.IOException;

import org.apache.hadoop.io.BytesWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class SequenceFileReducer extends Reducer<Text, BytesWritable, Text, BytesWritable> {
	@Override
	protected void reduce(Text key, Iterable<BytesWritable> values,
			Reducer<Text, BytesWritable, Text, BytesWritable>.Context context) throws IOException, InterruptedException {
		//传过来的1.txt 2.txt 3.txt 和他们的内容
		
		for (BytesWritable value : values) {
			context.write(key, value);
		}
	}
}

```



### SequenceFileDriver

```java
package com.aishang.inpuformat;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.BytesWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.lib.output.SequenceFileOutputFormat;

public class SequenceFileDriver {
	public static void main(String[] args) throws IllegalArgumentException, IOException, ClassNotFoundException, InterruptedException {
		// 输入输出路径需要根据自己电脑上实际的输入输出路径设置
		args = new String[] { "/Users/liujiang/Documents/hadoop-test-txt/inputformat", "/Users/liujiang/Documents/hadoop-test-txt/inputformat/output1" };

		// 1 获取job对象
		Configuration conf = new Configuration();
		Job job = Job.getInstance(conf);

		// 2 设置jar包存储位置、关联自定义的mapper和reducer
		job.setJarByClass(SequenceFileDriver.class);
		job.setMapperClass(SequenceFileMapper.class);
		job.setReducerClass(SequenceFileReducer.class);

		// 7设置输入的inputFormat
		job.setInputFormatClass(WholeFileInputformat.class);

		// 8设置输出的outputFormat
		job.setOutputFormatClass(SequenceFileOutputFormat.class);

		// 3 设置map输出端的kv类型
		job.setMapOutputKeyClass(Text.class);
		job.setMapOutputValueClass(BytesWritable.class);

		// 4 设置最终输出端的kv类型
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(BytesWritable.class);

		// 5 设置输入输出路径
		FileInputFormat.setInputPaths(job, new Path(args[0]));
		FileOutputFormat.setOutputPath(job, new Path(args[1]));

		// 6 提交job
		boolean result = job.waitForCompletion(true);
		System.exit(result ? 0 : 1);
	}
}

```

