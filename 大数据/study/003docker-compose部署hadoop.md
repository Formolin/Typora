## 下载docker镜像

首先下载需要使用的五个docker镜像

```javascript
docker pull bde2020/hadoop-namenode:1.1.0-hadoop2.7.1-java8
docker pull bde2020/hadoop-datanode:1.1.0-hadoop2.7.1-java8
docker pull bde2020/hadoop-resourcemanager:1.1.0-hadoop2.7.1-java8
docker pull bde2020/hadoop-historyserver:1.1.0-hadoop2.7.1-java8
docker pull bde2020/hadoop-nodemanager:1.1.0-hadoop2.7.1-java8
```

## 设置hadoop配置参数

创建 hadoop.env 文件，内容如下：

```javascript
CORE_CONF_fs_defaultFS=hdfs://namenode:8020
CORE_CONF_hadoop_http_staticuser_user=root
CORE_CONF_hadoop_proxyuser_hue_hosts=*
CORE_CONF_hadoop_proxyuser_hue_groups=*

HDFS_CONF_dfs_webhdfs_enabled=true
HDFS_CONF_dfs_permissions_enabled=false

YARN_CONF_yarn_log___aggregation___enable=true
YARN_CONF_yarn_resourcemanager_recovery_enabled=true
YARN_CONF_yarn_resourcemanager_store_class=org.apache.hadoop.yarn.server.resourcemanager.recovery.FileSystemRMStateStore
YARN_CONF_yarn_resourcemanager_fs_state___store_uri=/rmstate
YARN_CONF_yarn_nodemanager_remote___app___log___dir=/app-logs
YARN_CONF_yarn_log_server_url=http://historyserver:8188/applicationhistory/logs/
YARN_CONF_yarn_timeline___service_enabled=true
YARN_CONF_yarn_timeline___service_generic___application___history_enabled=true
YARN_CONF_yarn_resourcemanager_system___metrics___publisher_enabled=true
YARN_CONF_yarn_resourcemanager_hostname=resourcemanager
YARN_CONF_yarn_timeline___service_hostname=historyserver
YARN_CONF_yarn_resourcemanager_address=resourcemanager:8032
YARN_CONF_yarn_resourcemanager_scheduler_address=resourcemanager:8030
YARN_CONF_yarn_resourcemanager_resource___tracker_address=resourcemanager:8031
```

## 创建docker-compose文件

创建 docker-compose.yml 文件，内如如下：

```javascript
version: "2"

services:
  namenode:
    image: bde2020/hadoop-namenode:1.1.0-hadoop2.7.1-java8
    container_name: namenode
    volumes:
      - hadoop_namenode:/hadoop/dfs/name
    environment:
      - CLUSTER_NAME=test
    env_file:
      - ./hadoop.env

  resourcemanager:
    image: bde2020/hadoop-resourcemanager:1.1.0-hadoop2.7.1-java8
    container_name: resourcemanager
    depends_on:
      - namenode
      - datanode1
      - datanode2
      - datanode3
    env_file:
      - ./hadoop.env

  historyserver:
    image: bde2020/hadoop-historyserver:1.1.0-hadoop2.7.1-java8
    container_name: historyserver
    depends_on:
      - namenode
      - datanode1
      - datanode2
      - datanode3
    volumes:
      - hadoop_historyserver:/hadoop/yarn/timeline
    env_file:
      - ./hadoop.env

  nodemanager1:
    image: bde2020/hadoop-nodemanager:1.1.0-hadoop2.7.1-java8
    container_name: nodemanager1
    depends_on:
      - namenode
      - datanode1
      - datanode2
      - datanode3
    env_file:
      - ./hadoop.env

  datanode1:
    image: bde2020/hadoop-datanode:1.1.0-hadoop2.7.1-java8
    container_name: datanode1
    depends_on:
      - namenode
    volumes:
      - hadoop_datanode1:/hadoop/dfs/data
    env_file:
      - ./hadoop.env

  datanode2:
    image: bde2020/hadoop-datanode:1.1.0-hadoop2.7.1-java8
    container_name: datanode2
    depends_on:
      - namenode
    volumes:
      - hadoop_datanode2:/hadoop/dfs/data
    env_file:
      - ./hadoop.env

  datanode3:
    image: bde2020/hadoop-datanode:1.1.0-hadoop2.7.1-java8
    container_name: datanode3
    depends_on:
      - namenode
    volumes:
      - hadoop_datanode3:/hadoop/dfs/data
    env_file:
      - ./hadoop.env

volumes:
  hadoop_namenode:
  hadoop_datanode1:
  hadoop_datanode2:
  hadoop_datanode3:
  hadoop_historyserver:
```

