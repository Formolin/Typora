# Docker搭建Hadoop集群

- docker pull centos

- 以centos7镜像为基础，构建一个带有SSH功能的centos，使用Dockerfile创建vi Dockerfile

- 在usr/local创建docker文件夹，进入创建Dockerfile

- 在Dockerfile所在目录下准备好jdk-8u144-linux-x64.tar.gz 与 hadoop-2.7.2.tar.gz

  ```sh
  FROM centos
  MAINTAINER dys
  
  RUN yum install -y openssh-server sudo
  RUN sed -i 's/UsePAM yes/UsePAM no/g' /etc/ssh/sshd_config
  RUN yum  install -y openssh-clients
  
  RUN echo "root:root" | chpasswd
  RUN echo "root   ALL=(ALL)       ALL" >> /etc/sudoers
  RUN ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
  RUN ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
  
  RUN mkdir /var/run/sshd
  EXPOSE 22
  CMD ["/usr/sbin/sshd", "-D"]
  ```

- 这里是基于 c以 centos 镜像为基础，安装SSH的相关包，设置了root用户的密码为 root，并启动SSH服务

- 执行构建命令，新镜像命名为 centos7-ssh

- docker build -t="centos7-ssh" .

  ```sh
  docker build -t="centos7-ssh" .    #注意这里最后的点
  ```

# 构建Hadoop镜像

- 前面构建的centos7-ssh镜像为基础，安装Java、Hadoop构建Hadoop镜像

- vi Dockerfile

  ```sh
  FROM centos7-ssh
  ADD jdk-8u144-linux-x64.tar.gz /usr/local/docker
  RUN mv /usr/local/docker/jdk1.8.0_144 /usr/local/docker/jdk1.8
  ENV JAVA_HOME /usr/local/docker/jdk1.8
  ENV PATH $JAVA_HOME/bin:$PATH
  
  ADD hadoop-2.7.2.tar.gz /usr/local/docker
  RUN mv /usr/local/docker/hadoop-2.7.2 /usr/local/docker/hadoop
  ENV HADOOP_HOME /usr/local/docker/hadoop
  ENV PATH $HADOOP_HOME/bin:$PATH
  
  RUN yum install -y which sudo
  ```

- docker build -t="hadoop" .

- 运行3个hadoop容器，分别命名为 hadoop0,hadoop1,hadoop2，hadoop0 作为master

```sh
docker run --name hadoop0 --hostname hadoop0 --privileged -d -P -p 50070:50070 -p 8088:8088 hadoop /usr/sbin/init
docker run --name hadoop1 --hostname hadoop1 --privileged -d -P hadoop /usr/sbin/init
docker run --name hadoop2 --hostname hadoop2 --privileged -d -P hadoop /usr/sbin/init
```

- 容器hadoop0启动时，映射了端口号，50070和8088，是用来在浏览器中访问hadoop WEB界面的。

# 为容器配置IP及SSH无密码登陆

为容器配置IP
设置固定IP，需要用到 pipework，用于给容器设置IP

安装git

```
yum install git -y
```

```sh
#先下载
$ git clone https://github.com/jpetazzo/pipework.git
$ cp pipework/pipework /usr/local/bin/

#安装bridge-utils
$ yum -y install bridge-utils

#创建网络
$ brctl addbr br1
$ ip link set dev br1 up
$ ip addr add 192.168.10.1/24 dev br1

```



此时已经创建好网桥br1，为前面启动的容器hadoop0、hadoop1、hadoop2分别指定IP

配置IP

```sh
$ pipework br1 hadoop0 192.168.10.30/24
$ pipework br1 hadoop1 192.168.10.31/24
$ pipework br1 hadoop2 192.168.10.32/24
```



分别使用 ping 与 ssh 命令进行验证，看是否可以ping通和成功登录

```sh
$ ping 192.168.10.30
$ ssh 192.168.10.30
```



# 容器间SSH无密码登陆

前面已经为容器配置IP了，在进行ssh 时需要输入要登陆的容器的root密码，Hadoop集群要求集群间机器SSH连接时无密码登陆，下面讲述容器间如何配置 SSH无密码登陆。

