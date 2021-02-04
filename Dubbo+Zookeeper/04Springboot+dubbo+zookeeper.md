# Springboot+dubbo+zookeeper项目

Zookeeper 部署有三种方式，单机模式、集群模式、伪集群模式，以下采用 Docker 的方式部署

**注意：** 集群为大于等于3个奇数，如 3、5、7,不宜太多，集群机器多了选举和数据同步耗时长，不稳定。

```sh
cd /usr/local/docker

mkdir zookeeper

cd zooleeper

vi docker-compose.yml #复制yml内容


docker pull zookeeper


docker images


docker-compose up -d


docker ps



#查看主从，以交互模式进入

docker exec -it zookeeper_zoo1_1 /bin/bash

pwd


cd bin/


ls -al


./zkServer.sh status


Mode:follower#从节点模式
```

docker-compose.yml

```yml
version: '3.1'
services:
    'zoo1':
        image: zookeeper
        restart: always
        hostname: zoo1
        ports:
            - '2181:2181'
        environment:
            ZOO_MY_ID: 1
            ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888

    'zoo2':
        image: zookeeper
        restart: always
        hostname: zoo2
        ports:
            - '2182:2181'
        environment:
            ZOO_MY_ID: 2
            ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888

    'zoo3':
        image: zookeeper
        restart: always
        hostname: zoo3
        ports:
            - '2183:2181'
        environment:
            ZOO_MY_ID: 3
            ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
```

- 2181
  - 客户端连接zookeeper集群使用的监听端口号
- 3888
  - 选举leader使用
- 2888
  - 集群内机器通讯使用，leader和follower之间数据同步使用的端口号，leader监听此端口

# Dubbo

面向接口的远程方法调用

```java
@Resource
UserService userService;
userService.login()
  
  
  //服务提供者-定义接口
  
UserService


//服务消费者(远程方法调用)-调用接口

@Resource
UserService userService;
```

