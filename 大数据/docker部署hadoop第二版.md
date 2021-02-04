- docker info

  ```sh
  WARNING: bridge-nf-call-iptables is disabled
  WARNING: bridge-nf-call-ip6tables is disabled
  
  需要关闭防火墙
  
  systemctl stop firewalld
  systemctl disable firewalld
  注意：执行完上面的命令之后需要重启系统
  reboot -h(重启系统)
  ```

- docker pull centos

- (可省略)测试-运行容器可能会报错  

  ```sh
  docker run -it centos
  ```

  需要关闭selinux
  解决方法：

```
1：setenforce 0(立刻生效，不需要重启操作系统)



2：修改/etc/selinux/config文件中的SELINUX=disabled ，然后重启系统生效



建议两个步骤都执行，这样可以确保系统重启之后selinux也是关闭状态
```

- 需要先构建一个hadoop的基础镜像，使用dockerfile文件方式进行构建。

- 先构建一个具备ssh功能的镜像，方便后期使用

- 这个镜像中的root用户的密码是root

  ```sh
  mkdir centos-ssh-root
  
  cd centos-ssh-root
  
  vi Dockerfile
  ```

  ```sh
  # 选择一个已有的os镜像作为基础  
  FROM centos 
   
  # 镜像的作者  
  MAINTAINER crxy 
   
  # 安装openssh-server和sudo软件包，并且将sshd的UsePAM参数设置成no  
  RUN yum install -y openssh-server sudo  
  RUN sed -i 's/UsePAM yes/UsePAM no/g' /etc/ssh/sshd_config  
  #安装openssh-clients
  RUN yum  install -y openssh-clients
   
  # 添加测试用户root，密码root，并且将此用户添加到sudoers里  
  RUN echo "root:root" | chpasswd  
  RUN echo "root   ALL=(ALL)       ALL" >> /etc/sudoers  
  # 下面这两句比较特殊，在centos6上必须要有，否则创建出来的容器sshd不能登录  
  RUN ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key  
  RUN ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key  
   
  # 启动sshd服务并且暴露22端口  
  RUN mkdir /var/run/sshd  
  EXPOSE 22  
  CMD ["/usr/sbin/sshd", "-D"]
  ```

- 构建命令： 

  ```sh
  docker build -t="centos-ssh-root" .
  ```

  

- 基于这个镜像再构建一个带有jdk的镜像

  ```sh
  mkdir centos-ssh-root-jdk 
  cd centos-ssh-root-jdk
  
  导入jdk到当前目录
  
  
  vi Dockerfile
  
  ```

  ```sh
  FROM centos-ssh-root
  ADD jdk-8u144-linux-x64.tar.gz /usr/local/
  RUN mv /usr/local/jdk1.8.0_144 /usr/local/jdk1.8
  ENV JAVA_HOME /usr/local/jdk1.8
  ENV PATH $JAVA_HOME/bin:$PATH
  ```

  ```sh
  构建命令： 
  docker build -t="centos-ssh-root-jdk" .
  ```

- 基于这个jdk镜像再构建一个带有hadoop的镜像 

  ```sh
  mkdir centos-ssh-root-jdk-hadoop 
  cd centos-ssh-root-jdk-hadoop 
  导入hadoop包到当前目录
  
  vi Dockerfile
  ```

  ```sh
  FROM centos-ssh-root-jdk
  ADD hadoop-2.7.2.tar.gz /usr/local
  RUN mv /usr/local/hadoop-2.7.2 /usr/local/hadoop
  ENV HADOOP_HOME /usr/local/hadoop
  ENV PATH $HADOOP_HOME/bin:$PATH
  ```

  ```sh
  构建命令： 
  docker build -t="centos-ssh-root-jdk-hadoop" .
  ```

- 保存一个克隆的镜像docker-jdk-hadoop

