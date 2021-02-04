## 一、微服务哪些框架

### Dubbo

是阿里巴巴服务化治理的核心框架，并被广泛应用于阿里巴巴集团的各成员站点。阿里巴巴近几年对开源社区的贡献不论在国内还是国外都是引人注目的，比如：JStorm 捐赠给 Apache 并加入 Apache 基金会等，为中国互联网人争足了面子，使得阿里巴巴在国人眼里已经从电商升级为一家科技公司了。

|              | Dubbo     | Spring Cloud                 |
| :----------- | :-------- | :--------------------------- |
| 服务注册中心 | Zookeeper | Spring Cloud Netflix Eureka  |
| 服务调用方式 | RPC       | REST API                     |
| 服务网关     | 无        | Spring Cloud Netflix Zuul    |
| 断路器       | 不完善    | Spring Cloud Netflix Hystrix |
| 分布式配置   | 无        | Spring Cloud Config          |
| 服务跟踪     | 无        | Spring Cloud Sleuth          |
| 消息总线     | 无        | Spring Cloud Bus             |
| 数据流       | 无        | Spring Cloud Stream          |
| 批量任务     | 无        | Spring Cloud Task            |
| ……           | ……        | ……                           |

## 二、Spring Cloud

从命名我们就可以知道，它是 Spring Source 的产物，Spring 社区的强大背书可以说是 Java 企业界最有影响力的组织了，除了 Spring Source 之外，还有 Pivotal 和 Netflix 是其强大的后盾与技术输出。其中 Netflix 开源的整套微服务架构套件是 Spring Cloud 的核心。

Spring Cloud 是一个相对比较新的微服务框架，2016 才推出 1.0 的 Release 版本. 但是其更新特别快，几乎每 1-2 个月就有一次更新，虽然 Spring Cloud 时间最短, 但是相比 Dubbo 等 RPC 框架, Spring Cloud 提供的全套的分布式系统解决方案。

Spring Cloud 为开发者提供了在分布式系统（配置管理，服务发现，熔断，路由，微代理，控制总线，一次性 Token，全居琐，Leader 选举，分布式 Session，集群状态）中快速构建的工具，使用 Spring Cloud 的开发者可以快速的启动服务或构建应用、同时能够快速和云平台资源进行对接。