![](https://ws4.sinaimg.cn/large/006tNc79ly1g2n97rkuhrj30tg0mw3ze.jpg)

## Dubbo管理控制台

- 安装管理员界面

  ### 注意必须先开启zookeeper

- 创建项目

  - 新建文件夹hello-dubbo

  - 创建第一个maven项目，他不是提供者，也不是消费者，它就是接口

  - 放在文件夹内

    ![New_Project](https://ws1.sinaimg.cn/large/006tNc79ly1g2n713kb2cj31460b8dgn.jpg)

  - pom

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <!--只负责定义接口，所以就是jar包-->
        <groupId>com.aishang</groupId>
        <artifactId>hello-dubbo-service-user-api</artifactId>
        <version>1.0-SNAPSHOT</version>
        <packaging>jar</packaging>
        <!--改变字符集-->
        <properties>
            <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
            <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
            <java.version>1.8</java.version>
        </properties>
    
    </project>
    ```

  - 创建package(com.aishang.hello.dubbo.service.user.api)

    ![hello-dubbo-service-user-api____Documents_IDEA_hello-dubbo_hello-dubbo-service-user-api__-_____src_main_java_com_aishang_hello_dubbo_service_user_api_UserService_java__hello-dubbo-service-user-api_](https://ws2.sinaimg.cn/large/006tNc79ly1g2n7eqgax6j31640gyq5o.jpg)

    

- 创建第二个项目

  - Spring initializr创建

  - 创建服务提供者provider

    ![New_Project](https://ws3.sinaimg.cn/large/006tNc79ly1g2n89acda3j31520u0acb.jpg)

  - 这个项目不是web项目，他是dubbo的服务提供者，通讯是rpc，不是http，所以不是web项目，就是一个普通javase项目![New_Project](https://ws2.sinaimg.cn/large/006tNc79ly1g2n88w2yhxj31ae0h0dhl.jpg)

  - 引入pom依赖时

    ```xml
      <!--配置自己的服务提供者接口-->
            <dependency>
                <groupId>com.aishang</groupId>
                <artifactId>hello-dubbo-service-user-api</artifactId>
                <version>${project.version}</version>
            </dependency>
    
    
    这个版本是引用user-api的版本号
    但是不同项目他引用不到，maven先上本地仓库中找，没有在上中央仓库
    
    但是这个项目的我们自定义的，多以在user-api项目
    的终端下执行命令，构建maven的依赖
    mvn clean install
    ```

    ![hello-dubbo-service-user-api____Documents_IDEA_hello-dubbo_hello-dubbo-service-user-api__-_hello-dubbo-service-user-api](https://ws4.sinaimg.cn/large/006tNc79ly1g2n7y8mavbj31rs0d8771.jpg)

  pom

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <parent>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-parent</artifactId>
          <version>2.1.4.RELEASE</version>
          <relativePath/> <!-- lookup parent from repository -->
      </parent>
      <groupId>com.aishang</groupId>
      <artifactId>hello-dubbo-service-user-provider</artifactId>
      <version>0.0.1-SNAPSHOT</version>
      <name>hello-dubbo-service-user-provider</name>
      <description>Demo project for Spring Boot</description>
  
      <properties>
          <java.version>1.8</java.version>
      </properties>
  
      <dependencies>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter</artifactId>
          </dependency>
  
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-test</artifactId>
              <scope>test</scope>
          </dependency>
  
          <!--springboot状态检查的依赖-->
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-actuator</artifactId>
          </dependency>
          <!--dubbo-->
          <dependency>
              <groupId>com.alibaba.boot</groupId>
              <artifactId>dubbo-spring-boot-starter</artifactId>
              <version>0.2.0</version>
          </dependency>
          <!--配置自己的服务提供者接口-->
          <dependency>
              <groupId>com.aishang</groupId>
              <artifactId>hello-dubbo-service-user-api</artifactId>
              <version>1.0-SNAPSHOT</version>
          </dependency>
  
      </dependencies>
  
      <build>
          <plugins>
              <plugin>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-maven-plugin</artifactId>
              </plugin>
          </plugins>
      </build>
  
  </project>
  
  ```

  ![hello-dubbo-service-user-provider____Documents_IDEA_hello-dubbo_hello-dubbo-service-user-provider__-_hello-dubbo-service-user-provider](https://ws4.sinaimg.cn/large/006tNc79ly1g2n89sj8tdj31gb0u00zr.jpg)
  
  - 新建包api.impl
  
    ![hello-dubbo-service-user-provider____Documents_IDEA_hello-dubbo_hello-dubbo-service-user-provider__-_hello-dubbo-service-user-provider](https://ws3.sinaimg.cn/large/006tNc79ly1g2n8bxi7z1j30mi0mijsx.jpg)
  
  - 接口实现
  
    ```java
    package com.aishang.hello.dubbo.service.user.provider.api.impl;
    
    import com.aishang.hello.dubbo.service.user.api.UserService;
    import com.alibaba.dubbo.config.annotation.Service;
    
    @Service//这里用的alibaba的注解,但是还需要spring的service，用下面这个代替
    @Component
    public class UserServiceImpl implements UserService {
        @Override
        public String sayHi() {
            return "hello dubbo";
        }
    }
    
    ```
  
    
  
    在主程序中加多一个方法
  
    ```java
    package com.aishang.hello.dubbo.service.user.provider;
    
    import com.alibaba.dubbo.container.Main;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    @SpringBootApplication
    public class HelloDubboServiceUserProviderApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(HelloDubboServiceUserProviderApplication.class, args);
            Main.main(args);//阿里巴巴的
        }
    
    }
    
    ```
  
    
  
  - yml配置文件
  
    ```yml
    spring:
      application:
        name: hello-dubbo-service-user-provider
    
    user:
      service:
        version: 1.0
    
    dubbo:
      scan:
        basePackages: com.aishang.hello.dubbo.service.user.provider.api
      application:
        id: hello-dubbo-service-user-provider
        name: hello-dubbo-service-user-provider
        qos-port: 22222
        qos-enable: true
      protocol:
        id: dubbo
        name: dubbo
        port: 12345
        status: server
      registry:
        id: zookeeper
        address: zookeeper://192.168.0.109:2181?backup=192.168.0.109:2182,192.168.0.109:2183
    
    # Enables Dubbo All Endpoints
    management:
      endpoint:
        dubbo:
          enabled: true
        dubbo-shutdown:
          enabled: true
        dubbo-configs:
          enabled: true
        dubbo-services:
          enabled: true
        dubbo-references:
          enabled: true
        dubbo-properties:
          enabled: true
      # Dubbo Health
      health:
        dubbo:
          status:
            ## StatusChecker Name defaults (default : "memory", "load" )
            defaults: memory
            ## StatusChecker Name extras (default : empty )
            extras: load,threadpool
    ```
  
  - 启动运行
  
    ![hello-dubbo-service-user-provider____Documents_IDEA_hello-dubbo_hello-dubbo-service-user-provider__-_____src_main_java_com_aishang_hello_dubbo_service_user_provider_HelloDubboServiceUserProviderApplication_java__hello-dubbo-service-user-pro](https://ws3.sinaimg.cn/large/006tNc79ly1g2n8ylrpwij31jh0u0thp.jpg)

- 新建服务消费者项目

  - 如果spring创建器不好使，我们自己创建

  - 新建文件夹，文件夹名称就是项目名

  - 用idea打开这个文件夹

  - new pom.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>com.aishang</groupId>
        <artifactId>hello-dubbo-service-user-consumer</artifactId>
        <version>1.0-SNAPSHOT</version>
        <packaging>jar</packaging>
    
        <name>hello-dubbo-service-user-consumer</name>
        <description>Demo project for Spring Boot</description>
    
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.0.6.RELEASE</version>
            <relativePath/> <!-- lookup parent from repository -->
        </parent>
    
        <properties>
            <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
            <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
            <java.version>1.8</java.version>
        </properties>
    
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
    
            <dependency>
                <groupId>com.alibaba.boot</groupId>
                <artifactId>dubbo-spring-boot-starter</artifactId>
                <version>0.2.0</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba.boot</groupId>
                <artifactId>dubbo-spring-boot-actuator</artifactId>
                <version>0.2.0</version>
            </dependency>
    
            <dependency>
                <groupId>com.aishang</groupId>
                <artifactId>hello-dubbo-service-user-api</artifactId>
                <version>1.0-SNAPSHOT</version>
            </dependency>
        </dependencies>
    
        <build>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <configuration>
                        <mainClass>com.aishang.hello.dubbo.service.user.consumer.HelloDubboServiceUserConsumerApplication</mainClass>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    
    </project>
    ```

    ![hello-dubbo-service-user-consumer____Documents_IDEA_hello-dubbo_hello-dubbo-service-user-consumer__-_hello-dubbo-service-user-consumer](https://ws4.sinaimg.cn/large/006tNc79ly1g2n97jpesaj30w40qojtg.jpg)

  - 创建目录

    - src/main/java
    - resources
    - 这个项目是个web项目，因为调用需要controller了
    - controller

  ```java
  package com.aishang.hello.dubbo.service.user.consumer.controller;
  
  import com.aishang.hello.dubbo.service.user.api.UserService;
  import com.alibaba.dubbo.config.annotation.Reference;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RestController;
  
  @RestController
  public class UserController {
  //    目的调用sayhi
  
      @Reference//这里是alibaba的
      private UserService userService;
  
      @RequestMapping("/hi")
      public String sayHi(){
          return  userService.sayHi();
      }
  }
  
  ```

  - ​	yml

    ```yml
    spring:
      application:
        name: hello-dubbo-service-user-consumer
    
    user:
      service:
        version: 1.0
    
    dubbo:
      scan:
        basePackages: com.aishang.hello.dubbo.service.user.consumer.controller
      application:
        id: hello-dubbo-service-user-consumer
        name: hello-dubbo-service-user-consumer
      protocol:
        id: dubbo
        name: dubbo
        port: 12345
      registry:
        id: zookeeper
        address: zookeeper://192.168.0.109:2181?backup=192.168.0.109:2182,192.168.0.109:2183
    
    # Enables Dubbo All Endpoints
    management:
      endpoint:
        dubbo:
          enabled: true
        dubbo-shutdown:
          enabled: true
        dubbo-configs:
          enabled: true
        dubbo-services:
          enabled: true
        dubbo-references:
          enabled: true
        dubbo-properties:
          enabled: true
      # Dubbo Health
      health:
        dubbo:
          status:
            ## StatusChecker Name defaults (default : "memory", "load" )
            defaults: memory
            ## StatusChecker Name extras (default : empty )
            extras: load,threadpool
    ```

    

  - 启动，并访问

    [http://localhost:8080/hi](http://localhost:8080/hi)

### 我们的高可用依赖的是zookeeper，那么高并发呢？需要负载均衡

## Ngnix

要实现负载均衡，就是要不是多个服务提供者，然后我们的服务消费者去轮询的消费提供者，那么就实现了服务内部的负载均衡，高并发就能成为可能

- 在集群负载均衡时，Dubbo 提供了多种均衡策略，缺省为 `random` 随机调用。

#### 负载均衡策略

- ### Random LoadBalance

  - **随机**，按权重设置随机概率
  - 在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。

- ### RoundRobin LoadBalance

  - **轮询**，按公约后的权重设置轮询比率。
  - 存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。

- ### LeastActive LoadBalance

  - **最少活跃调用数**，相同活跃数的随机，活跃数指调用前后计数差。
  - 使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大

- ### ConsistentHash LoadBalance

  - **一致性 Hash**，相同参数的请求总是发到同一提供者。
  - 当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动。
  - 算法参见：http://en.wikipedia.org/wiki/Consistent_hashing
  - 缺省只对第一个参数 Hash，如果要修改，请配置 `<dubbo:parameter key="hash.arguments" value="0,1" />`
  - 缺省用 160 份虚拟节点，如果要修改，请配置 `<dubbo:parameter key="hash.nodes" value="320" />`

- ## 配置

  - ### 服务端服务级别

    ```xml
    在服务提供者的yml中的dubbo选项下，添加
    dubbo:
    
    
      provider:
        loadbalance: leastactive
    ```

  - ### 客户端服务级别

    ```yml
    dubbo:
      consumer:
        loadbalance: leastactive
    ```

    

- ## 测试负载均衡

  - ### 测试负载均衡

    ```java
    package com.aishang.hello.dubbo.service.user.provider.api.impl;
    
    import com.aishang.hello.dubbo.service.user.api.UserService;
    import com.alibaba.dubbo.config.annotation.Service;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.stereotype.Component;
    
    @Service
    @Component
    public class UserServiceImpl implements UserService {
        //得到端口号
        @Value("${dubbo.protocol.port}")
        private String port;
    
    
        @Override
        public String sayHi() {
            return "hello dubbo i am from :"+port;
        }
    }
    
    ```

  因为端口设定的是12345，在启动的时候一杯占用，那么负载均衡需要在启动一个服务提供者，端口为12346，那么手动改动一下启动，怎么启动多个？

## 附：在 IDEA 中配置一个工程启动多个实例

### 步骤一

点击 `Run -> Edit Configurations...`

![img](https://ws2.sinaimg.cn/large/006tNc79ly1g2naiva953j30ww0daq4f.jpg)

### 步骤二

选择需要启动多实例的项目并去掉 `Single instance only` 前面的勾

![img](https://ws2.sinaimg.cn/large/006tNc79ly1g2naiw0bduj31360h8751.jpg)

或者

![Run_Debug_Configurations_和_hello-dubbo-service-user-provider____Documents_IDEA_hello-dubbo_hello-dubbo-service-user-provider__-_____src_main_resources_application_yml__hello-dubbo-service-user-provider_](https://ws4.sinaimg.cn/large/006tNc79ly1g2naxndzopj31bm0u0mzw.jpg)

### 步骤三

通过修改 `application.yml` 配置文件的 `dubbo.protocol.port` 的端口，启动多个实例，需要多个端口，分别进行启动即可。

![hello-dubbo-service-user-provider____Documents_IDEA_hello-dubbo_hello-dubbo-service-user-provider__-_____src_main_resources_application_yml__hello-dubbo-service-user-provider_](https://ws1.sinaimg.cn/large/006tNc79ly1g2naygzsd6j32ic0icdrk.jpg)

- 访问

  ```http
  http://localhost:8080/hi
  
  
  hello dubbo i am from :12345
  
  再次访问
  
  hello dubbo i am from :12346
  ```

  

## Kryo实现高效java序列化

### Dubbo 中的序列化

Dubbo RPC 是 Dubbo 体系中最核心的一种高性能、高吞吐量的远程调用方式，可以称之为多路复用的 TCP 长连接调用：

- 长连接：避免了每次调用新建 TCP 连接，提高了调用的响应速度
- 多路复用：单个 TCP 连接可交替传输多个请求和响应的消息，降低了连接的等待闲置时间，从而减少了同样并发数下的网络连接数，提高了系统吞吐量

Dubbo RPC 主要用于两个 Dubbo 系统之间的远程调用，特别适合高并发、小数据的互联网场景。而序列化对于远程调用的响应速度、吞吐量、网络带宽消耗等同样也起着至关重要的作用，是我们提升分布式系统性能的最关键因素之一。

Dubbo 中支持的序列化方式：

- dubbo 序列化：阿里尚未开发成熟的高效 java 序列化实现，阿里不建议在生产环境使用它
- hessian2 序列化：hessian 是一种跨语言的高效二进制序列化方式。但这里实际不是原生的 hessian2 序列化，而是阿里修改过的 hessian lite，它是 dubbo RPC 默认启用的序列化方式
- json 序列化：目前有两种实现，一种是采用的阿里的 fastjson 库，另一种是采用 dubbo 中自己实现的简单 json 库，但其实现都不是特别成熟，而且 json 这种文本序列化性能一般不如上面两种二进制序列化。
- java 序列化：主要是采用 JDK 自带的 Java 序列化实现，性能很不理想。

在通常情况下，这四种主要序列化方式的性能从上到下依次递减。对于 dubbo RPC 这种追求高性能的远程调用方式来说，实际上只有 1、2 两种高效序列化方式比较般配，而第 1 个 dubbo 序列化由于还不成熟，所以实际只剩下 2 可用，所以 dubbo RPC 默认采用 hessian2 序列化。

但 hessian 是一个比较老的序列化实现了，而且它是跨语言的，所以不是单独针对 Java 进行优化的。而 dubbo RPC 实际上完全是一种 Java to Java 的远程调用，其实没有必要采用跨语言的序列化方式（当然肯定也不排斥跨语言的序列化）。

最近几年，各种新的高效序列化方式层出不穷，不断刷新序列化性能的上限，最典型的包括：

- 专门针对 Java 语言的：Kryo，FST 等等
- 跨语言的：Protostuff，ProtoBuf，Thrift，Avro，MsgPack 等等

这些序列化方式的性能多数都显著优于 hessian2（甚至包括尚未成熟的 dubbo 序列化）

有鉴于此，我们为 dubbo 引入 Kryo 和 FST 这两种高效 Java 序列化实现，来逐步取代 hessian2。

其中，Kryo 是一种非常成熟的序列化实现，已经在 Twitter、Groupon、Yahoo 以及多个著名开源项目（如 Hive、Storm）中广泛的使用。而 FST 是一种较新的序列化实现，目前还缺乏足够多的成熟使用案例。

**在面向生产环境的应用中，目前更优先选择 Kryo。**

#### 启用 Kryo

在 Provider 和 Consumer 项目启用 Kryo 高速序列化功能，两个项目的配置方式相同

### 增加 Kryo 依赖

```text
<dependency>
    <groupId>de.javakaffee</groupId>
    <artifactId>kryo-serializers</artifactId>
    <version>0.42</version>
</dependency>
```

### 增加配置

![img](https://ws1.sinaimg.cn/large/006tNc79ly1g2nb7ssa8rj30s20f5mxp.jpg)

```yml
dubbo:
  scan:
    basePackages: com.aishang.hello.dubbo.service.user.consumer.controller
  application:
    id: hello-dubbo-service-user-consumer
    name: hello-dubbo-service-user-consumer
  protocol:
    id: dubbo
    name: dubbo
    port: 12345
    serialization: kryo
    optimizer: com.aishang.hello.dubbo.service.user.provider.api.impl.UserServiceImpl #换成自己的实现类,这个不配置也没问题
  registry:
    id: zookeeper
    address: zookeeper://192.168.0.109:2181?backup=192.168.0.109:2182,192.168.0.109:2183
  consumer:
    loadbalance: leastactive
```



### 注册被序列化类

要让 Kryo 和 FST 完全发挥出高性能，最好将那些需要被序列化的类注册到 dubbo 系统中，例如，我们可以实现如下回调接口：

```text
public class SerializationOptimizerImpl implements SerializationOptimizer {
    public Collection<Class> getSerializableClasses() {
        List<Class> classes = new LinkedList<Class>();
        classes.add(BidRequest.class);
        classes.add(BidResponse.class);
        classes.add(Device.class);
        classes.add(Geo.class);
        classes.add(Impression.class);
        classes.add(SeatBid.class);
        return classes;
    }
}
```



在注册这些类后，序列化的性能可能被大大提升，特别针对小数量的嵌套对象的时候。

当然，在对一个类做序列化的时候，可能还级联引用到很多类，比如 Java 集合类。针对这种情况，我们已经自动将 JDK 中的常用类进行了注册，所以你不需要重复注册它们（当然你重复注册了也没有任何影响），包括：

```text
GregorianCalendar
InvocationHandler
BigDecimal
BigInteger
Pattern
BitSet
URI
UUID
HashMap
ArrayList
LinkedList
HashSet
TreeSet
Hashtable
Date
Calendar
ConcurrentHashMap
SimpleDateFormat
Vector
BitSet
StringBuffer
StringBuilder
Object
Object[]
String[]
byte[]
char[]
int[]
float[]
double[]
```

由于注册被序列化的类仅仅是出于性能优化的目的，所以即使你忘记注册某些类也没有关系。事实上，即使不注册任何类，Kryo 和 FST 的性能依然普遍优于 hessian 和 dubbo 序列化。

#### 为什么需要手动注册

当然，有人可能会问为什么不用配置文件来注册这些类？这是因为要注册的类往往数量较多，导致配置文件冗长；而且在没有好的 IDE 支持的情况下，配置文件的编写和重构都比 Java 类麻烦得多；最后，这些注册的类一般是不需要在项目编译打包后还需要做动态修改的。

另外，有人也会觉得手工注册被序列化的类是一种相对繁琐的工作，是不是可以用 annotation 来标注，然后系统来自动发现并注册。但这里 annotation 的局限是，它只能用来标注你可以修改的类，而很多序列化中引用的类很可能是你没法做修改的（比如第三方库或者 JDK 系统类或者其他项目的类）。另外，添加 annotation 毕竟稍微的“污染”了一下代码，使应用代码对框架增加了一点点的依赖性。

除了 annotation，我们还可以考虑用其它方式来自动注册被序列化的类，例如扫描类路径，自动发现实现 Serializable 接口（甚至包括 Externalizable）的类并将它们注册。当然，我们知道类路径上能找到 Serializable 类可能是非常多的，所以也可以考虑用 package 前缀之类来一定程度限定扫描范围。

当然，在自动注册机制中，特别需要考虑如何保证服务提供端和消费端都以同样的顺序（或者 ID）来注册类，避免错位，毕竟两端可被发现然后注册的类的数量可能都是不一样的。

#### 无参构造函数和 Serializable 接口

如果被序列化的类中 `不包含无参的构造函数，则在 Kryo 的序列化中，性能将会大打折扣`，因为此时我们在底层将用 Java 的序列化来透明的取代 Kryo 序列化。所以，`尽可能为每一个被序列化的类添加无参构造函数是一种最佳实践`（当然一个 Java 类如果不自定义构造函数，默认就有无参构造函数）。

另外，Kryo 和 FST 都不需要被序列化类实现 Serializable 接口，但我们还是`建议每个被序列化类都去实现 Serializable 接口，因为这样可以保持和 Java 序列化以及 dubbo 序列化的兼容性`，另外也使我们未来采用上述某些自动注册机制带来可能。

#### 附：序列化性能分析与测试

### 测试环境

- 两台独立服务器
- 4 核 Intel(R) Xeon(R) CPU E5-2603 0 @ 1.80GHz
- 8G 内存
- 虚拟机之间网络通过百兆交换机
- CentOS 5
- JDK 7
- Tomcat 7
- JVM 参数 `-server -Xms1g -Xmx1g -XX:PermSize=64M -XX:+UseConcMarkSweepGC`

**注意：** 当然这个测试环境较有局限，故当前测试结果未必有非常权威的代表性

### 测试脚本

和 dubbo 自身的基准测试保持接近，10 个并发客户端持续不断发出请求：

- 传入嵌套复杂对象（但单个数据量很小），不做任何处理，原样返回
- 传入 50K 字符串，不做任何处理，原样返回（TODO：结果尚未列出）

进行 5 分钟性能测试。（引用 dubbo 自身测试的考虑：“主要考察序列化和网络 IO 的性能，因此服务端无任何业务逻辑。**取 10 并发是考虑到 http 协议在高并发下对 CPU 的使用率较高可能会先达到瓶颈。**”）

### Dubbo RPC 中不同序列化生成字节大小比较

序列化生成字节码的大小是一个比较有确定性的指标，它决定了远程调用的网络传输时间和带宽占用。

**针对复杂对象的结果如下（数值越小越好）：**

![img](https://ws4.sinaimg.cn/large/006tNc79ly1g2nb7t3g7vj30tl0apaa8.jpg)

![img](https://ws1.sinaimg.cn/large/006tNc79ly1g2nb7tmt1tj30ky0cm3yt.jpg)

### Dubbo RPC 中不同序列化响应时间和吞吐量对比

![img](https://ws4.sinaimg.cn/large/006tNc79ly1g2nb7u2qorj30th0g1dgc.jpg)

![img](https://ws1.sinaimg.cn/large/006tNc79ly1g2nb7uif7ej30oq0g4dge.jpg)

![img](https://ws4.sinaimg.cn/large/006tNc79ly1g2nb7vjoy0j30p00fwaao.jpg)

#### 结论

就目前结果而言，我们可以看到不管从生成字节的大小，还是平均响应时间和平均 TPS，Kryo 和 FST 相比 Dubbo RPC 中原有的序列化方式都有非常显著的改进。

## Hystrix 实现服务熔断

#### 熔断器简介

在微服务架构中，根据业务来拆分成一个个的服务，服务与服务之间可以通过 `RPC` 相互调用。为了保证其高可用，单个服务通常会集群部署。由于网络原因或者自身的原因，服务并不能保证 100% 可用，如果单个服务出现问题，调用这个服务就会出现线程阻塞，此时若有大量的请求涌入，`Servlet` 容器的线程资源会被消耗完毕，导致服务瘫痪。服务与服务之间的依赖性，故障会传播，会对整个微服务系统造成灾难性的严重后果，这就是服务故障的 **“雪崩”** 效应。

为了解决这个问题，业界提出了熔断器模型。

Netflix 开源了 Hystrix 组件，实现了熔断器模式，Spring Cloud 对这一组件进行了整合。在微服务架构中，一个请求需要调用多个服务是非常常见的，如下图：

![img](https://ws2.sinaimg.cn/large/006tNc79ly1g2nds81210j30iu0a5t8n.jpg)

较底层的服务如果出现故障，会导致连锁故障。当对特定的服务的调用的不可用达到一个阀值（Hystrix 是 **5 秒 20 次**） 熔断器将会被打开。

![img](https://ws3.sinaimg.cn/large/006tNc79ly1g2nds8aas6j30mc0d6glr.jpg)

熔断器打开后，为了避免连锁故障，通过 `fallback` 方法可以直接返回一个固定值。

### Dubbo Provider 中使用熔断器

### 在 `pom.xml` 中增加依赖

```text
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
    <version>2.0.1.RELEASE</version>
</dependency>
```

### 在 Application 中增加 `@EnableHystrix` 注解

```java
package com.funtl.hello.dubbo.service.user.provider;

import com.alibaba.dubbo.container.Main;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;

@EnableHystrix
@SpringBootApplication
public class HelloDubboServiceUserProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloDubboServiceUserProviderApplication.class, args);
        Main.main(args);
    }
}
```

### 在 Service 中增加 `@HystrixCommand` 注解

在调用方法上增加 `@HystrixCommand` 配置，此时调用会经过 Hystrix 代理

```text
package com.funtl.hello.dubbo.service.user.provider.api.impl;

import com.alibaba.dubbo.config.annotation.Service;
import com.funtl.hello.dubbo.service.user.api.UserService;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import org.springframework.beans.factory.annotation.Value;

@Service(version = "${user.service.version}")
public class UserServiceImpl implements UserService {

    @Value("${dubbo.protocol.port}")
    private String port;

    @HystrixCommand(commandProperties = {
            @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"),
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "2000")
    })
    @Override
    public String sayHi() {
//        return "Hello Dubbo, i am from port:" + port;
        throw new RuntimeException("Exception to show hystrix enabled.");
    }
}
```

### 测试熔断器

此时我们再次请求服务提供者，浏览器会报 500 异常

```text
Exception to show hystrix enabled.
```

### Dubbo Consumer 中使用熔断器

### 在 `pom.xml` 中增加依赖

```text
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
    <version>2.0.1.RELEASE</version>
</dependency>
```

### 在 Application 中增加 `@EnableHystrix` 注解

```text
package com.funtl.hello.dubbo.service.user.consumer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;

@EnableHystrix
@SpringBootApplication
public class HelloDubboServiceUserConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloDubboServiceUserConsumerApplication.class, args);
    }
}
```

### 在调用方法上增加 `@HystrixCommand` 注解，并指定 `fallbackMethod` 方法

```text
package com.funtl.hello.dubbo.service.user.consumer.controller;