新开3个终端窗口，分别连接到 hadoop0,hadoop1,hadoop2，便于操作

```sh
$ docker exec -it hadoop0 /bin/bash
$ docker exec -it hadoop1 /bin/bash
$ docker exec -it hadoop2 /bin/bash
```

- 修改HOST 

在各个容器中修改 /etc/hosts，添加：

```sh
192.168.10.30    hadoop0
192.168.10.31    hadoop1
192.168.10.32    hadoop2
```

注意：将原先容器创建时产生的host去掉，比如172.17.0.0这个网段的host，具体类似如下：

```sh
172.17.0.1 hadoop0
```

- SSH无密码登陆配置

Hadoop镜像是基于centos7-ssh镜像创建的，而centos7-ssh镜像在centos镜像基础上安装了SSH相关的工具包，

所以在每个Hadoop容器命令窗口内执行下面命令：

```sh
$ ssh-keygen
（执行后会有多个输入提示，不用输入任何内容，全部直接回车即可）
$ ssh-copy-id -i /root/.ssh/id_rsa -p 22 root@hadoop0
$ ssh-copy-id -i /root/.ssh/id_rsa -p 22 root@hadoop1
$ ssh-copy-id -i /root/.ssh/id_rsa -p 22 root@hadoop2

#测试
$ ssh hadoop1
#测试完，回退到原先窗口
$ exit

```



# 配置Hadoop

### (1)环境确认检查

在配置Hadoop之前，在hadoop0、hadoop1、hadoop2各命令窗口检查下java、hadoop环境变量是否设置正确（构建Hadoop镜像的时候，已经安装Java、Hadoop并设置了相关环境变量）

注意这里不要以容器交互方式进入，用ssh进入

```sh
$ java
$ hadoop
```

如上述两命令不起作用，则需要手动配置下两者的环境变量

```sh
vi /etc/profile
```

添加以下环境变量

```sh
export JAVA_HOME=/usr/local/docker/jdk1.8
export PATH=$PATH:$JAVA_HOME/bin

export HADOOP_HOME=/usr/local/docker/hadoop
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin

```



加载刷新环境变量

```sh
source /etc/profile
```

重新检查下java 和hadoop命令

## （2）Hadoop文件配置

hadoop0中执行

```sh
$ cd /usr/local/docker/hadoop
$ mkdir tmp hdfs
$ mkdir hdfs/data hdfs/name
```

1、core-site.xml配置

```sh
cd /usr/local/docker/hadoop/etc/hadoop
vi core-site.xml
```

在 < configuration> 块儿中添加：

```xml
#指定HDFS中NameNode的地址
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://hadoop0:9000</value>
</property>
#指定hadoop运行时产生文件的存储目录
<property>
    <name>hadoop.tmp.dir</name>
    <value>file:/usr/local/docker/hadoop/tmp</value>
</property>
<property>
    <name>io.file.buffer.size</name>
    <value>131702</value>
</property>
```
2、hdfs-site.xml配置

```sh
cd /etc/hadoop
vi hdfs-site.xml
```



在 < configuration> 块儿中添加：

    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/usr/local/docker/hadoop/hdfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/usr/local/docker/hadoop/hdfs/data</value>
    </property>
    <property>
        <name>dfs.replication</name>
        <value>2</value>
    </property>
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>hadoop0:9001</value>
    </property>
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>

3、mapred-site.xml配置

这个文件默认不存在，需要从 mapred-site.xml.template 复制过来

```sh
$ cp mapred-site.xml.template mapred-site.xml
vi mapred-site.xml
```



在 < configuration> 块儿中添加：

    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>hadoop0:10020</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>hadoop0:19888</value>
    </property>

4、yarn-site.xml配置

```sh
vi yarn-site.xml
```

在 < configuration> 块儿中添加：

    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.nodemanager.auxservices.mapreduce.shuffle.class</name>
        <value>org.apache.hadoop.mapred.ShuffleHandler</value>
    </property>
    <property>
        <name>yarn.resourcemanager.address</name>
        <value>hadoop0:8032</value>
    </property>
    <property>
        <name>yarn.resourcemanager.scheduler.address</name>
        <value>hadoop0:8030</value>
    </property>
    <property>
        <name>yarn.resourcemanager.resource-tracker.address</name>
        <value>hadoop0:8031</value>
    </property>
    <property>
        <name>yarn.resourcemanager.admin.address</name>
        <value>hadoop0:8033</value>
    </property>
    <property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>hadoop0:8088</value>
    </property>

