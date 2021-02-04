# Redis

Redis 是用 C 语言开发的一个开源的高性能键值对（key-value）数据库。它通过提供多种键值数据类型来适应不同场景下的存储需求，目前为止 Redis 支持的键值数据类型如下：

- 字符串类型
- 散列类型
- 列表类型
- 集合类型
- 有序集合类型

## Redis 的应用场景

- 缓存（数据查询、短连接、新闻内容、商品内容等等）
- 分布式集群架构中的 session 分离
- 聊天室的在线好友列表
- 任务队列（秒杀、抢购、12306 等等）
- 应用排行榜
- 网站访问统计
- 数据过期处理（可以精确到毫秒）

## Redis HA 方案

HA(High Available，高可用性群集)机集群系统简称，是保证业务连续性的有效解决方案，一般有两个或两个以上的节点，且分为活动节点及备用节点。通常把正在执 行业务的称为活动节点，而作为活动节点的一个备份的则称为备用节点。当活动节点出现问题，导致正在运行的业务（任务）不能正常运行时，备用节点此时就会侦测到，并立即接续活动节点来执行业务。从而实现业务的不中断或短暂中断。

Redis 一般以主/从方式部署（这里讨论的应用从实例主要用于备份，主实例提供读写）该方式要实现 HA 主要有如下几种方案：

- **keepalived：** 通过 keepalived 的虚拟 IP，提供主从的统一访问，在主出现问题时， 通过 keepalived 运行脚本将从提升为主，待主恢复后先同步后自动变为主，该方案的好处是主从切换后，应用程序不需要知道(因为访问的虚拟 IP 不变)，坏处是引入 keepalived 增加部署复杂性，在有些情况下会导致数据丢失
- **zookeeper：** 通过 zookeeper 来监控主从实例， 维护最新有效的 IP， 应用通过 zookeeper 取得 IP，对 Redis 进行访问，该方案需要编写大量的监控代码
- **sentinel：** 通过 Sentinel 监控主从实例，自动进行故障恢复，该方案有个缺陷：因为主从实例地址( IP & PORT )是不同的，当故障发生进行主从切换后，应用程序无法知道新地址，故在 Jedis2.2.2 中新增了对 Sentinel 的支持，应用通过 `redis.clients.jedis.JedisSentinelPool.getResource()` 取得的 Jedis 实例会及时更新到新的主实例地址

![部署逻辑图](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g35c986kgkj30ne0fyq6a.jpg)

**注意：** sentinel 是解决 HA 问题的，cluster 是解决主从复制问题的，不重复，并且经常一起用

## Redis Sentinel 集群部署

Redis 集群可以在一组 redis 节点之间实现高可用性和 sharding。在集群中会有 1 个 master 和多个 slave 节点。当 master 节点失效时，应选举出一个 slave 节点作为新的 master。然而 Redis 本身(包括它的很多客户端)没有实现自动故障发现并进行主备切换的能力，需要外部的监控方案来实现自动故障恢复。

Redis Sentinel 是官方推荐的高可用性解决方案。它是 Redis 集群的监控管理工具，可以提供节点监控、通知、自动故障恢复和客户端配置发现服务。

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g35ca15ks0j30ic07e403.jpg)

## Redis Sentinel 核心配置