import com.alibaba.dubbo.config.annotation.Reference;
import com.funtl.hello.dubbo.service.user.api.UserService;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {

    @Reference(version = "${user.service.version}")
    private UserService userService;

    @HystrixCommand(fallbackMethod = "hiError")
    @RequestMapping(value = "hi")
    public String sayHi() {
        return userService.sayHi();
    }

    public String hiError() {
        return "Hystrix fallback";
    }
}
```

### 测试熔断器

此时我们再次请求服务提供者，浏览器会显示：

```text
Hystrix fallback
```

至此，Dubbo + Hystrix 配置完成

## Hystrix 熔断器仪表盘

## 使用熔断器仪表盘监控

在 Provider 和 Consumer 项目增加 Hystrix 仪表盘功能，两个项目的改造方式相同

在 `pom.xml` 中增加依赖

```text
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
    <version>2.0.1.RELEASE</version>
</dependency>
```

## 在 Application 中增加 `@EnableHystrixDashboard` 注解

```text
package com.funtl.hello.dubbo.service.user.consumer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;
import org.springframework.cloud.netflix.hystrix.dashboard.EnableHystrixDashboard;

@EnableHystrix
@EnableHystrixDashboard
@SpringBootApplication
public class HelloDubboServiceUserConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloDubboServiceUserConsumerApplication.class, args);
    }
}
```

## 创建 `hystrix.stream` 的 Servlet 配置

Spring Boot 2.x 版本开启 Hystrix Dashboard 与 Spring Boot 1.x 的方式略有不同，需要增加一个 `HystrixMetricsStreamServlet` 的配置，代码如下：

```text
package com.funtl.hello.dubbo.service.user.consumer.config;

