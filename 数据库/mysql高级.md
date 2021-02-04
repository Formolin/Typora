# 索引

索引在mysql中也叫键，是存储引擎用于快速找到记录的一种数据结构，相当于字典的音序表，如果要查某个字，如果不使用音序表，则需要从几百页中逐页去查找

## 一、分类

### 1、普通索引

### 2、唯一索引

### 3、全文索引

### 4、单列索引

### 5、多列索引

### 6、空间索引

## 二、虚拟机安装mysql

```sh
yum -y install wget
```

#### 下载并安装MySQL官方的 Yum Repository

```sh
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```

```sh
yum -y install mysql57-community-release-el7-10.noarch.rpm
```

```sh
yum -y install mysql-community-server
```

## 三、创建表

```sql
create table t1(
	id int,
	name varchar(50)
);
```

批量插入

```sql
use t1;
show tables;

delimiter $$
create procedure autoinsert1()
BEGIN
declare i int default 1;
while(i<200000)do
	insert into t1.t1 values(i,'ccc');   	#t1库的t1表
	set i=i+1;
	end while;
	END$$
```

```sql
\d ;
```

```sh
#调用,往表中插入记录
call autoinsert1();
```

![4_1_MySQL索引index_环境准备_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5ir0gk4axj30tw0ew7dw.jpg)

## 四、创建表时创建索引

### 1、普通索引

- 值可以重复的

![4_2_MySQL索引index_创建及测试_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5ir2oxjfgj30rq0mkh02.jpg)

### 2、唯一索引

- unique index

![4_2_MySQL索引index_创建及测试_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5irbhb2yvj30to0b07az.jpg)

### 3、全文索引

- 一般针对文章 

![4_2_MySQL索引index_创建及测试_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5irc0zbu1j30mo0ck0zi.jpg)

### 4、多列索引

![4_2_MySQL索引index_创建及测试_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5irdc6sabj310w0c2gt5.jpg)

## 四、在已存在的表上创建索引

![4_2_MySQL索引index_创建及测试_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5irfsyvhzj31dk0n4nib.jpg)

![4_2_MySQL索引index_创建及测试_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5irh94d1xj310k0fu12d.jpg)

## 五、测试

- 没有索引查询

  ```sh
  select * from t1 where id = 190000
  
  #时间0.06秒
  
  EXPLAIN select * from t1 where id = 190000
  #显示如何查询的，里面显示200226行
  200226
  
  ```

- 加索引

```sh
create index id_index on t1.t1(id)
#查看索引
desc t1;



#时间0.00秒

EXPLAIN select * from t1 where id = 190000
#里面显示1行
```

#### 注意：如果没有where，或者没用到索引的字段，那么索引就没用

## 六、视图

### 单表视图

![5_1_MySQL视图view_基于单表创建视图_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5is3te29lj31e20ce4ml.jpg)

![5_1_MySQL视图view_基于单表创建视图_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5is4itz13j30y80fogu8.jpg)

![5_1_MySQL视图view_基于单表创建视图_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5is63f832j310003g403.jpg)

### 多表视图

![5_2_MySQL视图view_基于多表创建视图_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5is8qlbmtj30ta0ai7bt.jpg)

![5_2_MySQL视图view_基于多表创建视图_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5is9wm901j313g0k8tin.jpg)

![5_2_MySQL视图view_基于多表创建视图_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5isaz2s4qj30rk0q2h1z.jpg)

![5_2_MySQL视图view_基于多表创建视图_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5isbwqon2j30mo0hsjxt.jpg)

## 七、**MySQL** 主从备份

**1.1**主从备份概念 

什么是主从备份: 就是一种主备模式的数据库应用. 主库(Master)数据与备库(Slave)数据完全一致.
 实现数据的多重备份, 保证数据的安全.
 可以在 Master[InnoDB]和 Slave[MyISAM]中使用不同的数据库引擎,实现读写的分离 

**1.1.1 MySQL5.5** 版本后本身支持主从备份
 在老旧版本的 MySQL 数据库系统中,不支持主从备份,需要安装额外的 RPM 包. 

如果需要安装 RPM,只能在一个位置节点安装. 

**1.1.3.1** 主库操作同步到备库

所有对 Master 的操作,都会同步到 Slave 中.
 如果 Master 和 Salve 天生上环境不同,那么对 Master 的操作,可能会在 Slave 中出现错误 如: 在创建主从模式之前,Master 有 database : db1, db2, db3. Slave 有 database: db1, db2. 创建主从模式.现在的情况 Master 和 Slave 天生不同.
 主从模式创建成功后,在 Master 中 drop database db3. Slave 中抛出数据库 SQL 异常.后续 

所有的命令不能同步.
 一旦出现错误. 只能重新实现主从模式. 

## 八、安装mycat

### 1、linux安装java

```sh
vi /etc/profile


export JAVA_HOME=/opt/module/jdk1.8
export CLASSPATH=$:CLASSPATH:$JAVA_HOME/lib/ 
export PATH=$PATH:$JAVA_HOME/bin

source /etc/profile

java -version


echo $JAVA_HOME
```

安装mysql

```sh
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm

yum -y install mysql57-community-release-el7-10.noarch.rpm

yum -y install mysql-community-server



查看防火墙状态

firewall-cmd --state

停止firewall

systemctl stop firewalld.service

禁止firewall开机启动

systemctl disable firewalld.service 


systemctl start  mysqld.service

systemctl status mysqld.service

此时MySQL已经开始正常运行，不过要想进入MySQL还得先找出此时root用户的密码，通过如下命令可以在日志文件中找出密码

grep "password" /var/log/mysqld.log

mysql -uroot -p
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password';
```