```text
# Example sentinel.conf

# *** IMPORTANT ***
#
# By default Sentinel will not be reachable from interfaces different than
# localhost, either use the 'bind' directive to bind to a list of network
# interfaces, or disable protected mode with "protected-mode no" by
# adding it to this configuration file.
#
# Before doing that MAKE SURE the instance is protected from the outside
# world via firewalling or other means.
#
# For example you may use one of the following:
#
# bind 127.0.0.1 192.168.1.1
#
# protected-mode no

# port <sentinel-port>
# The port that this sentinel instance will run on
port 26379

# sentinel announce-ip <ip>
# sentinel announce-port <port>
#
# The above two configuration directives are useful in environments where,
# because of NAT, Sentinel is reachable from outside via a non-local address.
#
# When announce-ip is provided, the Sentinel will claim the specified IP address
# in HELLO messages used to gossip its presence, instead of auto-detecting the
# local address as it usually does.
#
# Similarly when announce-port is provided and is valid and non-zero, Sentinel
# will announce the specified TCP port.
#
# The two options don't need to be used together, if only announce-ip is
# provided, the Sentinel will announce the specified IP and the server port
# as specified by the "port" option. If only announce-port is provided, the
# Sentinel will announce the auto-detected local IP and the specified port.
#
# Example:
#
# sentinel announce-ip 1.2.3.4

# dir <working-directory>
# Every long running process should have a well-defined working directory.
# For Redis Sentinel to chdir to /tmp at startup is the simplest thing
# for the process to don't interfere with administrative tasks such as
# unmounting filesystems.
dir /tmp

# sentinel monitor <master-name> <ip> <redis-port> <quorum>
#
# Tells Sentinel to monitor this master, and to consider it in O_DOWN
# (Objectively Down) state only if at least <quorum> sentinels agree.
#
# Note that whatever is the ODOWN quorum, a Sentinel will require to
# be elected by the majority of the known Sentinels in order to
# start a failover, so no failover can be performed in minority.
#
# Slaves are auto-discovered, so you don't need to specify slaves in
# any way. Sentinel itself will rewrite this configuration file adding
# the slaves using additional configuration options.
# Also note that the configuration file is rewritten when a
# slave is promoted to master.
#
# Note: master name should not include special characters or spaces.
# The valid charset is A-z 0-9 and the three characters ".-_".
sentinel monitor mymaster 127.0.0.1 6379 2

# sentinel auth-pass <master-name> <password>
#
# Set the password to use to authenticate with the master and slaves.
# Useful if there is a password set in the Redis instances to monitor.
#
# Note that the master password is also used for slaves, so it is not
# possible to set a different password in masters and slaves instances
# if you want to be able to monitor these instances with Sentinel.
#
# However you can have Redis instances without the authentication enabled
# mixed with Redis instances requiring the authentication (as long as the
# password set is the same for all the instances requiring the password) as
# the AUTH command will have no effect in Redis instances with authentication
# switched off.
#
# Example:
#
# sentinel auth-pass mymaster MySUPER--secret-0123passw0rd

# sentinel down-after-milliseconds <master-name> <milliseconds>
#
# Number of milliseconds the master (or any attached slave or sentinel) should
# be unreachable (as in, not acceptable reply to PING, continuously, for the
# specified period) in order to consider it in S_DOWN state (Subjectively
# Down).
#
# Default is 30 seconds.
sentinel down-after-milliseconds mymaster 30000

# sentinel parallel-syncs <master-name> <numslaves>
#
# How many slaves we can reconfigure to point to the new slave simultaneously
# during the failover. Use a low number if you use the slaves to serve query
# to avoid that all the slaves will be unreachable at about the same
# time while performing the synchronization with the master.
sentinel parallel-syncs mymaster 1

# sentinel failover-timeout <master-name> <milliseconds>
#
# Specifies the failover timeout in milliseconds. It is used in many ways:
#
# - The time needed to re-start a failover after a previous failover was
#   already tried against the same master by a given Sentinel, is two
#   times the failover timeout.
#
# - The time needed for a slave replicating to a wrong master according
#   to a Sentinel current configuration, to be forced to replicate
#   with the right master, is exactly the failover timeout (counting since
#   the moment a Sentinel detected the misconfiguration).
#
# - The time needed to cancel a failover that is already in progress but
#   did not produced any configuration change (SLAVEOF NO ONE yet not
#   acknowledged by the promoted slave).
#
# - The maximum time a failover in progress waits for all the slaves to be
#   reconfigured as slaves of the new master. However even after this time
#   the slaves will be reconfigured by the Sentinels anyway, but not with
#   the exact parallel-syncs progression as specified.
#
# Default is 3 minutes.
sentinel failover-timeout mymaster 180000

# SCRIPTS EXECUTION
#
# sentinel notification-script and sentinel reconfig-script are used in order
# to configure scripts that are called to notify the system administrator
# or to reconfigure clients after a failover. The scripts are executed
# with the following rules for error handling:
#
# If script exits with "1" the execution is retried later (up to a maximum
# number of times currently set to 10).
#
# If script exits with "2" (or an higher value) the script execution is
# not retried.
#
# If script terminates because it receives a signal the behavior is the same
# as exit code 1.
#
# A script has a maximum running time of 60 seconds. After this limit is
# reached the script is terminated with a SIGKILL and the execution retried.

# NOTIFICATION SCRIPT
#
# sentinel notification-script <master-name> <script-path>
# 
# Call the specified notification script for any sentinel event that is
# generated in the WARNING level (for instance -sdown, -odown, and so forth).
# This script should notify the system administrator via email, SMS, or any
# other messaging system, that there is something wrong with the monitored
# Redis systems.
#
# The script is called with just two arguments: the first is the event type
# and the second the event description.
#
# The script must exist and be executable in order for sentinel to start if
# this option is provided.
#
# Example:
#
# sentinel notification-script mymaster /var/redis/notify.sh

# CLIENTS RECONFIGURATION SCRIPT
#
# sentinel client-reconfig-script <master-name> <script-path>
#
# When the master changed because of a failover a script can be called in
# order to perform application-specific tasks to notify the clients that the
# configuration has changed and the master is at a different address.
# 
# The following arguments are passed to the script:
#
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
#
# <state> is currently always "failover"
# <role> is either "leader" or "observer"
# 
# The arguments from-ip, from-port, to-ip, to-port are used to communicate
# the old address of the master and the new address of the elected slave
# (now a master).
#
# This script should be resistant to multiple invocations.
#
# Example:
#
# sentinel client-reconfig-script mymaster /var/redis/reconfig.sh

# SECURITY
#
# By default SENTINEL SET will not be able to change the notification-script
# and client-reconfig-script at runtime. This avoids a trivial security issue
# where clients can set the script to anything and trigger a failover in order
# to get the program executed.

sentinel deny-scripts-reconfig yes
```