- 搭建hadoop分布式集群 
  1：集群规划 
  准备搭建一个具有三个节点的集群，一主两从 
  主节点：hadoop0 ip：192.168.2.10 
  从节点1：hadoop1 ip：192.168.2.11 
  从节点2：hadoop2 ip：192.168.2.12

  但是由于docker容器重新启动之后ip会发生变化，所以需要我们给docker设置固定ip。使用pipework给docker容器设置固定ip 
  2：启动三个容器，分别作为hadoop0 hadoop1 hadoop2 
  在宿主机上执行下面命令，给容器设置主机名和容器的名称，并且在hadoop0中对外开放端口50070 和8088

  ```sh
  docker network create --subnet=192.168.2.1/24 network_my
  
  # docker network ls 
  NETWORK ID          NAME                DRIVER              SCOPE
  1fb22da7d8a3        bridge              bridge              local
  fe259334b842        host                host                local
  8c5971ff48d8        network_my          bridge              local
  3aaf0356c19c        none                null                local
  
  
  ```

  

```sh
docker run --name hadoop0 --hostname hadoop0 -d -P -p 50070:50070 -p 8088:8088 centos-ssh-root-jdk-hadoop
 
docker run --name hadoop1 --hostname hadoop1 -d -P centos-ssh-root-jdk-hadoop
 
docker run --name hadoop2 --hostname hadoop2 -d -P centos-ssh-root-jdk-hadoop
```



```sh
docker run --name hadoop0 --hostname hadoop0 --net network_my --ip 192.168.2.10 --add-host hadoop1:192.168.2.11 --add-host hadoop2:192.168.2.12 -d -P -p 50070:50070 -p 8088:8088 centos-ssh-root-jdk-hadoop

docker run --name hadoop1 --hostname hadoop1 --net network_my --ip 192.168.2.11 --add-host hadoop0:192.168.2.10 --add-host hadoop2:192.168.2.12 -d -P centos-ssh-root-jdk-hadoop

docker run --name hadoop2 --hostname hadoop2 --net network_my --ip 192.168.2.12 --add-host hadoop0:192.168.2.10 --add-host hadoop1:192.168.2.11 -d -P centos-ssh-root-jdk-hadoop
```

--hostname ：指定hostname;
--net : 指定网络模式
--ip：指定IP
--add-host ：指定往/etc/hosts添加的host





通过docker inspect可以查看容器ip为192.168.0.2，关闭容器并重启，发现容器ip并未发生改变





#### 上面启动时固定了ip下面步骤可以省略

- 给这三台容器设置固定IP

  ```sh
  yum install git -y
  
  #先下载
  $ git clone https://github.com/jpetazzo/pipework.git
  $ cp pipework/pipework /usr/local/bin/
  
  #安装bridge-utils
  $ yum -y install bridge-utils
  
  #创建网络
  
  brctl addbr br0
  ip link set dev br0 up
  ip addr add 192.168.2.1/24 dev br0
  ```

  

- 给容器设置固定ip

  ```sh
  
  pipework br0 hadoop0 192.168.2.10/24
  pipework br0 hadoop1 192.168.2.11/24
  pipework br0 hadoop2 192.168.2.12/24
  ```

  

- ## 验证一下，分别ping三个ip，能ping通就说明没问题

- 配置hadoop集群 
  先连接到hadoop0上， 
  使用命令

  ```sh
  docker exec -it hadoop0 /bin/bash
  ```

  

- 下面的步骤就是hadoop集群的配置过程 
  1：设置主机名与ip的映射，修改三台容器：vi /etc/hosts 
  添加下面配置

  ```sh
  192.168.2.10    hadoop0
  192.168.2.11    hadoop1
  192.168.2.12    hadoop2
  ```

  

- 设置ssh免密码登录 
  在hadoop0上执行下面操作

  ```sh
  cd  ~
  mkdir .ssh
  cd .ssh
  ssh-keygen -t rsa
  ssh-copy-id -i localhost
  ssh-copy-id -i hadoop0
  ssh-copy-id -i hadoop1
  ssh-copy-id -i hadoop2
  在hadoop1上执行下面操作
  cd  ~
  cd .ssh
  ssh-keygen -t rsa
  ssh-copy-id -i localhost
  ssh-copy-id -i hadoop1
  在hadoop2上执行下面操作
  cd  ~
  cd .ssh
  ssh-keygen -t rsa
  ssh-copy-id -i localhost
  ssh-copy-id -i hadoop2
  ```

  

- 在hadoop0上修改hadoop的配置文件 
  进入到/usr/local/hadoop/etc/hadoop目录 
  修改目录下的配置文件core-site.xml、hdfs-site.xml、yarn-site.xml、mapred-site.xml 

