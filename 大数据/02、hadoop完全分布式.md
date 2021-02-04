# hadoop完全分布式

- 准备三台虚拟机(102\103\104)

  - 关闭防火墙

  - 静态ip

    - vi  /etc/sysconfig/network-scripts/ifcfg-ens33

      ```sh
      IPADDR=192.168.83.102
      GATEWAY=192.168.83.2
      DNS1=192.168.83.2
      
      IPADDR=192.168.83.103
      GATEWAY=192.168.83.2
      DNS1=192.168.83.2
      
      
      IPADDR=192.168.83.104
      GATEWAY=192.168.83.2
      DNS1=192.168.83.2
      
      
      
      
      service network restart
      ```

      

  - 主机名称

    - vi /etc/hostname

  - vi /etc/hosts

    ```sh
    192.168.83.100 hadoop100
    192.168.83.101 hadoop101
    192.168.83.102 hadoop102
    192.168.83.103 hadoop103
    192.168.83.104 hadoop104
    ```

    

- 安装jdk

- 配置环境变量

- 安装hadoop

- 配置环境变量

- 单点启动

- 配置ssh

- 群起并测试集群

## xsync集群分发脚本

在/usr/local/bin这个目录下存放的脚本

```sh
#!/bin/bash
#1 获取输入参数个数，如果没有参数，直接退出
pcount=$#
if((pcount==0)); then
echo no args;
exit;
fi

#2 获取文件名称
p1=$1
fname=`basename $p1`
echo fname=$fname

#3 获取上级目录到绝对路径
pdir=`cd -P $(dirname $p1); pwd`
echo pdir=$pdir

#4 获取当前用户名称
user=`whoami`

#5 循环
for((host=103; host<105; host++)); do
        echo ------------------- hadoop$host --------------
        rsync -rvl $pdir/$fname $user@hadoop$host:$pdir
done
```

修改脚本 xsync 具有执行权限

安装rsync

yum install rsync -y 每个都需要安装

chmod 777 xsync

## 集群配置

|      | hadoop102        | hadoop103                  | hadoop104                 |
| ---- | ---------------- | -------------------------- | ------------------------- |
| HDFS | NameNodeDataNode | DataNode                   | SecondaryNameNodeDataNode |
| YARN | NodeManager      | ResourceManagerNodeManager | NodeManager               |

- 配置集群
  - 配置core-site.xml

    ```xml
    vi core-site.xml
    
    
    
    <!-- 指定HDFS中NameNode的地址 -->
    <property>
    		<name>fs.defaultFS</name>
          <value>hdfs://hadoop102:9000</value>
    </property>
    
    <!-- 指定Hadoop运行时产生文件的存储目录 -->
    <property>
    		<name>hadoop.tmp.dir</name>
    		<value>/opt/module/hadoop-2.7.2/data/tmp</value>
    </property>
    ```

  - HDFS配置文件

    ```xml
    vi hadoop-env.sh
    
    export JAVA_HOME=/opt/module/jdk1.8.0_144
    ```

    

  - 配置hdfs-site.xml

    ```xml
    vi hdfs-site.xml
    
    <property>
    		<name>dfs.replication</name>
    		<value>3</value>
    </property>
    
    <!-- 指定Hadoop辅助名称节点主机配置 -->
    <property>
          <name>dfs.namenode.secondary.http-address</name>
          <value>hadoop104:50090</value>
    </property>
    ```

    

  - YARN配置文件

    ```xml
    vi yarn-env.sh
    
    export JAVA_HOME=/opt/module/jdk1.8.0_144
    
    
    yarn-site.xml
    
    <!-- Reducer获取数据的方式 -->
    <property>
    		<name>yarn.nodemanager.aux-services</name>
    		<value>mapreduce_shuffle</value>
    </property>
    
    <!-- 指定YARN的ResourceManager的地址 -->
    <property>
    		<name>yarn.resourcemanager.hostname</name>
    		<value>hadoop103</value>
    </property>
    ```

    

  - MapReduce配置文件

    ```xml
    vi mapred-env.sh
    
    export JAVA_HOME=/opt/module/jdk1.8.0_144
    
    cp mapred-site.xml.template mapred-site.xml
    vi mapred-site.xml
    
    <!-- 指定MR运行在Yarn上 -->
    <property>
    		<name>mapreduce.framework.name</name>
    		<value>yarn</value>
    </property>
    ```

    

  - 在集群上分发配置好的Hadoop配置文件

    ```xml
    [root@hadoop102 etc]# xsync /opt/module/hadoop-2.7.2/
    注意，在etc目录
    ```

    