import com.netflix.hystrix.contrib.metrics.eventstream.HystrixMetricsStreamServlet;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class HystrixDashboardConfiguration {
    @Bean
    public ServletRegistrationBean getServlet() {
        HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
        ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
        registrationBean.setLoadOnStartup(1);
        registrationBean.addUrlMappings("/hystrix.stream");
        registrationBean.setName("HystrixMetricsStreamServlet");
        return registrationBean;
    }
}
```

## 测试 Hystrix Dashboard

浏览器端访问 http://localhost:9090/hystrix 界面如下：

![img](https://ws3.sinaimg.cn/large/006tNc79ly1g2nvjs65hhj30tp0hmtbr.jpg)

点击 Monitor Stream，进入下一个界面，访问 http://localhost:9090/hi 触发熔断后，监控界面显示效果如下：

![img](https://ws3.sinaimg.cn/large/006tNc79ly1g2nvjta2dzj31h10d2my8.jpg)

## 附：Hystrix 说明

### 什么情况下会触发 `fallback` 方法

| 名字                 | 描述                               | 触发fallback |
| -------------------- | ---------------------------------- | ------------ |
| EMIT                 | 值传递                             | NO           |
| SUCCESS              | 执行完成，没有错误                 | NO           |
| FAILURE              | 执行抛出异常                       | YES          |
| TIMEOUT              | 执行开始，但没有在允许的时间内完成 | YES          |
| BAD_REQUEST          | 执行抛出HystrixBadRequestException | NO           |
| SHORT_CIRCUITED      | 断路器打开，不尝试执行             | YES          |
| THREAD_POOL_REJECTED | 线程池拒绝，不尝试执行             | YES          |
| SEMAPHORE_REJECTED   | 信号量拒绝，不尝试执行             | YES          |

### `fallback` 方法在什么情况下会抛出异常

| 名字              | 描述                           | 抛异常 |
| ----------------- | ------------------------------ | ------ |
| FALLBACK_EMIT     | Fallback值传递                 | NO     |
| FALLBACK_SUCCESS  | Fallback执行完成，没有错误     | NO     |
| FALLBACK_FAILURE  | Fallback执行抛出出错           | YES    |
| FALLBACK_REJECTED | Fallback信号量拒绝，不尝试执行 | YES    |
| FALLBACK_MISSING  | 没有Fallback实例               | YES    |

### Hystrix Dashboard 界面监控参数

![img](https://ws4.sinaimg.cn/large/006tNc79ly1g2nvjwl73dj31i90lcaj4.jpg)

### Hystrix 常用配置信息

#### 超时时间（默认1000ms，单位：ms）

- `hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds`：在调用方配置，被该调用方的所有方法的超时时间都是该值，优先级低于下边的指定配置
- `hystrix.command.HystrixCommandKey.execution.isolation.thread.timeoutInMilliseconds`：在调用方配置，被该调用方的指定方法（HystrixCommandKey 方法名）的超时时间是该值

#### 线程池核心线程数

- `hystrix.threadpool.default.coreSize`：默认为 10

#### Queue

- `hystrix.threadpool.default.maxQueueSize`：最大排队长度。默认 -1，使用 `SynchronousQueue`。其他值则使用 `LinkedBlockingQueue`。如果要从 -1 换成其他值则需重启，即该值不能动态调整，若要动态调整，需要使用到下边这个配置
- `hystrix.threadpool.default.queueSizeRejectionThreshold`：排队线程数量阈值，默认为 5，达到时拒绝，如果配置了该选项，队列的大小是该队列

**注意：** 如果 `maxQueueSize=-1` 的话，则该选项不起作用

#### 断路器

- `hystrix.command.default.circuitBreaker.requestVolumeThreshold`：当在配置时间窗口内达到此数量的失败后，进行短路。默认 20 个（10s 内请求失败数量达到 20 个，断路器开）
- `hystrix.command.default.circuitBreaker.sleepWindowInMilliseconds`：短路多久以后开始尝试是否恢复，默认 5s
- `hystrix.command.default.circuitBreaker.errorThresholdPercentage`：出错百分比阈值，当达到此阈值后，开始短路。默认 50%

#### fallback

- `hystrix.command.default.fallback.isolation.semaphore.maxConcurrentRequests`：调用线程允许请求 `HystrixCommand.GetFallback()` 的最大数量，默认 10。超出时将会有异常抛出，注意：该项配置对于 THREAD 隔离模式也起作用

#### 属性配置参数

- 参数说明：https://github.com/Netflix/Hystrix/wiki/Configuration
- HystrixProperty 参考代码：http://www.programcreek.com/java-api-examples/index.php?source_dir=Hystrix-master/hystrix-contrib/hystrix-javanica/src/test/java/com/netflix/hystrix/contrib/javanica/test/common/configuration/command/BasicCommandPropertiesTest.java

# 项目

新建文件夹dubbo-shop

### 在文件夹下创建第一个项目

- 这个项目只做统一的依赖管理

- 新建文件夹shop-dependencies

- 新建pom文件

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <parent>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-parent</artifactId>
          <version>2.0.6.RELEASE</version>
          <relativePath/> <!-- lookup parent from repository -->
      </parent>
  
  
      <groupId>com.aishang</groupId>
      <artifactId>shop-dependencies</artifactId>
      <version>0.0.1-SNAPSHOT</version>
      <packaging>pom</packaging>
  
      <properties>
          <java.version>1.8</java.version>
          <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
          <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
          <boot-dubbo.version>0.2.0</boot-dubbo.version>
          <!--tk.mybatis-->
          <boot-mapper.version>2.0.2</boot-mapper.version>
          <cloud-hystrix.version>2.0.2.RELEASE</cloud-hystrix.version>
          <kyro.version>0.42</kyro.version>
          <mysql.version>8.0.12</mysql.version>
      </properties>
      <dependencyManagement>
          <dependencies>
  
              <dependency>
                  <groupId>com.alibaba.boot</groupId>
                  <artifactId>dubbo-spring-boot-starter</artifactId>
                  <version>${boot-dubbo.version}</version>
              </dependency>
              <dependency>
                  <groupId>com.alibaba.boot</groupId>
                  <artifactId>dubbo-spring-boot-actuator</artifactId>
                  <version>${boot-dubbo.version}</version>
              </dependency>
              <dependency>
                  <groupId>tk.mybatis</groupId>
                  <artifactId>mapper-spring-boot-starter</artifactId>
                  <version>${boot-mapper.version}</version>
              </dependency>
  
              <dependency>
                  <groupId>com.github.pagehelper</groupId>
                  <artifactId>pagehelper-spring-boot-starter</artifactId>
                  <version>1.2.5</version>
              </dependency>
  
              <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
                  <version>${cloud-hystrix.version}</version>
              </dependency>
              <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
                  <version>${cloud-hystrix.version}</version>
              </dependency>
  
              <dependency>
                  <groupId>de.javakaffee</groupId>
                  <artifactId>kryo-serializers</artifactId>
                  <version>${kryo.version}</version>
              </dependency>
  
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-web</artifactId>
              </dependency>
  
              <dependency>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-starter-test</artifactId>
                  <scope>test</scope>
              </dependency>
  
          </dependencies>
      </dependencyManagement>
  
  
  
      <build>
          <plugins>
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-compiler-plugin</artifactId>
                  <configuration>
                      <showWarnings>true</showWarnings>
                  </configuration>
              </plugin>
  
              <!--打包jar文件时，配置manifest文件，加入lib包的jar依赖-->
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-jar-plugin</artifactId>
                  <configuration>
                      <archive>
                          <addMavenDescriptor>false</addMavenDescriptor>
                      </archive>
                  </configuration>
                  <executions>
                      <execution>
                          <configuration>
                              <archive>
                                  <manifest>
                                      <addDefaultImplementationEntries>true</addDefaultImplementationEntries>
                                      <addDefaultSpecificationEntries>true</addDefaultSpecificationEntries>
                                      <addClasspath>true</addClasspath>
                                  </manifest>
                              </archive>
                          </configuration>
                      </execution>
                  </executions>
              </plugin>
  
              <!--resource-->
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-resources-plugin</artifactId>
              </plugin>
  
              <!--install-->
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-install-plugin</artifactId>
              </plugin>
              <!--clean-->
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-clean-plugin</artifactId>
              </plugin>
  
              <!--ant-->
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-antrun-plugin</artifactId>
              </plugin>
              
          </plugins>
  
          <pluginManagement>
              <plugins>
                  <plugin>
                      <groupId>org.apache.maven.plugins</groupId>
                      <artifactId>maven-javadoc-plugin</artifactId>
                      <executions>
                          <execution>
                              <phase>prepare-package</phase>
                              <goals>
                                  <goal>jar</goal>
                              </goals>
                          </execution>
                      </executions>
                  </plugin>
              </plugins>
          </pluginManagement>
  
  
          <!--资源配置文件-->
          <resources>
              <resource>
                  <directory>src/main/java</directory>
                  <excludes>
                      <exclude>**/*.java</exclude>
                  </excludes>
              </resource>
              <resource>
                  <directory>src/main/resources</directory>
              </resource>
          </resources>
      </build>
  
  </project>
  ```

  