- hadoop-env.sh

  ```sh
  export JAVA_HOME=/usr/local/jdk1.8
  ```

  

- core-site.xml

  ```sh
  <configuration>
          <property>
                  <name>fs.defaultFS</name>
                  <value>hdfs://hadoop0:9000</value>
          </property>
          <property>
                  <name>hadoop.tmp.dir</name>
                  <value>/usr/local/hadoop/tmp</value>
          </property>
           <property>
                   <name>fs.trash.interval</name>
                   <value>1440</value>
          </property>
  </configuration>
  ```

  

- hdfs-site.xml

  ```sh
  <configuration>
      <property>
          <name>dfs.replication</name>
          <value>1</value>
      </property>
      <property>
          <name>dfs.permissions</name>
          <value>false</value>
      </property>
  </configuration>
  ```

  

- yarn-site.xml

  ```sh
  <configuration>
          <property>
                  <name>yarn.nodemanager.aux-services</name>
                  <value>mapreduce_shuffle</value>
          </property>
          <property> 
                  <name>yarn.log-aggregation-enable</name> 
                  <value>true</value> 
          </property>
  </configuration>
  ```

  

- 修改文件名：mv mapred-site.xml.template mapred-site.xml 
- vi mapred-site.xml

```sh
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

- 格式化 ,进入到/usr/local/hadoop目录下 

  ```sh
  bin/hdfs namenode -format
  
  注意：在执行的时候会报错，是因为缺少which命令，安装即可
   
  执行下面命令安装
  yum install -y which
  
  
  Storage directory /usr/local/hadoop/tmp/dfs/name has been successfully formatted.
  看到这个成功
  ```

  

- 格式化操作不能重复执行。如果一定要重复格式化，带参数-force即可。

- 启动伪分布hadoop

  ```sh
  sbin/start-all.sh
  ```

  ```sh
  [root@hadoop0 hadoop]# sbin/start-all.sh
  This script is Deprecated. Instead use start-dfs.sh and start-yarn.sh
  Starting namenodes on [hadoop0]
  hadoop0: starting namenode, logging to /usr/local/hadoop/logs/hadoop-root-namenode-hadoop0.out
  localhost: starting datanode, logging to /usr/local/hadoop/logs/hadoop-root-datanode-hadoop0.out
  Starting secondary namenodes [0.0.0.0]
  The authenticity of host '0.0.0.0 (0.0.0.0)' can't be established.
  RSA key fingerprint is SHA256:j5368dF35ORFeDG1QNTpkBDjbcyryWMVkftirtYny3k.
  RSA key fingerprint is MD5:86:eb:1d:02:78:e6:42:07:f5:57:90:36:67:1b:17:df.
  Are you sure you want to continue connecting (yes/no)? yes
  0.0.0.0: Warning: Permanently added '0.0.0.0' (RSA) to the list of known hosts.
  0.0.0.0: starting secondarynamenode, logging to /usr/local/hadoop/logs/hadoop-root-secondarynamenode-hadoop0.out
  starting yarn daemons
  starting resourcemanager, logging to /usr/local/hadoop/logs/yarn--resourcemanager-hadoop0.out
  localhost: starting nodemanager, logging to /usr/local/hadoop/logs/yarn-root-nodemanager-hadoop0.out
  [root@hadoop0 hadoop]# jps
  416 NameNode
  917 NodeManager
  682 SecondaryNameNode
  827 ResourceManager
  1147 Jps
  511 DataNode
  ```

  

- 使用jps，检查进程是否正常启动？能看到下面几个进程表示伪分布启动成功

  ```sh
  [root@hadoop0 hadoop]# jps
  3267 SecondaryNameNode
  3003 NameNode
  3664 Jps
  3397 ResourceManager
  3090 DataNode
  3487 NodeManager
  ```

  

- 停止伪分布hadoop

- ```
  sbin/stop-all.sh
  ```

- 指定nodemanager的地址，修改文件yarn-site.xml

  ```sh
  
  <property>
      <description>The hostname of the RM.</description>
      <name>yarn.resourcemanager.hostname</name>
      <value>hadoop0</value>
    </property>
  ```

  

- 修改hadoop0中hadoop的一个配置文件etc/hadoop/slaves 
  删除原来的所有内容，修改为如下

  ```sh
  hadoop1
  hadoop2
  ```

  

- 在hadoop0中执行命令

  ```sh
  scp  -rq /usr/local/hadoop   hadoop1:/usr/local
  scp  -rq /usr/local/hadoop   hadoop2:/usr/local
  ```

  注意：是因为两个从节点缺少which命令，安装即可

  分别在两个从节点执行下面命令安装

  ```
  docker exec -it hadoop1 /bin/bash
  yum install -y which
  docker exec -it hadoop2 /bin/bash
  yum install -y which
  ```

  

- 启动hadoop分布式集群服务

  ```sh
  sbin/start-all.sh
  ```

  

- 验证集群是否正常 
  首先查看进程： 
  Hadoop0上需要有这几个进程

```sh
[root@hadoop0 hadoop]# jps
4643 Jps
4073 NameNode
4216 SecondaryNameNode
4381 ResourceManager