- springboot-springMvc-mybatis的yml配置文件

  ```yml
  server:
   port:8001  #微服务的端口号
   
  mybatis:
   config-location:classpath:mybatis/mybatis.cfg.xml #mybatis 配置文件所在路径，在resource下新建mybatis的文件夹，下面在写配置文件
   type-aliases-package:com.aishang.springcloud.dep #所有部门别名类所在包
   mapper-locations:
   - classpath:mybatis/mapper/**/*.xml #mapper映射文件
   
  spring:
   application:
    name: microservicecloud-dept  #微服务的名字
   datasource:
    type: com.alibaba.druid.pool.DruidDataSource #当前数据源操作类型
    driver-class-name:org.gjt.mm.mysql.Driver #mysql驱动包
    url: jdbc:mysql://localhost:3306/cloudDB01 #数据库名称
    username: root
    password: 123456
    dbcp2:
     min-idle: 5 #数据库连接池的最小维持连接数
     initial-size: 5 # 初始化连接数
     max-total: 5 # 最大连接数
     max-wait-millis: 200 #等待连接获取的最大超时时间
  ```

  ![截屏2019_3_11_13_39](https://ws2.sinaimg.cn/large/006tKfTcly1g0ysuhameuj30aq08edgk.jpg)

  ![截屏2019_3_11_13_41](https://ws2.sinaimg.cn/large/006tKfTcly1g0ysw9jqj7j30ye0dh443.jpg)

![截屏2019_3_11_13_42](https://ws2.sinaimg.cn/large/006tKfTcly1g0ysx2ii0wj30xu0e1grj.jpg)



![截屏2019_3_11_13_50](https://ws3.sinaimg.cn/large/006tKfTcly1g0yt5ptapcj30eu0bidgz.jpg)

![截屏2019_3_11_13_51](https://ws3.sinaimg.cn/large/006tKfTcly1g0yt6yfghyj30jk0fe0w3.jpg)

![截屏2019_3_11_13_53](https://ws3.sinaimg.cn/large/006tKfTcly1g0yt95km4uj30bw0e2acw.jpg)

## 三、创建统一的依赖管理

### 简介

Spring Cloud 项目都是基于 Spring Boot 进行开发，并且都是使用 Maven 做项目管理工具。在实际开发中，我们一般都会创建一个依赖管理项目作为 Maven 的 Parent 项目使用，这样做可以极大的方便我们对 Jar 包版本的统一管理。

### 创建依赖管理项目

创建一个工程名为 `hello-spring-cloud-dependencies` 的项目，`pom.xml` 配置文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
    </parent>

    <groupId>com.aishang</groupId>
    <artifactId>hello-spring-cloud-dependencies</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <name>hello-spring-cloud-dependencies</name>

    <properties>
        <!-- Environment Settings -->
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>

        <!-- Spring Settings -->
        <spring-cloud.version>Finchley.RELEASE</spring-cloud.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <!-- Compiler 插件, 设定 JDK 版本 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <showWarnings>true</showWarnings>
                </configuration>
            </plugin>

            <!-- 打包 jar 文件时，配置 manifest 文件，加入 lib 包的 jar 依赖 -->
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
                                    <!-- Add directory entries -->
                                    <addDefaultImplementationEntries>true</addDefaultImplementationEntries>
                                    <addDefaultSpecificationEntries>true</addDefaultSpecificationEntries>
                                    <addClasspath>true</addClasspath>
                                </manifest>
                            </archive>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

            <!-- resource -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
            </plugin>

            <!-- install -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-install-plugin</artifactId>
            </plugin>

            <!-- clean -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-clean-plugin</artifactId>
            </plugin>

            <!-- ant -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-antrun-plugin</artifactId>
            </plugin>

            <!-- dependency -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
            </plugin>
        </plugins>

        <pluginManagement>
            <plugins>
                <!-- Java Document Generate -->
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

                <!-- YUI Compressor (CSS/JS压缩) -->
                <plugin>
                    <groupId>net.alchim31.maven</groupId>
                    <artifactId>yuicompressor-maven-plugin</artifactId>
                    <version>1.5.1</version>
                    <executions>
                        <execution>
                            <phase>prepare-package</phase>
                            <goals>
                                <goal>compress</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <encoding>UTF-8</encoding>
                        <jswarn>false</jswarn>
                        <nosuffix>true</nosuffix>
                        <linebreakpos>30000</linebreakpos>
                        <force>true</force>
                        <includes>
                            <include>**/*.js</include>
                            <include>**/*.css</include>
                        </includes>
                        <excludes>
                            <exclude>**/*.min.js</exclude>
                            <exclude>**/*.min.css</exclude>
                        </excludes>
                    </configuration>
                </plugin>
            </plugins>
        </pluginManagement>

        <!-- 资源文件配置 -->
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

    <repositories>
        <repository>
            <id>aliyun-repos</id>
            <name>Aliyun Repository</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>

        <repository>
            <id>sonatype-repos</id>
            <name>Sonatype Repository</name>
            <url>https://oss.sonatype.org/content/groups/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>sonatype-repos-s</id>
            <name>Sonatype Repository</name>
            <url>https://oss.sonatype.org/content/repositories/snapshots</url>
            <releases>
                <enabled>false</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>

        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>aliyun-repos</id>
            <name>Aliyun Repository</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </pluginRepository>
    </pluginRepositories>
</project>
```

- parent：继承了 Spring Boot 的 Parent，表示我们是一个 Spring Boot 工程
- package：`pom`，表示该项目仅当做依赖项目，没有具体的实现代码
- `spring-cloud-dependencies`：在 `properties` 配置中预定义了版本号为 `Finchley.RC1` ，表示我们的 Spring Cloud 使用的是 F 版
- build：配置了项目所需的各种插件
- repositories：配置项目下载依赖时的第三方库

在实际开发中，我们所有的项目都会依赖这个 `dependencies` 项目，整个项目周期中的所有第三方依赖的版本也都由该项目进行管理。

## 四、创建项目

1. 创建文件夹(springcloud)
2. 用idea打开这个文件夹
3. 这个文件夹就是放所有工程的，不是在new modules，而是分布式开发
4. 继续创建文件夹（hello-dep）
5. 在创建pom文件，每一个目录都是一个工程
6. 手动添加，托管pom![springcloud____Documents_springcloud__-_hello-dep](https://ws3.sinaimg.cn/large/006tNc79ly1fzl6sxg7hhj313z0caad6.jpg)

## 五、服务注册与发现

在这里，我们需要用的组件是 Spring Cloud Netflix 的 Eureka，Eureka 是一个服务注册和发现模块，相当于zookeeper

- 创建hello-spring-cloud-eureka

### 创建服务注册中心

其 `pom.xml` 文件配置如下：

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.aishang</groupId>
        <artifactId>hello-spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-eureka</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-eureka</name>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.aishang.hello.spring.cloud.eureka.EurekaApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

- 由于打包是jar，那么运行的时候，需要java -jar 包名的方式运行，那么会先找main方法，那么就需要指定入口函数是哪个

#### Application

启动一个服务注册中心，只需要一个注解 `@EnableEurekaServer`

```
package com.aishang.hello.spring.cloud.eureka;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class EurekaApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }
}
```

#### application.yml

Eureka 是一个高可用的组件，它没有后端缓存，每一个实例注册之后需要向注册中心发送心跳（因此可以在内存中完成），在默认情况下 Erureka Server 也是一个 Eureka Client ,必须要指定一个 Server。

```
spring:
  application:
    name: hello-spring-cloud-eureka
    #给项目起名，服务注册与发现，别人通过项目名找到服务注册中心

server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    #上面两句改为false代表是服务端，否则是客户端
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      #尤里卡访问地址
```

通过 `eureka.client.registerWithEureka:false` 和 `fetchRegistry:false` 来表明自己是一个 Eureka Server.

### 操作界面

Eureka Server 是有界面的，启动工程，打开浏览器访问：

[http://localhost:8761](http://localhost:8761/)

![Window_和_Eureka](https://ws4.sinaimg.cn/large/006tNc79ly1g2wjehy2lpj31gf0u0n1j.jpg)

1. 创建一个目录hello-eureka

2. 创建pom文件，复制粘贴

3. 手动添加pom，让maven管理

4. 依赖完后，创建src/mian/java

5. 在main下创建resources,右键mark dir as  选择resources

6. 在resources下创建application.yml

7. 然后创建包com.liujiang.hello.eureka

8. 创建springboot的入口类EurekaApplication

9. 启动

10. ```java
   @SpringBootApplication
   @EnableEurekaServer //开启尤里卡服务
   public class EurekaApplication {
       public static void main(String[] args) {
           SpringApplication.run(EurekaApplication.class,args);
       }
   }
   ```

## 六、创建服务提供者

当 Client 向 Server 注册时，它会提供一些元数据，例如主机和端口，URL，主页等。Eureka Server 从每个 Client 实例接收心跳消息。 如果心跳超时，则通常将该实例从注册 Server 中删除。

- hello-spring-cloud-service-provider

### POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.aishang</groupId>
        <artifactId>hello-spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-service-provider</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-service-provider</name>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.aishang.hello.spring.cloud.service.provider.ProviderApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

主要添加了依赖

```xml
  <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
```



### Application

通过注解 `@EnableEurekaClient` 表明自己是一个 Eureka Client.

```java
package com.aishang.hello.spring.cloud.service.provider;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class ProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProviderApplication.class, args);
    }
}
```

### application.yml

```yml
spring:
  application:
    name: hello-spring-cloud-service-provider

server:
  port: 8762

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
      #这里我是客户端，我需要找到尤里卡注册到服务端上
```

**注意：** 需要指明 `spring.application.name`，这个很重要，这在以后的服务与服务之间相互调用一般都是根据这个 `name`

### Controller

```java
package com.aishang.hello.spring.cloud.service.provider.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {
    @Value("${server.port}")
    private String port;

    @RequestMapping("/hi")
    public String sayHi(String message) {
        return String.format("Hi，your message is : %s i am from port : %s", message, port);
    }
}

```

启动工程，打开 [http://localhost:8761](http://localhost:8761/) ，即 Eureka Server 的网址：

![hello-spring-cloud____Documents_IDEA_hello-spring-cloud__-_____hello-spring-cloud-service-provider_src_main_java_com_aishang_hello_spring_cloud_service_provider_ProviderApplication_java__hello-spring-cloud-service-provider_](https://ws3.sinaimg.cn/large/006tNc79ly1g2wjqfx9pdj30yc0ee781.jpg)

你会发现一个服务已经注册在服务中了，服务名为 `HELLO-SPRING-CLOUD-SERVICE-ADMIN` ,端口为 `8762`

![Eureka](https://ws3.sinaimg.cn/large/006tNc79ly1g2wjrm2mwbj324g0ssjvl.jpg)

```
http://localhost:8762/hi?message=HelloSpring

Hi，your message is :"HelloSpring" i am from port：8762
```

1. 在创建目录，服务提供者（curd等）hello-service-admin(对admin进行curd)

2. 先启动服务端，在启动客户端

3. 写一个controller

   ```java
   @RestController
   public class AdminController {
       @Value("${server.port}")
       private String port;
       @RequestMapping("/hi")
       public String hi(String message){
   //        message = "ahahahahah";
           return String.format("你的端口号是：%s,message:%s",port,message);
       }
   }
   http://localhost:8762/hi?message=hellocloud
   ```

   

## 七、服务消费者

### 1、Ribbon

Ribbon 是一个负载均衡客户端，可以很好的控制 `http` 和 `tcp` 的一些行为。

#### 准备工作

- 启动服务提供者hello-spring-cloud-service-consumer-ribbon端口号为：`8764`

##### 创建服务消费者

创建一个工程名为 `hello-spring-cloud-web-admin-ribbon` 的服务消费者项目，`pom.xml` 配置如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.aishang</groupId>
        <artifactId>hello-spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-service-consumer-ribbon</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-service-consumer-ribbon</name>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
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
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
        </dependency>
        <!-- Spring Cloud End -->

        <!-- 解决 thymeleaf 模板引擎一定要执行严格的 html5 格式校验问题 -->
        <dependency>
            <groupId>net.sourceforge.nekohtml</groupId>
            <artifactId>nekohtml</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.aishang.hello.spring.cloud.service.consumer.ribbon.ConsumerRibbonApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

主要是增加了 Ribbon 的依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
</dependency>
```

#### Application

通过 `@EnableDiscoveryClient` 注解注册到服务中心

```java
package com.aishang.hello.spring.cloud.service.consumer.ribbon;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient//发现服务提供者
public class ConsumerRibbonApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerRibbonApplication.class, args);
    }
}


```

#### application.yml

设置程序端口号为：`8764`

```yml
spring:
  application:
    name: hello-spring-cloud-service-consumer-ribbon
  thymeleaf:
    cache: false
    mode: LEGACYHTML5
    encoding: UTF-8
    servlet:
      content-type: text/html

server:
  port: 8764

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

#### Configuration

配置注入 `RestTemplate` 的 Bean，并通过 `@LoadBalanced` 注解表明开启负载均衡功能

```java
package com.aishang.hello.spring.cloud.service.consumer.ribbon.config;

import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class RestTemplateConfiguration {

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

#### 创建测试用的 Service

在这里我们直接用的程序名替代了具体的 URL 地址，在 Ribbon 中它会根据服务名来选择具体的服务实例，根据服务实例在请求的时候会用具体的 URL 替换掉服务名，代码如下：

```java
package com.aishang.hello.spring.cloud.service.consumer.ribbon.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

@Service
public class UserService {
    @Autowired
    private RestTemplate restTemplate;

    public String sayHi(String message) {
        return restTemplate.getForObject("http://HELLO-SPRING-CLOUD-SERVICE-PROVIDER/hi?message=" + message, String.class);
    }
}

```

#### 创建测试用的 Controller

```java
package com.aishang.hello.spring.cloud.service.consumer.ribbon.controller;

import com.aishang.hello.spring.cloud.service.consumer.ribbon.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {
    @Autowired
    private UserService userService;

    @RequestMapping(value = "hi", method = RequestMethod.GET)
    public String sayHi(String message) {
        return userService.sayHi(message);
    }
}

```

#### 测试访问

在浏览器上多次访问 <http://localhost:8764/hi?message=HelloRibbon>

浏览器交替显示：

```
Hi，your message is :"HelloRibbon" i am from port：8762
Hi，your message is :"HelloRibbon" i am from port：8763
```

请求成功则表示我们已经成功实现了负载均衡功能来访问不同端口的实例

#### 此时的架构

![img](http://blog.funtl.com/assets/Lusifer201805292246004.png)

- 一个服务注册中心，Eureka Server，端口号为：`8761`
- `service-admin` 工程运行了两个实例，端口号分别为：`8762`，`8763`
- `web-admin-ribbon` 工程端口号为：`8764`
- `web-admin-ribbon` 通过 `RestTemplate` 调用 `service-admin` 接口时因为启用了负载均衡功能故会轮流调用它的 `8762` 和 `8763` 端口

#### 附

#### 在 IDEA 中配置一个工程启动多个实例

##### 步骤一

点击 `Run -> Edit Configurations...`

##### 步骤二

选择需要启动多实例的项目并去掉 `Single instance only` 前面的勾，或者在allow running in parallel上打钩

##### 步骤三

通过修改 `application.yml` 配置文件的 `server.port` 的端口，启动多个实例，需要多个端口，分别进行启动即可。

1. 创建目录hello-client-admin-ribbon
2. 添加pom
3. 创建java、resources
4. 创建包
5. 创建入口
6. 在包下创建配置文件夹config,创建RestTemplateConfiguration.java
7. 添加注解@Configuration（相当于创建了springcontext.xml），返回RestTemplate实例
8. 添加@bean注解,相当于找到了RestTemlate这个类，我要访问的是负载均衡@LoadBalanced（自己去找服务提供者）
9. 此时只有一台尤里卡，如果他崩了就出现单点故障，所以我们部署的尤里卡不安全，不能实现高可用，需要部署多台尤里卡，部署成集群模式，达到高可用
10. 模拟，启动两台service-admin
11. 先启动8762，之后改端口号为8763，相当于启动了两台

![springcloud____Documents_springcloud__-_____hello-service-admin_src_main_resources_application_yml__hello-service-admin_](https://ws1.sinaimg.cn/large/006tNc79ly1fzm3v2sfroj30qf0ldzol.jpg)

![springcloud____Documents_springcloud__-_____hello-service-admin_src_main_java_com_liujiang_hello_service_admin_ServiceAdminApplication_java__hello-service-admin_](https://ws4.sinaimg.cn/large/006tNc79ly1fzm3vvzhmhj30cn05kgly.jpg)

![Eureka](https://ws1.sinaimg.cn/large/006tNc79ly1fzm3z1h8hpj313z0jidjz.jpg)

负载效果出现了，那么消费应该一会去62，一会去63，压力就分担了

12. 继续模拟，创建一个service类

13. 我要请求的是adminService

    ```java
    @Service
    public class AdminService {
        @Autowired
        private RestTemplate restTemplate;
    
        public String sayHi(String message) {
            return restTemplate.getForObject("http://hello-service-admin/hi?message=" + message, String.class);
        }
    }
    ```

14. 创建controller

```java
@RestController
public class AdminController {
    @Autowired
    private AdminService adminService;

    @RequestMapping("/hi")
    public String sayHi(String message) {
        return adminService.sayHi(message);
    }
}
```

15. 启动ClientAdminRibbonApplication  8764

![Eureka](https://ws3.sinaimg.cn/large/006tNc79ly1fzm5nr3innj312l0kzn24.jpg)

达到负载均衡、高可用效果！

两台服务提供者，一台服务消费者

### 2、Feign(实际开发中用)

Feign 是一个声明式的伪 Http 客户端，它使得写 Http 客户端变得更简单。使用 Feign，只需要创建一个接口并注解。它具有可插拔的注解特性，可使用 Feign 注解和 JAX-RS 注解。Feign 支持可插拔的编码器和解码器。Feign 默认集成了 Ribbon，并和 Eureka 结合，默认实现了负载均衡的效果

- Feign 采用的是基于接口的注解
- Feign 整合了 ribbon

#### 创建服务消费者

- 由于是消费者，就是需要是war包了，依赖web，因为只有war包，客户可以通过浏览器访问，而jar包，springboot默认集成tomcat，只有安装jdk的才能找到入口函数，运行，对于客户来说，本机是不可能安装jdk的，多以通过war部署，也就是传统servlet模式

创建一个工程名为 `hello-spring-cloud-service-consumer-feign` 的服务消费者项目，`pom.xml` 配置如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.aishang</groupId>
        <artifactId>hello-spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-service-consumer-feign</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-service-consumer-feign</name>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
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
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!-- Spring Cloud End -->

        <!-- 解决 thymeleaf 模板引擎一定要执行严格的 html5 格式校验问题 -->
        <dependency>
            <groupId>net.sourceforge.nekohtml</groupId>
            <artifactId>nekohtml</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.aishang.hello.spring.cloud.service.consumer.feign.ConsumerFeignApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

主要是增加了 Feign 的依赖

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

#### Application

通过 `@EnableFeignClients` 注解开启 Feign 功能

```java
package com.aishang.hello.spring.cloud.service.consumer.feign;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients
public class ConsumerFeignApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerFeignApplication.class, args);
    }
}

```

#### application.yml

设置程序端口号为：`8765`

```java
spring:
  application:
    name: hello-spring-cloud-service-consumer-feign
  thymeleaf:
    cache: false
    mode: LEGACYHTML5
    encoding: UTF-8
    servlet:
      content-type: text/html

server:
  port: 8765

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

#### 创建 Feign 接口

- 创建service文件夹-创建UserService接口

通过 `@FeignClient("服务名")` 注解来指定调用哪个服务。代码如下：

```java
package com.aishang.hello.spring.cloud.service.consumer.feign.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@FeignClient(value = "hello-service-provider")
public interface UserService {
    @RequestMapping(value = "/hi",method = RequestMethod.GET)
    public String sayHi(@RequestParam("message") String message);
}
//必须这么写，否则message为空
```

- 原理是别人先请求我，之后我在找provider

#### 创建测试用的 Controller

```java
package com.aishang.hello.spring.cloud.service.consumer.feign.controller;

import com.aishang.hello.spring.cloud.service.consumer.feign.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {
    @Autowired
    private UserService userService;

    @RequestMapping("/hi")
    public String sayHi(String message) {
        return userService.sayHi(message);
    }
}

```

#### 测试访问

- 首先在启动一个provider 之前的端口是8762，手动改成8763启动后

在浏览器上多次访问 http://localhost:8765/hi?message=HelloFeign

浏览器交替显示：

```
Hi，your message is :"HelloFeign" i am from port：8762
Hi，your message is :"HelloFeign" i am from port：8763
```

请求成功则表示我们已经成功实现了 Feign 功能来访问不同端口的实例

- 相当于把provider的sayHi方法提供出去了，之后我们消费的时候，调用他

## 八、使用熔断器防止服务雪崩

### 熔断器简介

在微服务架构中，根据业务来拆分成一个个的服务，服务与服务之间可以通过 `RPC` 相互调用，在 Spring Cloud 中可以用 `RestTemplate + Ribbon` 和 `Feign`来调用。为了保证其高可用，单个服务通常会集群部署。由于网络原因或者自身的原因，服务并不能保证 100% 可用，如果单个服务出现问题，调用这个服务就会出现线程阻塞，此时若有大量的请求涌入，`Servlet` 容器的线程资源会被消耗完毕，导致服务瘫痪。服务与服务之间的依赖性，故障会传播，会对整个微服务系统造成灾难性的严重后果，这就是服务故障的 **“雪崩”** 效应。

为了解决这个问题，业界提出了熔断器模型。

Netflix 开源了 Hystrix 组件，实现了熔断器模式，Spring Cloud 对这一组件进行了整合。在微服务架构中，一个请求需要调用多个服务是非常常见的



较底层的服务如果出现故障，会导致连锁故障。当对特定的服务的调用的不可用达到一个阀值（Hystrix 是 **5 秒 20 次**） 熔断器将会被打开。



熔断器打开后，为了避免连锁故障，通过 `fallback` 方法可以直接返回一个固定值。

### 1、Ribbon 中使用熔断器

#### 在 `pom.xml` 中增加依赖

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

#### 在 Application 中增加 `@EnableHystrix` 注解

```
package com.funtl.hello.spring.cloud.web.admin.ribbon;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;

@SpringBootApplication
@EnableDiscoveryClient
@EnableHystrix
public class WebAdminRibbonApplication {
    public static void main(String[] args) {
        SpringApplication.run(WebAdminRibbonApplication.class, args);
    }
}
```

#### 在 Service 中增加 `@HystrixCommand` 注解

在 Ribbon 调用方法上增加 `@HystrixCommand` 注解并指定 `fallbackMethod` 熔断方法

```java
package com.funtl.hello.spring.cloud.web.admin.ribbon.service;

import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

@Service
public class AdminService {

    @Autowired
    private RestTemplate restTemplate;

    @HystrixCommand(fallbackMethod = "hiError")
    public String sayHi(String message) {
        return restTemplate.getForObject("http://HELLO-SPRING-CLOUD-SERVICE-ADMIN/hi?message=" + message, String.class);
    }

    public String hiError(String message) {
        return "Hi，your message is :\"" + message + "\" but request error.";
    }
}
```

#### 测试熔断器

此时我们关闭服务提供者，再次请求 <http://localhost:8764/hi?message=HelloRibbon> 浏览器会显示：

```
Hi，your message is :"HelloRibbon" but request error.
```

### 2、Feign 中使用熔断器

Feign 是自带熔断器的，但默认是关闭的。需要在配置文件中配置打开它，在配置文件增加以下代码：

```
feign:
  hystrix:
    enabled: true
```

#### 在 Service 中增加 `fallback` 指定类

```java
package com.aishang.hello.spring.cloud.service.consumer.feign.service;

import com.aishang.hello.spring.cloud.service.consumer.feign.service.hystrix.ConsumerServiceHystrix;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

@FeignClient(value = "hello-spring-cloud-service-provider",fallback = ConsumerServiceHystrix.class)
public interface UserService {
    @RequestMapping("/hi")
    public String sayHi(@RequestParam(value = "message") String message);
}

```

#### 创建熔断器类并实现对应的 Feign 接口

```java
package com.aishang.hello.spring.cloud.service.consumer.feign.service.hystrix;

import com.aishang.hello.spring.cloud.service.consumer.feign.service.UserService;
import org.springframework.stereotype.Component;

@Component
public class ConsumerServiceHystrix implements UserService {
    @Override
    public String sayHi(String message) {
        return "Hi，your message is :" + message + " but request error.";
    }
}

```

#### 测试熔断器

此时我们关闭服务提供者，再次请求 <http://localhost:8765/hi?message=HelloFeign> 浏览器会显示：

```
Hi，your message is :"HelloFeign" but request error.
```

## 九、使用熔断器仪表盘监控

### 使用熔断器仪表盘监控

在 Ribbon 和 Feign 项目增加 Hystrix 仪表盘功能，两个项目的改造方式相同

### 在 `pom.xml` 中增加依赖

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
</dependency>
```

### 在 Application 中增加 `@EnableHystrixDashboard` 注解

```java
package com.aishang.hello.spring.cloud.service.consumer.feign;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.hystrix.dashboard.EnableHystrixDashboard;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients
@EnableHystrixDashboard
public class ConsumerFeignApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerFeignApplication.class, args);
    }
}

```

### 创建 `hystrix.stream` 的 Servlet 配置

Spring Boot 2.x 版本开启 Hystrix Dashboard 与 Spring Boot 1.x 的方式略有不同，需要增加一个 `HystrixMetricsStreamServlet` 的配置，代码如下：

![hello-spring-cloud____Documents_IDEA_hello-spring-cloud__-_____hello-spring-cloud-service-consumer-feign_src_main_java_com_aishang_hello_spring_cloud_service_consumer_feign_ConsumerFeignApplication_java__hello-spring-cloud-service-consumer-](https://ws2.sinaimg.cn/large/006tNc79ly1g2wm1ka4k7j30qw0jw408.jpg)

```java
package com.aishang.hello.spring.cloud.service.consumer.feign.config;

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

### 测试 Hystrix Dashboard

浏览器端访问 http://localhost:8765/hystrix 界面如下：

![Hystrix_Dashboard](https://ws2.sinaimg.cn/large/006tNc79ly1g2wm65u2dvj31gf0u0n19.jpg)

/hystrix.stream

点击 Monitor Stream，进入下一个界面，访问 <http://localhost:8764/hi?message=HelloRibbon> 此时会出现监控界面：

### 附：Hystrix 说明

#### 什么情况下会触发 `fallback` 方法

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

#### `fallback` 方法在什么情况下会抛出异常

| 名字              | 描述                           | 抛异常 |
| ----------------- | ------------------------------ | ------ |
| FALLBACK_EMIT     | Fallback值传递                 | NO     |
| FALLBACK_SUCCESS  | Fallback执行完成，没有错误     | NO     |
| FALLBACK_FAILURE  | Fallback执行抛出出错           | YES    |
| FALLBACK_REJECTED | Fallback信号量拒绝，不尝试执行 | YES    |
| FALLBACK_MISSING  | 没有Fallback实例               | YES    |

#### Hystrix Dashboard 界面监控参数



#### Hystrix 常用配置信息

##### 超时时间（默认1000ms，单位：ms）

- `hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds`：在调用方配置，被该调用方的所有方法的超时时间都是该值，优先级低于下边的指定配置
- `hystrix.command.HystrixCommandKey.execution.isolation.thread.timeoutInMilliseconds`：在调用方配置，被该调用方的指定方法（HystrixCommandKey 方法名）的超时时间是该值

##### 线程池核心线程数

- `hystrix.threadpool.default.coreSize`：默认为 10

##### Queue

- `hystrix.threadpool.default.maxQueueSize`：最大排队长度。默认 -1，使用 `SynchronousQueue`。其他值则使用 `LinkedBlockingQueue`。如果要从 -1 换成其他值则需重启，即该值不能动态调整，若要动态调整，需要使用到下边这个配置
- `hystrix.threadpool.default.queueSizeRejectionThreshold`：排队线程数量阈值，默认为 5，达到时拒绝，如果配置了该选项，队列的大小是该队列

**注意：** 如果 `maxQueueSize=-1` 的话，则该选项不起作用

##### 断路器

- `hystrix.command.default.circuitBreaker.requestVolumeThreshold`：当在配置时间窗口内达到此数量的失败后，进行短路。默认 20 个（10s 内请求失败数量达到 20 个，断路器开）
- `hystrix.command.default.circuitBreaker.sleepWindowInMilliseconds`：短路多久以后开始尝试是否恢复，默认 5s
- `hystrix.command.default.circuitBreaker.errorThresholdPercentage`：出错百分比阈值，当达到此阈值后，开始短路。默认 50%

##### fallback

- `hystrix.command.default.fallback.isolation.semaphore.maxConcurrentRequests`：调用线程允许请求 `HystrixCommand.GetFallback()` 的最大数量，默认 10。超出时将会有异常抛出，注意：该项配置对于 THREAD 隔离模式也起作用

##### 属性配置参数

- 参数说明：<https://github.com/Netflix/Hystrix/wiki/Configuration>
- HystrixProperty 参考代码：<http://www.programcreek.com/java-api-examples/index.php?source_dir=Hystrix-master/hystrix-contrib/hystrix-javanica/src/test/java/com/netflix/hystrix/contrib/javanica/test/common/configuration/command/BasicCommandPropertiesTest.java>

## 十、使用路由网关统一访问接口

### 使用路由网关统一访问接口

在微服务架构中，需要几个基础的服务治理组件，包括服务注册与发现、服务消费、负载均衡、熔断器、智能路由、配置管理等，由这几个基础组件相互协作，共同组建了一个简单的微服务系统。一个简单的微服务系统

![082_第05章-微服务框架-SpringCloud-使用路由网关统一访问接口_mp4](https://ws4.sinaimg.cn/large/006tNc79ly1g2wmeetry9j30u00ub40y.jpg)

在 Spring Cloud 微服务系统中，一种常见的负载均衡方式是，客户端的请求首先经过负载均衡（Zuul、Ngnix），再到达服务网关（Zuul 集群），然后再到具体的服务。服务统一注册到高可用的服务注册中心集群，服务的所有的配置文件由配置服务管理，配置服务的配置文件放在 GIT 仓库，方便开发人员随时改配置。

### Zuul 简介

Zuul 的主要功能是路由转发和过滤器。路由功能是微服务的一部分，比如 `/api/user` 转发到 User 服务，`/api/shop` 转发到到 Shop 服务。Zuul 默认和 Ribbon 结合实现了负载均衡的功能。

### 创建路由网关

```text
项目名 hello-spring-cloud-zuul
```



`pom.xml` 文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>hello-spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-zuul</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-zuul</name>
    <url>http://www.funtl.com</url>
    <inceptionYear>2018-Now</inceptionYear>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
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
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.hello.spring.cloud.zuul.ZuulApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

主要是增加了 Zuul 的依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
</dependency>
```

#### Application

增加 `@EnableZuulProxy` 注解开启 Zuul 功能

```java
package com.aishang.hello.spring.cloud.zuul;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableEurekaClient
@EnableZuulProxy
public class ZuulApplication {
    public static void main(String[] args) {
        SpringApplication.run(ZuulApplication.class, args);
    }
}
```

#### application.yml

- 设置端口号为：`8769`
- 增加 Zuul 配置

```yml
spring:
  application:
    name: hello-spring-cloud-zuul

server:
  port: 8769

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/

zuul:
  routes:
#    api-a:
#      path: /api/a/**
#      serviceId: hello-spring-cloud-service-consumer-ribbon
    api-b:
      path: /api/b/**
      serviceId: hello-spring-cloud-service-consumer-feign
```

路由说明：

- 以 `/api/a` 开头的请求都转发给 `hello-spring-cloud-web-admin-ribbon` 服务
- 以 `/api/b` 开头的请求都转发给 `hello-spring-cloud-web-admin-feign` 服务

### 测试访问

依次运行 `EurekaApplication`、`ProviderApplication`、ConsumerFeignApplication`、`ZuulApplication`

![hello-spring-cloud____Documents_IDEA_hello-spring-cloud__-_____hello-spring-cloud-zuul_src_main_java_com_aishang_hello_spring_cloud_zuul_ZuulApplication_java__hello-spring-cloud-zuul_](https://ws3.sinaimg.cn/large/006tNc79ly1g2wmr6pdtjj30j60fugmv.jpg)

打开浏览器访问：<http://localhost:8769/api/a/hi?message=HelloZuul> 浏览器显示

```
Hi，your message is :"HelloZuul" i am from port：8763
```

打开浏览器访问：<http://localhost:8769/api/b/hi?message=HelloZuul> 浏览器显示

```
Hi，your message is :"HelloZuul" i am from port：8763
```

至此说明 Zuul 的路由功能配置成功

### 配置网关路由失败时的回调

```java
package com.funtl.hello.spring.cloud.zuul.fallback;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.cloud.netflix.zuul.filters.route.FallbackProvider;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.client.ClientHttpResponse;
import org.springframework.stereotype.Component;

import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.util.HashMap;
import java.util.Map;

/**
 * 路由 hello-spring-cloud-web-admin-feign 失败时的回调
 * <p>Title: WebAdminFeignFallbackProvider</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2018/7/27 6:55
 */
@Component
public class WebAdminFeignFallbackProvider implements FallbackProvider {

    @Override
    public String getRoute() {
        // ServiceId，如果需要所有调用都支持回退，则 return "*" 或 return null
        return "hello-spring-cloud-web-admin-feign";
    }

    /**
     * 如果请求服务失败，则返回指定的信息给调用者
     * @param route
     * @param cause
     * @return
     */
    @Override
    public ClientHttpResponse fallbackResponse(String route, Throwable cause) {
        return new ClientHttpResponse() {
            /**
             * 网关向 api 服务请求失败了，但是消费者客户端向网关发起的请求是成功的，
             * 不应该把 api 的 404,500 等问题抛给客户端
             * 网关和 api 服务集群对于客户端来说是黑盒
             * @return
             * @throws IOException
             */
            @Override
            public HttpStatus getStatusCode() throws IOException {
                return HttpStatus.OK;
            }

            @Override
            public int getRawStatusCode() throws IOException {
                return HttpStatus.OK.value();
            }

            @Override
            public String getStatusText() throws IOException {
                return HttpStatus.OK.getReasonPhrase();
            }

            @Override
            public void close() {

            }

            @Override
            public InputStream getBody() throws IOException {
                ObjectMapper objectMapper = new ObjectMapper();
                Map<String, Object> map = new HashMap<>();
                map.put("status", 200);
                map.put("message", "无法连接，请检查您的网络");
                return new ByteArrayInputStream(objectMapper.writeValueAsString(map).getBytes("UTF-8"));
            }

            @Override
            public HttpHeaders getHeaders() {
                HttpHeaders headers = new HttpHeaders();
                // 和 getBody 中的内容编码一致
                headers.setContentType(MediaType.APPLICATION_JSON_UTF8);
                return headers;
            }
        };
    }
}
```

![Eureka](https://ws1.sinaimg.cn/large/006tNc79ly1fzn697iomlj312l08pwg2.jpg)

## 十一、使用路由网关的服务过滤功能

### 使用路由网关的服务过滤功能

Zuul 不仅仅只是路由，还有很多强大的功能，本节演示一下它的服务过滤功能，比如用在安全验证方面。

- com.aishang.hello.spring.cloud.zuul下创建包filter

### 创建服务过滤器

继承 `ZuulFilter` 类并在类上增加 `@Component` 注解就可以使用服务过滤功能了，非常简单方便

```java
package com.aishang.hello.spring.cloud.zuul.filter;
import com.netflix.zuul.ZuulFilter;
import com.netflix.zuul.context.RequestContext;
import com.netflix.zuul.exception.ZuulException;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletRequest;
import java.io.IOException;
@Component
public class LoginFilter extends ZuulFilter {

    private static final Logger logger = LoggerFactory.getLogger(LoginFilter.class);

    /**
     * 配置过滤类型，有四种不同生命周期的过滤器类型
     * 1. pre：路由之前
     * 2. routing：路由之时
     * 3. post：路由之后
     * 4. error：发送错误调用
     * @return
     */
    @Override
    public String filterType() {
        return "pre";
    }

    /**
     * 配置过滤的顺序
     * @return
     */
    @Override
    public int filterOrder() {
        return 0;
    }

    /**
     * 配置是否需要过滤：true/需要，false/不需要
     * @return
     */
    @Override
    public boolean shouldFilter() {
        return true;
    }

    /**
     * 过滤器的具体业务代码
     * @return
     * @throws ZuulException
     */
    @Override
    public Object run() throws ZuulException {
        RequestContext context = RequestContext.getCurrentContext();
        HttpServletRequest request = context.getRequest();
        logger.info("{} >>> {}", request.getMethod(), request.getRequestURL().toString());
        String token = request.getParameter("token");
        if (token == null) {
            logger.warn("Token is empty");
            context.setSendZuulResponse(false);
            context.setResponseStatusCode(401);
            try {
                context.getResponse().getWriter().write("Token is empty");
            } catch (IOException e) {
            }
        } else {
            logger.info("OK");
        }
        return null;
    }
}
```

#### filterType

返回一个字符串代表过滤器的类型，在 Zuul 中定义了四种不同生命周期的过滤器类型

- pre：路由之前
- routing：路由之时
- post： 路由之后
- error：发送错误调用

#### filterOrder

过滤的顺序

#### shouldFilter

是否需要过滤，这里是 `true`，需要过滤

#### run

过滤器的具体业务代码

### 测试过滤器

浏览器访问：<http://localhost:8769/api/a/hi?message=HelloZuul> 网页显示

```
Token is empty
```

浏览器访问：<http://localhost:8769/api/b/hi?message=HelloZuul&token=123> 网页显示

```
Hi，your message is :"HelloZuul" i am from port：8763
```

## 十二、Spring Cloud 分布式配置中心

在分布式系统中，由于服务数量巨多，为了方便服务配置文件统一管理，实时更新，所以需要分布式配置中心组件。在 Spring Cloud 中，有分布式配置中心组件 Spring Cloud Config ，它支持配置服务放在配置服务的内存中（即本地），也支持放在远程 Git 仓库中。在 Spring Cloud Config 组件中，分两个角色，一是 Config Server，二是 Config Client。

### 1、分布式配置中心服务端

创建一个工程名为 `hello-spring-cloud-config` 的项目，`pom.xml` 配置文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.aishang</groupId>
        <artifactId>hello-spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-config</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-config</name>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
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
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.aishang.hello.spring.cloud.config.ConfigApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

主要增加了 `spring-cloud-config-server` 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

#### Application

通过 `@EnableConfigServer` 注解，开启配置服务器功能

```java
package com.aishang.hello.spring.cloud.config;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableConfigServer
@EnableEurekaClient
public class ConfigApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigApplication.class, args);
    }
}
```

#### application.yml

增加 Config 相关配置，并设置端口号为：`8888`

```java
spring:
  application:
    name: hello-spring-cloud-config
  cloud:
    config:
      label: master
      server:
        git:
          uri: https://github.com/Formolin/spring-cloud-config
          search-paths: respo
          username:
          password:

server:
  port: 8888

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

相关配置说明，如下：

- `spring.cloud.config.label`：配置仓库的分支
- `spring.cloud.config.server.git.uri`：配置 Git 仓库地址（GitHub、GitLab、码云 …）
- `spring.cloud.config.server.git.search-paths`：配置仓库路径（存放配置文件的目录）
- `spring.cloud.config.server.git.username`：访问 Git 仓库的账号
- `spring.cloud.config.server.git.password`：访问 Git 仓库的密码

注意事项：

- 如果使用 GitLab 作为仓库的话，`git.uri` 需要在结尾加上 `.git`，GitHub 则不用

### 码云

在码云上新建仓库

- spring-cloud-config
- 创建文件夹respo
- 克隆项目到本地文件夹
- 目的是以后配置文件往respo里放，之后，上传到gitlab

### github

- spring-cloud-config
- 创建文件夹respo

### 在idea上传文件测试

- 新建文件夹respo
- 随便复制一个yml文件，例如fegin的yml到repso文件夹下
- 改个名字service-consumer-feign

#### 测试

浏览器端访问：

```sh
http://localhost:8888/service-consumer-feign/master
```



显示如下：



```
<Environment>
<name>service-consumer-feign</name>
<profiles>
<profiles>master</profiles>
</profiles>
<label/>
<version>1a284f875901afae1c13712cddf559dbb86e7020</version>
<state/>
<propertySources>
<propertySources>
<name>
https://gitee.com/liujiang8800/spring-cloud-config/respo/service-consumer-feign.yml
</name>
<source>
<spring.application.name>hello-spring-cloud-service-consumer-feign</spring.application.name>
<spring.thymeleaf.cache>false</spring.thymeleaf.cache>
<spring.thymeleaf.mode>LEGACYHTML5</spring.thymeleaf.mode>
<spring.thymeleaf.encoding>UTF-8</spring.thymeleaf.encoding>
<spring.thymeleaf.servlet.content-type>text/html</spring.thymeleaf.servlet.content-type>
<server.port>8765</server.port>
<eureka.client.serviceUrl.defaultZone>http://localhost:8761/eureka/</eureka.client.serviceUrl.defaultZone>
<feign.hystrix.enabled>true</feign.hystrix.enabled>
</source>
</propertySources>
</propertySources>
</Environment>

```

证明配置服务中心可以从远程程序获取配置信息

#### 附：HTTP 请求地址和资源文件映射

- [http://ip:port/{application}/{profile}[/{label}\]](http://ip:port/%7Bapplication%7D/%7Bprofile%7D[/%7Blabel%7D])
- [http://ip:port/{application}-{profile}.yml](http://ip:port/%7Bapplication%7D-%7Bprofile%7D.yml)
- [http://ip:port/{label}/{application}-{profile}.yml](http://ip:port/%7Blabel%7D/%7Bapplication%7D-%7Bprofile%7D.yml)
- [http://ip:port/{application}-{profile}.properties](http://ip:port/%7Bapplication%7D-%7Bprofile%7D.properties)
- [http://ip:port/{label}/{application}-{profile}.properties](http://ip:port/%7Blabel%7D/%7Bapplication%7D-%7Bprofile%7D.properties)

### 2、分布式配置中心客户端

- 目的就是修改feign的配置文件，让本地配置文件改为云配置就是码云仓库的，这样不用修改代码，直接修改云配置文件即可，而且我们可以在spring-cloud-config更改代码，提交到云即可

- 修改feign项目的pom文件，主要添加依赖

  ```xml
  
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-config</artifactId>
  </dependency>
  
  ```

- 项目上线分为三个环境

  - 开发环境dev
  - 测试环境test
  - 生产环境prod

- 更改spring-cloud-config配置文件名service-consumer-feign-dev

- 重新提交

- 增加了一个配置

  ```yml
  spring:
    #新增配置，之前的都删掉，这个feign的配置文件只需要配置到能找到spring-cloud-config配置文件即可
    cloud:
      config:
        uri: http://localhost:8888
        name: service-consumer-feign
        label: master
        profile: dev
  ```

- 在创建一个生产环境的配置文件

- service-consumer-feign-prod

  ```yml
  spring:
    application:
      name: hello-spring-cloud-service-consumer-feign
    thymeleaf:
      cache: false
      mode: LEGACYHTML5
      encoding: UTF-8
      servlet:
        content-type: text/html
  
  server:
    port: 8766
  
  eureka:
    client:
      serviceUrl:
        defaultZone: http://localhost:8761/eureka/
  
  feign:
    hystrix:
      enabled: true
  
  #生产环境的配置文件，改成端口号8766加以区分
  ```

- 上传

- 重启feign服务器

- 访问

  ```sh
  http://localhost:8765/hi?message=aaaa
  ```

- 停掉服务器，更改配置文件中的dev改成prod

- 再次启动，此时端口号为8766

- 访问

  ```sh
  http://localhost:8766/hi?message=aaaa
  ```

- 那么所有的项目配置文件都应该这么使用，以后统一在spring-cloud-config项目中修改后提交云仓库，其他项目直接即可更新

- 那么还有一个问题，运行是怎么运行的了解了，最终我还是要本地配置文件中的profile啊

- 项目最终都是以jar包形式运行的

  #### 附：开启 Spring Boot Profile

  我们在做项目开发的时候，生产环境和测试环境的一些配置可能会不一样，有时候一些功能也可能会不一样，所以我们可能会在上线的时候手工修改这些配置信息。但是 Spring 中为我们提供了 Profile 这个功能。我们只需要在启动的时候添加一个虚拟机参数，激活自己环境所要用的 Profile 就可以了。

  操作起来很简单，只需要为不同的环境编写专门的配置文件，如：`application-dev.yml`、`application-prod.yml`，
  启动项目时只需要增加一个命令参数 `--spring.profiles.active=环境配置` 即可，启动命令如下：

  ```
  java -jar hello-spring-cloud-web-admin-feign-1.0.0-SNAPSHOT.jar --spring.profiles.active=prod
  ```

- 先停掉feign服务器

- 之后用idea的终端，右键项目打开终端

- mvn clean package

- 打包成功后进入target文件下，有一个jar包

- Java -jar hello-spring-cloud-service-consumer-feign-1.0.0-SNAPSHOT.jar

  ```sh
  http://localhost:8766/hi?message=aaaa
  ```

- 那么现在默认运行的8766 prod环境

- 在真正开发中长期肯定是dev环境，我们在改回dev模式

- 之后再创建一个新的配置文件，是开发环境的

  ![hello-spring-cloud____Documents_IDEA_hello-spring-cloud__-_____hello-spring-cloud-service-consumer-feign_src_main_resources_application-prod_yml__hello-spring-cloud-service-consumer-feign_](https://ws1.sinaimg.cn/large/006tNc79ly1g2x9k65us5j31dg0eygo7.jpg)

- 在重新打包下

  ```sh
  运行命令
  
  java -jar hello-spring-cloud-service-consumer-feign-1.0.0-SNAPSHOT.jar --spring.profiles.active=prod
  
  
  正常运行，不加后面的，我们直接找的是默认的application.yml的里面是dev环境配置文件
  
  
  http://localhost:8766/hi?message=helloconfig
  ```

- 以后上docker 先pull个jdk，把jar包放进去，之后再启动docker的时候执行这个命令，在把8766这个端口export暴露出来就OK了





下面目前没使用过

创建一个工程名为 `hello-spring-cloud-config-client` 的项目，`pom.xml` 文件配置如下：

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>hello-spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-config-client</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-config-client</name>
    <url>http://www.funtl.com</url>
    <inceptionYear>2018-Now</inceptionYear>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
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
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.hello.spring.cloud.config.client.ConfigClientApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

#### Application

入口类没有需要特殊处理的地方，代码如下：

```
package com.funtl.hello.spring.cloud.config.client;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class ConfigClientApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigClientApplication.class, args);
    }
}
```

#### application.yml

增加 Config Client 相关配置，并设置端口号为：`8889`

```
spring:
  application:
    name: hello-spring-cloud-config-client
  cloud:
    config:
      uri: http://localhost:8888
      name: config-client
      label: master
      profile: dev

server:
  port: 8889

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

相关配置说明，如下：

- `spring.cloud.config.uri`：配置服务中心的网址

- `spring.cloud.config.name`：配置文件名称的前缀

- `spring.cloud.config.label`：配置仓库的分支

- ```
  spring.cloud.config.profile
  ```

  ：配置文件的环境标识

  - dev：表示开发环境
  - test：表示测试环境
  - prod：表示生产环境

注意事项：

- 配置服务器的默认端口为 `8888`，如果修改了默认端口，则客户端项目就不能在 `application.yml` 或 `application.properties` 中配置 `spring.cloud.config.uri`，必须在 `bootstrap.yml` 或是 `bootstrap.properties` 中配置，原因是 `bootstrap` 开头的配置文件会被优先加载和配置，切记。

#### 创建测试用 Controller

我们创建一个 Controller 来测试一下通过远程仓库的配置文件注入 `foo` 属性

```
package com.funtl.hello.spring.cloud.config.client.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class TestConfigController {

    @Value("${foo}")
    private String foo;

    @RequestMapping(value = "/hi", method = RequestMethod.GET)
    public String hi() {
        return foo;
    }
}
```

一般情况下，能够正常启动服务就说明注入是成功的。

#### 测试访问

浏览器端访问：<http://localhost:8889/hi> 显示如下：

```
foo version 1
```

## 十三、Spring Cloud 服务链路追踪

### ZipKin 简介

ZipKin 是一个开放源代码的分布式跟踪系统，由 Twitter 公司开源，它致力于收集服务的定时数据，以解决微服务架构中的延迟问题，包括数据的收集、存储、查找和展现。它的理论模型来自于 Google Dapper 论文。

每个服务向 ZipKin 报告计时数据，ZipKin 会根据调用关系通过 ZipKin UI 生成依赖关系图，显示了多少跟踪请求通过每个服务，该系统让开发者可通过一个 Web 前端轻松的收集和分析数据，例如用户每次请求服务的处理时间等，可方便的监测系统中存在的瓶颈。

### 服务追踪说明

微服务架构是通过业务来划分服务的，使用 REST 调用。对外暴露的一个接口，可能需要很多个服务协同才能完成这个接口功能，如果链路上任何一个服务出现问题或者网络超时，都会形成导致接口调用失败。随着业务的不断扩张，服务之间互相调用会越来越复杂。

随着服务的越来越多，对调用链的分析会越来越复杂。它们之间的调用关系也许如下：



### 术语解释

- Span：基本工作单元，例如，在一个新建的 Span 中发送一个 RPC 等同于发送一个回应请求给 RPC，Span 通过一个 64 位 ID 唯一标识，Trace 以另一个 64 位 ID 表示。
- Trace：一系列 Spans 组成的一个树状结构，例如，如果你正在运行一个分布式大数据工程，你可能需要创建一个 Trace。
- Annotation：用来即使记录一个事件的存在，一些核心 Annotations 用来定义一个请求的开始和结束
  - cs：Client Sent，客户端发起一个请求，这个 Annotation 描述了这个 Span 的开始
  - sr：Server Received，服务端获得请求并准备开始处理它，**如果将其 sr 减去 cs 时间戳便可得到网络延迟**
  - ss：Server Sent 表明请求处理的完成(当请求返回客户端)，**如果 ss 减去 sr 时间戳便可得到服务端需要的处理请求时间**
  - cr：Client Received 表明 Span 的结束，客户端成功接收到服务端的回复，**如果 cr 减去 cs 时间戳便可得到客户端从服务端获取回复的所有所需时间**

将 Span 和 Trace 在一个系统中使用 Zipkin 注解的过程图形化：



### 创建 ZipKin 服务端

创建一个工程名为 `hello-spring-cloud-zipkin` 的项目，`pom.xml` 文件如下：

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>hello-spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-zipkin</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-zipkin</name>
    <url>http://www.funtl.com</url>
    <inceptionYear>2018-Now</inceptionYear>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
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
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>io.zipkin.java</groupId>
            <artifactId>zipkin</artifactId>
        </dependency>
        <dependency>
            <groupId>io.zipkin.java</groupId>
            <artifactId>zipkin-server</artifactId>
        </dependency>
        <dependency>
            <groupId>io.zipkin.java</groupId>
            <artifactId>zipkin-autoconfigure-ui</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.hello.spring.cloud.zipkin.ZipKinApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

主要增加了 3 个依赖，`io.zipkin.java:zipkin`、`io.zipkin.java:zipkin-server`、`io.zipkin.java:zipkin-autoconfigure-ui`

```
<dependency>
    <groupId>io.zipkin.java</groupId>
    <artifactId>zipkin</artifactId>
</dependency>
<dependency>
    <groupId>io.zipkin.java</groupId>
    <artifactId>zipkin-server</artifactId>
</dependency>
<dependency>
    <groupId>io.zipkin.java</groupId>
    <artifactId>zipkin-autoconfigure-ui</artifactId>
</dependency>
```

注意版本号为：`2.10.1`，这里没写版本号是因为我已将版本号托管到 `dependencies` 项目中

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
    </parent>

    <groupId>com.aishang</groupId>
    <artifactId>hello-spring-cloud-dependencies</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <name>hello-spring-cloud-dependencies</name>

    <properties>
        <!-- Environment Settings -->
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>

        <!-- Spring cloud 版本 -->
        <spring-cloud.version>Finchley.RELEASE</spring-cloud.version>

        <!--zipkin-->
        <zipkin.version>2.10.1</zipkin.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>io.zipkin.java</groupId>
                <artifactId>zipkin</artifactId>
                <version>${zipkin.version}</version>
            </dependency>
            <dependency>
                <groupId>io.zipkin.java</groupId>
                <artifactId>zipkin-server</artifactId>
                <version>${zipkin.version}</version>
            </dependency>
            <dependency>
                <groupId>io.zipkin.java</groupId>
                <artifactId>zipkin-autoconfigure-ui</artifactId>
                <version>${zipkin.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <!-- Compiler 插件, 设定 JDK 版本 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <showWarnings>true</showWarnings>
                </configuration>
            </plugin>

            <!-- 打包 jar 文件时，配置 manifest 文件，加入 lib 包的 jar 依赖 -->
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
                                    <!-- Add directory entries -->
                                    <addDefaultImplementationEntries>true</addDefaultImplementationEntries>
                                    <addDefaultSpecificationEntries>true</addDefaultSpecificationEntries>
                                    <addClasspath>true</addClasspath>
                                </manifest>
                            </archive>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

            <!-- resource -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
            </plugin>

            <!-- install -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-install-plugin</artifactId>
            </plugin>

            <!-- clean -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-clean-plugin</artifactId>
            </plugin>

            <!-- ant -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-antrun-plugin</artifactId>
            </plugin>

            <!-- dependency -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
            </plugin>
        </plugins>

        <pluginManagement>
            <plugins>
                <!-- Java Document Generate -->
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

                <!-- YUI Compressor (CSS/JS压缩) -->
                <plugin>
                    <groupId>net.alchim31.maven</groupId>
                    <artifactId>yuicompressor-maven-plugin</artifactId>
                    <version>1.5.1</version>
                    <executions>
                        <execution>
                            <phase>prepare-package</phase>
                            <goals>
                                <goal>compress</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <encoding>UTF-8</encoding>
                        <jswarn>false</jswarn>
                        <nosuffix>true</nosuffix>
                        <linebreakpos>30000</linebreakpos>
                        <force>true</force>
                        <includes>
                            <include>**/*.js</include>
                            <include>**/*.css</include>
                        </includes>
                        <excludes>
                            <exclude>**/*.min.js</exclude>
                            <exclude>**/*.min.css</exclude>
                        </excludes>
                    </configuration>
                </plugin>
            </plugins>
        </pluginManagement>

        <!-- 资源文件配置 -->
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

    <repositories>
        <repository>
            <id>aliyun-repos</id>
            <name>Aliyun Repository</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>

        <repository>
            <id>sonatype-repos</id>
            <name>Sonatype Repository</name>
            <url>https://oss.sonatype.org/content/groups/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>sonatype-repos-s</id>
            <name>Sonatype Repository</name>
            <url>https://oss.sonatype.org/content/repositories/snapshots</url>
            <releases>
                <enabled>false</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>

        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>aliyun-repos</id>
            <name>Aliyun Repository</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </pluginRepository>
    </pluginRepositories>
</project>
```



### Application

通过 `@EnableZipkinServer` 注解开启 Zipkin Server 功能

```
package com.aishang.hello.spring.cloud.zipkin;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import zipkin.server.internal.EnableZipkinServer;

@SpringBootApplication
@EnableEurekaClient
@EnableZipkinServer
public class ZipKinApplication {
    public static void main(String[] args) {
        SpringApplication.run(ZipKinApplication.class, args);
    }
}
```

### application.yml

设置端口号为：`9411`，该端口号为 Zipkin Server 的默认端口号

```
spring:
  application:
    name: hello-spring-cloud-zipkin

server:
  port: 9411

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/

management:
  metrics:
    web:
      server:
        auto-time-requests: false
```

### 追踪服务

在 **所有需要被追踪的项目（就当前教程而言，除了 dependencies 项目外都需要被追踪，包括 Eureka Server）** 中增加 `spring-cloud-starter-zipkin` 依赖

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

在这些项目的 `application.yml` 配置文件中增加 Zipkin Server 的地址即可

```
spring:
  zipkin:
    base-url: http://localhost:9411
```

- 启动顺序
  - 尤里卡
  - 配置中心
  - 追踪zipkin
  - provider
  - consumer
  - zuul

### 测试追踪

启动全部项目，打开浏览器访问：<http://localhost:9411/> 会出现以下界面：

![Zipkin_-_Index](https://ws3.sinaimg.cn/large/006tNc79ly1g2xbac8u4pj32eq0l0gog.jpg)

**刷新之前项目中的全部测试接口（刷多几次）**

点击 `Find a trace`，可以看到具体服务相互调用的数据



点击 `Dependencies`，可以发现服务的依赖关系

![Zipkin_-_Dependency](https://ws1.sinaimg.cn/large/006tNc79ly1g2xbk8wcgwj32920u0whs.jpg)

至此就代表 ZipKin 配置成功

## 十四、Spring Boot Admin 服务监控

随着开发周期的推移，项目会不断变大，切分出的服务也会越来越多，这时一个个的微服务构成了错综复杂的系统。对于各个微服务系统的健康状态、会话数量、并发数、服务资源、延迟等度量信息的收集就成为了一个挑战。Spring Boot Admin 应运而生，它正式基于这些需求开发出的一套功能强大的监控管理系统。

Spring Boot Admin 有两个角色组成，一个是 Spring Boot Admin Server，一个是 Spring Boot Admin Client，本章节将带领大家实现 Spring Boot Admin 的搭建。

### 1、Spring Boot Admin 服务端

#### 创建 Spring Boot Admin Server



创建一个工程名为 `hello-spring-cloud-admin-server` 的项目，`pom.xml` 文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.aishang</groupId>
        <artifactId>hello-spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-admin-server</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-admin-server</name>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-webflux</artifactId>
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
            <groupId>org.jolokia</groupId>
            <artifactId>jolokia-core</artifactId>
        </dependency>
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-server</artifactId>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zipkin</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.aishang.hello.spring.cloud.admin..server.AdminServerApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

主要增加了 2 个依赖，`org.jolokia:jolokia-core`、`de.codecentric:spring-boot-admin-starter-server`

```
<dependency>
    <groupId>org.jolokia</groupId>
    <artifactId>jolokia-core</artifactId>
</dependency>
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-server</artifactId>
</dependency>
```

其中 `spring-boot-admin-starter-server` 的版本号为：`2.0.0`，org.jolokia:jolokia-core已经被springboot管理，无需在`dependencies`中添加依赖。这里没写版本号是因为我已将版本号托管到 `dependencies` 项目中

#### Application

通过 `@EnableAdminServer` 注解开启 Admin 功能

```
package com.aishang.hello.spring.cloud.admin.server;

import de.codecentric.boot.admin.server.config.EnableAdminServer;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
@EnableAdminServer
public class AdminServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(AdminServerApplication.class, args);
    }
}

```

#### application.yml

设置端口号为：`8084`

```
spring:
  application:
    name: hello-spring-cloud-admin
  zipkin:
    base-url: http://localhost:9411

server:
  port: 8084

management:
  endpoint:
    health:
      show-details: always
  endpoints:
    web:
      exposure:
        include: ["health", "info"]

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

主要增加了 Spring Boot Admin Server 的相关配置

```
management:
  endpoint:
    health:
      show-details: always
  endpoints:
    web:
      exposure:
        include: ["health", "info"]
```

- 启动

  ```sh
  错误: 找不到或无法加载主类 com.aishang.hello.spring.cloud.admin.server.AdminServerApplication
  
  如果找到不类
  
  1、看pom文件
  <configuration>                    <mainClass>com.aishang.hello.spring.cloud.admin.server.AdminServerApplication</mainClass>
  </configuration>
  
  2、mvn clean package
  ```

  #### 测试访问监控中心

  打开浏览器访问：[http://localhost:8084](http://localhost:8084/) 会出现以下界面

  ![Window_和_Spring_Boot_Admin](https://ws1.sinaimg.cn/large/006tNc79ly1g2xcxtwu9qj325q0t2dk9.jpg)



### 2、Spring Boot Admin 客户端

- 增加了这个依赖，把他添加到depencens中

  ```xml
  						<dependency>
                  <groupId>de.codecentric</groupId>
                  <artifactId>spring-boot-admin-starter-client</artifactId>
                  <version>${codecentric.version}</version>
              </dependency>
  ```

- 添加yml配置

  ```yml
   spring:
     boot:
        admin:
          client:
            url: http://localhost:8084
  ```

- 除了自己，把所有项目的pom和yml添加这两项，除了主依赖

  ```xml
  <dependency>
      <groupId>org.jolokia</groupId>
      <artifactId>jolokia-core</artifactId>
  </dependency>
  <dependency>
      <groupId>de.codecentric</groupId>
      <artifactId>spring-boot-admin-starter-client</artifactId>
  </dependency>
  
  
     boot:
        admin:
          client:
            url: http://localhost:8084
  
  
  
  ```

- 启动

  - 尤里卡-服务注册与发现

  - config- 配置中心

  - zipkin- 链路追踪

  - admin客户端-服务客户端

  - provider-服务提供者

  - consumer-服务消费者

  - zuul-服务网关

    ![hello-spring-cloud____Documents_IDEA_hello-spring-cloud_](https://ws2.sinaimg.cn/large/006tNc79ly1g2xds3uf16j30rm0detaa.jpg)



- 直接点击上图的8084

下面没使用过

#### 创建 Spring Boot Admin Client

创建一个工程名为 `hello-spring-cloud-admin-client` 的项目，`pom.xml` 文件如下：

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>hello-spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-admin-client</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-admin-client</name>
    <url>http://www.funtl.com</url>
    <inceptionYear>2018-Now</inceptionYear>

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
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
            <groupId>org.jolokia</groupId>
            <artifactId>jolokia-core</artifactId>
        </dependency>
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-client</artifactId>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zipkin</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.hello.spring.cloud.admin.client.AdminClientApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

主要增加了 2 个依赖，`org.jolokia:jolokia-core`、`de.codecentric:spring-boot-admin-starter-client`

```
<dependency>
    <groupId>org.jolokia</groupId>
    <artifactId>jolokia-core</artifactId>
</dependency>
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
</dependency>
```

其中 `spring-boot-admin-starter-client` 的版本号为：`2.0.0`，这里没写版本号是因为我已将版本号托管到 `dependencies` 项目中

#### Application

程序入口类没有特别需要修改的地方

```
package com.funtl.hello.spring.cloud.admin.client;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class AdminClientApplication {
    public static void main(String[] args) {
        SpringApplication.run(AdminClientApplication.class, args);
    }
}
```

#### application.yml

设置端口号为：`8085`，并设置 Spring Boot Admin 的服务端地址

```
spring:
  application:
    name: hello-spring-cloud-admin-client
  boot:
    admin:
      client:
        url: http://localhost:8084
  zipkin:
    base-url: http://localhost:9411

server:
  port: 8085

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

主要增加了 Spring Boot Admin Client 相关配置

```
spring:
  boot:
    admin:
      client:
        url: http://localhost:8084
```

#### 测试服务监控

依次启动两个应用，打开浏览器访问：[http://localhost:8084](http://localhost:8084/) 界面显示如下

![img](http://blog.funtl.com/assets/Lusifer2018060105410005.png)

从图中可以看到，我们的 Admin Client 已经上线了，至此说明监控中心搭建成功

##### WallBoard

![img](http://blog.funtl.com/assets/Lusifer2018060105410006.png)

##### Journal

![img](http://blog.funtl.com/assets/Lusifer2018060105410007.png)