### 创建第二个通用工具类项目

- shop-commons

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
  
  
  
      <parent>
          <groupId>com.aishang</groupId>
          <artifactId>shop-dependencies</artifactId>
          <version>0.0.1-SNAPSHOT</version>
          <relativePath>../shop-dependencies/pom.xml</relativePath>
      </parent>
  
      <artifactId>shop-commons</artifactId>
      <packaging>jar</packaging>
  
      <dependencies>
  
          <dependency>
              <groupId>org.projectlombok</groupId>
              <artifactId>lombok</artifactId>
          </dependency>
          <dependency>
              <groupId>com.google.guava</groupId>
              <artifactId>guava</artifactId>
              <version>27.0.1-jre</version>
          </dependency>
  
          <dependency>
              <groupId>org.hibernate.validator</groupId>
              <artifactId>hibernate-validator</artifactId>
          </dependency>
   				<dependency>
              <groupId>de.javakaffee</groupId>
              <artifactId>kryo-serializers</artifactId>
          </dependency>
          <dependency>
              <groupId>org.hibernate.javax.persistence</groupId>
              <artifactId>hibernate-jpa-2.1-api</artifactId>
          </dependency>
  
  
      </dependencies>
  
  </project>
  
  
  
  ```

  

### 第三个项目-通用的领域模型

- 就是通用的实体类

- Shop-domain

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
  
  
  
      <parent>
          <groupId>com.aishang</groupId>
          <artifactId>shop-dependencies</artifactId>
          <version>0.0.1-SNAPSHOT</version>
          <relativePath>../shop-dependencies/pom.xml</relativePath>
      </parent>
  
      <artifactId>shop-domain</artifactId>
      <packaging>jar</packaging>
  
      <dependencies>
  
          <dependency>
              <groupId>com.aishang</groupId>
              <artifactId>shop-commons</artifactId>
              <version>${project.parent.version}</version>
          </dependency>
  
  
      </dependencies>
  
  </project>
  
  
  
  ```

  