## 创建并启动hadoop集群

```javascript
docker-compose up
```

启动hadoop集群后，可以使用下面命令查看一下hadoop集群的容器信息

```javascript
# 查看集群包含的容器，以及export的端口号
sudo docker-compose ps
     Name                Command           State     Ports
------------------------------------------------------------
datanode1         /entrypoint.sh /run.sh   Up      50075/tcp
datanode2         /entrypoint.sh /run.sh   Up      50075/tcp
datanode3         /entrypoint.sh /run.sh   Up      50075/tcp
historyserver     /entrypoint.sh /run.sh   Up      8188/tcp
namenode          /entrypoint.sh /run.sh   Up      50070/tcp
nodemanager1      /entrypoint.sh /run.sh   Up      8042/tcp
resourcemanager   /entrypoint.sh /run.sh   Up      8088/tc

# 查看namenode的IP地址
sudo docker inspect namenode  | grep IPAddress    
```

也可以通过 http://<namenode ip>:50070 查看集群状态。

访问：访问时使用容器的IP替换下面域名

```
Namenode: http://namenode:50070/dfshealth.html#tab-overview
History server: http://historyserver:8188/applicationhistory
Datanode: http://datanode:50075/
Nodemanager: http://nodemanager:8042/node
Resource manager: http://resourcemanager:8088/
```

我们首先需要登录到集群中的一个节点，这里我们就登录到namenode节点。

```javascript
sudo docker exec -it namenode /bin/bash
```

准备数据并提交作业

```javascript
cd /opt/hadoop-2.7.1

# 创建用户目录
hdfs dfs -mkdir /user
hdfs dfs -mkdir /user/root

# 准备数据
hdfs dfs -mkdir input
hdfs dfs -put etc/hadoop/*.xml input

# 提交作业
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.1.jar grep input output 'dfs[a-z.]+'

# 查看作业执行结果
hdfs dfs -cat output/*
```

清空数据

```javascript
hdfs dfs -rm input/*
hdfs dfs -rmdir input/
hdfs dfs -rm output/*
hdfs dfs -rmdir output/
```

## 停止集群

可以通过CTRL+C来终止集群，也可以通过 "sudo docker-compose stop"。

停止集群后，创建的容器并不会被删除，此时可以使用 "sudo docker-compose rm" 来删除已经停止的容器。也可以使用 "sudo docker-compose down" 来停止并删除容器。

删除容器后，使用 “sudo docker volume ls” 可以看到上面集群使用的volume信息，我们可以使用 “sudo docker rm <volume>” 来删除。

# docker-compose自定义网络，固定容器ip地址

由于默认的bridge桥接网络，重启容器后会改变ip地址。在一些场景下我们希望固定容器IP地址。
docker-compose是docker的一个编排工具，相对于命令模式创建网络，容器等。使用配置文件相对来说更方便，可追溯问题。

直接粘贴docker-compose.yml文件

```
version: '2'
services:
   nginx:
      image: nginx:1.13.12
      container_name: nginx
      restart: always
      tty: true
      networks:
         extnetwork:
            ipv4_address: 172.19.0.2
 
networks:
   extnetwork:
      ipam:
         config:
         - subnet: 172.19.0.0/16
           gateway: 172.19.0.1

```

说明：
gateway是网关地址
subnet是网络号段
extnetwork是自定义的网络名称

上面配置中我们的nginx容器固定ip为172.19.0.2

示例，自定义网络模式：

```sh
version: '2'
services:
   nginx:
      image: nginx:1.13.12
      container_name: nginx
      restart: always
      networks:
         extnetwork:
      ports:
         - 80:80
      volumes:
         - '/nginx/conf.d:/etc/nginx/conf.d'
   nginx2:
      image: nginx:1.13.12
      container_name: nginx2
      restart: always
      networks:
         extnetwork:
            ipv4_address: 172.19.0.2
         
   db:
     image: mysql:5.7
     container_name: db
     volumes:
       - /var/lib/mysql:/var/lib/mysql
     restart: always
     networks:
         extnetwork:
     ports:
         - 3306:3306
     environment:
       MYSQL_ROOT_PASSWORD: wordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress      
   
   wordpress:
      image: wordpress:latest
      container_name: wordpress
      depends_on:
         - db
      ports:
         - "8000:80"
      restart: always
      networks:
         extnetwork:
      environment:
         WORDPRESS_DB_HOST: db:3306
         WORDPRESS_DB_NAME: wordpress
         WORDPRESS_DB_USER: wordpress
         WORDPRESS_DB_PASSWORD: wordpress
networks:
   extnetwork:
      ipam:
         config:
         - subnet: 172.19.0.0/16
           gateway: 172.19.0.1

```

