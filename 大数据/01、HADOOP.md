# 大数据

![粘贴的图片2019_4_30_下午8_56](/Users/liujiang/Documents/Typora/imgs/粘贴的图片2019_4_30_下午8_56.png)

## Hadoop

### 1、什么是Hadoop

- 是apache的一套开源软件平台
- 利用服务器集群，根据用户的自定义业务逻辑，对海量数据进行分布式处理
- 核心组件
  - HDFS分布式文件系统
  - yarn运算资源调度系统
  - MapReduce分布式运算编程框架
- 通常指一个更广泛的概念—hadoop生态圈

### 2、企业级应用

- 存储层（HDFS，Hbase）
- 数据处理层（Hive MapReduce）
- 实时访问层（Spark）

### 3、HDFS架构

分布式存储系统，分布式的架构上存在主/从的架构关系，在HDFS文件系统上存在主节点以及从节点

- 主节点：namenode

  - 负责管理HDFS集群文件中的元数据（文件的名称，位置，副本）

- 从节点：datanode

  - 存储真正的数据

  ![HDFS Architecture](/Users/liujiang/Documents/Typora/imgs/hdfsarchitecture.png)

- 在HDFS中数据的存储是以块的方式进行存储的，block 默认块的大小为128M

### 4、yarn架构

![MapReduce NextGen Architecture](/Users/liujiang/Documents/Typora/imgs/yarn_architecture.gif)

分布式的架构，分为主从架构

- 主节点：resourceManager负责管理集群中的所有资源（cpu、内存，网络、IO）
- 从节点：nodeManager负责管理集群中每一台服务器的资源

### 5、MapReduce架构

核心思想：分而治之

- map端和reduc端e进行数据分析
- 数据在map阶段进行分开处理，处理完成之后交给reduce进行统计，在map和reduce中间的阶段通过shuffle来进行连接

### 6、搭建hadoop环境

安装前的准备：

- 设置普通用户以及设置sudo权限

- 关闭防火墙

- 设置主机名称

- ip与主机名称进行绑定

- 在/opt目录下创建module、software文件夹
  - soft用于存放jar包
  - module存放解压后的jar包

- 步骤：

  - 修改主机名

    ```sh
    vi /etc/hostname
    
    hadoop101
    ```

  - vi /etc/hosts

    ```sh
    192.168.83.100 hadoop100
    192.168.83.101 hadoop101
    192.168.83.102 hadoop102
    192.168.83.103 hadoop103
    192.168.83.104 hadoop104
    ```

  - 重启reboot

  - 修改ip地址

    ```sh
    vi  /etc/sysconfig/network-scripts/ifcfg-ens33
    
    
    DEVICE=eth0  网卡对应的设备别名，如ifcfg-eth0第一块网卡
    
    BOOTPROTO=static 网卡获得ip地址的方式，
    
    static（静态 ip地址）
    
    dhcp（通过dhcp协议获取ip）
    
    bootip（通过bootp协议获得的ip地址）
    
    BROADCAST=192.168.0.255         子网广播地址
    
    GATEWAY=192.168.1.1        默认网关
    
    HWADDR=00:50:56:8E:47:EE        网卡物理地址
    
    IPADDR=192.168.1.117             网卡IP地址
    
    IPV6INIT=no                     是否启用IPV6
    
    IPV6_AUTOCONF=no
    
    NETMASK=255.255.255.0           网卡对应网络掩码
    
    NETWORK=192.168.1.0             网卡对应的网络地址
    
    ONBOOT=yes           系统启动时是否设置此网络接口，设置为yes时，系统启动时激活此设备。默认设置为yes
    
    至于后面的 TYPE 和UUID这个就不用管了，这是网卡的类型
    ```

    将光标移动到 IPADDR 设置部分，改成需要设置的IP地址，

    BOOTPROTO=static需要改成静态

    - nat模式

    - 先在本机查看ip，由于是nat模式需要看ifconfig

      ```sh
      vmnet8: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
      	ether 00:50:56:c0:00:08 
      	inet 192.168.83.1 netmask 0xffffff00 broadcast 192.168.83.255
      ```

      

    ```sh
    IPADDR=192.168.83.101
    GATEWAY=192.168.83.2
    DNS1=192.168.83.2
    ```

  - 重启网络

    ```sh
    service network restart
    ```

  - 相互ping同即可

