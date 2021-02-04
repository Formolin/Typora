# Zookeeper注册中心

Zookeeper 部署有三种方式，单机模式、集群模式、伪集群模式，以下采用 Docker 的方式部署

**注意：** 集群为大于等于3个奇数，如 3、5、7,不宜太多，集群机器多了选举和数据同步耗时长，不稳定。

## 基于Docker集群部署

准备 3 台 Ubuntu Server 系统，并分别配置 Zookeeper

### docker-compose.yml

```text
version: '3.1'
services:
    zoo1:
        image: zookeeper
        restart: always
        hostname: zoo1
        ports:
            - 2181:2181
        environment:
            ZOO_MY_ID: 1
            ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888

    zoo2:
        image: zookeeper
        restart: always
        hostname: zoo2
        ports:
            - 2182:2181
        environment:
            ZOO_MY_ID: 2
            ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888

    zoo3:
        image: zookeeper
        restart: always
        hostname: zoo3
        ports:
            - 2183:2181
        environment:
            ZOO_MY_ID: 3
            ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
```

- 在docker目录下新建zookeeper目录
- 新建yml文件
- 赋值上面配置文件
- 运行命令docker-compose up -d

验证是否安装成功

- 分别以交互方式进入容器查看

```text
docker exec -it zookeeper_zoo1_1 /bin/bash
```

```text
docker exec -it zookeeper_zoo2_1 /bin/bash
```

```text
docker exec -it zookeeper_zoo3_1 /bin/bash
```

- 使用服务端工具检查服务器状态

```text
root@UbuntuBase:/usr/local/docker/zookeeper# docker exec -it zookeeper_zoo1_1 /bin/bash
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: follower
```

```text
root@UbuntuBase:/usr/local/docker/zookeeper# docker exec -it zookeeper_zoo2_1 /bin/bash
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: follower
```

```text
root@UbuntuBase:/usr/local/docker/zookeeper# docker exec -it zookeeper_zoo3_1 /bin/bash
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: leader
```

从上面的验证结果可以看出：zoo1 为跟随者，zoo2 为跟随者，zoo3 为领导者

- 配置文件,根目录conf下的zoo.cfg

  ```sh
  clientPort=2181
  dataDir=/data
  dataLogDir=/datalog
  tickTime=2000
  initLimit=5
  syncLimit=2
  autopurge.snapRetainCount=3
  autopurge.purgeInterval=0
  maxClientCnxns=60
  server.1=zoo1:2888:3888
  server.2=zoo2:2888:3888
  server.3=zoo3:2888:3888
  ```

## Zookeeper 的三种端口号

- 2181：客户端连接 Zookeeper 集群使用的监听端口号
- 3888：选举 leader 使用
- 2888：集群内机器通讯使用（Leader 和 Follower 之间数据同步使用的端口号，Leader 监听此端口）

## Zookeeper 单机模式配置文件