### 第四个项目-通用的数据访问

- dao层

- Shop-mapper

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
  
  
  
      <parent>
          <groupId>com.aishang</groupId>
          <artifactId>shop-dependencies</artifactId>
          <version>0.0.1-SNAPSHOT</version>
          <relativePath>../shop-dependencies/pom.xml</relativePath>
      </parent>
  
      <artifactId>shop-mapper</artifactId>
      <packaging>jar</packaging>
  
      <dependencies>
  
          <dependency>
              <groupId>tk.mybatis</groupId>
              <artifactId>mapper-spring-boot-starter</artifactId>
          </dependency>
          <dependency>
              <groupId>com.aishang</groupId>
              <artifactId>shop-commons</artifactId>
              <version>${project.parent.version}</version>
          </dependency>
          <dependency>
              <groupId>com.github.pagehelper</groupId>
              <artifactId>pagehelper-spring-boot-starter</artifactId>
          </dependency>
          <dependency>
              <groupId>com.alibaba</groupId>
              <artifactId>druid-spring-boot-starter</artifactId>
              <version>1.1.10</version>
          </dependency>
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <scope>runtime</scope>
          </dependency>
      </dependencies>
  
  </project>
  
  
  
  ```

- 创建通用的MyMapper接口

  ```java
  //自己的mapper，注意，该接口不能被扫描到，否则会报错
  package tk.mybaits.mapper;//注意这里的包
  
  public interface MyMapper<T>{
  
  }
  
  ```

  

### 第五个项目-数据库生成项目

- shop-database

在src/main/resources/generator/ 目录下创建generatorConfig.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <properties resource="jdbc.properties" />
    <context id="Mysql" targetRuntime="MyBatis3Simple" defaultModelType="flat">

        <property name="beginningDelimiter" value="`"></property>
        <property name="endingDelimiter" value="`"></property>

        <!-- 生成的pojo，将implements Serializable-->
        <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
            <property name="mappers" value="com.ooqiu.gaming.server.util.MyMapper"></property>
        </plugin>
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true" />
        </commentGenerator>

        <!-- 数据库链接URL、用户名、密码 -->
        <jdbcConnection driverClass="${jdbc.driverClass}"
                        connectionURL="${jdbc.connectionURL}"
                        userId="${jdbc.username}"
                        password="${jdbc.password}">
        </jdbcConnection>

        <!--
        默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer
            true，把JDBC DECIMAL 和 NUMERIC 类型解析为java.math.BigDecimal
        -->
        <!--<javaTypeResolver>-->
            <!--<property name="forceBigDecimals" value="false" />-->
        <!--</javaTypeResolver>-->

        <!--
        生成model模型，对应的包路径，以及文件存放路径(targetProject)，targetProject可以指定具体的路径,如./src/main/java，
        也可以使用“MAVEN”来自动生成，这样生成的代码会在target/generatord-source目录下
        -->
        <!--<javaModelGenerator targetPackage="com.joey.mybaties.test.pojo" targetProject="MAVEN">-->
        <javaModelGenerator targetPackage="com.ooqiu.gaming.server.database.domain" targetProject="src/main/java">
            <!--<property name="enableSubPackages" value="true"/>-->
            <!--&lt;!&ndash; 从数据库返回的值被清理前后的空格  &ndash;&gt;-->
            <!--<property name="trimStrings" value="true" />-->
        </javaModelGenerator>

        <!--对应的mapper.xml文件  -->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources">
            <!--<property name="enableSubPackages" value="true"/>-->
        </sqlMapGenerator>

        <!-- 对应的Mapper接口类文件 -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.ooqiu.gaming.server.database.mapper" targetProject="src/main/java">
            <!--<property name="enableSubPackages" value="true"/>-->
        </javaClientGenerator>


        <!-- 列出要生成代码的所有表，这里配置的是不生成Example文件 -->
        <table tableName="%" >
            <generatedKey column="id" sqlStatement="Mysql" identity="true"></generatedKey>
        </table>
    </context>