### 搭建 Redis 集群

搭建一主两从环境，docker-compose.yml 配置如下：

```text
version: '3.1'
services:
  master:
    image: redis
    container_name: redis-master
    ports:
      - 6379:6379

  slave1:
    image: redis
    container_name: redis-slave-1
    ports:
      - 6380:6379
    command: redis-server --slaveof redis-master 6379

  slave2:
    image: redis
    container_name: redis-slave-2
    ports:
      - 6381:6379
    command: redis-server --slaveof redis-master 6379
```

用redis客户端连接测试

## 搭建 Sentinel 集群

我们至少需要创建三个 Sentinel 服务，docker-compose.yml 配置如下：

```text
version: '3.1'
services:
  sentinel1:
    image: redis
    container_name: redis-sentinel-1
    ports:
      - 26379:26379
    command: redis-sentinel /usr/local/etc/redis/sentinel.conf
    volumes:
      - ./sentinel1.conf:/usr/local/etc/redis/sentinel.conf

  sentinel2:
    image: redis
    container_name: redis-sentinel-2
    ports:
      - 26380:26379
    command: redis-sentinel /usr/local/etc/redis/sentinel.conf
    volumes:
      - ./sentinel2.conf:/usr/local/etc/redis/sentinel.conf

  sentinel3:
    image: redis
    container_name: redis-sentinel-3
    ports:
      - 26381:26379
    command: redis-sentinel /usr/local/etc/redis/sentinel.conf
    volumes:
      - ./sentinel3.conf:/usr/local/etc/redis/sentinel.conf
```





### 修改 Sentinel 配置文件

需要三份 sentinel.conf 配置文件，分别为 `sentinel1.conf`，`sentinel2.conf`，`sentinel3.conf`，配置文件内容相同

```text
port 26379
dir /tmp
# 自定义集群名，其中 127.0.0.1 为 redis-master 的 ip，6379 为 redis-master 的端口，2 为最小投票数（因为有 3 台 Sentinel 所以可以设置成 2）
sentinel monitor mymaster 192.168.83.200 6379 2
sentinel down-after-milliseconds mymaster 30000
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 180000
sentinel deny-scripts-reconfig yes
```

启动

### 查看集群是否生效

进入 Sentinel 容器，使用 Sentinel API 查看监控情况：

```text
docker exec -it redis-sentinel-1 /bin/bash
redis-cli -p 26379
sentinel master mymaster
sentinel slaves mymaster
```

