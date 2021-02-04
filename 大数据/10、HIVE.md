# HIVE

## 1、安装mysql

- 在hadoop102上安装mysql

```sh
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm 
rpm -ivh mysql-community-release-el7-5.noarch.rpm
#查看下
[root@master ~]# ls -1 /etc/yum.repos.d/mysql-community*
/etc/yum.repos.d/mysql-community.repo
/etc/yum.repos.d/mysql-community-source.repo
#安装
yum install mysql-server
#启动
service mysqld start
#查看
service mysqld status

#修改文件
vi /etc/my.cnf
在[mysqld]下添加一行
skip-grant-tables
重启
#进入
mysql -uroot -p


set password for 'root'@'localhost'=password('123456');
ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement

键入无效，
flush privileges;

再次进行设置密码
set password for 'root'@'localhost'=password('123456');









grep "password" /var/log/mysqld.log
 cA)ppcyyQ6E
/var/lib/mysql/mysql.sock
mysql -u root -p -S /var/lib/mysql/mysql.sock

修改/etc/my.cnf

添加以下内容到/etc/my.conf:
[client]
socket = /var/lib/mysql/mysql.sock
[mysqld]
socket = /var/lib/mysql/mysql.sock



whereis mysql
 /usr/bin/mysql
 ln -s /usr/bin/mysql /usr/bin
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)

先停止数据库
service mysqld stop
service mysqld status
然后
mysqld_safe --skip-grant-tables &
然后进入数据库：
开始对用户赋权
grant all on *.* to 'root'@'localhost' identified by 'hadoop';
 flush privileges;
重新启动数据库
```

## 2、安装hive

- 上传jar包
- cd conf

```sh
 cp hive-default.xml.template hive-site.xml
```

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
<property>
  <name>javax.jdo.option.ConnectionURL</name>
  <value>jdbc:mysql://localhost:3306/hivetest?createDatabaseIfNotExist=true</value>
 </property>
<property>
  <name>javax.jdo.option.ConnectionUserName</name>
  <value>root</value>
  <description>username to use against metastore database</description>
</property>

<property>
  <name>javax.jdo.option.ConnectionPassword</name>
  <value>123456</value>
  <description>password to use against metastore database</description>
</property>

<property>
  <name>javax.jdo.option.ConnectionDriverName</name>
  <value>com.mysql.jdbc.Driver</value>
  <description>Driver class name for a JDBC metastore</description>
</property>
</configuration>
```



把驱动包：mysql-connector-java-5.1.10.jar

上传到hive/lib目录下

执行hive命令

然后进入mysql，showdatabases;看见新建的数据库hivetest

```sh
hive> show databases;
OK
default
Time taken: 1.019 seconds, Fetched: 1 row(s)
#创建数据库
create database test;
#使用数据库
use test
#创建表
create table hive_log(
log string
)
#通过读取文件，把数据添加到表里
load data local inpath '/opt/module/hive/conf/hive-site.xml' into table hive_log;

Loading data to table test.hive_log
Table test.hive_log stats: [numFiles=1, totalSize=825]
OK
Time taken: 1.537 seconds

select count(*) from hive_log;
#显示25 (行)

#hadoop103:8088 查看
```

