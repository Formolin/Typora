# Spring Cloud Alibaba Dubbo

非主流微服务架构解决方案 **Spring Cloud Alibaba + Dubbo + Vue**，主要目的是想实现之前在视频里说的对内 RPC，对外 REST 并利用 **Vue Element Admin** 实现一个相对完整的前后分离效果。

## 概述

非主流微服务架构解决方案 **Spring Cloud Alibaba + Dubbo + Vue**，该章节为拓展章节，主要目的是想实现之前在视频里说的对内 RPC，对外 REST 并利用 **Vue Element Admin** 实现一个相对完整的前后分离效果。



## 目标

Spring Cloud Alibaba Dubbo 项目的目标是将 Dubbo 融入到 Spring Cloud Alibaba 生态中，使微服务之间的调用同时具备 RESTful 和 Dubbo 调用的能力。做到对业务代码无侵入，无感知；引入 JAR 包则微服务间调用使用 Dubbo，去掉 JAR 包则使用默认的 RESTful；实现参考的是 Spring Cloud 中文社区的 [Spring Cloud Dubbo](https://github.com/SpringCloud/spring-cloud-dubbo) 项目。

## Nacos

既然已经将 Dubbo 集成进了 Spring Cloud Alibaba，那么理所当然的我们的注册中心也不再采用 Zookeeper 方案而是转为 Nacos 方案了，部署 Nacos 请参考我的 [**服务注册与发现**](http://www.funtl.com/zh/spring-cloud-alibaba/服务注册与发现.html) 章节

**注：截止到博客发表时间 2019 年 03 月 13 日，Nacos 已发行版为 0.9.0，在 0.8.0 时登录 Nacos 需要使用账号密码，默认账号密码为 nacos/nacos**

## 实现基于 Feign 的注册方案

由于 Nacos、Dubbo、Spring Cloud Alibaba 都是阿里系产品，所以我们可以很容的将 Dubbo 和 Http 服务注册到 Nacos 中。

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3i0qccranj30jn04qmx4.jpg)

此时服务提供者即注册了 Dubbo 又注册了 Http 服务，服务消费者根据配置方式可以在 Dubbo 与 Http 调用中随意切换

### 服务提供者

服务提供者在使用 Dubbo 注册服务时是需要使用 `@Service` 注解将服务注册到注册中心的，现在改用 `@FeignClient` 注解来注册

以 `spring-cloud-alibaba-dubbo-provider-api` 项目中定义的 API 接口为例

```java
package com.funtl.alibaba.dubbo.provider.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;

@FeignClient("provider")
public interface ProviderService {
    @GetMapping("/hi")
    String hi();
}
```



以 `spring-cloud-alibaba-dubbo-provider-service` 项目中实现接口为例

```java
package com.funtl.alibaba.dubbo.provider.service.impl;

import com.funtl.alibaba.dubbo.provider.service.ProviderService;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ProviderServiceImpl implements ProviderService {
    @Override
    public String hi() {
        return "Hi Spring Cloud Alibaba Dubbo";
    }
}
```



以上代码是一个典型的 Spring Cloud RESTFul API，服务提供者需要做的就是引入 Dubbo 相关依赖，扫描包含 `@FeignClient` 注解的类并注册到 Nacos 即可，关键代码在 `spring-cloud-alibaba-dubbo-core` 项目的 `FeignClientToDubboProviderBeanPostProcessor` 类中

```java
    /**
     * Registers Beans whose classes was annotated {@link FeignClient}
     *
     * @param packagesToScan The base packages to scan
     * @param registry       {@link BeanDefinitionRegistry}
     */
    private void registerServiceBeans(Set<String> packagesToScan, BeanDefinitionRegistry registry) {

        DubboClassPathBeanDefinitionScanner scanner =
                new DubboClassPathBeanDefinitionScanner(registry, environment, resourceLoader);

        BeanNameGenerator beanNameGenerator = resolveBeanNameGenerator(registry);

        scanner.setBeanNameGenerator(beanNameGenerator);

        scanner.addIncludeFilter(new AnnotationTypeFilter(FeignClient.class, true, true));

        for (String packageToScan : packagesToScan) {

            // Registers @Service Bean first
            scanner.scan(packageToScan);

            // Finds all BeanDefinitionHolders of @Service whether @ComponentScan scans or not.
            Set<BeanDefinitionHolder> beanDefinitionHolders =
                    findServiceBeanDefinitionHolders(scanner, packageToScan, registry, beanNameGenerator);

            if (!CollectionUtils.isEmpty(beanDefinitionHolders)) {

                for (BeanDefinitionHolder beanDefinitionHolder : beanDefinitionHolders) {
                    registerServiceBean(beanDefinitionHolder, registry, scanner);
                }

                if (logger.isInfoEnabled()) {
                    logger.info(beanDefinitionHolders.size() + " annotated Dubbo's @Service Components { " +
                            beanDefinitionHolders +
                            " } were scanned under package[" + packageToScan + "]");
                }

            } else {

                if (logger.isWarnEnabled()) {
                    logger.warn("No Spring Bean annotating Dubbo's @Service was found under package["
                            + packageToScan + "]");
                }

            }

        }

    }
```



### 服务消费者

服务消费者需要依赖 `spring-cloud-alibaba-dubbo-provider-api`，并直接使用 `@Autowired` 注解即可实现注入，可以不使用 Dubbo 提供的 `@Reference` 注解

```java
package com.funtl.alibaba.dubbo.consumer.service;

import com.funtl.alibaba.dubbo.provider.service.ProviderService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class TestService {

    @Autowired
    private ProviderService providerService;

    @GetMapping("/test")
    public String test() {
        return providerService.hi();
    }
}
```



以上代码是一个典型的 Spring Cloud Feign 调用。我们只需要替换 Feign 的实现。产生 `ProviderService` 接口的 `ProxyBean` 时，使用 Dubbo 产生的 Bean 替换默认的 Feign 产生的 RESTFul 调用的 Bean 即可，关键代码在 `spring-cloud-alibaba-dubbo-core` 项目的 `DubboFeignBuilder` 类中

```java
    @Override
    public <T> T target(Target<T> target) {
        ReferenceBeanBuilder beanBuilder = ReferenceBeanBuilder
                .create(defaultReference, target.getClass().getClassLoader(), applicationContext)
                .interfaceClass(target.type());

        try {
            T object = (T) beanBuilder.build().getObject();
            return object;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
```



## 消费者使用 RESTFul 调用

只需将 Dubbo 相关依赖排除即可

```xml
<dependency>
    <groupId>com.funtl</groupId>
    <artifactId>spring-cloud-alibaba-dubbo-starter</artifactId>
    <exclusions>
        <exclusion>
            <groupId>com.funtl</groupId>
            <artifactId>spring-cloud-alibaba-dubbo-core</artifactId>
        </exclusion>
        <exclusion>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```



## 附：阿里为何放弃 Zookeeper

### CAP

有个思考，从 CAP 角度考虑，服务注册中心是 CP 系统还是 AP 系统呢？

- 服务注册中心是为了服务间调用服务的，那么绝对不允许因为服务注册中心出现了问题而导致服务间的调用出问题
- 假如有 node1，node2，node3 集群节点。保存着可用服务列表 ip1，ip2，ip3，试想如果此时不一致，比如 node1 只保存了ip1，ip2，此时服务读取 node1 的节点，那么会造成什么影响？

调用 node1 的服务，顶多就是负载均衡时不会有流量打到 ip3，然后等 node1 同步回 ip3 后，又一致了，这对服务其实没什么太大影响。所以，推测出服务注册中心应该是个 AP 系统。

**注：CAP 定理**指的是在一个分布式系统中，[一致性](https://baike.baidu.com/item/一致性/9840083)（Consistency）、[可用性](https://baike.baidu.com/item/可用性/109628)（Availability）、分区容错性（Partition tolerance）。CAP 原则指的是，这三个要素最多只能同时实现两点，不可能三者兼顾

### Zookeeper 是个 CP 系统，强一致性

- 场景1，当 master 挂了，此时 Zookeeper 集群需要重新选举，而此时服务需要来读取可用服务，是不可用的。影响到了服务的可用性当然你可以说服务本地有缓存可用列表。然而下面这种方式就更无法处理了。
- 场景2，分区可用。试想，有 3 个机房，如果其中机房 3 和机房 1，2 网络断了，那么机房 3 的注册中心就不能注册新的机器了，这显然也不合理从健康检查角度来看

![img](http://ww3.sinaimg.cn/large/006tNc79ly1g3i0qd61k5j30ov0hswhb.jpg)

Zookeeper 是通过 TCP 的心跳判断服务是否可用，但 TCP 的活性并不代表服务是可用的，如：连接池已满，DB 挂了等

**注意：Zookeeper 可以参考我 Apache Dubbo Zookeeper 解决方案的 Apache Zookeeper 章节**

### 理想的注册中心

- 服务自动注册发现。最好有新的服务注册上去时还能推送到调用端
- 能对注册上来的机器方便的进行管理，能手动删除（发送信号让服务优雅下线）、恢复机器
- 服务的健康检查，能真正的检测到服务是否可用
- 可以看到是否有其他调用服务正在订阅注册上来的服务
- 能够带上些除了 IP 外的其它信息

# 基于 Docker 安装 Nacos

## 概述

关于 Nacos 相关说明请参考我博客 [Spring Cloud Alibaba](https://www.funtl.com/zh/spring-cloud-alibaba/) 章节的 [服务注册与发现](https://www.funtl.com/zh/spring-cloud-alibaba/服务注册与发现.html)

## 安装

```bash
git clone https://github.com/nacos-group/nacos-docker.git
cd nacos-docker
docker-compose -f example/standalone-mysql.yaml up
```



## 访问

http://127.0.0.1:8848/nacos/

**注：从 0.8.0 版本开始，需要登录才可访问，默认账号密码为 nacos/nacos**

# 创建项目工程

## POM

创建一个名为 `spring-cloud-alibaba-dubbo-parent` 的工程项目

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.funtl</groupId>
    <artifactId>spring-cloud-alibaba-dubbo-parent</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <name>spring-cloud-alibaba-dubbo-parent</name>
    <url>http://www.funtl.com</url>

    <modules>
        <module>spring-cloud-alibaba-dubbo-dependencies</module>
        <module>spring-cloud-alibaba-dubbo-core</module>
        <module>spring-cloud-alibaba-dubbo-nacos</module>
        <module>spring-cloud-alibaba-dubbo-starter</module>
        <module>spring-cloud-alibaba-dubbo-examples</module>
    </modules>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>${java.version}</maven.compiler.source>
        <maven.compiler.target>${java.version}</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>com.funtl</groupId>
                <artifactId>spring-cloud-alibaba-dubbo-dependencies</artifactId>
                <version>${project.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

    <profiles>
        <profile>
            <id>release</id> <!-- 部署要用到 -->
            <build>
                <plugins>
                    <plugin>
                        <artifactId>maven-compiler-plugin</artifactId>
                        <configuration>
                            <parameters>true</parameters>
                        </configuration>
                    </plugin>

                    <!-- Source -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-source-plugin</artifactId>
                        <version>3.0.1</version>
                        <executions>
                            <execution>
                                <phase>package</phase>
                                <goals>
                                    <goal>jar-no-fork</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>

                    <!-- Javadoc -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-javadoc-plugin</artifactId>
                        <version>2.9.1</version>
                        <executions>
                            <execution>
                                <phase>package</phase>
                                <goals>
                                    <goal>jar</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>

                    <!-- GPG -->
                    <plugin> <!-- 进行延签 -->
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-gpg-plugin</artifactId>
                        <version>1.6</version>
                        <executions>
                            <execution>
                                <phase>verify</phase>
                                <goals>
                                    <goal>sign</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
            <distributionManagement>
                <snapshotRepository>
                    <id>oss</id>
                    <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                </snapshotRepository>
                <repository>
                    <id>oss</id>
                    <url>https://oss.sonatype.org/service/local/staging/deploy/maven2/</url>
                </repository>
            </distributionManagement>
        </profile>
    </profiles>

    <licenses>
        <license>
            <name>The Apache Software License, Version 2.0</name>
            <url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
            <comments>A business-friendly OSS license</comments>
        </license>
    </licenses>

    <scm>
        <url>https://github.com/funtl/spring-cloud-alibaba-dubbo</url>
        <connection>scm:githttps://github.com/funtl/spring-cloud-alibaba-dubbo.git</connection>
        <developerConnection>scm:git:https://github.com/funtl/spring-cloud-alibaba-dubbo.git</developerConnection>
    </scm>

    <developers>
        <developer>
            <name>Lusifer</name>
            <email>lee.lusifer@gmail.com</email>
            <url>http://www.funtl.com</url>
        </developer>
    </developers>

</project>
```

# 创建统一的依赖管理

## POM

创建一个名为 `spring-cloud-alibaba-dubbo-dependencies` 的模块项目

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.funtl</groupId>
    <artifactId>spring-cloud-alibaba-dubbo-dependencies</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <url>http://www.funtl.com</url>

    <properties>
        <spring-boot.version>2.1.1.RELEASE</spring-boot.version>
        <spring-cloud.version>Greenwich.RELEASE</spring-cloud.version>
        <spring-cloud-alibaba.version>0.2.1.RELEASE</spring-cloud-alibaba.version>

        <dubbo.version>2.6.6</dubbo.version>
        <dubbo-spring-boot.version>0.2.1.RELEASE</dubbo-spring-boot.version>
        <dubbo-registry-nacos.version>0.0.1</dubbo-registry-nacos.version>
        <dubbo-serialization-kryo.version>2.6.6</dubbo-serialization-kryo.version>
        <alibaba-spring-context-support.version>1.0.2</alibaba-spring-context-support.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <!-- Spring Boot -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

            <!-- Spring Cloud -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring-cloud-alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

            <!-- Dubbo  -->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>dubbo</artifactId>
                <version>${dubbo.version}</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>dubbo-registry-nacos</artifactId>
                <version>${dubbo-registry-nacos.version}</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>dubbo-serialization-kryo</artifactId>
                <version>${dubbo-serialization-kryo.version}</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba.boot</groupId>
                <artifactId>dubbo-spring-boot-starter</artifactId>
                <version>${dubbo-spring-boot.version}</version>
            </dependency>

            <!-- Alibaba -->
            <dependency>
                <groupId>com.alibaba.spring</groupId>
                <artifactId>spring-context-support</artifactId>
                <version>${alibaba-spring-context-support.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <profiles>
        <profile>
            <id>release</id> <!-- 部署要用到 -->
            <build>
                <plugins>
                    <plugin>
                        <artifactId>maven-compiler-plugin</artifactId>
                    </plugin>

                    <!-- Source -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-source-plugin</artifactId>
                        <version>3.0.1</version>
                        <executions>
                            <execution>
                                <phase>package</phase>
                                <goals>
                                    <goal>jar-no-fork</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>

                    <!-- Javadoc -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-javadoc-plugin</artifactId>
                        <version>2.9.1</version>
                        <executions>
                            <execution>
                                <phase>package</phase>
                                <goals>
                                    <goal>jar</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>

                    <!-- GPG -->
                    <plugin> <!-- 进行延签 -->
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-gpg-plugin</artifactId>
                        <version>1.6</version>
                        <executions>
                            <execution>
                                <phase>verify</phase>
                                <goals>
                                    <goal>sign</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
            <distributionManagement>
                <snapshotRepository>
                    <id>oss</id>
                    <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                </snapshotRepository>
                <repository>
                    <id>oss</id>
                    <url>https://oss.sonatype.org/service/local/staging/deploy/maven2/</url>
                </repository>
            </distributionManagement>
        </profile>
    </profiles>

    <licenses>
        <license>
            <name>The Apache Software License, Version 2.0</name>
            <url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
            <comments>A business-friendly OSS license</comments>
        </license>
    </licenses>

    <scm>
        <url>https://github.com/funtl/spring-cloud-dubbo</url>
        <connection>scm:githttps://github.com/funtl/spring-cloud-dubbo.git</connection>
        <developerConnection>scm:git:https://github.com/funtl/spring-cloud-dubbo.git</developerConnection>
    </scm>

    <developers>
        <developer>
            <name>Lusifer</name>
            <email>lee.lusifer@gmail.com</email>
            <url>http://www.funtl.com</url>
        </developer>
    </developers>
</project>
```

# 创建核心代码模块

## 概述

本模块依托于 Spring Boot 提供的 Spring Factories 扩展机制，该机制为 Spring Boot Starter 实现的基础，需要额外讲解。因与主线无关此处不做额外扩展。

## POM

创建一个名为 `spring-cloud-alibaba-dubbo-core` 的模块，其父工程为我们的工程项目 `spring-cloud-alibaba-dubbo-parent`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-cloud-alibaba-dubbo-parent</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-cloud-alibaba-dubbo-core</artifactId>
    <url>http://www.funtl.com</url>

    <dependencies>
        <!-- Spring Cloud -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
            <scope>provided</scope>
        </dependency>

        <!-- Dubbo -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <scope>provided</scope>
        </dependency>
    </dependencies>
</project>
```



## 创建核心工具类

在 `com.alibaba.dubbo.config.spring.beans.factory.annotation` 包下创建一个名为 `FeignClientToDubboProviderBeanPostProcessor` 的工具类

**注：包名不可修改，注释中已有详细说明**

```java
package com.alibaba.dubbo.config.spring.beans.factory.annotation;

import com.alibaba.dubbo.common.logger.Logger;
import com.alibaba.dubbo.common.logger.LoggerFactory;
import com.alibaba.dubbo.config.annotation.Service;
import com.alibaba.dubbo.config.spring.ServiceBean;
import com.alibaba.dubbo.config.spring.context.annotation.DubboClassPathBeanDefinitionScanner;
import org.springframework.beans.BeansException;
import org.springframework.beans.MutablePropertyValues;
import org.springframework.beans.factory.BeanClassLoaderAware;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.beans.factory.config.BeanDefinitionHolder;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.beans.factory.config.RuntimeBeanReference;
import org.springframework.beans.factory.config.SingletonBeanRegistry;
import org.springframework.beans.factory.support.AbstractBeanDefinition;
import org.springframework.beans.factory.support.BeanDefinitionBuilder;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.beans.factory.support.BeanDefinitionRegistryPostProcessor;
import org.springframework.beans.factory.support.BeanNameGenerator;
import org.springframework.beans.factory.support.ManagedList;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.context.EnvironmentAware;
import org.springframework.context.ResourceLoaderAware;
import org.springframework.context.annotation.AnnotationBeanNameGenerator;
import org.springframework.context.annotation.AnnotationConfigUtils;
import org.springframework.context.annotation.ClassPathBeanDefinitionScanner;
import org.springframework.context.annotation.ConfigurationClassPostProcessor;
import org.springframework.core.env.Environment;
import org.springframework.core.io.ResourceLoader;
import org.springframework.core.type.filter.AnnotationTypeFilter;
import org.springframework.util.Assert;
import org.springframework.util.ClassUtils;
import org.springframework.util.CollectionUtils;
import org.springframework.util.ObjectUtils;
import org.springframework.util.StringUtils;

import java.util.Arrays;
import java.util.Collection;
import java.util.LinkedHashSet;
import java.util.List;
import java.util.Set;

import static com.alibaba.dubbo.config.spring.util.ObjectUtils.of;
import static org.springframework.beans.factory.support.BeanDefinitionBuilder.rootBeanDefinition;
import static org.springframework.context.annotation.AnnotationConfigUtils.CONFIGURATION_BEAN_NAME_GENERATOR;
import static org.springframework.core.annotation.AnnotationUtils.findAnnotation;
import static org.springframework.util.ClassUtils.resolveClassName;

/**
 * 不要自定义该类所在包名，本项目依赖的是 Dubbo 2.6.6 版本，截止代码创建日期 2019/3/12 ，Dubbo 的最新版本为 2.7.0
 * 但由于 2.7.0 版本的 Dubbo 有个配置与 Eureka 的端点检查配置注入时冲突，暂时无法解决（按照官方 issues 做了，也没啥卵用，故降级到当前版本），该 Bug 官方说法是在 2.7.1 版本中修复
 * 自 Dubbo 2.7.0 版本开始，所有包名更改为 org.apache.dubbo.* ，原 com.alibaba.dubbo.* 包下的代码官方应该会停止维护了
 * 升级到 Dubbo 2.7.0 以上版本则当前包名也需要同步修改为 org.apache.dubbo.*
 * <p>Title: FeignClientToDubboProviderBeanPostProcessor</p>
 * <p>Description: </p>
 *
 * @author Lusifer
 * @version 1.0.0
 * @date 2019/3/12 12:13
 */
public class FeignClientToDubboProviderBeanPostProcessor implements BeanDefinitionRegistryPostProcessor, EnvironmentAware,
        ResourceLoaderAware, BeanClassLoaderAware {

    private static final String SEPARATOR = ":";

    private final Logger logger = LoggerFactory.getLogger(getClass());

    private final Set<String> packagesToScan;

    private Environment environment;

    private ResourceLoader resourceLoader;

    private ClassLoader classLoader;

    private Service defaultService;

    public FeignClientToDubboProviderBeanPostProcessor(String... packagesToScan) {
        this(Arrays.asList(packagesToScan));
    }

    public FeignClientToDubboProviderBeanPostProcessor(Collection<String> packagesToScan) {
        this(new LinkedHashSet<String>(packagesToScan));
    }

    public FeignClientToDubboProviderBeanPostProcessor(Set<String> packagesToScan) {
        this.packagesToScan = packagesToScan;
        @Service
        final class DefaultServiceClass {
        }
        ;
        this.defaultService = DefaultServiceClass.class.getAnnotation(Service.class);
    }

    @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException {

        Set<String> resolvedPackagesToScan = resolvePackagesToScan(packagesToScan);

        if (!CollectionUtils.isEmpty(resolvedPackagesToScan)) {
            registerServiceBeans(resolvedPackagesToScan, registry);
        } else {
            if (logger.isWarnEnabled()) {
                logger.warn("packagesToScan is empty , ServiceBean registry will be ignored!");
            }
        }

    }


    /**
     * Registers Beans whose classes was annotated {@link FeignClient}
     *
     * @param packagesToScan The base packages to scan
     * @param registry       {@link BeanDefinitionRegistry}
     */
    private void registerServiceBeans(Set<String> packagesToScan, BeanDefinitionRegistry registry) {

        DubboClassPathBeanDefinitionScanner scanner =
                new DubboClassPathBeanDefinitionScanner(registry, environment, resourceLoader);

        BeanNameGenerator beanNameGenerator = resolveBeanNameGenerator(registry);

        scanner.setBeanNameGenerator(beanNameGenerator);

        scanner.addIncludeFilter(new AnnotationTypeFilter(FeignClient.class, true, true));

        for (String packageToScan : packagesToScan) {

            // Registers @Service Bean first
            scanner.scan(packageToScan);

            // Finds all BeanDefinitionHolders of @Service whether @ComponentScan scans or not.
            Set<BeanDefinitionHolder> beanDefinitionHolders =
                    findServiceBeanDefinitionHolders(scanner, packageToScan, registry, beanNameGenerator);

            if (!CollectionUtils.isEmpty(beanDefinitionHolders)) {

                for (BeanDefinitionHolder beanDefinitionHolder : beanDefinitionHolders) {
                    registerServiceBean(beanDefinitionHolder, registry, scanner);
                }

                if (logger.isInfoEnabled()) {
                    logger.info(beanDefinitionHolders.size() + " annotated Dubbo's @Service Components { " +
                            beanDefinitionHolders +
                            " } were scanned under package[" + packageToScan + "]");
                }

            } else {

                if (logger.isWarnEnabled()) {
                    logger.warn("No Spring Bean annotating Dubbo's @Service was found under package["
                            + packageToScan + "]");
                }

            }

        }

    }

    /**
     * It'd better to use BeanNameGenerator instance that should reference
     * {@link ConfigurationClassPostProcessor#componentScanBeanNameGenerator},
     * thus it maybe a potential problem on bean name generation.
     *
     * @param registry {@link BeanDefinitionRegistry}
     * @return {@link BeanNameGenerator} instance
     * @see SingletonBeanRegistry
     * @see AnnotationConfigUtils#CONFIGURATION_BEAN_NAME_GENERATOR
     * @see ConfigurationClassPostProcessor#processConfigBeanDefinitions
     * @since 2.5.8
     */
    private BeanNameGenerator resolveBeanNameGenerator(BeanDefinitionRegistry registry) {

        BeanNameGenerator beanNameGenerator = null;

        if (registry instanceof SingletonBeanRegistry) {
            SingletonBeanRegistry singletonBeanRegistry = SingletonBeanRegistry.class.cast(registry);
            beanNameGenerator = (BeanNameGenerator) singletonBeanRegistry.getSingleton(CONFIGURATION_BEAN_NAME_GENERATOR);
        }

        if (beanNameGenerator == null) {

            if (logger.isInfoEnabled()) {

                logger.info("BeanNameGenerator bean can't be found in BeanFactory with name ["
                        + CONFIGURATION_BEAN_NAME_GENERATOR + "]");
                logger.info("BeanNameGenerator will be a instance of " +
                        AnnotationBeanNameGenerator.class.getName() +
                        " , it maybe a potential problem on bean name generation.");
            }

            beanNameGenerator = new AnnotationBeanNameGenerator();

        }

        return beanNameGenerator;

    }

    /**
     * Finds a {@link Set} of {@link BeanDefinitionHolder BeanDefinitionHolders} whose bean type annotated
     * {@link Service} Annotation.
     *
     * @param scanner       {@link ClassPathBeanDefinitionScanner}
     * @param packageToScan pachage to scan
     * @param registry      {@link BeanDefinitionRegistry}
     * @return non-null
     * @since 2.5.8
     */
    private Set<BeanDefinitionHolder> findServiceBeanDefinitionHolders(
            ClassPathBeanDefinitionScanner scanner, String packageToScan, BeanDefinitionRegistry registry,
            BeanNameGenerator beanNameGenerator) {

        Set<BeanDefinition> beanDefinitions = scanner.findCandidateComponents(packageToScan);

        Set<BeanDefinitionHolder> beanDefinitionHolders = new LinkedHashSet<BeanDefinitionHolder>(beanDefinitions.size());

        for (BeanDefinition beanDefinition : beanDefinitions) {

            String beanName = beanNameGenerator.generateBeanName(beanDefinition, registry);
            BeanDefinitionHolder beanDefinitionHolder = new BeanDefinitionHolder(beanDefinition, beanName);
            beanDefinitionHolders.add(beanDefinitionHolder);

        }

        return beanDefinitionHolders;

    }

    /**
     * Registers {@link ServiceBean} from new annotated {@link Service} {@link BeanDefinition}
     *
     * @param beanDefinitionHolder
     * @param registry
     * @param scanner
     * @see ServiceBean
     * @see BeanDefinition
     */
    private void registerServiceBean(BeanDefinitionHolder beanDefinitionHolder, BeanDefinitionRegistry registry,
                                     DubboClassPathBeanDefinitionScanner scanner) {

        Class<?> beanClass = resolveClass(beanDefinitionHolder);

        Service service = findAnnotation(beanClass, Service.class);
        if (null == service) {
            service = this.defaultService;
        }

        Class<?> interfaceClass = resolveServiceInterfaceClass(beanClass, service);

        String annotatedServiceBeanName = beanDefinitionHolder.getBeanName();

        AbstractBeanDefinition serviceBeanDefinition =
                buildServiceBeanDefinition(service, interfaceClass, annotatedServiceBeanName);

        // ServiceBean Bean name
        String beanName = generateServiceBeanName(service, interfaceClass, annotatedServiceBeanName);

        if (scanner.checkCandidate(beanName, serviceBeanDefinition)) { // check duplicated candidate bean
            registry.registerBeanDefinition(beanName, serviceBeanDefinition);

            if (logger.isInfoEnabled()) {
                logger.warn("The BeanDefinition[" + serviceBeanDefinition +
                        "] of ServiceBean has been registered with name : " + beanName);
            }

        } else {

            if (logger.isWarnEnabled()) {
                logger.warn("The Duplicated BeanDefinition[" + serviceBeanDefinition +
                        "] of ServiceBean[ bean name : " + beanName +
                        "] was be found , Did @DubboComponentScan scan to same package in many times?");
            }

        }

    }

    /**
     * Generates the bean name of {@link ServiceBean}
     *
     * @param service
     * @param interfaceClass           the class of interface annotated {@link Service}
     * @param annotatedServiceBeanName the bean name of annotated {@link Service}
     * @return ServiceBean@interfaceClassName#annotatedServiceBeanName
     * @since 2.5.9
     */
    private String generateServiceBeanName(Service service, Class<?> interfaceClass, String annotatedServiceBeanName) {

        StringBuilder beanNameBuilder = new StringBuilder(ServiceBean.class.getSimpleName());

        beanNameBuilder.append(SEPARATOR).append(annotatedServiceBeanName);

        String interfaceClassName = interfaceClass.getName();

        beanNameBuilder.append(SEPARATOR).append(interfaceClassName);

        String version = service.version();

        if (StringUtils.hasText(version)) {
            beanNameBuilder.append(SEPARATOR).append(version);
        }

        String group = service.group();

        if (StringUtils.hasText(group)) {
            beanNameBuilder.append(SEPARATOR).append(group);
        }

        return beanNameBuilder.toString();

    }

    private Class<?> resolveServiceInterfaceClass(Class<?> annotatedServiceBeanClass, Service service) {

        Class<?> interfaceClass = service.interfaceClass();

        if (void.class.equals(interfaceClass)) {

            interfaceClass = null;

            String interfaceClassName = service.interfaceName();

            if (StringUtils.hasText(interfaceClassName)) {
                if (ClassUtils.isPresent(interfaceClassName, classLoader)) {
                    interfaceClass = resolveClassName(interfaceClassName, classLoader);
                }
            }

        }

        if (interfaceClass == null) {

            Class<?>[] allInterfaces = annotatedServiceBeanClass.getInterfaces();

            if (allInterfaces.length > 0) {
                interfaceClass = allInterfaces[0];
            }

        }

        Assert.notNull(interfaceClass,
                "@Service interfaceClass() or interfaceName() or interface class must be present!");

        Assert.isTrue(interfaceClass.isInterface(),
                "The type that was annotated @Service is not an interface!");

        return interfaceClass;
    }

    private Class<?> resolveClass(BeanDefinitionHolder beanDefinitionHolder) {

        BeanDefinition beanDefinition = beanDefinitionHolder.getBeanDefinition();

        return resolveClass(beanDefinition);

    }

    private Class<?> resolveClass(BeanDefinition beanDefinition) {

        String beanClassName = beanDefinition.getBeanClassName();

        return resolveClassName(beanClassName, classLoader);

    }

    private Set<String> resolvePackagesToScan(Set<String> packagesToScan) {
        Set<String> resolvedPackagesToScan = new LinkedHashSet<String>(packagesToScan.size());
        for (String packageToScan : packagesToScan) {
            if (StringUtils.hasText(packageToScan)) {
                String resolvedPackageToScan = environment.resolvePlaceholders(packageToScan.trim());
                resolvedPackagesToScan.add(resolvedPackageToScan);
            }
        }
        return resolvedPackagesToScan;
    }

    private AbstractBeanDefinition buildServiceBeanDefinition(Service service, Class<?> interfaceClass,
                                                              String annotatedServiceBeanName) {

        BeanDefinitionBuilder builder = rootBeanDefinition(ServiceBean.class);

        AbstractBeanDefinition beanDefinition = builder.getBeanDefinition();

        MutablePropertyValues propertyValues = beanDefinition.getPropertyValues();

        String[] ignoreAttributeNames = of("provider", "monitor", "application", "module", "registry", "protocol", "interface");

        propertyValues.addPropertyValues(new AnnotationPropertyValuesAdapter(service, environment, ignoreAttributeNames));

        // References "ref" property to annotated-@Service Bean
        addPropertyReference(builder, "ref", annotatedServiceBeanName);
        // Set interface
        builder.addPropertyValue("interface", interfaceClass.getName());

        /**
         * Add {@link com.alibaba.dubbo.config.ProviderConfig} Bean reference
         */
        String providerConfigBeanName = service.provider();
        if (StringUtils.hasText(providerConfigBeanName)) {
            addPropertyReference(builder, "provider", providerConfigBeanName);
        }

        /**
         * Add {@link com.alibaba.dubbo.config.MonitorConfig} Bean reference
         */
        String monitorConfigBeanName = service.monitor();
        if (StringUtils.hasText(monitorConfigBeanName)) {
            addPropertyReference(builder, "monitor", monitorConfigBeanName);
        }

        /**
         * Add {@link com.alibaba.dubbo.config.ApplicationConfig} Bean reference
         */
        String applicationConfigBeanName = service.application();
        if (StringUtils.hasText(applicationConfigBeanName)) {
            addPropertyReference(builder, "application", applicationConfigBeanName);
        }

        /**
         * Add {@link com.alibaba.dubbo.config.ModuleConfig} Bean reference
         */
        String moduleConfigBeanName = service.module();
        if (StringUtils.hasText(moduleConfigBeanName)) {
            addPropertyReference(builder, "module", moduleConfigBeanName);
        }


        /**
         * Add {@link com.alibaba.dubbo.config.RegistryConfig} Bean reference
         */
        String[] registryConfigBeanNames = service.registry();

        List<RuntimeBeanReference> registryRuntimeBeanReferences = toRuntimeBeanReferences(registryConfigBeanNames);

        if (!registryRuntimeBeanReferences.isEmpty()) {
            builder.addPropertyValue("registries", registryRuntimeBeanReferences);
        }

        /**
         * Add {@link com.alibaba.dubbo.config.ProtocolConfig} Bean reference
         */
        String[] protocolConfigBeanNames = service.protocol();

        List<RuntimeBeanReference> protocolRuntimeBeanReferences = toRuntimeBeanReferences(protocolConfigBeanNames);

        if (!protocolRuntimeBeanReferences.isEmpty()) {
            builder.addPropertyValue("protocols", protocolRuntimeBeanReferences);
        }

        return builder.getBeanDefinition();

    }


    private ManagedList<RuntimeBeanReference> toRuntimeBeanReferences(String... beanNames) {

        ManagedList<RuntimeBeanReference> runtimeBeanReferences = new ManagedList<RuntimeBeanReference>();

        if (!ObjectUtils.isEmpty(beanNames)) {

            for (String beanName : beanNames) {

                String resolvedBeanName = environment.resolvePlaceholders(beanName);

                runtimeBeanReferences.add(new RuntimeBeanReference(resolvedBeanName));
            }

        }

        return runtimeBeanReferences;

    }

    private void addPropertyReference(BeanDefinitionBuilder builder, String propertyName, String beanName) {
        String resolvedBeanName = environment.resolvePlaceholders(beanName);
        builder.addPropertyReference(propertyName, resolvedBeanName);
    }


    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {

    }

    @Override
    public void setEnvironment(Environment environment) {
        this.environment = environment;
    }

    @Override
    public void setResourceLoader(ResourceLoader resourceLoader) {
        this.resourceLoader = resourceLoader;
    }

    @Override
    public void setBeanClassLoader(ClassLoader classLoader) {
        this.classLoader = classLoader;
    }

}
```



正常情况下我们都是使用 Dubbo 提供的 `@Service` 注解将服务注册到 Dubbo 的注册中心，现要求既能使用 RESTful 又能使用 Dubbo，故需要使用一个统一的注解 `@FeignClient` 处理，关键代码如下：

```java
/**
 * Registers Beans whose classes was annotated {@link FeignClient}
 *
 * @param packagesToScan The base packages to scan
 * @param registry       {@link BeanDefinitionRegistry}
 */
private void registerServiceBeans(Set<String> packagesToScan, BeanDefinitionRegistry registry) {

    DubboClassPathBeanDefinitionScanner scanner =
            new DubboClassPathBeanDefinitionScanner(registry, environment, resourceLoader);

    BeanNameGenerator beanNameGenerator = resolveBeanNameGenerator(registry);

    scanner.setBeanNameGenerator(beanNameGenerator);

    scanner.addIncludeFilter(new AnnotationTypeFilter(FeignClient.class, true, true));

    for (String packageToScan : packagesToScan) {

        // Registers @Service Bean first
        scanner.scan(packageToScan);

        // Finds all BeanDefinitionHolders of @Service whether @ComponentScan scans or not.
        Set<BeanDefinitionHolder> beanDefinitionHolders =
                findServiceBeanDefinitionHolders(scanner, packageToScan, registry, beanNameGenerator);

        if (!CollectionUtils.isEmpty(beanDefinitionHolders)) {

            for (BeanDefinitionHolder beanDefinitionHolder : beanDefinitionHolders) {
                registerServiceBean(beanDefinitionHolder, registry, scanner);
            }

            if (logger.isInfoEnabled()) {
                logger.info(beanDefinitionHolders.size() + " annotated Dubbo's @Service Components { " +
                        beanDefinitionHolders +
                        " } were scanned under package[" + packageToScan + "]");
            }

        } else {

            if (logger.isWarnEnabled()) {
                logger.warn("No Spring Bean annotating Dubbo's @Service was found under package["
                        + packageToScan + "]");
            }

        }

    }

}
```




## 创建 Dubbo 调用替换类

在 `com.alibaba.dubbo.config.spring.beans.factory.annotation` 包下创建一个名为 `DubboFeignBuilder` 的工具类

**注：包名不可修改**

```java
package com.alibaba.dubbo.config.spring.beans.factory.annotation;

import com.alibaba.dubbo.config.annotation.Reference;
import feign.Feign;
import feign.Target;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.util.ReflectionUtils;

public class DubboFeignBuilder extends Feign.Builder {
    @Autowired
    private ApplicationContext applicationContext;

    public Reference defaultReference;
    final class DefaultReferenceClass{
        @Reference(check = false) String field;
    }

    public DubboFeignBuilder() {
        this.defaultReference = ReflectionUtils.findField(DubboFeignBuilder.DefaultReferenceClass.class,"field").getAnnotation(Reference.class);
    }


    @Override
    public <T> T target(Target<T> target) {
        ReferenceBeanBuilder beanBuilder = ReferenceBeanBuilder
                .create(defaultReference, target.getClass().getClassLoader(), applicationContext)
                .interfaceClass(target.type());

        try {
            T object = (T) beanBuilder.build().getObject();
            return object;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```




正常情况下我们都是使用 Dubbo 提供的 `@Reference` 注解来调用 Dubbo 服务，改造后我们需要能够支持 RESRful 和 Dubbo 两种调用方式，如果项目中存在 Dubbo 依赖则使用 Dubbo 来调用服务，否则使用 RESTful 调用，关键代码如下：

```java
@Override
public <T> T target(Target<T> target) {
    ReferenceBeanBuilder beanBuilder = ReferenceBeanBuilder
            .create(defaultReference, target.getClass().getClassLoader(), applicationContext)
            .interfaceClass(target.type());

    try {
        T object = (T) beanBuilder.build().getObject();
        return object;
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}
```



## 创建自动配置类

利用 Spring Boot Starter 的扩展机制来制作我们自己的 Starter

```java
package com.funtl.alibaba.dubbo.core;

import com.alibaba.dubbo.config.AbstractConfig;
import com.alibaba.dubbo.config.spring.beans.factory.annotation.DubboFeignBuilder;
import com.alibaba.dubbo.config.spring.beans.factory.annotation.FeignClientToDubboProviderBeanPostProcessor;
import feign.Feign;
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
import org.springframework.boot.context.properties.source.ConfigurationPropertySources;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.env.Environment;

import java.util.Set;

import static com.alibaba.boot.dubbo.util.DubboUtils.BASE_PACKAGES_PROPERTY_NAME;
import static com.alibaba.boot.dubbo.util.DubboUtils.DUBBO_PREFIX;
import static java.util.Collections.emptySet;

@Configuration
@ConditionalOnProperty(prefix = DUBBO_PREFIX, name = "enabled", matchIfMissing = true, havingValue = "true")
@ConditionalOnClass(AbstractConfig.class)
public class SpringCloudAlibabaDubboAutoConfiguration {
    @ConditionalOnProperty(name = BASE_PACKAGES_PROPERTY_NAME)
    @ConditionalOnClass(ConfigurationPropertySources.class)
    @Bean
    public FeignClientToDubboProviderBeanPostProcessor feignClientToDubboProviderBeanPostProcessor(Environment environment) {
        Set<String> packagesToScan = environment.getProperty(BASE_PACKAGES_PROPERTY_NAME, Set.class, emptySet());
        return new FeignClientToDubboProviderBeanPostProcessor(packagesToScan);
    }

    @Bean
    public Feign.Builder feignDubboBuilder() {
        return new DubboFeignBuilder();
    }
}
```



说明：

- `@ConditionalOnProperty`：触发条件，当配置文件中存在预设条件则触发当前自动配置
- `@ConditionalOnClass`：触发条件，当 `classpath` 下存在预设类则触发当前自动配置

## spring.factories

在 `src/main/resources/META-INF` 目录下创建一个名为 `spring.factories` 的配置文件

```text
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  com.funtl.alibaba.dubbo.core.SpringCloudAlibabaDubboAutoConfiguration
```

# 创建注册中心管理模块

## POM

创建一个名为 `spring-cloud-alibaba-dubbo-nacos` 的模块，其父工程为我们的工程项目 `spring-cloud-alibaba-dubbo-parent`；改模块的主要作用是管理 Dubbo Nacos 注册中心的相关依赖。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-cloud-alibaba-dubbo-parent</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-cloud-alibaba-dubbo-nacos</artifactId>
    <url>http://www.funtl.com</url>

    <dependencies>
        <!-- Spring -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>

        <!-- Dubbo -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo-registry-nacos</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo-serialization-kryo</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
        </dependency>

        <!-- Alibaba -->
        <dependency>
            <groupId>com.alibaba.spring</groupId>
            <artifactId>spring-context-support</artifactId>
        </dependency>

        <!-- Commons -->
        <dependency>
            <groupId>io.netty</groupId>
            <artifactId>netty-all</artifactId>
        </dependency>
    </dependencies>
</project>
```

# 创建 Starter 项目

## POM

创建一个名为 `spring-cloud-alibaba-dubbo-starter` 的模块，其父工程为我们的工程项目 `spring-cloud-alibaba-dubbo-parent`；改模块就是利用 Spring Boot Starter 扩展机制实现的自定义 Starter 项目了；

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-cloud-alibaba-dubbo-parent</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-cloud-alibaba-dubbo-starter</artifactId>
    <url>http://www.funtl.com</url>

    <dependencies>
        <!-- Spring -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <!-- Projects -->
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>spring-cloud-alibaba-dubbo-core</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>spring-cloud-alibaba-dubbo-nacos</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
    </dependencies>

</project>
```

# 创建服务提供者

## 创建案例工程项目

创建一个名为 `spring-cloud-alibaba-dubbo-examples` 的工程项目

### POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.funtl</groupId>
    <artifactId>spring-cloud-alibaba-dubbo-examples</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <url>http://www.funtl.com</url>

    <modules>
        <module>spring-cloud-alibaba-dubbo-provider</module>
        <module>spring-cloud-alibaba-dubbo-consumer</module>
    </modules>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>${java.version}</maven.compiler.source>
        <maven.compiler.target>${java.version}</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>

        <spring-boot.version>2.1.1.RELEASE</spring-boot.version>
        <spring-cloud.version>Greenwich.RELEASE</spring-cloud.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <!-- Spring -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

            <!-- Projects -->
            <dependency>
                <groupId>com.funtl</groupId>
                <artifactId>spring-cloud-alibaba-dubbo-starter</artifactId>
                <version>${project.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <profiles>
        <profile>
            <id>release</id> <!-- 部署要用到 -->
            <build>
                <plugins>
                    <plugin>
                        <artifactId>maven-compiler-plugin</artifactId>
                    </plugin>
                    <!-- Source -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-source-plugin</artifactId>
                        <version>3.0.1</version>
                        <executions>
                            <execution>
                                <phase>package</phase>
                                <goals>
                                    <goal>jar-no-fork</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                    <!-- Javadoc -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-javadoc-plugin</artifactId>
                        <version>2.9.1</version>
                        <executions>
                            <execution>
                                <phase>package</phase>
                                <goals>
                                    <goal>jar</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                    <!-- GPG -->
                    <plugin> <!-- 进行延签 -->
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-gpg-plugin</artifactId>
                        <version>1.6</version>
                        <executions>
                            <execution>
                                <phase>verify</phase>
                                <goals>
                                    <goal>sign</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
            <distributionManagement>
                <snapshotRepository>
                    <id>oss</id>
                    <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                </snapshotRepository>
                <repository>
                    <id>oss</id>
                    <url>https://oss.sonatype.org/service/local/staging/deploy/maven2/</url>
                </repository>
            </distributionManagement>
        </profile>
    </profiles>

    <licenses>
        <license>
            <name>The Apache Software License, Version 2.0</name>
            <url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
            <comments>A business-friendly OSS license</comments>
        </license>
    </licenses>

    <scm>
        <url>https://github.com/funtl/spring-cloud-alibaba-dubbo</url>
        <connection>scm:githttps://github.com/funtl/spring-cloud-alibaba-dubbo.git</connection>
        <developerConnection>scm:git:https://github.com/funtl/spring-cloud-alibaba-dubbo.git</developerConnection>
    </scm>

    <developers>
        <developer>
            <name>Lusifer</name>
            <email>lee.lusifer@gmail.com</email>
            <url>http://www.funtl.com</url>
        </developer>
    </developers>
    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>
</project>
```



## 创建服务提供者

创建一个名为 `spring-cloud-alibaba-dubbo-provider` 的模块，其父工程为 `spring-cloud-alibaba-dubbo-examples`

### POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-cloud-alibaba-dubbo-examples</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-cloud-alibaba-dubbo-provider</artifactId>
    <packaging>pom</packaging>
    <url>http://www.funtl.com</url>

    <modules>
        <module>spring-cloud-alibaba-dubbo-provider-api</module>
        <module>spring-cloud-alibaba-dubbo-provider-service</module>
    </modules>
</project>
```



## 创建服务提供者接口

创建一个名为 `spring-cloud-alibaba-dubbo-provider-api` 的模块，其父工程为 `spring-cloud-alibaba-dubbo-provider`

### POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-cloud-alibaba-dubbo-provider</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-cloud-alibaba-dubbo-provider-api</artifactId>
    <url>http://www.funtl.com</url>

    <dependencies>
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>spring-cloud-alibaba-dubbo-starter</artifactId>
            <scope>provided</scope>
        </dependency>
    </dependencies>

</project>
```



### 定义接口

#### ProviderService

```java
package com.funtl.alibaba.dubbo.provider.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;

@FeignClient("provider")
public interface ProviderService {
    @GetMapping("/hi")
    String hi();
}
```



#### FooService

```java
package com.funtl.alibaba.dubbo.provider.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;

@FeignClient("provider")
public interface FooService {
    @GetMapping("/foo")
    String foo();
}
```



## 创建服务提供者实现

创建一个名为 `spring-cloud-alibaba-dubbo-provider-service` 的模块，其父工程为 `spring-cloud-alibaba-dubbo-provider`

### POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-cloud-alibaba-dubbo-provider</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-cloud-alibaba-dubbo-provider-service</artifactId>
    <url>http://www.funtl.com</url>

    <dependencies>
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>spring-cloud-alibaba-dubbo-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>spring-cloud-alibaba-dubbo-provider-api</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
    </dependencies>

</project>
```



### 实现接口

```java
package com.funtl.alibaba.dubbo.provider.service.impl;

import com.funtl.alibaba.dubbo.provider.service.ProviderService;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ProviderServiceImpl implements ProviderService {
    @Override
    public String hi() {
        return "Hi Spring Cloud Alibaba Dubbo";
    }
}
```



```java
package com.funtl.alibaba.dubbo.provider.service.impl;

import com.funtl.alibaba.dubbo.provider.service.FooService;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class FooServiceImpl implements FooService {
    @Override
    public String foo() {
        return "Foo Spring Cloud Alibaba Dubbo";
    }
}
```



### Application

```java
package com.funtl.alibaba.dubbo.provider;

import com.alibaba.dubbo.config.ProtocolConfig;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.context.annotation.Bean;

@EnableDiscoveryClient
@SpringBootApplication
public class ProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProviderApplication.class, args);
    }

    @Bean
    public ProtocolConfig protocolConfig() {
        ProtocolConfig protocolConfig = new ProtocolConfig();
        protocolConfig.setPort(20880);
        protocolConfig.setName("dubbo");
        return protocolConfig;
    }
}
```



### application.yml

```yaml
spring:
  main:
    allow-bean-definition-overriding: true
  application:
    name: provider
  cloud:
    nacos:
      discovery:
        server-addr: 192.168.10.146:8848

server:
  port: 10101

dubbo:
  application:
    name: provider
  registry:
    address: nacos://192.168.10.146:8848
  scan:
    basePackages: com.funtl.alibaba.dubbo.provider.service

management:
  endpoints:
    web:
      exposure:
        include: "*"
```



## 运行测试

访问 Nacos 可以看到同时注册了 Dubbo 和 Http 服务

![img](http://ww1.sinaimg.cn/large/006tNc79ly1g3i0su8fkoj31hc0gw752.jpg)

# 创建服务消费者

## 服务消费者工程项目

创建一个名为 `spring-cloud-alibaba-dubbo-consumer` 的模块，其父工程为 `spring-cloud-alibaba-dubbo-examples`

### POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-cloud-alibaba-dubbo-examples</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-cloud-alibaba-dubbo-consumer</artifactId>
    <packaging>pom</packaging>
    <url>http://www.funtl.com</url>

    <modules>
        <module>spring-cloud-alibaba-dubbo-consumer-service</module>
    </modules>
</project>
```



## 创建服务消费者模块

创建一个名为 `spring-cloud-alibaba-dubbo-consumer-service` 的模块，其父工程为 `spring-cloud-alibaba-dubbo-consumer`

### POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-cloud-alibaba-dubbo-consumer</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-cloud-alibaba-dubbo-consumer-service</artifactId>
    <url>http://www.funtl.com</url>

    <dependencies>
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>spring-cloud-alibaba-dubbo-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>spring-cloud-alibaba-dubbo-provider-api</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
    </dependencies>

</project>
```



### 创建服务消费者实现

```java
package com.funtl.alibaba.dubbo.consumer.service;

import com.funtl.alibaba.dubbo.provider.service.FooService;
import com.funtl.alibaba.dubbo.provider.service.ProviderService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class TestService {

    @Autowired
    private ProviderService providerService;

    @Autowired
    private FooService fooService;

    @GetMapping("/test")
    public String test() {
        return providerService.hi();
    }

    @GetMapping("/test/foo")
    public String testFoo() {
        return fooService.foo();
    }
}
```



### Application

注意此时需要使用 `@EnableFeignClients(basePackages = "com.funtl.alibaba.dubbo")` 做包路径扫描，这样才能使用 `@Autowired` 方式将 Provider API 项目中的接口注入进来

```java
package com.funtl.alibaba.dubbo.consumer;

import com.alibaba.dubbo.config.ProtocolConfig;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.context.annotation.Bean;

@EnableDiscoveryClient
@SpringBootApplication
@EnableFeignClients(basePackages = "com.funtl.alibaba.dubbo")
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class ,args);
    }

    @Bean
    public ProtocolConfig protocolConfig() {
        ProtocolConfig protocolConfig = new ProtocolConfig();
        protocolConfig.setPort(30880);
        protocolConfig.setName("dubbo");
        return protocolConfig;
    }
}
```



### application.yml

```yaml
spring:
  main:
    allow-bean-definition-overriding: true
  application:
    name: consumer
  cloud:
    nacos:
      discovery:
        server-addr: 192.168.10.146:8848

server:
  port: 10201

dubbo:
  application:
    name: consumer
    qos-enable: false
  registry:
    address: nacos://192.168.10.146:8848
  scan:
    basePackages: com.funtl.alibaba.dubbo.consumer.service

management:
  endpoints:
    web:
      exposure:
        include: "*"
```



## 运行测试

访问 Nacos 可以看到同时注册了 Dubbo 和 Http 服务

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3i0t5haxkj31ae0fc3z4.jpg)

# 测试请求

## 测试 Dubbo 调用

在 `TestService` 中下断点，并通过浏览器访问 http://localhost:10201/test ，由下图可以看出此时我们是通过 Dubbo 进行服务调用

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3i0tj3uuaj31gk0lr414.jpg)

浏览器会输出

```html
Hi Spring Cloud Alibaba Dubbo
```



## 测试 RESTful 调用

在 `spring-cloud-alibaba-dubbo-consumer-service` 项目中排除 Dubbo 相关依赖，POM 修改如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-cloud-alibaba-dubbo-consumer</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-cloud-alibaba-dubbo-consumer-service</artifactId>
    <url>http://www.funtl.com</url>

    <dependencies>
        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>spring-cloud-alibaba-dubbo-starter</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>com.funtl</groupId>
                    <artifactId>spring-cloud-alibaba-dubbo-core</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>com.alibaba.boot</groupId>
                    <artifactId>dubbo-spring-boot-starter</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>com.funtl</groupId>
            <artifactId>spring-cloud-alibaba-dubbo-provider-api</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
    </dependencies>

</project>
```



在 `TestService` 中下断点，并通过浏览器访问 http://localhost:10201/test ，由下图可以看出此时我们是通过 HTTP 进行服务调用

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3i0tkt30xj31hc0jl774.jpg)

浏览器会输出

```html
Hi Spring Cloud Alibaba Dubbo
```