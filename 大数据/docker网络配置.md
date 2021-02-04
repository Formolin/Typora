# 一、Docker网络模式

docker run 创建 Docker 容器时，可以用 –net 选项指定容器的网络模式，Docker 有以下 4 种网络模式：

host 模式，使用 –net=host 指定。
container 模式，使用 –net=container:NAME_or_ID 指定。
none 模式，使用 –net=none 指定。
bridge 模式，使用 –net=bridge 指定，默认设置。

# host 模式

如果启动容器的时候使用 host 模式，那么这个容器将不会获得一个独立的 Network Namespace，而是和宿主机共用一个 Network Namespace。容器将不会虚拟出自己的网卡，配置自己的 IP 等，而是使用宿主机的 IP 和端口。

例如，我们在 10.10.101.105/24 的机器上用 host 模式启动一个含有 web 应用的 Docker 容器，监听 tcp 80 端口。当我们在容器中执行任何类似 ifconfig 命令查看网络环境时，看到的都是宿主机上的信息。而外界访问容器中的应用，则直接使用 10.10.101.105:80 即可，不用任何 NAT 转换，就如直接跑在宿主机中一样。但是，容器的其他方面，如文件系统、进程列表等还是和宿主机隔离的。

## container 模式

这个模式指定新创建的容器和已经存在的一个容器共享一个 Network Namespace，而不是和宿主机共享。新创建的容器不会创建自己的网卡，配置自己的 IP，而是和一个指定的容器共享 IP、端口范围等。同样，两个容器除了网络方面，其他的如文件系统、进程列表等还是隔离的。两个容器的进程可以通过 lo 网卡设备通信。

## none模式

这个模式和前两个不同。在这种模式下，Docker 容器拥有自己的 Network Namespace，但是，并不为 Docker容器进行任何网络配置。也就是说，这个 Docker 容器没有网卡、IP、路由等信息。需要我们自己为 Docker 容器添加网卡、配置 IP 等。

## bridge模式

![20180411201219629](https://ws2.sinaimg.cn/large/006tKfTcly1g1dp65uderj30f80980t0.jpg)

bridge 模式是 Docker 默认的网络设置，此模式会为每一个容器分配 Network Namespace、设置 IP 等，并将一个主机上的 Docker 容器连接到一个虚拟网桥上。当 Docker server 启动时，会在主机上创建一个名为 docker0 的虚拟网桥，此主机上启动的 Docker 容器会连接到这个虚拟网桥上。虚拟网桥的工作方式和物理交换机类似，这样主机上的所有容器就通过交换机连在了一个二层网络中。接下来就要为容器分配 IP 了，Docker 会从 RFC1918 所定义的私有 IP 网段中，选择一个和宿主机不同的IP地址和子网分配给 docker0，连接到 docker0 的容器就从这个子网中选择一个未占用的 IP 使用。如一般 Docker 会使用 172.17.0.0/16 这个网段，并将 172.17.42.1/16 分配给 docker0 网桥（在主机上使用 ifconfig 命令是可以看到 docker0 的，可以认为它是网桥的管理接口，在宿主机上作为一块虚拟网卡使用）

# 二、Docker网络配置–设置固定IP

docker 容器在启动时默认使用的是bridge 模式，Docker 容器启动后，会连接到一个名为 docker0 的虚拟网桥，故每次启动docker容器的IP都不是固定的，不方便管理，有时候需要进行固定IP映射，比如Docker集群管理时。

Docker容器设置固定IP时需要用到需要用到 pipework，用于给容器设置IP：

1、host宿主机下载pipework如下：

```sh
$ git clone https://github.com/jpetazzo/pipework.git
$ cp pipework/pipework /usr/local/bin/

```



2、安装网桥工具包bridge-utils

```sh
$ yum -y install bridge-utils
```



3、创建网桥并设置IP网段

```sh
$ brctl addbr br1
$ ip link set dev br1 up
$ ip addr add 192.168.10.1/24 dev br1
```



4、启动一个容器

```sh
#基于 centos 这个镜像启动一个容器，名为 centos-ip
$ docker run -d --name=centos-ip centos
```

5、设置IP

```sh
$ pipework br1 centos-ip 192.168.10.20/24
```

为名为 centos-ip 的容器指定了IP 192.168.10.20

分别使用 ping 与 ssh 命令进行验证，看是否可以ping通和成功登录

```sh
$ ping 192.168.10.20
$ ssh 192.168.10.20
```

注意：SSH时需要输入容器centos的root密码，如果在创建centos容器时未指定root密码，可先进入容器设置root密码

```sh
docker exec -it 容器ID/NAME

sudo passwd root
```

附:删除网桥

```sh
brctl show  
#查看网桥状态  
brctl delif <网桥名> <端口名>  
#卸载网桥上的端口  
ifconfig  
#查看是否有网桥网卡名  
ifconfig <网桥名> down  
#关闭此网卡  
brctl delbr <网桥名>  
#删除网桥</span>  

```

**1.9版本后的Docker可使用下面这种方式：**

1、创建自定义网络

```sh
docker network create --subnet=192.168.10.1/24 network_my

# docker network ls 
NETWORK ID          NAME                DRIVER              SCOPE
1fb22da7d8a3        bridge              bridge              local
fe259334b842        host                host                local
8c5971ff48d8        network_my          bridge              local
3aaf0356c19c        none                null                local

```

2、 启动Docker容器

```sh
docker run -itd --name hadoop0 --hostname hadoop0 --net network_my --ip 192.168.10.30 -d -P -p 50070:50070 -p 8088:8088 hadoop:master
```

### 查看容器ip

### docker inspect 容器名称或 id

