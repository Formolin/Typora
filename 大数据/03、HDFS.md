# HDFS

## 一、客户端环境准备

配置hadoop环境变量

- 终端输入echo $JAVA_HOME

- /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home

  ```sh
  JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home
  
  PATH=$JAVA_HOME/bin:$PATH:.
  CLASSPATH=$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:.
  export JAVA_HOME
  export PATH
  export CLASSPATH
  ```

  

### mac配置环境变量

```sh
输入：
sudo vi ~/.bash_profile
按回车输入密码后用vi打开用户目录下的bash_profile文件。一定要用sudo，否则没权限保存文件。

export HADOOP_HOME=/Users/liujiang/Documents/hadoop-2.7.2
export PATH=$HADOOP_HOME/bin:$PATH:.

重启终端
echo $HADOOP_HOME

hadoop


source .bash_profile
使配置生效
```

### 新建maven项目

![粘贴的图片2019_4_30_下午8_54](/Users/liujiang/Documents/Typora/imgs/粘贴的图片2019_4_30_下午8_54.png)

![粘贴的图片2019_4_30_下午8_55](/Users/liujiang/Documents/Typora/imgs/粘贴的图片2019_4_30_下午8_55.png)

pom文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.liujiang</groupId>
	<artifactId>Hdfs0326</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.apache.logging.log4j</groupId>
			<artifactId>log4j-core</artifactId>
			<version>2.8.2</version>
		</dependency>
		<dependency>
			<groupId>org.apache.hadoop</groupId>
			<artifactId>hadoop-common</artifactId>
			<version>2.7.2</version>
		</dependency>
		<dependency>
			<groupId>org.apache.hadoop</groupId>
			<artifactId>hadoop-client</artifactId>
			<version>2.7.2</version>
		</dependency>
		<dependency>
			<groupId>org.apache.hadoop</groupId>
			<artifactId>hadoop-hdfs</artifactId>
			<version>2.7.2</version>
		</dependency>
		<dependency>
			<groupId>jdk.tools</groupId>
			<artifactId>jdk.tools</artifactId>
			<version>1.8</version>
			<scope>system</scope>
			<systemPath>/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/lib/tools.jar</systemPath>
		</dependency>
	</dependencies>
</project>
```

需要在项目的src/main/resources目录下，新建一个文件，命名为“log4j.properties”，在文件中填入

```properties
log4j.rootLogger=INFO, stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m%n
log4j.appender.logfile=org.apache.log4j.FileAppender
log4j.appender.logfile.File=target/spring.log
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=%d %p [%c] - %m%n
```

![粘贴的图片2019_4_30_下午9_02](/Users/liujiang/Documents/Typora/imgs/粘贴的图片2019_4_30_下午9_02.png)

## 二、客户端测试

```java
package com.aishang.hdfs;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;

public class HDFSClient {
	public static void main(String[] args) throws IOException {
				// 获取hdfs客户端对象
		Configuration conf = new Configuration();
		conf.set("fs.defaultFS", "hdfs://hadoop102:9000");
		// 这里的ip需要在hosts文件里修改成hadoop0,之后可以直接访问hdfs://hadoop0:9000
		FileSystem fs = FileSystem.get(conf);
		// 在hdfs上创建路径
		fs.mkdirs(new Path("/411/dashen"));
		// 关闭资源
		fs.close();
		System.out.println("over");
	}
}

```

![粘贴的图片2019_4_30_下午9_03](/Users/liujiang/Documents/Typora/imgs/粘贴的图片2019_4_30_下午9_03.png)

### mac修改host文件

前往文件夹-/etc/hosts

添加192.168.0.105 hadoop0

```java
org.apache.hadoop.security.AccessControlException: Permission denied: user=liujiang, access=WRITE, inode="/dashen1/pjl.txt":root:supergroup:drwxr-xr-x
用户没有权限，你的账户是liujiang，要操作root账户的文件
更改代码如下：

package hdfs523;

import java.io.IOException;
import java.net.URI;
import java.net.URISyntaxException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;