![zookeeper](https://ws3.sinaimg.cn/large/006tNc79ly1g2rswr50jwj30pu0qsgps.jpg)

配置文件路径：`/conf/zoo.cfg`

新建data文件夹，修改cfg配置文件

```text

dataDir=../data

```

- clientPort：这个端口就是客户端连接 Zookeeper 服务器的端口，Zookeeper 会监听这个端口，接受客户端的访问请求。

- dataDir：Zookeeper 保存数据的目录。

- dataLogDir：Zookeeper 保存日志的目录。

- tickTime：这个时间是作为 Zookeeper 服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每隔 tickTime 时间就会发送一个心跳。

- 启动zookeeper

  - 在bin目录下执行zkServer.sh start

- 进入dubbo-admin的src下

- 修改application.properties文件

  ```sh
  dubbo.registry.address=zookeeper://127.0.0.1:2181
  ```

- Mvn clean package

- 执行 java -jar jar包名

- 访问localhost：7001

- 账户密码默认root

## Zookeeper 集群模式配置文件

配置文件路径：`/conf/zoo.cfg`

```text
clientPort=2181
dataDir=/data
dataLogDir=/datalog
tickTime=2000
initLimit=5
syncLimit=2
autopurge.snapRetainCount=3
autopurge.purgeInterval=0
maxClientCnxns=60
server.1=192.168.0.1:2888:3888
server.2=192.168.0.2:2888:3888
server.3=192.168.0.3:2888:3888
```

- initLimit：配置 Zookeeper 接受客户端（这里所说的客户端不是用户连接 Zookeeper 服务器的客户端，而是 Zookeeper 服务器集群中连接到 Leader 的 Follower 服务器）初始化连接时最长能忍受多少个心跳时间间隔数。当已经超过 initLimit（默认为 10） 个心跳的时间（也就是 tickTime）长度后 Zookeeper 服务器还没有收到客户端的返回信息，那么表明这个客户端连接失败。总的时间长度就是 `5 * 2000 = 10` 秒
- syncLimit：配置 Leader 与 Follower 之间发送消息，请求和应答时间长度，最长不能超过多少个 tickTime 的时间长度，总的时间长度就是 `2 * 2000 = 4` 秒
- 定时清理（Zookeeper 从 3.4.0 开始提供了自动清理快照和事务日志的功能）以下两个参数配合使用：
  - autopurge.purgeInterval：指定了清理频率，单位是小时，需要填写一个 1 或更大的整数，默认是 0，表示不开启自己清理功能。
  - autopurge.snapRetainCount：指定了需要保留的文件数目。默认是保留 3 个。
- maxClientCnxns：限制连接到 Zookeeper 的客户端的数量，限制并发连接的数量，它通过 IP 来区分不同的客户端。此配置选项可以用来阻止某些类别的 Dos 攻击。将它设置为 0 或者忽略而不进行设置将会取消对并发连接的限制。
- server.A=B：C：D：其中 A 是一个数字，表示这个是第几号服务器。B 是这个服务器的 IP 地址。C 表示的是这个服务器与集群中的 Leader 服务器交换信息的端口(`2888`)；D 表示的是万一集群中的 Leader 服务器挂了，需要一个端口来重新进行选举，选出一个新的 Leader，而这个端口就是用来执行选举时服务器相互通信的端口(`3888`)。如果是伪集群的配置方式，由于 B 都是一样，所以不同的 Zookeeper 实例通信端口号不能一样，所以要给它们分配不同的端口号。

**注意：** `server.A` 中的 A 是在 `dataDir` 配置的目录中创建一个名为 `myid` 的文件里的值（如：1）

## Zookeeper 常用命令

### zkServer

- 启动服务

```text
./zkServer.sh start
```

- 停止服务

```text
./zkServer.sh stop
```

- 重启服务

```text
./zkServer.sh restart
```

- 执行状态

```text
./zkServer.sh status
```

### zkClient

- 客户端连接服务器并进入 Bash 模式

```text
./zkCli.sh -server <ip>:<port>

# 命令参数
ZooKeeper -server host:port cmd args
	stat path [watch]
	set path data [version]
	ls path [watch]
	delquota [-n|-b] path
	ls2 path [watch]
	setAcl path acl
	setquota -n|-b val path
	history 
	redo cmdno
	printwatches on|off
	delete path [version]
	sync path
	listquota path
	rmr path
	get path [watch]
	create [-s] [-e] path data acl
	addauth scheme auth
	quit 
	getAcl path
	close 
	connect host:port
```

- 创建节点（Bash 模式）

```text
create /test "hello zookeeper"
```

- 查询节点（Bash 模式）

```text
get /test

# 输出如下
Hello Zookeeper
cZxid = 0x100000004
ctime = Fri Oct 19 05:11:47 GMT 2018
mZxid = 0x100000004
mtime = Fri Oct 19 05:11:47 GMT 2018
pZxid = 0x100000004
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 15
numChildren = 0
```

- 删除节点（Bash 模式）

```text
delete /test
```