[root@hadoop1 hadoop]# jps
715 NodeManager
849 Jps
645 DataNode


[root@hadoop2 hadoop]# jps
456 NodeManager
589 Jps
388 DataNode
```

- 使用程序验证集群服务 ,创建一个本地文件

  ```sh
  cd /usr/local/hadoop/
  
  vi a.txt
  hello you
  hello me
  ```

  

- 上传a.txt到hdfs上

  ```sh
  hdfs dfs -put a.txt /
  ```

  

- 执行wordcount程序

  ```sh
  
  cd /usr/local/hadoop/share/hadoop/mapreduce
  
  
  hadoop jar hadoop-mapreduce-examples-2.7.2.jar wordcount /a.txt /out
  ```

  

- 查看程序执行结果

  ```sh
  hdfs dfs -text /out/part-r-00000
  
  
  [root@hadoop0 mapreduce]# hdfs dfs -text /out/part-r-00000
  hello	2
  me	1
  you	1
  
  成功
  
  ```

  

- 通过浏览器访问集群的服务 
  由于在启动hadoop0这个容器的时候把50070和8088映射到宿主机的对应端口上了

- 退出到宿主机，docker ps 看到正在运行了hadoop，浏览器访问

  ```
  http://192.168.0.106:50070
  http://192.168.0.106:8088
  ```

- 集群节点重启 
  停止三个容器，在宿主机上执行下面命令

  ```sh
  
  docker stop hadoop0
  docker stop hadoop1
  docker stop hadoop2
  ```

  

- 容器停止之后，之前设置的固定ip也会消失，重新再使用这几个容器的时候还需要重新设置固定ip 
  先把之前停止的三个容器开起来

  ```sh
  docker start hadoop0
  docker start hadoop1
  docker start hadoop2
  ```

  

- 在宿主机上执行下面命令重新给容器设置固定ip

  ```
  pipework br0 hadoop0 192.168.2.10/24
  pipework br0 hadoop1 192.168.2.11/24
  pipework br0 hadoop2 192.168.2.12/24
  ```

- 还需要重新在容器中配置主机名和ip的映射关系，每次都手工写比较麻烦 

- 写一个脚本，runhosts.sh

  ```
  #!/bin/bash
  echo 192.168.2.10       hadoop0 >> /etc/hosts
  echo 192.168.2.11       hadoop1 >> /etc/hosts
  echo 192.168.2.12       hadoop2 >> /etc/hosts
  ```

- 添加执行权限，`chmod +x runhosts.sh` 
  把这个脚本拷贝到所有节点，并且分别执行这个脚本

  ```
  scp runhosts.sh  hadoop1:~
  scp runhosts.sh  hadoop2:~
  ```

- 执行脚本的命令 `./runhosts.sh`
- 查看/etc/hosts文件中是否添加成功 

- 注意：有一些docker版本中不会在hosts文件中自动生成下面这些映射，所以我们才在这里手工给容器设置固定ip，并设置主机名和ip的映射关系。

```sh
172.17.0.25     hadoop0
172.17.0.25     hadoop0.bridge
172.17.0.26     hadoop1
172.17.0.26     hadoop1.bridge
172.17.0.27     hadoop2
172.17.0.27     hadoop2.bridge



sbin/start-all.sh
```