</generatorConfiguration>

```

- Jdbc.preoperties

  ```properties
  jdbc.driverClass=com.mysql.cj.jdbc.Driver
  jdbc.connectionURL=jdbc:mysql://localhost:3306/1901
  jdbc.username=root
  jdbc.password=password
  ```

  ![dubbo-shop____Documents_IDEA_dubbo-shop__-_____shop-database_src_main_resources_generator_generatorConfig_xml__shop-database_](https://ws1.sinaimg.cn/large/006tNc79ly1g2o37gf7edj30l20tcwhf.jpg)

- 更改mapper项目的pom文件

  ```xml
  <dependency>
              <groupId>com.aishang</groupId>
              <artifactId>shop-domain</artifactId>
              <version>${project.parent.version}</version>
          </dependency>
  ```

  原来依赖commons，现在实体类从shop-database中复制过来后，需要依赖domain

### 第六个项目-通用的静态资源

- Shop-static

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
  
  
  
      <parent>
          <groupId>com.aishang</groupId>
          <artifactId>shop-dependencies</artifactId>
          <version>0.0.1-SNAPSHOT</version>
          <relativePath>../shop-dependencies/pom.xml</relativePath>
      </parent>
  
      <artifactId>shop-static</artifactId>
      <packaging>jar</packaging>
  
  
  
  </project>
  
  
  
  ```

  存放静态资源文件，后台模板等通用的![dubbo-shop____Documents_IDEA_dubbo-shop__-_____shop-mapper_src_main_resources_mapper_UserMapper_xml__shop-mapper_](https://ws3.sinaimg.cn/large/006tNc79ly1g2o6w8sj6sj30hs0j8dgy.jpg)

- 后台模板-adminTle

### 第七个项目-通用的dubbo依赖项目

创建一个名为shop-dubbo项目，在之前我们知道dubbo需要创建服务提供者和消费者两个角色，他们都依赖dubbo的starterPOM，并且我们的项目都需要集成熔断机制，所以我们会提取出一个通用的项目以便于所有dubbo项目统一依赖，简化我们的开发流程

pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>




    <parent>
        <groupId>com.aishang</groupId>
        <artifactId>shop-dependencies</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <relativePath>../shop-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>shop-dubbo</artifactId>
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>dubbo-spring-boot-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
        </dependency>
      
    </dependencies>

</project>



```

### 第八个项目-管理接口

专门定义接口,shop-service-api

pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>




    <parent>
        <groupId>com.aishang</groupId>
        <artifactId>shop-dependencies</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <relativePath>../shop-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>shop-service-provider</artifactId>
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>com.aishang</groupId>
            <artifactId>shop-domain</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
    </dependencies>

</project>

```

java

```java
package com.aishang.shop.service.provider;

import com.aishang.shop.domain.TbUsers;

import java.util.List;

public interface TbUsersService {
    List<TbUsers> selectAll();
}

```



### 第九个项目-服务提供者

shop-service-provider

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>




    <parent>
        <groupId>com.aishang</groupId>
        <artifactId>shop-dependencies</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <relativePath>../shop-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>shop-service-provider</artifactId>
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <version>2.0.6.RELEASE</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>com.aishang</groupId>
            <artifactId>shop-dubbo</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
        <dependency>
            <groupId>com.aishang</groupId>
            <artifactId>shop-mapper</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
        <dependency>
            <groupId>com.aishang</groupId>
            <artifactId>shop-service-api</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
    </dependencies>
<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.aishang.shop.service.provider.ShopServiceProviderApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>


```

```java
package com.aishang.shop.service.provider;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import tk.mybatis.spring.annotation.MapperScan;

@SpringBootApplication
@MapperScan(basePackages = "com.aishang.shop.mapper")
//扫描mapper的包
public class ShopServiceProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(ShopServiceProviderApplication.class,args);
    }
}