#### 安装JDK环境

- 在/opt目录下创建module、software文件夹
  - software存放jar包
  - module存放解压后的软件

- 上传安装文件到linux

- 解压

  ```sh
  tar -zxvf jdk-8u144-linux-x64.tar.gz -C /opt/module/
  ```

- 配置环境变量：

- vi /etc/profile

  ```sh
  export JAVA_HOME=/opt/module/jdk1.8.0_144
  export PATH=$PATH:$JAVA_HOME/bin
  
  ```

- source /etc/profile使配置生效

- 测试java -version

#### 安装Hadoop

- 解压hadoop，配置环境变量

  ```sh
  tar -zxvf hadoop-2.7.2.tar.gz -C /opt/module/
  ```

- 配置环境变量

- vi /etc/profile

```sh
export HADOOP_HOME=/opt/module/hadoop-2.7.2
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
```

- source /etc/profile是配置生效
- 测试  hadoop

#### hadoop目录结构

```sh
drwxr-xr-x. 2 root root   194 5月  22 2017 bin
drwxr-xr-x. 3 root root    20 5月  22 2017 etc
drwxr-xr-x. 2 root root   106 5月  22 2017 include
drwxr-xr-x. 3 root root    20 5月  22 2017 lib
drwxr-xr-x. 2 root root   239 5月  22 2017 libexec
-rw-r--r--. 1 root root 15429 5月  22 2017 LICENSE.txt
-rw-r--r--. 1 root root   101 5月  22 2017 NOTICE.txt
-rw-r--r--. 1 root root  1366 5月  22 2017 README.txt
drwxr-xr-x. 2 root root  4096 5月  22 2017 sbin
drwxr-xr-x. 4 root root    31 5月  22 2017 share
```

### 7、伪分布式

1. etc/hadoop/core-site.xml:配置的是指定HDFS中NameNode的地址

   ```xml
   <configuration>
       <property>
           <name>fs.defaultFS</name>
           <value>hdfs://hadoop101:9000</value>
       </property>
   </configuration>
   
   //运行时产生文件的存储目录
   <property>
           <name>hadoop.tmp.dir</name>
           <value>/opt/module/hadoop-2.7.2/data/tmp</value>
   </property>
   ```

2. 配置hadoop-env.sh：linux获取jdk的安装路径

   vi hadoop-env.sh

   ```xml
   [root@172.16.141.141 ~]# echo $JAVA_HOME
   /opt/module/jdk1.8.0_144
   
   修改为
   
   export JAVA_HOME=/opt/module/jdk1.8.0_144
   ```

3. etc/hadoop/hdfs-site.xml:配置副本数量，默认值3，在三台机器上存储了同一份数据，任何一台节点挂掉，还有两份，在其他服务器增加节点，保证3。先设置成1，感受下

   ```xml
   <configuration>
       <property>
           <name>dfs.replication</name>
           <value>1</value>
       </property>
   </configuration>
   
   ```

4. 启动集群

- 格式化NameNode（第一次启动时格式化，以后就不要总格式化）

  ```sh
  [atguigu@hadoop101 hadoop-2.7.2]$ bin/hdfs namenode -format
  ```

  

- 启动NameNode

  ```sh
  sbin/hadoop-daemon.sh start namenode
  ```

  jps 查看是否成功

  ```sh
  [root@localhost hadoop-2.7.2]# jps
  1611 NameNode
  1678 Jps
  ```

  

- 启动DataNode

  ```sh
  sbin/hadoop-daemon.sh start datanode
  ```

  ```sh
  [root@localhost hadoop-2.7.2]# jps
  1701 DataNode
  1611 NameNode
  1773 Jps
  ```

