## Linux

### 一、简介

- Linux 是一种自由和开放源码的类 UNIX 操作系统，使用 Linux 内核。目前存在着许多不同的 Linux 发行版，可安装在各种各样的电脑硬件设备，从手机、平板电脑、路由器和影音游戏控制台，到桌上型电脑，大型电脑和超级电脑。 Linux 是一个领先的操作系统，世界上运算最快的 10 台超级电脑运行的都是 Linux 操作系统。

- Linux 操作系统也是自由软件和开放源代码发展中最著名的例子。只要遵循 GNU 通用公共许可证,任何人和机构都可以自由地使用 Linux 的所有底层源代码，也可以自由地修改和再发布。严格来讲，Linux 这个词本身只表示 Linux 内核，但在实际上人们已经习惯了用 Linux 来形容整个基于 Linux 内核，并且使用 GNU 工程各种工具和数据库的操作系统 (也被称为 GNU/ Linux)。通常情况下，Linux 被打包成供桌上型电脑和服务器使用的 Linux 发行版本。一些流行的主流 Linux 发行版本，包括 Debian (及其衍生版本 Ubuntu)，Fedora 和 OpenSUSE 等。Kernel + Softwares + Tools 就是 Linux Distribution

- 目前市面上较知名的发行版有：`Ubuntu`、RedHat、`CentOS`、Debian、Fedora、SuSE、OpenSUSE、TurboLinux、BluePoint、RedFlag、Xterm、SlackWare等。

### 二、虚拟机

- 首先说明白一点，不管是以任何方式克隆出来的虚拟机，cpu、内存的占用应该是多少就是多少，关键是在于虚拟磁盘是否和原始虚拟机共享。
- 完整克隆和链接克隆都可以从字面意思上来看出端倪，所说的完整克隆就是把原始的虚拟机全部状态的一个拷贝，除了mac地址和UUID，其余虚拟机的配置都是一样的，克隆出来的虚拟机和原始的虚拟机是相互独立的不共享任何资源，都有自己独立的CPU、内存和存储空间，好处就是安全性更高；而链接克隆呢，字面意思是通过链接......克隆出一个虚拟机，肯定是和原始的虚拟机有关系的，虽然服务器会给克隆出的虚拟机分配新的CPU和内存，但是他们共享一个虚拟磁盘文件，克隆出来的虚拟机是不能脱离原始虚拟机独立运行，好处就是链接克隆虚拟机更快，节省空间，相比较来说不是那么安全。

### 三、Linux 的目录结构

![04_局域网工作机制和网络地址配置_avi](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g39wome5t1j30y00mctk6.jpg)

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

- etc配置文件目录
- usr/local我们自己安装的程序放在这个目录下
- var存放数据的目录，比如数据库等，空间最大

### 四、命令

| 命令  | 说明                               | 语法                                          | 参数  | 参数说明                           |
| ----- | ---------------------------------- | --------------------------------------------- | ----- | ---------------------------------- |
| ls    | 显示文件和目录列表                 | ls [-alrtAFR] [name…]                         |       |                                    |
|       |                                    |                                               | -l    | 列出文件的详细信息                 |
|       |                                    |                                               | -a    | 列出当前目录所有文件，包含隐藏文件 |
| mkdir | 创建目录                           | mkdir [-p] dirName                            |       |                                    |
|       |                                    |                                               | -p    | 父目录不存在情况下先生成父目录     |
| cd    | 切换目录                           | cd [dirName]                                  |       |                                    |
| touch | 生成一个空文件                     |                                               |       |                                    |
| echo  | 生成一个带内容文件                 | echo abcd > 1.txt，echo 1234 >> 1.txt         |       |                                    |
| cat   | 显示文本文件内容                   | cat [-AbeEnstTuv] [–help] [–version] fileName |       |                                    |
| cp    | 复制文件或目录                     | cp [options] source dest                      |       |                                    |
| rm    | 删除文件                           | rm [options] name…                            |       |                                    |
|       |                                    |                                               | -f    | 强制删除文件或目录                 |
|       |                                    |                                               | -r    | 同时删除该目录下的所有文件         |
| mv    | 移动文件或目录                     | mv [options] source dest                      |       |                                    |
| find  | 在文件系统中查找指定的文件         |                                               |       |                                    |
|       |                                    |                                               | -name | 文件名                             |
| grep  | 在指定的文本文件中查找指定的字符串 |                                               |       |                                    |
| tree  | 用于以树状图列出目录的内容         |                                               |       |                                    |
| pwd   | 显示当前工作目录                   |                                               |       |                                    |
| ln    | 建立软链接                         |                                               |       |                                    |
| more  | 分页显示文本文件内容               |                                               |       |                                    |
| head  | 显示文件开头内容                   |                                               |       |                                    |
| tail  | 显示文件结尾内容                   |                                               |       |                                    |
|       |                                    |                                               | -f    | 跟踪输出                           |