public class HDFSClient {
	public static void main(String[] args) throws IOException, InterruptedException, URISyntaxException {
		// 获取hdfs客户端对象
		Configuration conf = new Configuration();
		//客户端设置副本数 权限 > 客户端配置文件 > hadoop配置文件
//		conf.set("dfs.replication", "5");
		// 这里的ip需要在hosts文件里修改成hadoop0,之后可以直接访问hdfs://hadoop0:9000
		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf, "root");
		// 在hdfs上创建路径
		fs.mkdirs(new Path("/411/523.txt"));
		// 关闭资源
		fs.close();
		System.out.println("over");
	}
}

```

## 三、HDFS文件上传

```java
@Test
	public void testCopyFromLocalFile() throws IOException, InterruptedException, URISyntaxException {
		Configuration conf = new Configuration();
		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf , "root");
		fs.copyFromLocalFile(new Path("/Users/liujiang/Pictures/logo/less.png"), new Path("/411/less.png"));
		fs.close();
	}
```

## 四、配置文件

放在resource目录下

- hdfs-site.xml
  - 改变副本数量，权重优先于集群里的配置

```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
	<property>
		<name>dfs.replication</name>
        <value>1</value>
	</property>
</configuration>
```

- 代码权重最高

```java
Configuration conf = new Configuration();
		conf.set("dfs.replication", "2");
		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf , "root");
		fs.copyFromLocalFile(new Path("/Users/liujiang/Pictures/logo/less.png"), new Path("/411/副本数为1.png"));
		fs.close();
```

参数优先级

参数优先级排序：（1）客户端代码中设置的值 >（2）ClassPath下的用户自定义配置文件 >（3）然后是服务器的默认配置

## 五、文件下载

```java
@Test
public void testCopyToLocalFile() throws IOException, InterruptedException, URISyntaxException{

Configuration conf = new Configuration();
		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf, "root");
		fs.copyToLocalFile(new Path("/411/less.png"), new Path("/Users/liujiang/Desktop"));
		fs.close();
}
```

## 六、文件删除

```java
//文件删除
	@Test
	public void testDelFile() throws IOException, InterruptedException, URISyntaxException {
		Configuration conf = new Configuration();
		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf, "root");
		fs.delete(new Path("/hadoop-2.7.2.tar.gz"), true);
		fs.delete(new Path("/411"), true);//true递归删除
		fs.close();
	}
```

## 七、文件更名

```java
// 文件重命名
	@Test
	public void testRename() throws IOException, InterruptedException, URISyntaxException {
		Configuration conf = new Configuration();
		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf, "root");
		fs.rename(new Path("/wc.input"), new Path("/ab.txt"));
		fs.close();
	}
```

## 八、查看文件详情

```java
// 查看文件详情
	@Test
	public void testFileInfo() throws IOException, InterruptedException, URISyntaxException {
		Configuration conf = new Configuration();
		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf, "root");
		RemoteIterator<LocatedFileStatus> listFiles = fs.listFiles(new Path("/"), true);
		while (listFiles.hasNext()) {
			LocatedFileStatus info = listFiles.next();
			System.out.println(info);
			//获取文件名称、权限、长度、块信息
			System.out.println(info.getPath().getName());
			System.out.println(info.getBlockSize());
			System.out.println(info.getPermission());
			System.out.println(info.getLen());
			System.out.println(info.getGroup());
			
			BlockLocation[] blockLocations = info.getBlockLocations();
			for (BlockLocation blockLocation : blockLocations) {
				String[] hosts = blockLocation.getHosts();
				for (String host : hosts) {
					System.out.println(host);
				}
			}
		}
		fs.close();
	}
```

## 九、判断文件和文件夹

```java
//文件还是文件夹
	@Test
	public void testFile() throws IOException, InterruptedException, URISyntaxException {
		Configuration conf = new Configuration();
		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf, "root");
		FileStatus[] listStatus = fs.listStatus(new Path("/"));
		for (FileStatus fileStatus : listStatus) {
			if (fileStatus.isFile()) {
				System.out.println("f:"+fileStatus.getPath().getName());
			}else {
				System.out.println("d:"+fileStatus.getPath().getName());
			}
		}
		fs.close();
	}