### 2、安装mycat

```sh
yum -y install wget

wget http://dl.mycat.io/1.6-RELEASE/Mycat-server-1.6-RELEASE-20161028204710-linux.tar.gz
```

- 解压缩
- 配置
- /opt/module/mycat/conf



### server.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- - - Licensed under the Apache License, Version 2.0 (the "License"); 
        - you may not use this file except in compliance with the License. - You 
        may obtain a copy of the License at - - http://www.apache.org/licenses/LICENSE-2.0 
        - - Unless required by applicable law or agreed to in writing, software - 
        distributed under the License is distributed on an "AS IS" BASIS, - WITHOUT 
        WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. - See the 
        License for the specific language governing permissions and - limitations 
        under the License. -->
<!DOCTYPE mycat:server SYSTEM "server.dtd">
<mycat:server xmlns:mycat="http://io.mycat/">
        <system>
        <property name="useSqlStat">0</property>  <!-- 1为开启实时统计、0为关闭 -->
        <property name="useGlobleTableCheck">0</property>  <!-- 1为开启全加班一致性检测、0为关闭 -->

                <property name="sequnceHandlerType">2</property>
      <!--  <property name="useCompression">1</property>--> <!--1为开启mysql压缩协议-->
        <!--  <property name="fakeMySQLVersion">5.6.20</property>--> <!--设置模拟的MySQL版本号-->
        <!-- <property name="processorBufferChunk">40960</property> -->
        <!-- 
        <property name="processors">1</property> 
        <property name="processorExecutor">32</property> 
         -->
                <!--默认为type 0: DirectByteBufferPool | type 1 ByteBufferArena-->
                <property name="processorBufferPoolType">0</property>
                <!--默认是65535 64K 用于sql解析时最大文本长度 -->
                <!--<property name="maxStringLiteralLength">65535</property>-->
                <!--<property name="sequnceHandlerType">0</property>-->
                <!--<property name="backSocketNoDelay">1</property>-->
                <!--<property name="frontSocketNoDelay">1</property>-->
                <!--<property name="processorExecutor">16</property>-->
                <!--
                        <property name="serverPort">8066</property> <property name="managerPort">9066</property> 
                        <property name="idleTimeout">300000</property> <property name="bindIp">0.0.0.0</property> 
                        <property name="frontWriteQueueSize">4096</property> <property name="processors">32</property> -->
                <!--分布式事务开关，0为不过滤分布式事务，1为过滤分布式事务（如果分布式事务内只涉及全局表，则不过滤），2为不过滤分布式事务,但是记录分布式事务日志-->
                <property name="handleDistributedTransactions">0</property>
                
                        <!--
                        off heap for merge/order/group/limit      1开启   0关闭
                -->
                <property name="useOffHeapForMerge">1</property>

                <!--
                        单位为m
                -->
                <property name="memoryPageSize">1m</property>

                <!--
                        单位为k
                -->
                <property name="spillsFileBufferSize">1k</property>

                <property name="useStreamOutput">0</property>

                <!--
                        单位为m
                -->
                <property name="systemReserveMemorySize">384m</property>


                <!--是否采用zookeeper协调切换  -->
                <property name="useZKSwitch">true</property>


        </system>
        
        <!-- 全局SQL防火墙设置 -->
        <!-- 
        <firewall> 
           <whitehost>
              <host host="127.0.0.1" user="mycat"/>
              <host host="127.0.0.2" user="mycat"/>
           </whitehost>
       <blacklist check="false">
       </blacklist>
        </firewall>
        -->
         <!-- mycat连接其他数据库 -->
        <user name="root">
                <property name="password">password</property>
                <property name="schemas">t1</property>
                
                <!-- 表级 DML 权限设置 -->
                <!--            
                <privileges check="false">
                        <schema name="TESTDB" dml="0110" >
                                <table name="tb01" dml="0000"></table>
                                <table name="tb02" dml="1111"></table>
                        </schema>
                </privileges>           
                 -->
        </user>
 <!-- 要连接我，下面是账号密码 -->
        <user name="user">
                <property name="password">user</property>
                <property name="schemas">t1</property>
                <property name="readOnly">true</property>
        </user>

</mycat:server>

```

### schema.xml

```xml
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">

        <schema name="t1" checkSQLschema="false" sqlMaxLimit="100" dataNode="dn1">           
        </schema>
        <dataNode name="dn1" dataHost="localhost1" database="t1" />
        <dataHost name="localhost1" maxCon="1000" minCon="10" balance="0"
                          writeType="0" dbType="mysql" dbDriver="native" switchType="1"  slaveThreshold="100">
                <heartbeat>select user()</heartbeat>
                <!-- can have multi write hosts -->
                <writeHost host="hostM1" url="localhost:3306" user="root"
                                   password="password">
                        <!-- can have multi read hosts -->
                        <readHost host="hostS2" url="192.168.1.200:3306" user="root" password="xxx" />
                </writeHost>
                <writeHost host="hostS1" url="localhost:3316" user="root"
                                   password="123456" />
                <!-- <writeHost host="hostM2" url="localhost:3316" user="root" password="123456"/> -->
        </dataHost>
</mycat:schema>
```

![13_2_MySQL_中间件_Mycat部署_1_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g5iug4ji16j31k80nob29.jpg)