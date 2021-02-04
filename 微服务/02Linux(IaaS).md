# 一、Linux


​	Linux 是一种自由和开放源码的类 UNIX 操作系统，使用 Linux 内核。目前存在着许多不同的 Linux 发行版，可安装在各种各样的电脑硬件设备，从手机、平板电脑、路由器和影音游戏控制台，到桌上型电脑，大型电脑和超级电脑。 Linux 是一个领先的操作系统，世界上运算最快的 10 台超级电脑运行的都是 Linux 操作系统。

​	Linux 操作系统也是自由软件和开放源代码发展中最著名的例子。只要遵循 GNU 通用公共许可证,任何人和机构都可以自由地使用 Linux 的所有底层源代码，也可以自由地修改和再发布。严格来讲，Linux 这个词本身只表示 Linux 内核，但在实际上人们已经习惯了用 Linux 来形容整个基于 Linux 内核，并且使用 GNU 工程各种工具和数据库的操作系统 (也被称为 GNU/ Linux)。通常情况下，Linux 被打包成供桌上型电脑和服务器使用的 Linux 发行版本。一些流行的主流 Linux 发行版本，包括 Debian (及其衍生版本 Ubuntu)，Fedora 和 OpenSUSE 等。Kernel + Softwares + Tools 就是 Linux Distribution

​	目前市面上较知名的发行版有：`Ubuntu`、RedHat、`CentOS`、Debian、Fedora、SuSE、OpenSUSE、TurboLinux、BluePoint、RedFlag、Xterm、SlackWare等。

目前国内 Linux 更多的是应用于服务器上，而桌面操作系统更多使用的是 Windows。主要区别如下

| 比较     | Windows                                                      | Linux                                                        |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 界面     | 界面统一，外壳程序固定所有 Windows 程序菜单几乎一致，快捷键也几乎相同 | 图形界面风格依发布版不同而不同，可能互不兼容。GNU/Linux 的终端机是从 UNIX 传承下来，基本命令和操作方法也几乎一致。 |
| 驱动程序 | 驱动程序丰富，版本更新频繁。默认安装程序里面一般包含有该版本发布时流行的硬件驱动程序，之后所出的新硬件驱动依赖于硬件厂商提供。对于一些老硬件，如果没有了原配的驱动有时很难支持。另外，有时硬件厂商未提供所需版本的 Windows 下的驱动，也会比较头痛。 | 由志愿者开发，由Linux核心开发小组发布，很多硬件厂商基于版权考虑并未提供驱动程序，尽管多数无需手动安装，但是涉及安装则相对复杂，使得新用户面对驱动程序问题（是否存在和安装方法）会一筹莫展。但是在开源开发模式下，许多老硬件尽管在 Windows 下很难支持的也容易找到驱动。HP、Intel、AMD 等硬件厂商逐步不同程度支持开源驱动，问题正在得到缓解。 |
| 使用     | 使用比较简单，容易入门。图形化界面对没有计算机背景知识的用户使用十分有利。 | 图形界面使用简单，容易入门。文字界面，需要学习才能掌握。     |
| 学习     | 系统构造复杂、变化频繁，且知识、技能淘汰快，深入学习困难。   | 系统构造简单、稳定，且知识、技能传承性好，深入学习相对容易。 |
| 软件     | 每一种特定功能可能都需要商业软件的支持，需要购买相应的授权。 | 大部分软件都可以自由获取，同样功能的软件选择较少。           |

# 二、安装

用vm安装centos：

- 账户root密码root

- home文件夹内安装

- 服务器不在你身边的，你只能远程控制

- IP地址 ip addr