```

## 十、IO流操作

- 文件上传

  ```java
  //io流文件上传
  	@Test
  	public void testIOFileUpload() throws IOException, InterruptedException, URISyntaxException {
  		Configuration conf = new Configuration();
  		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf, "root");
  		FileInputStream fileInputStream = new FileInputStream("/Users/liujiang/Desktop/less.png");
  		FSDataOutputStream fos = fs.create(new Path("/1.png"));//必须写文件名，不自动生成
  		IOUtils.copyBytes(fileInputStream, fos, conf);
  		IOUtils.closeStream(fos);
  		IOUtils.closeStream(fileInputStream);
  		fs.close();
  	}
  ```

- 文件下载

  ```java
  	// io流文件下载
  	@Test
  	public void testIOFileDownload() throws IOException, InterruptedException, URISyntaxException {
  		Configuration conf = new Configuration();
  		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf, "root");
  		FSDataInputStream fis = fs.open(new Path("/ab.txt"));
  		FileOutputStream fos = new FileOutputStream("User/liujiang/1.txt");
  		IOUtils.copyBytes(fis, fos, conf);
  		IOUtils.closeStream(fos);
  		IOUtils.closeStream(fis);
  		fs.close();
  	}
  ```

- 文件定位读取

  ```java
  //文件定位读取
  	@Test
  	public void testIOFileLoad1() throws IOException, InterruptedException, URISyntaxException {
  		Configuration conf = new Configuration();
  		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf, "root");
  		//下载第一块
  		FSDataInputStream fis = fs.open(new Path("/hadoop-2.7.2.tar.gz"));
  		FileOutputStream fos =  new FileOutputStream("User/liujiang/hadoop-2.7.2.tar.gz.part1");
  		byte[] buf = new byte[1024];
  		for (int i = 0; i < 1024*128; i++) {
  			fis.read(buf);
  			fos.write(buf);
  		}
  		IOUtils.closeStream(fos);
  		IOUtils.closeStream(fis);
  		fs.close();
  	}
  
  
  
  //下载第二块
  	//文件定位读取
  	@Test
  	public void testIOFileLoad2() throws IOException, InterruptedException, URISyntaxException {
  		Configuration conf = new Configuration();
  		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf, "root");
  		//下载第二块
  		FSDataInputStream fis = fs.open(new Path("/hadoop-2.7.2.tar.gz"));
  		FileOutputStream fos =  new FileOutputStream("User/liujiang/hadoop-2.7.2.tar.gz.part2");
  		//设置读取起点
  		fis.seek(1024*1024*128);
  		IOUtils.copyBytes(fis, fos, conf);
  		IOUtils.closeStream(fos);
  		IOUtils.closeStream(fis);
  		fs.close();
  	}
  
  
  //命令 当前文件目录
  cat hadoop-2.7.2.tar.gz.part2 >> hadoop-2.7.2.tar.gz.part1
  ```

- 删除文件

  ```java
  //删除
  	@Test
  	public void del() throws IOException, InterruptedException, URISyntaxException {
  		Configuration conf = new Configuration();
  		FileSystem fs = FileSystem.get(new URI("hdfs://hadoop102:9000"), conf, "root");
  		boolean delete = fs.delete(new Path("/1.png"), true);
  		System.out.println(delete);
  		
  	}
  ```

  

## 十一、数据流

![0_尚硅谷大数据技术之Hadoop（HDFS__docx_105553158314880_pptx](/Users/liujiang/Documents/Typora/imgs/0_尚硅谷大数据技术之Hadoop（HDFS__docx_105553158314880_pptx.png)

## 十二、节点距离计算

![](/Users/liujiang/Documents/Typora/imgs/图片1.png)

例如，假设有数据中心d1机架r1中的节点n1。该节点可以表示为/d1/r1/n1。利用这种标记，这里给出四种距离描述

![img](/Users/liujiang/Documents/Typora/imgs/尚硅谷大数据技术之Hadoop（HDFS__docx.png)

## 十三、HDFS读数据流程

![0_尚硅谷大数据技术之Hadoop（HDFS__docx_105553158822400_pptx](/var/folders/qz/s3tnr42j5kj8jgx72bclglzc0000gn/T/com.skitch.skitch/DMDB4EB8A93-5DB8-49AD-B513-8941E8F5AB3C/0_尚硅谷大数据技术之Hadoop（HDFS__docx_105553158822400_pptx.png)

