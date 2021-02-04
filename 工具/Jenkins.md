# Jenkins

## 下载

> Jenkins.io
>
> 下载.war格式

## 安装

- 运行

- java -jar jenkins.war

  ```sh
  访问地址
  
  192.168.83.200:8080
  ```

  ![SetupWizard__Jenkins_](https://ws4.sinaimg.cn/large/006tNc79ly1g31r2t2c1wj30z00h20tr.jpg)

- 安装推荐的插件

- 安装其他插件

  - 管理

  - 插件管理

  - 搜索

    ```sh
    Rebuilder
    
    Safe Restart
    
    Coding Webhook
    
    Maven info
    
    Generic Webhook Trigger Plugin
    
    Gitlab Hook Plugin
    ```

    

在互联网时代，对于每一家公司，软件开发和发布的重要性不言而喻，目前已经形成一套标准的流程，最重要的组成部分就是持续集成（CI）及持续部署、交付（CD）。本文基于Jenkins+Docker+Git实现一套CI自动化发布流程。

## 一、发布流程设计

![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/155abfe0c1625e2ae69bbe7cb6f37050.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/394a86ec27e5e3674dfb32dbc7f6b6d9.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
**工作流程：**

1. 开发人员提交代码到Git版本仓库；
2. Jenkins人工/定时触发项目构建；
3. Jenkins拉取代码、代码编码、打包镜像、推送到镜像仓库；
4. Jenkins在Docker主机创建容器并发布。

环境规划如下：

| 角色           |      IP       |
| :------------- | :-----------: |
| Jenkins/Docker | 192.168.0.217 |
| Docker         | 192.168.0.218 |
| Git/Registry   | 192.168.0.219 |

操作系统：CentOS7.4

## 二、部署Git仓库

```
# yum install git -y
```

1. 创建Git用户并设置密码

   ```
   # useradd git
   # passwd git
   ```

2. 创建仓库

   ```
   # su - git
   # mkdir solo.git
   # cd solo.git
   # git --bare init
   ```

3. 访问创建的这个仓库

   ```
   # git clone git@192.168.0.212:/home/git/solo.git
   ```

   ## 三、准备Jenkins环境

   Jenkins是一个开源软件项目，是基于Java开发的一种持续集成工具，用于代码编译、部署、测试等工作。 Jenkins也是一个跨平台的，大多数主流的平台都支持，而且安装很简单，我们这里以部署war包方式安装它。 
   官网下载地址：https://jenkins.io/download/ 
   如图点击下载最后一个Generic Java package(war)：
   ![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/9fedb5de2470695b0c896649488f94e9.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
   在安装前需要具备Java环境，安装方式如下：

   ```
   # tar zxf jdk-8u45-linux-x64.tar.gz 
   # mv jdk-8u45-linux-x64 /usr/local/jdk1.8 
   # vi /etc/profile 
   JAVA_HOME=/usr/local/jdk1.8 
   PATH=$PATH:$JAVA_HOME/bin
   CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar 
   export JAVA_HOME PATH CLASSPATH
   # source /etc/profile
   ```

在192.168.0.217主机安装Jenkins，下载Tomcat二进制包将war包到webapps下即可：**

```
# wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war 
# wget http://mirrors.shu.edu.cn/apache/tomcat/tomcat-8/v8.5.32/bin/apache-tomcat-8.5.32.tar.gz 
# tar zxf apache-tomcat-8.5.32.tar.gz 
# mv apache-tomcat-8.5.32 /usr/local/tomcat-jenkins 
# rm /usr/local/tomcat-jenkins/webapps/* -rf 
# unzip jenkins.war -d /usr/local/tomcat-jenkins/webapps/ROOT 
# cd /usr/local/tomcat-jenkins/bin/ 
# ./startup.sh 
# tail ../logs/catalina.out -f 
... 
Jenkins initial setup is required. An admin user has been created and a password generated. 
Please use the following password to proceed to installation: 

a5f1f7c167fd4b8ab62f9497d32d97db 

This may also be found at: /root/.jenkins/secrets/initialAdminPassword ...
```

部署成功，访问Jenkins：[http://ip:8080](http://ip:8080/)

第一步：输入上面日志输出的密码：a5f1f7c167fd4b8ab62f9497d32d97db，或者从本机/root/.jenkins/secrets/initialAdminPassword文件获取，点击继续
第二步：点击“选择插件来安装”
第三步：保持默认，点击继续
第四步：创建管理员用户，保存并完成
第五步：设置Jenkins访问地址，保持默认，点击保存完成

安装完成，开始使用Jenkins：
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/feb5367f4549d2a44fab7345061e8e83.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

## 四、部署私有镜像仓库

Docker Hub作为Docker默认官方公共镜像；如果想自己搭建私有镜像仓库，官方也提供registry镜像，使得搭建私有仓库非常简单。
在192.168.0.219部署：

```
# docker run -d -v /opt/registry:/var/lib/registry -p 5000:5000 --restart=always --name registry registry
```

接下来测试registry可用性。
由于Docker CLI默认以HTTPS访问，而部署的registry并未提供HTTPS，因此，需要在pull镜像的Docker主机（192.168.0.217，192.168.0.218）添加HTTP可信任：

```
 # vi /etc/docker/daemon.json 
{"insecure-registries":["192.168.0.219:5000"]}
# service docker restart
```

## 五、安装Docker

在192.168.0.217/192.168.0.218/192.168.0.219主机安装Docker，如下：

1. 安装依赖包

   ```
   # yum install -y yum-utils device-mapper-persistent-data lvm2
   ```

2. 添加Docker软件包源：

   ```
   # yum-config-manager \
   --add-repo \
   https://download.docker.com/linux/centos/docker-ce.repo
   ```

3. 安装Docker CE

   ```
   # yum install docker-ce -y
   ```

4. 配置加速器

   ```
   # curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://bc437cce.m.daocloud.io
   ```

5. 启动并开机启动

   ```
   # systemctl start docker
   # systemctl enable docker
   ```

   ## 六、构建Tomcat基础镜像

   JAVA程序必须有JDK环境才可以运行，为了减少镜像大小及提高性能，这里直接把JDK放到宿主机上，容器以挂载形式使用。
   在192.168.0.217/192.168.0.218安装JDK：

```
# tar zxvf jdk-8u45-linux-x64.tar.gz 
# mv jdk-8u45-linux-x64 /usr/local/jdk1.8
```

Tomcat基础镜像Dockerfile：

```
# cat Dockerfile
FROM centos:7
MAINTAINER www.aliangedu.com

ENV VERSION=8.5.32
ENV JAVA_HOME /usr/local/jdk

RUN yum install wget -y

RUN wget http://mirrors.shu.edu.cn/apache/tomcat/tomcat-8/v${VERSION}/bin/apache-tomcat-${VERSION}.tar.gz && \
    tar zxf apache-tomcat-${VERSION}.tar.gz && \
    mv apache-tomcat-${VERSION} /usr/local/tomcat && \
    rm -rf apache-tomcat-${VERSION}.tar.gz /usr/local/tomcat/webapps/* && \
    mkdir /usr/local/tomcat/webapps/ROOT

EXPOSE 8080
CMD ["catalina.sh", "run"]
```

构建镜像并上传到registry：

```
# docker build -t 192.168.0.219:5000/tomcat-85 -f Dockerfile .
# docker push 192.168.0.219:5000/tomcat-85
```

## 七、Jenkins配置全局工具配置

主页面 -> 系统管理 -> 全局工具配置

指定JDK、Maven路径，Git保持默认：
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/dee6fb690771243ba24bbb8d649fecfe.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
如果Jenkins主机没有git命令，需要安装Git：
`# yum install git -y`

## 八、Jenkins安装必要插件

**1. Jenkins安装必要插件**
主页面 -> 系统管理 ->管理插件：
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/d7338a59cfa01a4ff9cb4b8bdd7d2707.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
安装SSH与Git Parameter插件。

插件说明：

- SSH：用于SSH远程Docker主机执行Shell命令
- Git Parameter：动态获取Git仓库Branch、Tag

**2. 配置SSH插件**
**第一步：先创建一个用于连接Docker主机的凭据。**
主页面 -> 凭据 -> 系统 -> 右击全局凭据 -> 添加凭据：
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/5ba0cc5c2dfe886090a4f1a73fcb8dd9.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
输入连接Docker主机的用户名和密码：
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/86e1846f16bd61f433e8dd0162b2ad44.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
**第二步：添加SSH远程主机**
主页面 -> 系统管理 -> 系统设置 -> SSH remote hosts：
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/c7b72de445ca9eadad58d0090a82722c.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

## 九、上传JAVA项目代码到Git仓库

从Github拉取开源JAVA博客系统solo：

```
# git clone https://github.com/b3log/solo
# cd solo
移除旧的推送地址，添加新的：
# git remote remove origin 
# git remote add origin git@192.168.0.219:/home/git/solo.git
提交代码到Git仓库并创建tag：
# touch src/main/webapp/a.html
# git add .
# git commit -m “a”
创建标签：
# git tag 1.0.0
推送到Git服务器：
# git push origin 1.0.0 
```

## 十、Jenkins创建项目并发布测试

主页面 -> 新建任务 -> 输入任务名称，构建一个Maven项目：
![Jenkins与Docker的自动化CI/CD实战](https://ws4.sinaimg.cn/large/006tNc79ly1g31rp5miagj30tm0gvwhz.jpg)
注意：如果没有显示“构建一个Maven项目”选项，需要在管理插件里安装“Maven Integration plugin”插件。
配置Git参数化构建：
![Jenkins与Docker的自动化CI/CD实战](https://ws3.sinaimg.cn/large/006tNc79ly1g31rp6f7jwj30x70lpmzg.jpg)
动态获取Git仓库tag，与用户交互选择Tag发布：
![Jenkins与Docker的自动化CI/CD实战](https://ws4.sinaimg.cn/large/006tNc79ly1g31rp5w504j30w50h5q4a.jpg)
指定项目Git仓库地址：
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/834802cb7769159c1b53f372d39c5e60.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
修改*/master为$Tag，Tag是上面动态获取的变量名，表示根据用户选择打代码版本。

设置maven构建命令选项：
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/cc6e1bde1c2ff195fc79e2cf1451ae84.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

利用pom.xml文件构建项目。
在Jenkins本机镜像构建与推送到镜像仓库，并SSH远程连接到Docker主机使用推送的镜像创建容器：
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/d0c41400c300596a8f7ee249143593bb.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
上图中，在Jenkins主机执行的Shell命令如下：

```
REPOSITORY=192.168.0.219:5000/solo:${Tag}
# 构建镜像
cat > Dockerfile << EOF
FROM 192.168.0.219:5000/tomcat-85:latest
RUN rm -rf /usr/local/tomcat/webapps/ROOT
COPY target/*.war /usr/local/tomcat/webapps/ROOT.war
CMD ["catalina.sh", "run"]
EOF
docker build -t $REPOSITORY .
# 上传镜像
docker push $REPOSITORY
```

上图中，SSH远程Docker主机执行的Shell命令如下：

```
REPOSITORY=192.168.0.219:5000/solo:${Tag}
# 部署
docker rm -f blog-solo |true
docker image rm $REPOSITORY |true
docker container run -d --name blog-solo -v /usr/local/jdk1.8:/usr/local/jdk -p 88:8080 $REPOSITORY
```

注：容器名称blog-solo，暴露宿主机端口88，即使用宿主机IP:88访问blog-solo项目。
blog-solo项目已配置完成，开始构建：

选择tag，开始构建：

![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/ed88e957be0585d95c032c3005a97996.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/c01032179a981a4fe75b58023459b7c2.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
点击左下角构建历史里，右击第一个查看控制台输出：
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/e4919a78a0f709aa4a0f0acf68e2ea4f.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/404819aab7db21b53d0dffd6ad5debbc.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

浏览器访问solo项目：[http://192.168.0.218:88](http://192.168.0.218:88/)
![Jenkins与Docker的自动化CI/CD实战](https://s1.51cto.com/images/blog/201808/14/8ddb3e2339c32e973ea56ee4ee90b9d6.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)
如果输出上述页面说明是正常的，页面没有加载成功样式，需要修改下项目里访问地址。

至此，自动化CI环境搭建完成，你可以模拟提交代码并打tag测试自动化发布流程。

若你在容器运维中，遇到容器方面的问题，可以给我微信↓。同样，若发现有任何纰漏，还请随时指正，相互学习，共同进步！