- ping [www.baidu.com](http://www.baidu.com)

- ssh远程连接技术

- 连接服务器，使用工具，连接linux

- ip地址+用户名root+密码root+端口号22

- 使用终端连接

- ssh root@ip地址（ssh root@172.31.121.218）

  所有的操作都可以用客户端完成了

# 三、linux目录

![img](https://javasite.oss-cn-shenzhen.aliyuncs.com/blog/assets/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20171102134832.png)

| 目录 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| bin  | 存放二进制可执行文件(ls,cat,mkdir等)                         |
| boot | 存放用于系统引导时使用的各种文件                             |
| dev  | 用于存放设备文件                                             |
| etc  | 存放系统配置文件                                             |
| home | 存放所有用户文件的根目录                                     |
| lib  | 存放跟文件系统中的程序运行所需要的共享库及内核模块           |
| mnt  | 系统管理员安装临时文件系统的安装点                           |
| opt  | 额外安装的可选应用程序包所放置的位置                         |
| proc | 虚拟文件系统，存放当前内存的映射                             |
| root | 超级用户目录                                                 |
| sbin | 存放二进制可执行文件，只有root才能访问                       |
| tmp  | 用于存放各种临时文件                                         |
| usr  | 用于存放系统应用程序，比较重要的目录/usr/local 本地管理员软件安装目录 |
| var  | 用于存放运行时需要改变数据的文件                             |

**不要自己创建目录，只能操作usr/local 、 var**

# 四、命令

## 1、操作文件目录

| 命令  | 说明                               | 语法                                            | 参数  | 参数说明                           |
| ----- | ---------------------------------- | ----------------------------------------------- | ----- | ---------------------------------- |
| ls    | 显示文件和目录列表                 | ls [-alrtAFR] [name...]                         |       |                                    |
|       |                                    |                                                 | -l    | 列出文件的详细信息                 |
|       |                                    |                                                 | -a    | 列出当前目录所有文件，包含隐藏文件 |
| mkdir | 创建目录                           | mkdir [-p] dirName                              |       |                                    |
|       |                                    |                                                 | -p    | 父目录不存在情况下先生成父目录     |
| cd    | 切换目录                           | cd [dirName]                                    |       |                                    |
| touch | 生成一个空文件                     |                                                 |       |                                    |
| echo  | 生成一个带内容文件                 | echo abcd > 1.txt，echo 1234 >> 1.txt           |       |                                    |
| cat   | 显示文本文件内容                   | cat [-AbeEnstTuv] [--help] [--version] fileName |       |                                    |
| cp    | 复制文件或目录                     | cp [options] source dest                        |       |                                    |
| rm    | 删除文件                           | rm [options] name...                            |       |                                    |
|       |                                    |                                                 | -f    | 强制删除文件或目录                 |
|       |                                    |                                                 | -r    | 同时删除该目录下的所有文件         |
| mv    | 移动文件或目录                     | mv [options] source dest                        |       |                                    |
| find  | 在文件系统中查找指定的文件         |                                                 |       |                                    |
|       |                                    |                                                 | -name | 文件名                             |
| grep  | 在指定的文本文件中查找指定的字符串 |                                                 |       |                                    |
| tree  | 用于以树状图列出目录的内容         |                                                 |       |                                    |
| pwd   | 显示当前工作目录                   |                                                 |       |                                    |
| ln    | 建立软链接                         |                                                 |       |                                    |
| more  | 分页显示文本文件内容               |                                                 |       |                                    |
| head  | 显示文件开头内容                   |                                                 |       |                                    |
| tail  | 显示文件结尾内容                   |                                                 |       |                                    |
|       |                                    |                                                 | -f    | 跟踪输出                           |

## 2、系统管理命令

| 命令     | 说明                                         |
| -------- | -------------------------------------------- |
| stat     | 显示指定文件的相关信息,比ls命令显示内容更多  |
| who      | 显示在线登录用户                             |
| hostname | 显示主机名称                                 |
| uname    | 显示系统信息                                 |
| top      | 显示当前系统中耗费资源最多的进程             |
| ps       | 显示瞬间的进程状态                           |
| du       | 显示指定的文件（目录）已使用的磁盘空间的总量 |
| df       | 显示文件系统磁盘空间的使用情况               |
| free     | 显示当前内存和交换空间的使用情况             |
| ifconfig | 显示网络接口信息                             |
| ping     | 测试网络的连通性                             |
| netstat  | 显示网络状态信息                             |
| clear    | 清屏                                         |
| kill     | 杀死一个进程                                 |

## 3、开关机命令

| shutdown | shutdown [-t seconds] [-rkhncfF] time [message] |            |                                                              |
| -------- | ----------------------------------------------- | ---------- | ------------------------------------------------------------ |
|          |                                                 | -t seconds | 设定在几秒钟之后进行关机程序                                 |
|          |                                                 | -k         | 并不会真的关机，只是将警告讯息传送给所有只用者               |
|          |                                                 | -r         | 关机后重新开机（重启）                                       |
|          |                                                 | -h         | 关机后停机                                                   |
|          |                                                 | -n         | 不采用正常程序来关机，用强迫的方式杀掉所有执行中的程序后自行关机 |
|          |                                                 | -c         | 取消目前已经进行中的关机动作                                 |
|          |                                                 | -f         | 关机时，不做 fcsk 动作(检查 Linux 档系统)                    |
|          |                                                 | -F         | 关机时，强迫进行 fsck 动作                                   |
|          |                                                 | time       | 设定关机的时间                                               |
|          |                                                 | message    | 传送给所有使用者的警告讯息                                   |

#### 重启

- reboot
- shutdown -r now

#### 关机

- shutdown -h now

## 4、压缩命令

### tar

| 命令 | 语法                                        | 参数 | 参数说明                        |
| ---- | ------------------------------------------- | ---- | ------------------------------- |
| tar  | tar [-cxzjvf] 压缩打包文档的名称 欲打包目录 |      |                                 |
|      |                                             | -c   | 建立一个归档文件的参数指令      |
|      |                                             | -x   | 解开一个归档文件的参数指令      |
|      |                                             | -z   | 是否需要用 gzip 压缩            |
|      |                                             | -j   | 是否需要用 bzip2 压缩           |
|      |                                             | -v   | 压缩的过程中显示文件            |
|      |                                             | -f   | 使用档名，在 f 之后要立即接档名 |
|      |                                             | -tf  | 查看归档文件里面的文件          |

**例子：**

- 压缩文件夹：`tar -zcvf test.tar.gz test\`
- 解压文件夹：`tar -zxvf test.tar.gz`

### gzip

| 命令 | 语法                               | 参数 | 参数说明                                                     |
| ---- | ---------------------------------- | ---- | ------------------------------------------------------------ |
| gzip | gzip [选项] 压缩（解压缩）的文件名 |      |                                                              |
|      |                                    | -d   | 解压缩                                                       |
|      |                                    | -l   | 对每个压缩文件，显示压缩文件的大小，未压缩文件的大小，压缩比，未压缩文件的名字 |
|      |                                    | -v   | 对每一个压缩和解压的文件，显示文件名和压缩比                 |
|      |                                    | -num | 用指定的数字num调整压缩的速度，-1或--fast表示最快压缩方法（低压缩比），-9或--best表示最慢压缩方法（高压缩比）。系统缺省值为6 |

说明：压缩文件后缀为 gz

### bzip2

| 命令  | 语法         | 参数 | 参数说明                                                     |
| ----- | ------------ | ---- | ------------------------------------------------------------ |
| bzip2 | bzip2 [-cdz] |      |                                                              |
|       |              | -d   | 解压缩                                                       |
|       |              | -z   | 压缩参数                                                     |
|       |              | -num | 用指定的数字num调整压缩的速度，-1或--fast表示最快压缩方法（低压缩比），-9或--best表示最慢压缩方法（高压缩比）。系统缺省值为6 |

说明：压缩文件后缀为 bz2

# 五、编辑器

## vim

### 运行模式

编辑模式：等待编辑命令输入

插入模式：编辑模式下，输入 `i` 进入插入模式，插入文本信息

命令模式：在编辑模式下，输入 `:` 进行命令模式

### 命令

`:q` 直接退出vi

`:wq` 保存后退出vi ，并可以新建文件

`:q!` 强制退出

`:w file` 将当前内容保存成某个文件

`:set number` 在编辑文件显示行号

`:set nonumber`	在编辑文件不显示行号

# 六、Linux 软件包管理

## 概述

APT(Advanced Packaging Tool) 是 Debian/Ubuntu 类 Linux 系统中的软件包管理程序, 使用它可以找到想要的软件包, 而且安装、卸载、更新都很简便；也可以用来对 Ubuntu 进行升级; APT 的源文件为 `/etc/apt/` 目录下的 `sources.list` 文件。

## 修改数据源

由于国内的网络环境问题，我们需要将 Ubuntu 的数据源修改为国内数据源，操作步骤如下：

### 查看系统版本

```text
lsb_release -a
```

输出结果为

```text
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 16.04 LTS
Release:	16.04
Codename:	xenial
```

**注意：** Codename 为 `xenial`，该名称为我们 Ubuntu 系统的名称，修改数据源需要用到该名称

### 编辑数据源

```text
vi /etc/apt/sources.list
```

删除全部内容并修改为

```text
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
```

### 更新数据源

```text
apt-get update
```

## 常用 APT 命令

### 安装软件包

```text
apt-get install packagename
```

### 删除软件包

```text
apt-get remove packagename
```

### 更新软件包列表

```text
apt-get update
```

### 升级有可用更新的系统（慎用）

```text
apt-get upgrade
```

## 其它 APT 命令

### 搜索

```text
apt-cache search package
```

### 获取包信息

```text
apt-cache show package
```

### 删除包及配置文件

```text
apt-get remove package --purge
```

### 了解使用依赖

```text
apt-cache depends package
```

### 查看被哪些包依赖

```text
apt-cache rdepends package
```

### 安装相关的编译环境

```text
apt-get build-dep package
```

### 下载源代码

```text
apt-get source package
```

### 清理无用的包

```text
apt-get clean && apt-get autoclean
```

### 检查是否有损坏的依赖

```text
apt-get check
```

## centos命令是yum

# 七、权限管理

## 查看文件和目录的权限

ls –al`使用 ls 不带参数只显示文件名称，通过`ls –al` 可以显示文件或者目录的权限信息。

`ls -l 文件名` 显示信息包括：文件类型 (`d` 目录，`-` 普通文件，`l` 链接文件)，文件权限，文件的用户，文件的所属组，文件的大小，文件的创建时间，文件的名称

```
-rw-r--r-- 1 lusifer lusifer 675 Oct 26 17:20 .profile
```

- `-`：普通文件
- `rw-`：说明用户 lusifer 有读写权限，没有运行权限
- `r--`：表示用户组 lusifer 只有读权限，没有写和运行的权限
- `r--`：其他用户只有读权限，没有写权限和运行的权限

| -rw-r--r--     | 1      | lusifer      | lusifer    | 675      | Oct 26 17:20       | .profile |
| -------------- | ------ | ------------ | ---------- | -------- | ------------------ | -------- |
| 文档类型及权限 | 连接数 | 文档所属用户 | 文档所属组 | 文档大小 | 文档最后被修改日期 | 文档名称 |

| -        | rw-                    | r--                         | r--                   |
| -------- | ---------------------- | --------------------------- | --------------------- |
| 文档类型 | 文档所有者权限（user） | 文档所属用户组权限（group） | 其他用户权限（other） |

### 文档类型

- `d` 表示目录
- `l` 表示软连接
- `–` 表示文件
- `c` 表示串行端口字符设备文件
- `b` 表示可供存储的块设备文件
- 余下的字符 3 个字符为一组。`r` 只读，`w` 可写，`x` 可执行，`-` 表示无此权限

### 连接数

指有多少个文件指向同一个索引节点。

### 文档所属用户和所属组

就是文档属于哪个用户和用户组。文件所属用户和组是可以更改的

### 文档大小

默认是 bytes

## 更改操作权限

### chown

是 change owner 的意思，主要作用就是改变文件或者目录所有者，所有者包含用户和用户组

```
chown [-R] 用户名称 文件或者目录
chown [-R] 用户名称 用户组名称 文件或目录
```

-R：进行递归式的权限更改，将目录下的所有文件、子目录更新为指定用户组权限

### chmod

改变访问权限

```
chmod [who] [+ | - | =] [mode] 文件名
```

#### who

表示操作对象可以是以下字母的一个或者组合

- u：用户 user
- g：用户组 group
- o：表示其他用户
- a：表示所有用户是系统默认的

#### 操作符号

- +：表示添加某个权限
- -：表示取消某个权限
- =：赋予给定的权限，取消文档以前的所有权限

#### mode

表示可执行的权限，可以是 r、w、x

#### 文件名

文件名可以使空格分开的文件列表

#### 示例

```text
lusifer@UbuntuBase:~$ ls -al test.txt 
-rw-rw-r-- 1 lusifer lusifer 6 Nov  2 21:47 test.txt
lusifer@UbuntuBase:~$ chmod u=rwx,g+r,o+r test.txt 
lusifer@UbuntuBase:~$ ls -al test.txt 
-rwxrw-r-- 1 lusifer lusifer 6 Nov  2 21:47 test.txt
lusifer@UbuntuBase:~$
```

## 数字设定法

数字设定法中数字表示的含义

- 0 表示没有任何权限
- 1 表示有可执行权限 = `x`
- 2 表示有可写权限 = `w`
- 4 表示有可读权限 = `r`

也可以用数字来表示权限如 chmod 755 file_name

| r w x | r – x | r - x  |
| ----- | ----- | ------ |
| 4 2 1 | 4 - 1 | 4 - 1  |
| user  | group | others |

若要 rwx 属性则 4+2+1=7

若要 rw- 属性则 4+2=6

若要 r-x 属性则 4+1=5

```sh
lusifer@UbuntuBase:~$ chmod 777 test.txt 
lusifer@UbuntuBase:~$ ls -al test.txt 
-rwxrwxrwx 1 lusifer lusifer 6 Nov  2 21:47 test.txt

lusifer@UbuntuBase:~$ chmod 770 test.txt 
lusifer@UbuntuBase:~$ ls -al test.txt 
-rwxrwx--- 1 lusifer lusifer 6 Nov  2 21:47 test.txt
```

# 八、安装java

## 1、软件包安装

1、下载后需要把安装包传送到服务器

2、先克隆一个centos，为了防止安装损坏，为了节约空间使用连接克隆，链接克隆不会对原系统影响，完整克隆效率高

## 			![image-20190126183237471](https://ws4.sinaimg.cn/large/006tNc79ly1fzk61jkilij30de07b3zm.jpg)

![image-20190126183301174](https://ws4.sinaimg.cn/large/006tNc79ly1fzk61yk47ij30de08bdi4.jpg)

## 2、在线安装

- yum search java|grep jdk
-  查看yum库中都有哪些jdk版本

![pastedGraphic.png](https://ws2.sinaimg.cn/large/006tNc79ly1fzk66r59wsj30de0dndnw.jpg)

 

- yum install java-1.8.0-openjdk
-  安装Java1.8
-  按两次Y确认

- cd /usr/lib/jvm
-  进入安装目录

![image-20190126183822389](https://ws1.sinaimg.cn/large/006tNc79ly1fzk67j43rjj30de03eabe.jpg)

- vim /etc/profile
-  设置Java环境变量

- \#set java environment


```sh
 JAVA_HOME=/usr/lib/jvm/jdk1.8.0_144
 JRE_HOME=$JAVA_HOME/jre
 CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
 PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
 export JAVA_HOME JRE_HOME CLASS_PATH PATH
```

source /etc/profile
 使配置生效

java -version
 查看Java版本

![pastedGraphic.png](https://ws4.sinaimg.cn/large/006tNc79ly1fzk67tbnd6j30de022mxq.jpg)

# 九、安装tomcat

官网下载tomcat，跟jdk匹配

文件传输

1、Tar -xzvf apache….

2、mv apa…  /usr/local/

3、cd  到目录bin

![image-20190126193649148](https://ws1.sinaimg.cn/large/006tNc79ly1fzk7wc5rusj30de06lq44.jpg)

 4、启动./startup.sh

5、网址输入ip：8080

6、停止运行 ./shutdown.sh

7、如果访问不了，是防火墙的问题，

- 先关闭防火墙service iptables stop，发现报错

- yum install iptables-services,在关闭防火墙

​	不好使，在执行下面这个

##### 配置防火墙打开对应的端口：

vi /etc/sysconfig/iptables

-A INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT

#####  重启防火墙

service iptables restart

![image-20190126193750320](https://ws1.sinaimg.cn/large/006tNc79ly1fzk7xf3wsaj30de042t9u.jpg)

##### CentOS 7 执行service iptables start出现redirecting to systemctl ...Failed to ...not loaded.

在centOS 7系统中运行

service iptables stop

发现出错

解决办法

1. 使用systemctl

systemctl [start|stop|restart|save|status] iptables.service

2. 安装iptables-services

切换到root用户下，执行：

yum install iptables-services


![image-20190126193912484](https://ws1.sinaimg.cn/large/006tNc79ly1fzk7yti4kzj30de02mmy1.jpg)

systemctl enable iptables.service //设置开机启动

之后就可以使用以下指令了：

service iptables [start|stop|restart|save|status]

##### CentOS6关闭防火墙使用以下命令，

//临时关闭

service iptables stop

//禁止开机启动

chkconfig iptables off

##### CentOS7中若使用同样的命令会报错，

stop  iptables.service

Failed to stop iptables.service: Unit iptables.service not loaded.

这是因为CentOS7版本后防火墙默认使用firewalld，因此在CentOS7中关闭防火墙使用以下命令，

//临时关闭

systemctl stop firewalld

//禁止开机启动

systemctl disable firewalld

Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.

Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.

当然，如果安装了iptables-service，也可以使用下面的命令，

yum install -y iptables-services

//关闭防火墙

service iptables stop

Redirecting to /bin/systemctl stop  iptables.service

//检查防火墙状态

service iptables status

Redirecting to /bin/systemctl status  iptables.service

iptables.service - IPv4 firewall with iptables

Loaded: loaded (/usr/lib/systemd/system/iptables.service; disabled; vendor preset: disabled)

Active: inactive (dead)

访问成功！



### 怎么使用 [www.shop.com](http://www.shop.com)访问

修改host文件

Mac

1、首先开启一个文件夹，点击上方【前往】->【前往文件夹】。

2、输入“/private/etc/hosts”，点击【前往】。

3、自动开启“etc”文件夹，找到【hosts文件】，并将其拉到桌面上，也就是复制一份hosts文件到桌面上，这样才能修改桌面上的hosts文件。

4、“右键”桌面上hosts文件，选择【打开文件的应用程序】，使用【文字编辑】开启。

5、下面就可以开启编辑hosts文件

6、编辑完后就可以把桌面上的hosts文件拉回到“/private/etc文件夹中”，会弹出询问框点击“确认”，并“取代”即可。

之后把ip地址写进host文件172.31.12.218

172.31.12.218  [www.shop.com](http://www.shop.com) 保存

之后可以[www.shop.com:8080](http://www.shop.com:8080)这么访问

接着修改tomcat配置文件

先停止tomcat，切换到tomcat的conf目录下，修改配置文件

vi server.xml

```sh
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```



找到他的端口号位置，把8080改成80

保存，重启，在访问[www.shop.com](http://www.shop.com)

# 十、安装mysql

1、Yum install mysql

2、yum install mysql-devel

3、yum install mysql-server

安装mysql和mysql-devel都成功，但是安装mysql-server失败，如下：

![image-20190126194201847](https://ws2.sinaimg.cn/large/006tNc79ly1fzk81rbi0bj30cm0de3zj.jpg)

```sh
[root@yl-web yl]# yum install mysql-server
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.sina.cn
 * extras: mirrors.sina.cn
 * updates: mirrors.sina.cn
No package mysql-server available.
Error: Nothing to do
```

查资料发现是CentOS 7 版本将MySQL数据库软件从默认的程序列表中移除，用mariadb代替了。

##### 有两种解决办法：

##### 1、方法一：安装mariadb

MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可。开发这个分支的原因之一是：甲骨文公司收购了MySQL后，有将MySQL闭源的潜在风险，因此社区采用分支的方式来避开这个风险。MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。

安装mariadb，大小59 M。

[root@yl-web yl]# yum install mariadb-server mariadb 

mariadb数据库的相关命令是：

systemctl start mariadb  #启动MariaDB

systemctl stop mariadb  #停止MariaDB

systemctl restart mariadb  #重启MariaDB

systemctl enable mariadb  #设置开机启动

所以先启动数据库

[root@yl-web yl]# systemctl start mariadb

然后就可以正常使用mysql了

```sh
[root@yl-web yl]# mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 5.5.41-MariaDB MariaDB Server

Copyright (c) 2000, 2014, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.00 sec)

MariaDB [(none)]> 

```

安装mariadb后显示的也是 MariaDB [(none)]> ，可能看起来有点不习惯。

##### 下面是第二种方法。(使用第二种)

官网下载安装mysql-server

```sh
# wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
# rpm -ivh mysql-community-release-el7-5.noarch.rpm
# yum install mysql-community-server
安装成功后重启mysql服务。
# service mysqld restart
初次安装mysql，root账户没有密码。

[root@yl-web yl]# mysql -u root 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.6.26 MySQL Community Server (GPL)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.01 sec)

mysql> 
设置密码
mysql> set password for 'root'@'localhost' =password('password');
Query OK, 0 rows affected (0.00 sec)
//到此为止
mysql> 
不需要重启数据库即可生效。
在mysql安装过程中如下内容：

Installed:
  mysql-community-client.x86_64 0:5.6.26-2.el7                mysql-community-devel.x86_64 0:5.6.26-2.el7                
  mysql-community-libs.x86_64 0:5.6.26-2.el7                  mysql-community-server.x86_64 0:5.6.26-2.el7               

Dependency Installed:
  mysql-community-common.x86_64 0:5.6.26-2.el7                                                                            

Replaced:
  mariadb.x86_64 1:5.5.41-2.el7_0          mariadb-devel.x86_64 1:5.5.41-2.el7_0   mariadb-libs.x86_64 1:5.5.41-2.el7_0  
  mariadb-server.x86_64 1:5.5.41-2.el7_0  

所以安装完以后mariadb自动就被替换了，将不再生效。
[root@yl-web yl]# rpm -qa |grep mariadb
[root@yl-web yl]# 

```

##### 配置mysql

1、编码

mysql配置文件为/etc/my.cnf

最后加上编码配置

```sh
[mysql]

default-character-set =utf8
```



这里的字符编码必须和/usr/share/mysql/charsets/Index.xml中一致。

![image-20190126194429412](https://ws1.sinaimg.cn/large/006tNc79ly1fzk84bpplsj30be06ut8q.jpg)

2、远程连接设置

把在所有数据库的所有表的所有权限赋值给位于所有IP地址的root用户。

mysql> grant all privileges on *.* to root@'%'identified by 'password';

如果是新用户而不是root，则要先新建用户

mysql>create user 'username'@'%' identified by 'password';  

此时就可以进行远程连接了。

查询安装目录 whereis mysql

账户root 密码root

navicat远程连接数据库

# 十一、项目发布到服务器

1. 把之前数据库用到的sql表导出
2. 在远程连接新建是一个shop数据库
3. 右键数据库，运行sql文件
4. 导入远程mysql的数据库
5. 去到项目目录下，用maven打包
6. mvn -v查看maven版本
7. 正常打包mvn clean package.里面有测试需要删除
8. mvn clean package -Dmaven.test.skip=true