5、slaves配置

```sh
vi slaves
```

删除已有内容，添加：

```sh
hadoop1
hadoop2
```



6、hadoop-env.sh配置

```sh
vi hadoop-env.sh
```

找到 export JAVA_HOME=${JAVA_HOME}，改为自己JAVA_HOME的绝对路径，比如：

```sh
export JAVA_HOME=/usr/local/docker/jdk1.8
```

7、hadoop集群配置分发

注意：如果各台服务器中JAVA_HOME位置不同，记得对 hadoop-env.sh 进行相应修改

在 hadoop0 上执行

```sh
$ scp -r /usr/local/docker/hadoop hadoop1:/usr/local/docker
$ scp -r /usr/local/docker/hadoop hadoop2:/usr/local/docker
```





# Hadoop集群启动测试

### （1）、集群启动

在主节点(hadoop0)启动hadoop，从节点（hadoop1、hadoop2）会自动启动

1、初始化

第一次启动集群时，需要初始化

```sh
$ hdfs namenode -format
```



2、依次启动配置文件,在hadoop文件夹内

```sh
$ hadoop-daemon.sh start namenode
$ hadoop-daemon.sh start datanode
$ start-dfs.sh
$ start-yarn.sh
$ mr-jobhistory-daemon.sh start historyserver
```



附：停止hadoop集群

```sh
$ hadoop-daemon.sh stop namenode
$ hadoop-daemon.sh stop datanode
$ stop-dfs.sh
$ stop-yarn.sh
$ mr-jobhistory-daemon.sh stop historyserver
```



注：hadoop集群启动和停止可以使用start/stop-all.sh，但不推荐。

3、检查

查看状态，在3台服务器上分别执行jps

```sh
[root@hadoop0 hadoop]# jps
1442 Jps
979 SecondaryNameNode
1124 ResourceManager
534 NameNode
651 DataNode
1214 JobHistoryServer


[root@hadoop1 /]# jps
367 DataNode
463 NodeManager
575 Jps



[root@hadoop2 /]# jps
337 DataNode
433 NodeManager
545 Jps
```



浏览器中访问(宿主机Host可以配置下hadoop0的IP地址)：

http://172.16.141.144:50070

可以正常访问的话，可以说明集群启动成功了，但不一定可以正常运行，还需要下面的实际验证。

（2）测试验证

1、hdfs 操作

创建本地测试文件 
在/usr/local/docker/hadoop目录下创建测试文件目录

```sh
$ mkdir wcinput
$ cd wcinput
$ vi wc.input
```



wc.input文件内容如下：

```sh
hadoop mapreduce
hadoop yarn
hadoop hdfs
mapreduce spark
hadoop hello
```



创建HDFS目录

```sh
$ hdfs dfs -mkdir -p /user/hadoop/input
```



上传文件，把测试文件上传到刚刚创建的目录中

```sh
$ hdfs dfs -put /usr/local/docker/hadoop/wcinput/wc.input /user/hadoop/input
```



查看文件上传是否正确

```sh
[root@hadoop0 hadoop]# hdfs dfs -ls /user/hadoop/input
Found 1 items
-rw-r--r--   2 root supergroup         71 2018-04-17 09:12 /user/hadoop/input/wc.input
[root@hadoop0 hadoop]# 
```



运行mapreduce程序

hadoop 安装包中提供了一个示例程序，我们可以使用它对刚刚上传的文件进行测试

注：在执行过程中，如果长时间处于 running 状态不动，虽然没有报错，但实际上是出错了，后台在不断重试，需要到 logs 目录下查看日志文件中的错误信息。

