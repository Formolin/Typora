# Dubbo

## 安装zookeeper

### docker-compose.yml

```text
version: '3.1'
services:
    zoo1:
        image: zookeeper
        restart: always
        hostname: zoo1
        ports:
            - 2181:2181
        environment:
            ZOO_MY_ID: 1
            ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888

    zoo2:
        image: zookeeper
        restart: always
        hostname: zoo2
        ports:
            - 2182:2181
        environment:
            ZOO_MY_ID: 2
            ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888

    zoo3:
        image: zookeeper
        restart: always
        hostname: zoo3
        ports:
            - 2183:2181
        environment:
            ZOO_MY_ID: 3
            ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
```

- 在docker目录下新建zookeeper目录
- 新建yml文件
- 赋值上面配置文件
- 运行命令docker-compose up -d

验证是否安装成功

- 分别以交互方式进入容器查看

```text
docker exec -it zookeeper_zoo1_1 /bin/bash
```

```text
docker exec -it zookeeper_zoo2_1 /bin/bash
```

```text
docker exec -it zookeeper_zoo3_1 /bin/bash
```

- 使用服务端工具检查服务器状态

```text
root@UbuntuBase:/usr/local/docker/zookeeper# docker exec -it zookeeper_zoo1_1 /bin/bash
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: follower
```

```text
root@UbuntuBase:/usr/local/docker/zookeeper# docker exec -it zookeeper_zoo2_1 /bin/bash
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: follower
```

```text
root@UbuntuBase:/usr/local/docker/zookeeper# docker exec -it zookeeper_zoo3_1 /bin/bash
bash-4.3# ./bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: leader
```

从上面的验证结果可以看出：zoo1 为跟随者，zoo2 为跟随者，zoo3 为领导者

- 进入容器

- 配置文件,根目录conf下的zoo.cfg

  ```sh
  clientPort=2181
  dataDir=/data
  dataLogDir=/datalog
  tickTime=2000
  initLimit=5
  syncLimit=2
  autopurge.snapRetainCount=3
  autopurge.purgeInterval=0
  maxClientCnxns=60
  server.1=zoo1:2888:3888
  server.2=zoo2:2888:3888
  server.3=zoo3:2888:3888
  
  #zoo1可以换成ip  那么是在dataDir =  /data这个目录下配置
  
  # cd /data/
  #vi myid
  #docker 已经配置好了
  ```



## 安装dubbo客户端(前提先启动zookeeper)

- incubator-dubbo-ops-master-aishang

- Dubbo-admin

  - application.properties

- 指定注册表地址 `dubbo-admin-server/src/main/resources/application.properties`

  ```sh
  admin.registry.address=zookeeper://192.168.1.145:2182
  
  
  #集群模式
  dubbo.registry.address=zookeeper://192.168.0.105:2181?backup=192.168.0.105:2182,192.168.0.105:2183
  ```

  

- mvn clean package

- 生成jar包

- 之后切换包的所在目录

  - /Users/liujiang/Documents/jar/dubbo/incubator-dubbo-ops-master-aishang/dubbo-admin/target

- 终端 java -jar dubbo-admin-0.0.1-SNAPSHOT.jar

- 端口7001 访问localhost:7001

- 账号密码默认root



## 创建项目

### 创建第一个maven项目

- 创建文件夹hello-dubbo

- hello-dubbo-service-user-api，他不是提供者，也不是消费者，它就是接口

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

  - hello-dubbo-service-user-provider

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

  - com.aishang.hello.dubbo.service.user.provider.api.impl

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

    

  - application.yml

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

  - 此时启动的

    ```sh
    Subscribe: provider://192.168.1.171:12345/com.aishang.hello.dubbo.service.user.api.UserService
    
    
    #如果在开一服务提供者
    
    修改配置文件的端口为 123456
    
    
    Subscribe: provider://192.168.1.171:12346/com.aishang.hello.dubbo.service.user.api.UserService
    ```

    ![img](http://ww2.sinaimg.cn/large/006tNc79ly1g59oscveilj30s00dgwfo.jpg)

  目前是一个应用，两个提供者

  ## 新建服务消费者项目

  - hello-dubbo-service-user-consumer

  - pom.xml

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

  - com.aishang.hello.dubbo.service.user.consumer

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

  - ​	application.yml

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
        #找服务提供者的端口
      registry:
        id: zookeeper
        address: zookeeper://192.168.1.145:2181?backup=192.168.1.145:2182,192.168.1.145:2183
    
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

    [http://192.168.1.171:9090/hi](http://192.168.1.171:9090/hi)

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
        loadbalance: consistenthash
    
    #consistenthash\leastactive\random\roundrobin
    ```

  - ### 客户端服务级别

    ```yml
    dubbo:
      consumer:
        loadbalance: consistenthash
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

###  

通过修改 `application.yml` 配置文件的 `dubbo.protocol.port` 的端口，启动多个实例，需要多个端口，分别进行启动即可。

![hello-dubbo-service-user-provider____Documents_IDEA_hello-dubbo_hello-dubbo-service-user-provider__-_____src_main_resources_application_yml__hello-dubbo-service-user-provider_](https://ws1.sinaimg.cn/large/006tNc79ly1g2naygzsd6j32ic0icdrk.jpg)

- 访问

  ```http
  http://localhost:8080/hi
  
  
  hello dubbo i am from :12345
  
  再次访问
  
  hello dubbo i am from :12346
  ```

  