#### 1、查看ip

- Ubuntu-ifconfig
- centos-ip addr

### 五、安装java

mac的host文件

```sh
/etc/hosts
```

官网：

https://www.oracle.com/technetwork/java/javase/downloads/index.html

![Java_SE_-_Downloads___Oracle_Technology_Network___Oracle](https://ws1.sinaimg.cn/large/006tKfTcly1g0kr72pdfsj30f208qdgo.jpg)

下载jre，因为是运行环境，不是开发环境

![Java_SE_Runtime_Environment_8_-_Downloads](https://ws2.sinaimg.cn/large/006tKfTcly1g0kr8hod9kj30fa097t9x.jpg)

- 克隆带链接的虚拟机
  - 如果克隆坏了，不影响初始版本
  - 创建一个虚拟机安装java、tomcat、mysql

- ssh连接虚拟机

  ```sh
  ssh 用户名@IP地址
  ssh root@172.16.141.136
  ```

  - 注意网络连接选择nat模式

  

- 使用ftp传输文件,点击连接，输入密码![站点管理器](https://ws3.sinaimg.cn/large/006tKfTcly1g0krz1tbsnj30h50e7wfz.jpg)
- 在home目录下创建专门放软件的文件夹mysoft

- 解压缩

  ```sh
  tar -xzvf jdk....
  ```

  - x解压的意思
  - z解压gz包
  - v显示解压列表
  - f名字 jdk..

- 软件只能放到usr/local下

  - 在usr/local创建java目录

    ```sh
    mkdir /usr/local/java
    ```

  - 然后将我们的jre移动到该目录下

    ```sh
     mv jre1.8.0_201/ /usr/local/java/
    ```

  - 切换到bin目录下，这里的是专门用于linux的脚本命令，要想运行java，需要使用./java

  - 那么切换到别的目录直接运行可以吗？

    - ##### 配置系统环境变量

      ```
      vi /etc/environment
      ```

      ##### 添加如下语句

      ```
      PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
      export JAVA_HOME=/usr/local/java/jre1.8.0_201
      export JRE_HOME=/usr/local/java/jre1.8.0_201
      export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/lib
      ```

      ##### 配置用户环境变量

      ```
      vi /etc/profile
      ```

      ##### 添加如下语句

      ```
      if [ "$PS1" ]; then
        if [ "$BASH" ] && [ "$BASH" != "/bin/sh" ]; then
          # The file bash.bashrc already sets the default PS1.
          # PS1='\h:\w\$ '
          if [ -f /etc/bash.bashrc ]; then
            . /etc/bash.bashrc
          fi
        else
          if [ "`id -u`" -eq 0 ]; then
            PS1='# '
          else
            PS1='$ '
          fi
        fi
      fi
      #添加下面语句
      export JAVA_HOME=/usr/local/java/jre1.8.0_201
      export JRE_HOME=/usr/local/java/jre1.8.0_201
      export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/lib
      export PATH=$JAVA_HOME/bin:$JAVA_HOME/bin:$PATH:$HOME/bin
      
      if [ -d /etc/profile.d ]; then
        for i in /etc/profile.d/*.sh; do
          if [ -r $i ]; then
            . $i
          fi
        done
        unset i
      fi
      ```

      ##### 使用户环境变量生效

      ```
      source /etc/profile
      ```

      ##### 测试是否安装成功

      ```
      root@UbuntuBase:/usr/local/java# java -version
      java version "1.8.0_152"
      Java(TM) SE Runtime Environment (build 1.8.0_152-b16)
      Java HotSpot(TM) 64-Bit Server VM (build 25.152-b16, mixed mode)
      
      //注意这里是Server不能是client，服务端模式
      ```

      ##### 为其他用户更新用户环境变量

      ```
      su lusifer
      source /etc/profile
      ```

### 六、安装tomcat

#### 1、下载地址,jdk和tomcat是版本匹配

<https://tomcat.apache.org/>

#### 2、解压缩并移动到指定目录

##### 解压缩

```
tar -zxvf apache-tomcat-8.5.37.tar.gz
```

##### 移动目录

```
mv apache-tomcat-8.5.37 /usr/local/tomcat
```

#### 3、常用命令

##### 启动

```
/usr/local/tomcat/bin/./startup.sh
```

访问：查看自己的ip：172.16.141.136:8080

##### 停止

```
/usr/local/tomcat/bin/shutdown.sh
```

##### 目录内执行脚本

```
./startup.sh
```

五、安装mysql