- 测试

  - 192.168.83.101:50070(http://hadoop102:50070)

  - 访问不了，需要关闭防火墙

    ```sh
    sudo systemctl stop firewalld.service && sudo systemctl disable firewalld.service
    ```

- 格式化出错

  - 结束进程
  - 删掉data、logs文件夹
  - 之后再格式化![粘贴的图片2019_4_30_下午8_57](/Users/liujiang/Documents/Typora/imgs/粘贴的图片2019_4_30_下午8_57.png)



![粘贴的图片2019_4_30_下午8_59](/Users/liujiang/Documents/Typora/imgs/粘贴的图片2019_4_30_下午8_59.png)

![粘贴的图片2019_4_30_下午8_59](/Users/liujiang/Documents/Typora/imgs/粘贴的图片2019_4_30_下午8_59-6629187.png)

### 8、启动yarn并运行MapReduce程序

- 配置yarn-env.sh

  ```sh
  export JAVA_HOME=/opt/module/jdk1.8.0_144
  ```

- 配置yarn-site.xml

  ```sh
  <!-- Reducer获取数据的方式 -->
  <property>
   		<name>yarn.nodemanager.aux-services</name>
   		<value>mapreduce_shuffle</value>
  </property>
  
  <!-- 指定YARN的ResourceManager的地址 -->
  <property>
  <name>yarn.resourcemanager.hostname</name>
  <value>hadoop101</value>
  </property>
  ```

- 配置：mapred-env.sh

  ```sh
  export JAVA_HOME=/opt/module/jdk1.8.0_144
  ```

- 配置： (对mapred-site.xml.template重新命名为) mapred-site.xml

  ```sh
  mv mapred-site.xml.template mapred-site.xml
  
  vi mapred-site.xml
  
  <!-- 指定MR运行在YARN上 -->
  <property>
  		<name>mapreduce.framework.name</name>
  		<value>yarn</value>
  </property>
  ```

- 启动集群

  - 启动前必须保证NameNode和DataNode已经启动

  - 启动ResourceManager

  ```sh
  sbin/yarn-daemon.sh start resourcemanager
  ```

  - 启动NodeManager

  ```sh
  sbin/yarn-daemon.sh start nodemanager
  ```

  ```sh
  [root@hadoop101 hadoop-2.7.2]# jps
  8498 DataNode
  16691 Jps
  16596 NodeManager
  16358 ResourceManager
  8408 NameNode
  ```

  YARN的浏览器页面查看

  http://hadoop101:8088/cluster

  ![粘贴的图片2019_4_30_下午9_00](/Users/liujiang/Documents/Typora/imgs/粘贴的图片2019_4_30_下午9_00.png)

### 9、修改主机名

hostname查看主机名

etc hostname的文件，直接修改即可

whoami查看用户名:修改，在etc目录下的passwd文件

![粘贴的图片2019_4_30_下午9_01](/Users/liujiang/Documents/Typora/imgs/粘贴的图片2019_4_30_下午9_01.png)

目前一致

重启reboot，没有权限加sudo

### 10、wordCount案例

- 在hadoop文件夹下，mkdir wcinput  (hadoop  cd /usr/loacl/docker/hadoop)

  ```sh
  [root@hadoop0 hadoop]# cd wcinput/
  [root@hadoop0 wcinput]# ls
  wc.input
  [root@hadoop0 wcinput]# ll
  total 4
  -rw-r--r--. 1 root root 70 Mar  4 07:16 wc.input
  [root@hadoop0 wcinput]# cat wc.input 
  hadoop mapreduce
  hadoop yarn
  hadoop hdfs
  mapreduce spark
  hadoop hello
  [root@hadoop0 wcinput]# 
  ```

- 回到hadoop文件目录，执行程序

  ```sh
  [root@hadoop0 hadoop]# hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount wcinput/ wcoutput
  ```

  

```sh
sbin/hadoop-daemon.sh start datanode
sbin/hadoop-daemon.sh start namenode
sbin/yarn-daemon.sh start resourcemanager
sbin/yarn-daemon.sh start nodemanager


sbin/hadoop-daemon.sh stop datanode
sbin/hadoop-daemon.sh stop namenode
sbin/yarn-daemon.sh stop resourcemanager
sbin/yarn-daemon.sh stop nodemanager
```