```

yml

```yml
spring:
  application:
    name: shop-service-provider
  datasource:
    druid:
      url: jdbc:mysql://localhost:3306/1901
      username: root
      password: password
      initial-size: 1
      min-idle: 1
      max-active: 20
      test-on-borrow: true
      driver-class-name: com.mysql.cj.jdbc.Driver

server:
  port: 8501

mybatis:
  type-aliases-package: com.aishang.shop.domain
  mapper-locations: classpath:mapper/*.xml


services:
  versions:
    user:
      v1: 0.0.1

dubbo:
  scan:
    base-packages: com.aishang.shop.service.provider.api.impl
  application:
    id: shop-service-provider
    name: shop-service-provider
    qos-port: 22222
    qos-enable: true
  protocol:
    id: dubbo
    name: dubbo
    port: 20881
    status: server
    serialization: kryo
  registry:
    id: zookeeper
    address: zookeeper://192.168.0.109:2181?backup=192.168.0.109:2182,192.168.0.109:2183

# Enables Dubbo All Endpoints
management:
  endpoint:
    dubbo:
      enabled: true
    dubbo-shutdown:
      enabled: true
    dubbo-configs:
      enabled: true
    dubbo-services:
      enabled: true
    dubbo-references:
      enabled: true
    dubbo-properties:
      enabled: true
  # Dubbo Health
  health:
    dubbo:
      status:
        ## StatusChecker Name defaults (default : "memory", "load" )
        defaults: memory
        ## StatusChecker Name extras (default : empty )
        extras: load,threadpool
```

之后再provider下建包api.impl![dubbo-shop____Documents_IDEA_dubbo-shop__-_____shop-service-provider_src_main_resources_application_yml__shop-service-provider_](https://ws4.sinaimg.cn/large/006tNc79ly1g2obbj0wkuj30ig0b8my0.jpg)

实现类

```java

```