```sh
hadoop jar /usr/local/docker/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount /user/hadoop/input /user/hadoop/output

#执行完
19/03/04 07:26:08 INFO client.RMProxy: Connecting to ResourceManager at hadoop0/192.168.10.30:8032
19/03/04 07:26:20 INFO input.FileInputFormat: Total input paths to process : 1
19/03/04 07:26:42 INFO mapreduce.JobSubmitter: number of splits:1
19/03/04 07:26:45 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1551683157028_0001
19/03/04 07:27:01 INFO impl.YarnClientImpl: Submitted application application_1551683157028_0001
19/03/04 07:27:02 INFO mapreduce.Job: The url to track the job: http://hadoop0:8088/proxy/application_1551683157028_0001/
19/03/04 07:27:02 INFO mapreduce.Job: Running job: job_1551683157028_0001
19/03/04 07:28:44 INFO mapreduce.Job: Job job_1551683157028_0001 running in uber mode : false
19/03/04 07:28:45 INFO mapreduce.Job:  map 0% reduce 0%
19/03/04 07:29:40 INFO mapreduce.Job:  map 100% reduce 0%
19/03/04 07:30:20 INFO mapreduce.Job:  map 100% reduce 67%
19/03/04 07:30:24 INFO mapreduce.Job:  map 100% reduce 100%
19/03/04 07:30:39 INFO mapreduce.Job: Job job_1551683157028_0001 completed successfully
19/03/04 07:30:45 INFO mapred.ClientServiceDelegate: Application state is completed. FinalApplicationStatus=SUCCEEDED. Redirecting to job history server
19/03/04 07:31:22 INFO mapreduce.Job: Counters: 49
	File System Counters
		FILE: Number of bytes read=81
		FILE: Number of bytes written=235143
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=181
		HDFS: Number of bytes written=51
		HDFS: Number of read operations=6
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=2
	Job Counters 
		Launched map tasks=1
		Launched reduce tasks=1
		Data-local map tasks=1
		Total time spent by all maps in occupied slots (ms)=56124
		Total time spent by all reduces in occupied slots (ms)=35687
		Total time spent by all map tasks (ms)=56124
		Total time spent by all reduce tasks (ms)=35687
		Total vcore-milliseconds taken by all map tasks=56124
		Total vcore-milliseconds taken by all reduce tasks=35687
		Total megabyte-milliseconds taken by all map tasks=57470976
		Total megabyte-milliseconds taken by all reduce tasks=36543488
	Map-Reduce Framework
		Map input records=5
		Map output records=10
		Map output bytes=110
		Map output materialized bytes=81
		Input split bytes=111
		Combine input records=10
		Combine output records=6
		Reduce input groups=6
		Reduce shuffle bytes=81
		Reduce input records=6
		Reduce output records=6
		Spilled Records=12
		Shuffled Maps =1
		Failed Shuffles=0
		Merged Map outputs=1
		GC time elapsed (ms)=1082
		CPU time spent (ms)=4340
		Physical memory (bytes) snapshot=272732160
		Virtual memory (bytes) snapshot=3843428352
		Total committed heap usage (bytes)=141803520
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	File Input Format Counters 
		Bytes Read=70
	File Output Format Counters 
		Bytes Written=51

```



查看输出结果

```sh
[root@hadoop0 hadoop]# hdfs dfs -ls /user/hadoop/output
Found 2 items
-rw-r--r--   2 root supergroup          0 2018-04-17 09:14 /user/hadoop/output/_SUCCESS
-rw-r--r--   2 root supergroup         51 2018-04-17 09:14 /user/hadoop/output/part-r-00000
[root@hadoop0 hadoop]# 
```



_SUCCESS表示HDFS文件状态，生成的结果在part-r-00000中，可查看

```sh
[root@hadoop0 hadoop]# hdfs dfs -cat /user/hadoop/output/part-r-00000
hadoop  4
hdfs    1
hello   1
mapreduce   2
spark   1
yarn    1
[root@hadoop0 hadoop]# 

```



总结：以上就是使用Docker环境搭建Hadoop镜像容器，配置Hadoop集群，并启动和测试的实例，测试用的是hadoop官方给的一个wordcount统计，利用hadoop安装包里的mapreduce示例jar 计算指定HDFS文件里的单词数，并将结果输出到指定HDFS目录。后面会介绍HDFS常用文件操作命令。