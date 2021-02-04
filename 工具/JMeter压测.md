# JMeter

### 安装JMeter

进入JMeter的[下载地址](http://jmeter.apache.org/download_jmeter.cgi)页面，如下图，有两个版本可供下载：

- Binaries：二进制版，即已经编译好、可直接执行；

- Source：源代码版，需要自己编译；

  ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3carvuli6j311c0l2dhv.jpg)

  

我们下载[apache-jmeter-5.0.tgz](http://mirrors.tuna.tsinghua.edu.cn/apache//jmeter/binaries/apache-jmeter-5.0.tgz)这个Binaries版本，下载完成后，解压，可以通过Finder（访达）页面双击这个文件解压，也可以通过终端输入`tar zxvf apache-jmeter-5.0.tgz`解压。

### 启动JMeter



解压完成后，进入到bin目录下，通过`sh jmeter`命令来启动JMeter，如下图

![bin_—_java_◂_sh_jmeter_—_80×24](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3cavi4n4kj314a0d47af.jpg)

Don't use GUI mode for load testing：这是一段提示信息，不要使用GUI模式进行负载测试，要使用NON GUI模式

实际上，只要配置好Java的环境变量，下载JMeter，即可启动。

![Apache_JMeter__5_1_1_r1855137__和_JMeter压测_md_和_bin_—_java_◂_sh_jmeter_—_80×24](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3caw32wx6j31ca0u00yv.jpg)

### 进一步优化

现在，我们已经可以成功启动JMeter了，但是每次都需要打开终端、进入到JMeter的bin目录下，输入`sh jmeter`命令来启动，显得有点繁琐。

当我们对~/.bash_profile这个文件熟悉后，可以直接把JMeter配置到环境变量中。

还是通过`vi .bash_profile`进入到vim编辑器，输入以下命令：

```
export JMETER_HOME=/Users/liujiang/Documents/jar/apache-jmeter-5.1.1
export PATH=$JMETER_HOME/bin:$PATH
export CLASSPATH=.$JMETER_HOME/lib/ext/ApacheJMeter_core.jar:$JMETER_HOME/lib/jorphan.jar:$JMETER_HOME/lib/logkit-2.0.jar
```





退出vim编辑器，输入`source ~/.bash_profile`。接下来重点来了，直接在终端（任意目录）输入`jmeter`，即可启动JMeter。
 



### 7、更改JMeter语言为中文





 启动JMeter的GUI模式后，默认语言是英文，它也自带了几种语言，我们可以把JMeter切换成中文，从菜单栏中进行切换，方法如下图。Chinese（Simplified）的意思是中文（简体），Chinese（Traditional）的意思是中文（繁体）。

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3cb54q5c2j31560iq48l.jpg)



设置完成后，关闭JMeter，重新启动GUI模式，会发现，语言又变成英文了。所以如果要更改默认语言为中文，需要修改配置文件，即/Users/liujiang/Documents/jar/apache-jmeter-5.1.1/bin/jmeter.properties`这个文件。

用Sublime Text或者其他的文本编辑器打开这个文件，找到这块区域：

```
#Preferred GUI language. Comment out to use the JVM default locale's language.
#language=en
```

修改为：

```
#Preferred GUI language. Comment out to use the JVM default locale's language.
language=zh_CN
```

再次打开JMeter后，会发现默认语言变为中文。

# 1、入门

- 右键添加线程组

- 线程数就是并发数

- 0一起启动这10个线程

- http默认请求值，设置后，每次不用再更改

- http请求，设置请求接口

- 聚合报告

- 图形结果

  ![Java秒杀项目_哔哩哔哩__゜-゜_つロ_干杯_-bilibili](http://ww1.sinaimg.cn/large/006tNc79ly1g3cbt32qrhj312w0p2wtk.jpg)

# 2、自定义变量模拟多个用户

# 3、JMeter命令行使用

# 4、Redis压测工具，redis-benchmark

# 5、springboot打成war包