## 单点启动

检查之前的文件

- 结束进程

- 删除data、logs

  

- 在102上启动

  ```sh
  bin/hdfs namenode -format
  
  
  102
  sbin/hadoop-daemon.sh start namenode
  sbin/hadoop-daemon.sh start datanode
  
  103
  sbin/hadoop-daemon.sh start datanode
  
  104
  sbin/hadoop-daemon.sh start datanode
  ```

  

- 访问hadoop102:50070
- 问题：每次都需要一个一个启动，ssh方法

## ssh

- Ssh hadoop103
- 输入密码

```sh
102\103\104


cd ~
cd .ssh/
ssh-keygen -t rsa


ssh-copy-id hadoop102
ssh-copy-id hadoop103
ssh-copy-id hadoop104
```

### 群起集群

- 配置slaves

  ```sh
  /opt/module/hadoop-2.7.2/etc/hadoop/slaves
  
  vi slaves
  
  在该文件中增加如下内容：
  hadoop102
  hadoop103
  hadoop104
  
  该文件中添加的内容结尾不允许有空格，文件中不允许有空行。
  
  同步所有节点配置文件
  
  xsync slaves 注意在hadoop目录下
  ```

- 启动集群

  - 如果集群是第一次启动，需要格式化NameNode（注意格式化之前，一定要先停止上次启动的所有namenode和datanode进程，然后再删除data和log数据）

    ```sh
    bin/hdfs namenode -format
    
    #不是的话执行执行
    sbin/start-dfs.sh
    
    
    [root@hadoop102 hadoop-2.7.2]# jps
    8835 DataNode
    8710 NameNode
    9036 Jps
    
    [root@hadoop103 hadoop-2.7.2]# jps
    8131 Jps
    8058 DataNode
    
    [root@hadoop104 hadoop-2.7.2]# jps
    8487 Jps
    8348 DataNode
    8445 SecondaryNameNode
    ```

- 启动YARN

  ```sh
  
  注意：NameNode和ResourceManger如果不是同一台机器，不能在NameNode上启动 YARN，应该在ResouceManager所在的机器上启动YARN。
  
  在103上启动
  sbin/start-yarn.sh
  
  
  
  [root@hadoop102 hadoop-2.7.2]# jps
  9184 Jps
  8835 DataNode
  8710 NameNode
  9085 NodeManager
  [root@hadoop103 hadoop-2.7.2]# jps
  8192 ResourceManager
  8560 Jps
  8058 DataNode
  8302 NodeManager
  [root@hadoop104 hadoop-2.7.2]# jps
  8641 Jps
  8348 DataNode
  8445 SecondaryNameNode
  8541 NodeManager
  
  
  
  Web端查看SecondaryNameNode
  （a）浏览器中输入：http://hadoop104:50090/status.html
  ```

### 启动命令

```sh
102上执行
sbin/start-dfs.sh
103上执行
sbin/start-yarn.sh
```



## 集群基本测试

- 上传小文件

```sh
bin/hdfs dfs -put wcinput/wc.input /
```

- 上传大文件

```sh
bin/hdfs dfs -put /opt/software/hadoop-2.7.2.tar.gz /
```

- 存储路径

```sh
/opt/module/hadoop-2.7.2/data/tmp/dfs/data/current/BP-486929679-192.168.83.102-1554981511340/current/finalized/subdir0/subdir0



[root@hadoop102 subdir0]# ll
总用量 194552
-rw-r--r--. 1 root root        59 4月  11 20:12 blk_1073741825
-rw-r--r--. 1 root root        11 4月  11 20:12 blk_1073741825_1001.meta
-rw-r--r--. 1 root root 134217728 4月  11 21:30 blk_1073741826
-rw-r--r--. 1 root root   1048583 4月  11 21:30 blk_1073741826_1002.meta
-rw-r--r--. 1 root root  63439959 4月  11 21:30 blk_1073741827
-rw-r--r--. 1 root root    495635 4月  11 21:30 blk_1073741827_1003.meta


[root@hadoop102 subdir0]# cat blk_1073741825
hadoop yarn
hadoop mapreduce
hadoop hdfs
liujiang
liujiang


cat blk...26 >> tmp.txt
cat blk...27 >> tmp.txt

tar -zvxf tmp.txt
```

## 集群停止

```sh
start-dfs.sh
stop-dfs.sh

start-yarn.sh
stop-yarn.sh
```