![img](https://ws4.sinaimg.cn/large/006tNc79ly1g35ca6k6rbj30j80mfmzu.jpg)

客户端连接sentinel

## Redis集群（redis-cluster）

Redis 集群是一个提供在多个Redis节点之间共享数据的程序集。

Redis 集群并不支持同时处理多个键的 Redis 命令，因为这需要在多个节点间移动数据，这样会降低redis集群的性能，在高负载的情况下可能会导致不可预料的错误。

Redis 集群通过分区来提供一定程度的可用性，即使集群中有一部分节点失效或者无法进行通讯， 集群也可以继续处理命令请求。

#### Redis 集群的优势:

1.缓存永不宕机：启动集群，永远让集群的一部分起作用。主节点失效了子节点能迅速改变角色成为主节点，整个集群的部分节点失败或者不可达的情况下能够继续处理命令；

2.迅速恢复数据：持久化数据，能在宕机后迅速解决数据丢失的问题；

3.Redis可以使用所有机器的内存，变相扩展性能；

4.使Redis的计算能力通过简单地增加服务器得到成倍提升,Redis的网络带宽也会随着计算机和网卡的增加而成倍增长；

5.Redis集群没有中心节点，不会因为某个节点成为整个集群的性能瓶颈;

6.异步处理数据，实现快速读写。

#### Redis 集群的数据分片

Redis 集群没有使用一致性hash，而是引入了哈希槽的概念。

Redis 集群内置了0-16383个哈希槽，每个key通过CRC16校验后对16384取模来决定放置哪个槽。集群的每个节点负责一部分hash槽，举个例子，比如当前集群有3个节点，那么:

节点 A 包含 0 到 5500号哈希槽.
节点 B 包含5501 到 11000 号哈希槽.
节点 C 包含11001 到 16383号哈希槽.

这种结构很容易添加或者删除节点.
比如如果我想新添加个节点D，我需要从节点 A, B, C中得部分槽到D上.
如果我想移除节点A，需要将A中的槽移到B和C节点上，然后将没有任何槽的A节点从集群中移除即可.

由于从一个节点将哈希槽移动到另一个节点并不会停止服务，所以无论添加删除或者改变某个节点的哈希槽的数量都不会造成集群不可用的状态.

#### Redis 集群的主从复制模型

为了使在部分节点失败或者大部分节点无法通信的情况下集群仍然可用，所以集群使用了主从复制模型，每个节点都会有N-1个复制品.

比如之前的例子有A，B，C三个节点的集群，在没有复制模型的情况下，如果节点B失败了，那么整个集群就会以为缺少5501-11000这个范围的槽而不可用.

然而如果在集群创建的时候（或者过一段时间）我们为每个节点添加一个从节点A1，B1，C1,那么整个集群便有三个master节点和三个slave节点组成，这样在节点B失败后，集群便会选举B1为新的主节点继续服务，整个集群便不会因为槽找不到而不可用了。不过当B和B1 都失败后，集群就不可用了。

主从复制的一些特点

1）采用异步复制；

2）一个主redis可以含有多个从redis；

3）每个从redis可以接收来自其他从redis服务器的连接；

4）主从复制对于主redis服务器来说是非阻塞的，这意味着当从服务器在进行主从复制同步过程中，主redis仍然可以处理外界的访问请求；

5）主从复制对于从redis服务器来说也是非阻塞的，这意味着，即使从redis在进行主从复制过程中也可以接受外界的查询请求，只不过这时候从redis返回的是以前老的数据，如果你不想这样，那么在启动redis时，可以在配置文件中进行设置，那么从redis在复制同步过程中来自外界的查询请求都会返回错误给客户端；
（虽然说主从复制过程中对于从redis是非阻塞的，但是当从redis从主redis同步过来最新的数据后还需要将新数据加载到内存中，在加载到内存的过程中是阻塞的，在这段时间内的请求将会被阻，但是即使对于大数据集，加载到内存的时间也是比较多的）；

6）主从复制提高了redis服务的扩展性，避免单个redis服务器的读写访问压力过大的问题，同时也可以给为数据备份及冗余提供一种解决方案；

7）为了编码主redis服务器写磁盘压力带来的开销，可以配置让主redis不在将数据持久化到磁盘，而是通过连接让一个配置的从redis服务器及时的将相关数据持久化到磁盘，不过这样会存在一个问题，就是主redis服务器一旦重启，因为主redis服务器数据为空，这时候通过主从同步可能导致从redis服务器上的数据也被清空；

#### 3.常见问题

集群节点的操作方式：

集群重新分片
./redis-trib.rb reshard 127.0.0.1:7000

添加一个新节点
./redis-trib.rb add-node 127.0.0.1:7006

添加一个从节点
./redis-trib.rb add-node --slave 127.0.0.1:7006

移除一个节点（如果是主节点要确保这个主节点是空的）
./redis-trib del-node 127.0.0.1:7000 <\node-id>

迁移从节点
CLUSTER REPLICATE <\master-node-id>

停止节点
./redis1/redis-cli -p 7001 shutdown

集群方式登录
./redis-cli -c -h -p 如：./redis-cli -p 7003 -c 默认情况下不能从slaves读取数据，但建立连接后，执行一次命令READONLY，该slaves即可读取数据。

#### Redis 持久化

redis提供了不同级别的持久化方式，一种是RDB，一种AOF。
可以同时开启两种持久化方式, 在这种情况下,当redis重启的时候会优先载入AOF文件来恢复原始的数据,因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整.

–

RDB：在指定的时间间隔能对数据进行快照存储(就是隔一段时间把内存里的数据转存到硬盘等介质上)

–

AOF：每次对服务器写的操作指令记录下来,当服务器重启的时候会重新执行这些命令来恢复原始的数据,AOF命令以redis协议追加保存每次写的操作到文件末尾.Redis还能对AOF文件进行后台重写,使得AOF文件的体积不至于过大.

#### 4.编码实战

Redis集群实现方式
*参考页首配置步骤

1.创建不同的redis节点，在需要的地方运行redis实例，每个redis实例有单独的ip或端口，并且配置启用集群管理；

2.创建redis-trib的运行环境：安装ruby，redis.gem

3.通过使用Redis集群命令工具redis-trib创建集群；

./redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002

–replicas 1 表示 自动为每一个master节点分配一个slave节点，上面有6个节点，程序会按照一定规则生成3个master（主）3个slave(从)

redis.conf 集群的基础配置

绑定ip（这里是本地配置，其他服务器配置对应的ip）
bind 127.0.0.1
端口
port 6379
后台守护启动
daemonize yes
开启aof模式
appendonly yes
开启集群管理
cluster-enabled yes
节点响应超时时间 如果超过这个响应时间就会认定节点不可用
cluster-node-timeout 5000

#### 5.扩展思考

Redis 一致性保证

Redis 并不能保证数据的强一致性. 这意味这在实际中集群在特定的条件下可能会丢失写操作.

第一个原因是因为集群是用了异步复制和写入的操作过程:
客户端向主节点B写入一条命令；
主节点B向客户端回复命令状态；
主节点将写入操作复制给他的从节点 B1, B2 和 B3；
这时候主节点对命令的复制工作发生在返回命令回复之后， 因为如果每次处理命令请求都需要等待复制操作完成的话， 那主节点处理命令请求的速度将极大地降低 —— 我们必须在性能和一致性之间做出权衡。
注意：Redis 集群可能会在将来提供同步写的方法。

另外一种可能会丢失命令的情况是集群出现了网络分区， 并且一个客户端与至少包括一个主节点在内的少数实例被孤立。
举个例子 假设集群包含 A 、 B 、 C 、 A1 、 B1 、 C1 六个节点， 其中 A 、B 、C 为主节点， A1 、B1 、C1 为A，B，C的从节点， 还有一个客户端 Z1 假设集群中发生网络分区，那么集群可能会分为两方，大部分的一方包含节点 A 、C 、A1 、B1 和 C1 ，小部分的一方则包含节点 B 和客户端 Z1 . Z1仍然能够向主节点B中写入, 如果网络分区发生时间较短,那么集群将会继续正常运作,如果分区的时间足够让大部分的一方将B1选举为新的master，那么Z1写入B中得数据便丢失了.

不同方式的redis的集群化方案

（1）Twitter开发的twemproxy代理方案
（2）豌豆荚开发的codis
（3）redis官方的redis-cluster
（4）客户端分片
（5）Sentinel哨兵模式

客户端分区
就是在客户端就已经决定数据会被存储到哪个redis节点或者从哪个redis节点读取。大多数客户端已经实现了客户端分
区。

代理分区
意味着客户端将请求发送给代理，然后代理决定去哪个节点写数据或者读数据。代理根据分区规则决定请求哪些Redis实例，然后根据Redis的响应结果返回给客户端。redis和memcached的一种代理实现就是Twemproxy

查询路由(Query routing)
意思是客户端随机地请求任意一个redis实例，然后由Redis将请求转发给正确的Redis节点。Redis Cluster实现了一种混合形式的查询路由，但并不是直接将请求从一个redis节点转发到另一个redis节点，而是在客户端的帮助下直接redirected到正确的redis节点。

持久化数据还是缓存？

无论是把Redis当做持久化的数据存储还是当作一个缓存，从分区的角度来看是没有区别的。当把Redis当做一个持久化的存储（服务）时，一个key必须严格地每次被映射到同一个Redis实例。当把Redis当做一个缓存（服务）时，即使Redis的其中一个节点不可用而把请求转给另外一个Redis实例，也不对我们的系统产生什么影响，我们可用任意的规则更改映射，进而提高系统的高可用（即系统的响应能力）。

一致性哈希能够实现当一个key的首选的节点不可用时切换至其他节点。同样地，如果你增加了一个新节点，立刻就会有新的key被分配至这个新节点。

重要结论如下:

如果Redis被当做缓存使用，使用一致性哈希实现动态扩容缩容，也就是说可以动态扩展操作节点
如果Redis被当做一个持久化存储使用，必须使用固定的keys-to-nodes映射关系，节点的数量一旦确定不能变化。否则的话(即Redis节点需要动态变化的情况），必须使用可以在运行时进行数据再平衡的一套系统，而当前只有Redis集群可以做到这样





# 创建缓存服务提供者

## 创建项目

创建一个名为 `token-service-redis` 的服务提供者项目

## POM

```text
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>itoken-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../itoken-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>itoken-service-redis</artifactId>
    <packaging>jar</packaging>

    <name>itoken-service-redis</name>
    <url>http://www.funtl.com</url>
    <inceptionYear>2018-Now</inceptionYear>

    <dependencies>
       

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zipkin</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->

        <!-- Spring Boot Admin Begin -->
        <dependency>
            <groupId>org.jolokia</groupId>
            <artifactId>jolokia-core</artifactId>
        </dependency>
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-client</artifactId>
        </dependency>
        <!-- Spring Boot Admin End -->

        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-pool2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.itoken.service.redis.RedisApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>

```

主要增加了 `org.springframework.boot:spring-boot-starter-data-redis`，`org.apache.commons:commons-pool2` 两个依赖

## Application

```text
package com.funtl.itoken.service.redis;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@EnableEurekaClient
@SpringBootApplication
public class RedisApplication {
    public static void main(String[] args) {
        SpringApplication.run(RedisApplication.class, args);
    }
}
```



## 本地配置

### bootstrap.yml

```text
spring:
  cloud:
    config:
      uri: http://localhost:8888
      name: token-service-redis
      label: master
      profile: dev
```



### bootstrap-prod.yml

```text
spring:
  cloud:
    config:
      uri: http://192.168.75.137:8888
      name: token-service-redis
      label: master
      profile: prod
```



## 云配置

### token-service-redis-dev.yml

```text
spring:
  application:
    name: token-service-redis
  boot:
    admin:
      client:
        url: http://localhost:8084
  zipkin:
    base-url: http://localhost:9411
  redis:
    lettuce:
      pool:
        max-active: 8
        max-idle: 8
        max-wait: -1ms
        min-idle: 0
    sentinel:
      master: mymaster
      nodes: 192.168.83.200:26379

server:
  port: 8502

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/

management:
  endpoint:
    health:
      show-details: always
  endpoints:
    web:
      exposure:
        include: health,info
```



### itoken-service-redis-prod.yml

```text
spring:
  application:
    name: token-service-redis
  boot:
    admin:
      client:
        url: http://192.168.83.200:8084
  zipkin:
    base-url: http://192.168.83.200:9411
  redis:
    lettuce:
      pool:
        max-active: 8
        max-idle: 8
        max-wait: -1ms
        min-idle: 0
    sentinel:
      master: mymaster
      nodes: 192.168.83.200:26379, 192.168.83.200:26380, 192.168.83.200:26381

server:
  port: 8502

eureka:
  client:
    serviceUrl:
      defaultZone: http://192.168.83.200:8761/eureka/,http://192.168.83.200:8861/eureka/,http://192.168.83.200:8961/eureka/

management:
  endpoint:
    health:
      show-details: always
  endpoints:
    web:
      exposure:
        include: health,info
```



## 创建接口

### RedisService

```text
package com.funtl.itoken.service.redis.service;

public interface RedisService {
    public void set(String key, Object value, long seconds);
    public Object get(String key);
}
```



### RedisServiceImpl

```text
package com.funtl.itoken.service.redis.service.impl;

import com.funtl.itoken.service.redis.service.RedisService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

@Service
public class RedisServiceImpl implements RedisService {

    @Autowired
    private RedisTemplate redisTemplate;

    @Override
    public void set(String key, Object value, long seconds) {
        redisTemplate.opsForValue().set(key, value, seconds, TimeUnit.SECONDS);
    }

    @Override
    public Object get(String key) {
        return redisTemplate.opsForValue().get(key);
    }
}
```



## Controller

```text
package com.funtl.itoken.service.redis.controller;

import com.funtl.itoken.service.redis.service.RedisService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class RedisController {

    private static final String RESULT_OK = "ok";

    @Autowired
    private RedisService redisService;

    @RequestMapping(value = "put", method = RequestMethod.GET)
    public String set(String key, String value, long seconds) {
        redisService.set(key, value, seconds);
        return RESULT_OK;
    }

    @RequestMapping(value = "find", method = RequestMethod.GET)
    public String find(String key) {
        String json = null;

        Object obj = redisService.get(key);
        if (obj != null) {
            json = (String) redisService.get(key);
        }

        return json;
    }
}
```

启动尤里卡，看是否注册

```sh
http://127.0.0.1:8502/put?key=test&value=helloredis&seconds=300




http://127.0.0.1:8502/find?key=test
```

![Redis_Desktop_Manager_0_9_3_39](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g35f5l4qhxj31oa0mkn2a.jpg)

# Redis 命令汇总

## 参考资料

- http://redisdoc.com/
- http://redis.io/commands

## 连接操作相关的命令

- ping：测试连接是否存活如果正常会返回 pong
- echo：打印
- select：切换到指定的数据库，数据库索引号 index 用数字值指定，以 0 作为起始索引值
- quit：关闭连接（connection）
- auth：简单密码认证

## 服务端相关命令

- time：返回当前服务器时间
- client list: 返回所有连接到服务器的客户端信息和统计数据 参见 http://redisdoc.com/server/client_list.html
- client kill ip:port：关闭地址为 ip:port 的客户端
- save：将数据同步保存到磁盘
- bgsave：将数据异步保存到磁盘
- lastsave：返回上次成功将数据保存到磁盘的Unix时戳
- shundown：将数据同步保存到磁盘，然后关闭服务
- info：提供服务器的信息和统计
- config resetstat：重置 info 命令中的某些统计数据
- config get：获取配置文件信息
- config set：动态地调整 Redis 服务器的配置(configuration)而无须重启，可以修改的配置参数可以使用命令 CONFIG GET * 来列出
- config rewrite：Redis 服务器时所指定的 redis.conf 文件进行改写
- monitor：实时转储收到的请求
- slaveof：改变复制策略设置

## 发布订阅相关命令

- psubscribe：订阅一个或多个符合给定模式的频道 例如 psubscribe news.* tweet.*
- publish：将信息 message 发送到指定的频道 channel 例如 publish msg "good morning"
- pubsub channels：列出当前的活跃频道 例如 PUBSUB CHANNELS news.i*
- pubsub numsub：返回给定频道的订阅者数量 例如 PUBSUB NUMSUB news.it news.internet news.sport news.music
- pubsub numpat：返回客户端订阅的所有模式的数量总和
- punsubscribe：指示客户端退订所有给定模式。
- subscribe：订阅给定的一个或多个频道的信息。例如 subscribe msg chat_room
- unsubscribe：指示客户端退订给定的频道。

## 对 KEY 操作的命令

- exists(key)：确认一个 key 是否存在
- del(key)：删除一个 key
- type(key)：返回值的类型
- keys(pattern)：返回满足给定 pattern 的所有 key
- randomkey：随机返回 key 空间的一个
- keyrename(oldname, newname)：重命名 key
- dbsize：返回当前数据库中 key 的数目
- expire：设定一个 key 的活动时间（s）
- ttl：获得一个 key 的活动时间
- move(key, dbindex)：移动当前数据库中的 key 到 dbindex 数据库
- flushdb：删除当前选择数据库中的所有 key
- flushall：删除所有数据库中的所有 key

## 对 String 操作的命令

- set(key, value)：给数据库中名称为 key 的 string 赋予值 value
- get(key)：返回数据库中名称为 key 的 string 的 value
- getset(key, value)：给名称为 key 的 string 赋予上一次的 value
- mget(key1, key2,…, key N)：返回库中多个 string 的 value
- setnx(key, value)：添加 string，名称为 key，值为 value
- setex(key, time, value)：向库中添加 string，设定过期时间 time
- mset(key N, value N)：批量设置多个 string 的值
- msetnx(key N, value N)：如果所有名称为 key i 的 string 都不存在
- incr(key)：名称为 key 的 string 增 1 操作
- incrby(key, integer)：名称为 key 的 string 增加 integer
- decr(key)：名称为 key 的 string 减 1 操作
- decrby(key, integer)：名称为 key 的 string 减少 integer
- append(key, value)：名称为 key 的 string 的值附加 value
- substr(key, start, end)：返回名称为 key 的 string 的 value 的子串

## 对 List 操作的命令

- rpush(key, value)：在名称为 key 的 list 尾添加一个值为 value 的元素
- lpush(key, value)：在名称为 key 的 list 头添加一个值为 value 的元素
- llen(key)：返回名称为 key 的 list 的长度
- lrange(key, start, end)：返回名称为 key 的 list 中 start 至 end 之间的元素
- ltrim(key, start, end)：截取名称为 key 的 list
- lindex(key, index)：返回名称为 key 的 list 中 index 位置的元素
- lset(key, index, value)：给名称为 key 的 list 中 index 位置的元素赋值
- lrem(key, count, value)：删除 count 个 key 的 list 中值为 value 的元素
- lpop(key)：返回并删除名称为 key 的 list 中的首元素
- rpop(key)：返回并删除名称为 key 的 list 中的尾元素
- blpop(key1, key2,… key N, timeout)：lpop 命令的 block 版本。
- brpop(key1, key2,… key N, timeout)：rpop 的 block 版本。
- rpoplpush(srckey, dstkey)：返回并删除名称为 srckey 的 list 的尾元素，并将该元素添加到名称为 dstkey 的 list 的头部

## 对 Set 操作的命令

- sadd(key, member)：向名称为 key 的 set 中添加元素 member
- srem(key, member) ：删除名称为 key 的 set 中的元素 member
- spop(key) ：随机返回并删除名称为 key 的 set 中一个元素
- smove(srckey, dstkey, member) ：移到集合元素
- scard(key) ：返回名称为 key 的 set 的基数
- sismember(key, member) ：member 是否是名称为 key 的 set 的元素
- sinter(key1, key2,…key N) ：求交集
- sinterstore(dstkey, (keys)) ：求交集并将交集保存到 dstkey 的集合
- sunion(key1, (keys)) ：求并集
- sunionstore(dstkey, (keys)) ：求并集并将并集保存到 dstkey 的集合
- sdiff(key1, (keys)) ：求差集
- sdiffstore(dstkey, (keys)) ：求差集并将差集保存到 dstkey 的集合
- smembers(key) ：返回名称为 key 的 set 的所有元素
- srandmember(key) ：随机返回名称为 key 的 set 的一个元素

## 对 Hash 操作的命令

- hset(key, field, value)：向名称为 key 的 hash 中添加元素 field
- hget(key, field)：返回名称为 key 的 hash 中 field 对应的 value
- hmget(key, (fields))：返回名称为 key 的 hash 中 field i 对应的 value
- hmset(key, (fields))：向名称为 key 的 hash 中添加元素 field
- hincrby(key, field, integer)：将名称为 key 的 hash 中 field 的 value 增加 integer
- hexists(key, field)：名称为 key 的 hash 中是否存在键为 field 的域
- hdel(key, field)：删除名称为 key 的 hash 中键为 field 的域
- hlen(key)：返回名称为 key 的 hash 中元素个数
- hkeys(key)：返回名称为 key 的 hash 中所有键
- hvals(key)：返回名称为 key 的 hash 中所有键对应的 value
- hgetall(key)：返回名称为 key 的 hash 中所有的键（field）及其对应的 value

## Redis Sentinel

- ping ：返回 pong
- sentinel masters ：列出所有被监视的主服务器，以及这些主服务器的当前状态。
- sentinel slaves：列出给定主服务器的所有从服务器，以及这些从服务器的当前状态。
- sentinel get-master-addr-by-name：返回给定名字的主服务器的 IP 地址和端口号。如果这个主服务器正在执行故障转移操作，或者针对这个主服务器的故障转移操作已经完成，那么这个命令返回新的主服务器的 IP 地址和端口号。
- sentinel reset：重置所有名字和给定模式 pattern 相匹配的主服务器。pattern 参数是一个 Glob 风格的模式 重置操作清楚主服务器目前的所有状态，包括正在执行中的故障转移，并移除目前已经发现和关联的，主服务器的所有从服务器和 Sentinel 。
- sentinel failover：当主服务器失效时，在不询问其他 Sentinel 意见的情况下，强制开始一次自动故障迁移（不过发起故障转移的 Sentinel 会向其他 Sentinel 发送一个新的配置，其他 Sentinel 会根据这个配置进行相应的更新）。

# 