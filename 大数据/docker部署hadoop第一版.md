- 在docker上安装ubuntu

- 创建共享文件夹

  ```sh
  用于本地与docker中的Ubuntu系统传输数据
  本地路径
      /home/spc/build
  ```

  

- 进入docker中的ubuntu

  ```sh
  sudo docker run -it -v /home/spc/build:/root/build --name ubuntu ubuntu
  ```

  

- 更新docker中的ubuntu安装需要的软件

  ```sh
  apt-get update 更新
  apt-get install vim 安装vim
  apt-get install ssh 安装ssh以便分布式hadoop连接
  ```

- 把下面命令写入~/.bashrc文件，这样每次登录Ubuntu系统时，都能自动启动sshd服务

  ```sh
  vim ~/.bashrc
      在最后一行添加
      /etc/init.d/ssh start
  ```

- 配置ssh无密码连接本地ssh

  ```sh
  ssh-keygen -t rsa
  cd ~/.ssh
  ll
  cat id_rsa.pub >> authorized_keys
  之后使用dsa
  ssh-keygen -t dsa
  cat id_dsa.pub >> authorized_keys
  ```

- 安装JDK

  ```sh
  apt-get install default-jdk
  
  安装后的路径
          /usr/lib/jvm/java-1.8.0-openjdk-amd64
          /usr/lib/jvm/java-8-openjdk-amd64
  添加环境变量
  vi ~/.bashrc
  
  添加环境变量
  
  
  export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
  export PATH=$PATH:$JAVA_HOME/bin
  
  保存后 ，执行下面命令更新
  
  
  source ~/.bashrc
  ```

  

- 将当前容器保存为镜像

  ```sh
  exit退出容器
  docker commit 进入容器内的id ubuntu-jdk
  ```

- 启动保存的镜像

  ```sh
  docker run -it -v /home/spc/build:/root/build --name hadooptext ubuntu-jdk bash                                
  ```

- 下载hadoop准备安装, 将下载好的hadoop tar.gz文件放到共享文件夹

- 在启动的容器中安装hadoop

  ```sh
  进入/root/build
  解压tar -zxvf haddop-…… -C /usr/local/
  ```

-  查看版本检查安装

  ```sh
  root@d83136b16ff1:/usr/local/hadoop-2.7.5# ./bin/hadoop version
  ```

- 在hadoop-env.sh中修改

  ```sh
  cd /etc/hadoop
  
  vi hadoop-env.sh
  
  export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64/ 这里换成自己的java路径
  ```

  

- 修改core-site.xml

  vi core-site.xml

  ```xml
  <configuration>
      <property>
      <name>hadoop.tmp.dir</name>
      <value>file:/usr/local/hadoop/tmp</value>
      <description>Abase for other temporary directories.</description>
      </property>
      <property>
      <name>fs.defaultFS</name>
      <value>hdfs://master:9000</value>
      </property>
  </configuration>
  
  ```

- 修改hdfs-site.xml

  ```xml
  <property>
              <name>dfs.namenode.name.dir</name>
              <value>file:/usr/local/hadoop/namenode_dir</value>
          </property>
          <property>
              <name>dfs.datanode.data.dir</name>
              <value>file:/usr/local/hadoop/datanode_dir</value>
          </property>
          <property>
              <name>dfs.replication</name>
              <value>3</value>
          </property>
  
  ```

  

- 修改mapred-site.xml(复制mapred-site.xml.template,再修改文件名)

  ```sh
  cp mapred-site.xml.template mapred-site.xml
  
  vi mapred-site.xml
  
   <property>
              <name>mapreduce.framework.name</name>
              <value>yarn</value>
          </property>
  ```

  

- 修改yarn-site.xml

  ```xml
     <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
            </property>
            <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>master</value>
            </property>
  ```

  

  

- 保存配置好的容器

  ```sh
  docker commit 441e8d311da8 hadoop
  ```

  

- 利用配置好的镜像启动创建Hadoop集群,首先启动三个终端

  ```sh
  docker run -it -h master --name master hadoop
  
  docker run -it -h slave01 --name slave01 hadoop
  
  docker run -it -h slave02 --name slave02 hadoop
  ```

  

- 进入master修改hosts

  ```sh
  docker exec -it master bash
  ```

  

- 修改/etc/hosts

  ```sh
  root@master:/# vim /etc/hosts
  
  
  127.0.0.1   localhost
  ::1 localhost ip6-localhost ip6-loopback
  fe00::0 ip6-localnet
  ff00::0 ip6-mcastprefix
  ff02::1 ip6-allnodes
  ff02::2 ip6-allrouters
  172.17.0.2  master
  172.17.0.3  slave01
  172.17.0.4  slave02
  
  ```

  

- 使用ssh检查master是否可以连接slave01和slave02

  ```sh
  root@master:/# ssh slave01
  ```

  

- 修改master的slaves文件

  ```sh
  cd /usr/local/hadoop-2.7.2/etc/hadoop/
  
  vi slaves
  
  slave01
  slave02
  
  
  ```

  

- 启动集群

  ```sh
  进入 hadoop-2.7.2
  
  bin/hdfs namenode -format  (第一次格式化)
  
  进入sbin
  
  ./start-all.sh
  
  过程中输入三次yes
  
  
  ```

  

- 在master和slave01和slave02上使用jps查看结果

  ```sh
  root@master:/usr/local/hadoop-2.7.2/sbin# jps
  532 ResourceManager
  791 Jps
  189 NameNode
  381 SecondaryNameNode
  
  
  root@slave01:/# jps
  89 DataNode
  187 NodeManager
  285 Jps
  
  root@slave02:/# jps
  52 DataNode
  149 NodeManager
  247 Jps
  ```

  

- 测试命令

  在bin目录下

  ```sh
  root@master:/usr/local/hadoop-2.7.2/bin# dfs ls /user/hadoop/input
  bash: dfs: command not found
  
  
  修改~/.bashrc
  
  vi ~/.bashrc
  
  最后添加
  export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME
  
  
  
  source ~/.bashrc
  ```

  

- 关闭集群

  ```sh
  root@master:/usr/local/hadoop-2.7.5/sbin# ./stop-all.sh 
  ```

  