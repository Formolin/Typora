

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g2vfpyun4wj316y0u076h.jpg)

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g2vg1gwaapj31690u0abx.jpg)

- 快速启动

  ```java
  package com.aishang.hello.boot;
  
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  
  @SpringBootApplication
  public class HelloBootApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(HelloBootApplication.class, args);
      }
  
  }
  
  ```

  ```java
  package com.aishang.hello.boot.controller;
  
  import org.springframework.stereotype.Controller;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.ResponseBody;
  
  @Controller
  public class UserController {
      @RequestMapping("/hi")
      @ResponseBody
      public String hi(){
          return "hello boot";
      }
  }
  
  ```

  

主要是通过 `@RunWith` 和 `@SpringBootTest` 注解来开启单元测试功能

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = HelloSpringBootApplication.class, webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class HelloSpringBootApplicationTests {

    @LocalServerPort
    private int port;

    private URL base;

    @Autowired
    private TestRestTemplate template;

    @Before
    public void setUp() throws Exception {
        this.base = new URL("http://localhost:" + port + "/");
    }

    @Test
    public void contextLoads() {
        ResponseEntity<String> response = template.getForEntity(base.toString(), String.class);
        assertThat(response.getBody(), equalTo("Hello Spring Boot"));
    }

}
```

application.yml

```yml
#配置tomcat端口号和默认访问路径
#server:
#  port: 9090
#  servlet:
#    context-path: /boot
# 访问地址http://localhost:9090/boot/hi



```

## 日志配置

Spring Boot 对各种日志框架都做了支持，我们可以通过配置来修改默认的日志的配置

默认情况下，Spring Boot 使用 Logback 作为日志框架

```yaml
logging:
  file: logs/log.log
  level.org.springframework.web: DEBUG
  
  
  
  启动后在项目文件夹下
```



## 关闭特定的自动配置

关闭特定的自动配置使用 `@SpringBootApplication` 注解的 `exclude` 参数即可，这里以关闭数据源的自动配置为例

```java
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
```

# Thymeleaf 简介

Thymeleaf 是一个跟 Velocity、FreeMarker 类似的模板引擎，它可以完全替代 JSP 。相较与其他的模板引擎，它有如下三个极吸引人的特点

- Thymeleaf 在有网络和无网络的环境下皆可运行，即它可以让美工在浏览器查看页面的静态效果，也可以让程序员在服务器查看带数据的动态页面效果。这是由于它支持 html 原型，然后在 html 标签里增加额外的属性来达到模板 + 数据的展示方式。浏览器解释 html 时会忽略未定义的标签属性，所以 thymeleaf 的模板可以静态地运行；当有数据返回到页面时，Thymeleaf 标签会动态地替换掉静态内容，使页面动态显示。

- Thymeleaf 开箱即用的特性。它提供标准和 Spring 标准两种方言，可以直接套用模板实现 JSTL、 OGNL 表达式效果，避免每天套模板、改 JSTL、改标签的困扰。同时开发人员也可以扩展和创建自定义的方言。

- Thymeleaf 提供 Spring 标准方言和一个与 SpringMVC 完美集成的可选模块，可以快速的实现表单绑定、属性编辑器、国际化等功能。

  ## 概述

  如果希望以 Jar 形式发布模块则尽量不要使用 JSP 相关知识，这是**因为 JSP 在内嵌的 Servlet 容器上运行有一些问题 (内嵌 Tomcat、 Jetty 不支持 Jar 形式运行 JSP**，Undertow 不支持 JSP)。

  Spring Boot 中推荐使用 Thymeleaf 作为模板引擎，因为 Thymeleaf 提供了完美的 Spring MVC 支持

  Spring Boot 提供了大量模板引擎，包括：

  - FreeMarker
  - Groovy
  - Mustache
  - **Thymeleaf**
  - Velocity
  - **Beetl**

```html
<span th:text="${username}">letter</span>

th拿到后台数据，如果没有，就显示span里的静态数据

就是html
```



### 引入依赖

主要增加 `spring-boot-starter-thymeleaf` 和 `nekohtml` 这两个依赖

- `spring-boot-starter-thymeleaf`：Thymeleaf 自动配置
- `nekohtml`：允许使用非严格的 HTML 语法

添加 `pom.xml` 如下

```pom

    <dependencies>
       
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
     
         <!--允许使用非严格的html语法-->
        <dependency>
            <groupId>net.sourceforge.nekohtml</groupId>
            <artifactId>nekohtml</artifactId>
            <version>1.9.22</version>
        </dependency>

    </dependencies>

  
```

## 在 `application.yml` 中配置 Thymeleaf

```yml
spring:
  thymeleaf:
    cache: false # 开发时关闭缓存,不然没法看到实时页面
    mode: HTML # 用非严格的 HTML
    encoding: UTF-8
    servlet:
      content-type: text/html
```

## 创建html页面

- 在templates文件夹下

- 添加标签

  ```html
  <!DOCTYPE html SYSTEM "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-spring4-4.dtd">
  <html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
  ```

  修改 html 标签用于引入 thymeleaf 引擎，这样才可以在其他标签里使用 `th:*` 语法

  

## 创建测试用 JavaBean

```java
package com.aishang.hello.boot.pojo;

import java.io.Serializable;

public class User implements Serializable {
    private String username;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }
}

```



## 创建测试用 Controller

```java
package com.aishang.hello.boot.controller;

import com.aishang.hello.boot.pojo.User;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class UserController {
    @RequestMapping("/hi")
    @ResponseBody
    public String hi(){
        return "hello boot";
    }

//    测试thymeleaf,默认调转到templates文件夹下的index.html
    @RequestMapping("/thy")
    public String thymeleaf(Model model){
        User user = new User();
        user.setUsername("letter");
        model.addAttribute("user",user);
        return "index";
    }
}

```



## 创建测试页面

```html
<!DOCTYPE html SYSTEM "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-spring4-4.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1 th:text="${user.username}">我是thymeleaf</h1>
</body>
</html>
```



## 测试访问

![Title](https://ws4.sinaimg.cn/large/006tNc79ly1g2vhmf8z8ij313g0d2abf.jpg)

# Spring Boot 整合 Druid

## 引入依赖

```pom
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
```

## properties

```properties
# 驱动配置信息
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource  
spring.datasource.url = jdbc:mysql://127.0.0.1:3306/busmap?useUnicode=true&characterEncoding=utf-8  
spring.datasource.username = root  
spring.datasource.password = password  
spring.datasource.driverClassName = com.mysql.cj.jdbc.Driver  

#连接池的配置信息
spring.datasource.initialSize=5  
spring.datasource.minIdle=5  
spring.datasource.maxActive=20  
spring.datasource.maxWait=60000  
spring.datasource.timeBetweenEvictionRunsMillis=60000  
spring.datasource.minEvictableIdleTimeMillis=300000  
spring.datasource.validationQuery=SELECT 1 FROM DUAL  
spring.datasource.testWhileIdle=true  
spring.datasource.testOnBorrow=false  
spring.datasource.testOnReturn=false  
spring.datasource.poolPreparedStatements=true  
spring.datasource.maxPoolPreparedStatementPerConnectionSize=20  
spring.datasource.filters=stat,wall,log4j  
spring.datasource.connectionProperties=druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000 
```



## 配置 `application.yml`

```yml
spring:
  datasource:
    druid:
      url: jdbc:mysql://localhost:3306/1901?useUnicode=true&characterEncoding=utf-8&useSSL=false
      username: root
      password: password
      initial-size: 1
      min-idle: 1
      max-active: 20
      test-on-borrow: true
      # MySQL 8.x: com.mysql.cj.jdbc.Driver
      driver-class-name: com.mysql.cj.jdbc.Driver
```

这里可以用docker启动mysql，连接docker的mysql

# Spring Boot 整合 tk.mybatis

tk.mybatis 是在 MyBatis 框架的基础上提供了很多工具，让开发更加高效

## 引入依赖

在 `pom.xml` 文件中引入 `mapper-spring-boot-starter` 依赖，该依赖会自动引入 MyBaits 相关依赖

```text
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
    <version>2.0.2</version>
</dependency>
```

## 配置 `application.yml`

```text
mybatis:
    type-aliases-package: 实体类的存放路径，如：com.funtl.hello.spring.boot.entity
    mapper-locations: classpath:mapper/*.xml
    
    
    
    
mybatis:
  type-aliases-package: com.aishang.hello.boot.pojo
  mapper-locations: classpath:mapper/*.xml
  
  
  
  

mybatis.type-aliases-package=com.aishang.dropzone.wang.entity
mybatis.mapper-locations=classpath:mapper/*.xml
```

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g2vhyk3wrpj31d40sgwkf-20191022134118603.jpg)

## 创建一个通用的父级接口

主要作用是让 DAO 层的接口继承该接口，以达到使用 tk.mybatis 的目的

```text
package tk.mybatis;

import tk.mybatis.mapper.common.Mapper;
import tk.mybatis.mapper.common.MySqlMapper;
/**
 * 自己的 Mapper
 * 特别注意，该接口不能被扫描到，否则会出错
 * <p>Title: MyMapper</p>
 * <p>Description: </p>
 *
 * @author letter
 * @version 1.0.0
 * @date
 */
public interface MyMapper<T> extends Mapper<T>, MySqlMapper<T> {
}

```

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g2vi5rcu6ij31r40u0gsz.jpg)

# Spring Boot 整合 PageHelper

PageHelper 是 Mybatis 的分页插件，支持多数据库、多数据源。可以简化数据库的分页查询操作，整合过程也极其简单，只需引入依赖即可

## 引入依赖

在 `pom.xml` 文件中引入 `pagehelper-spring-boot-starter` 依赖

```text
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.5</version>
</dependency>
```

# 使用 MyBatis 的 Maven 插件生成代码

我们无需手动编写 实体类、DAO、XML 配置文件，只需要使用 MyBatis 提供的一个 Maven 插件就可以自动生成所需的各种文件便能够满足基本的业务需求，如果业务比较复杂只需要修改相关文件即可

## 配置插件

在 `pom.xml` 文件中增加 `mybatis-generator-maven-plugin` 插件

```text
<build>
    <plugins>
        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-maven-plugin</artifactId>
            <version>1.3.5</version>
            <configuration>
                <configurationFile>${basedir}/src/main/resources/generator/generatorConfig.xml</configurationFile>
                <overwrite>true</overwrite>
                <verbose>true</verbose>
            </configuration>
            <dependencies>
                <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>${mysql.version}</version>
                </dependency>
                <dependency>
                    <groupId>tk.mybatis</groupId>
                    <artifactId>mapper</artifactId>
                    <version>3.4.4</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```

- configurationFile：自动生成所需的配置文件路径

在 `src/main/resources/generator/` 目录下创建 `generatorConfig.xml` 配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <!-- 引入数据库连接配置 -->
    <properties resource="jdbc.properties"/>

    <context id="Mysql" targetRuntime="MyBatis3Simple" defaultModelType="flat">
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>

        <!-- 配置 tk.mybatis 插件 -->
        <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
            <property name="mappers" value="tk.mybatis.MyMapper"/>
        </plugin>

        <!-- 配置数据库连接 -->
        <jdbcConnection
                driverClass="${jdbc.driverClass}"
                connectionURL="${jdbc.connectionURL}"
                userId="${jdbc.username}"
                password="${jdbc.password}">
        </jdbcConnection>

        <!-- 配置实体类存放路径 -->
        <javaModelGenerator targetPackage="com.aishang.hello.boot.pojo" targetProject="src/main/java"/>

        <!-- 配置 XML 存放路径 -->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources"/>

        <!-- 配置 DAO 存放路径 -->
        <javaClientGenerator
                targetPackage="com.aishang.hello.boot.mapper"
                targetProject="src/main/java"
                type="XMLMAPPER"/>

        <!-- 配置需要指定生成的数据库和表，% 代表所有表 -->
        <table catalog="1901" tableName="%">
            <!-- mysql 配置 -->
            <generatedKey column="id" sqlStatement="Mysql" identity="true"/>
        </table>
    </context>
</generatorConfiguration>
```

## 配置数据源

在 `src/main/resources` 目录下创建 `jdbc.properties` 数据源配置：

```text
# MySQL 8.x: com.mysql.cj.jdbc.Driver
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.connectionURL=jdbc:mysql://ip:port/dbname?useUnicode=true&characterEncoding=utf-8&useSSL=false
jdbc.username=root
jdbc.password=123456
```

![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g2w547ocgjj31840u0wn2.jpg)

## 测试tk.mybatis操作数据库

mapper接口

```java
import com.aishang.hello.boot.pojo.TbUsers;
import com.aishang.hello.boot.pojo.User;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;
import tk.mybatis.MyMapper;

import java.util.List;
@Mapper
public interface UserMapper extends MyMapper<User> {
    @Select("select * from tb_users")
    public List<TbUsers> findAllUser();
}
```

xml配置写法:把接口的注解去掉

```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.aishang.hello.boot.mapper.UserMapper">
  <resultMap id="BaseResultMap" type="com.aishang.hello.boot.pojo.User">
    <!--
      WARNING - @mbg.generated
    -->
    <id column="id" jdbcType="INTEGER" property="id" />
    <result column="username" jdbcType="VARCHAR" property="username" />
    <result column="birthday" jdbcType="DATE" property="birthday" />
    <result column="sex" jdbcType="CHAR" property="sex" />
    <result column="address" jdbcType="VARCHAR" property="address" />
  </resultMap>

  <select id="findAllUser" resultType="com.aishang.hello.boot.pojo.TbUsers">

    select * from tb_users
  </select>
</mapper>









  <select id="findAllUser" resultType="TbUsers">

    select * from tb_users
  </select>
  
 不写全限定类名也好使
```



controller

```java
package com.aishang.hello.boot.controller;

import com.aishang.hello.boot.pojo.TbUsers;
import com.aishang.hello.boot.pojo.User;
import com.aishang.hello.boot.service.UserService;
import com.alibaba.fastjson.JSON;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.List;

@Controller
public class UserController {
    @Autowired
    private UserService userService;

    @RequestMapping("/hi")
    @ResponseBody
    public String hi(){
        return "hello boot";
    }

//    测试thymeleaf
    @RequestMapping("/thy")
    public String thymeleaf(Model model){
        User user = new User();
        user.setUsername("letter");
        model.addAttribute("user",user);
        return "index";
    }

    //测试mybatis
    @RequestMapping("/list")
    @ResponseBody
    public List<TbUsers> findAllUser(){
        List<TbUsers> allUser = userService.findAllUser();
        for (TbUsers user : allUser) {
            System.out.println(user);
        }
        return allUser;
    }
}

```

Userservice接口

```java
package com.aishang.hello.boot.service;

import com.aishang.hello.boot.pojo.TbUsers;

import java.util.List;

public interface UserService {
    public List<TbUsers> findAllUser();
}

```

UserService实现类

```java
package com.aishang.hello.boot.service.impl;

import com.aishang.hello.boot.mapper.UserMapper;
import com.aishang.hello.boot.pojo.TbUsers;
import com.aishang.hello.boot.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;
import java.util.List;
@Service
public class UserServiceImpl implements UserService {
    @Resource
    private UserMapper userMapper;
    @Override
    public List<TbUsers> findAllUser() {
        List<TbUsers> allUser = userMapper.findAllUser();
        return allUser;
    }
}

```

# 注解

获取配置文件中的属性

```java
@Value("${server.port}")
```

# Properties多环境配置

```xml
配置激活选项
spring.profiles.active=dev
添加其他配置文件
```

![image-20191022134737699](/Users/liujiang/Documents/Typora/imgs/image-20191022134737699.png)

yml配置

```yaml
1.配置激活选项
	spring:
  	  profiles:
active: dev
	2.在配置文件添加三个英文状态下的短横线即可区分
	---
spring:
  		profiles: dev



1. Properties配置多环境，需要添加多个配置文件，YAML只需要一个配件文件
	2.书写格式的差异，yaml相对比较简洁，优雅
3. YAML的缺点：不能通过@PropertySource注解加载。如果需要使用@PropertySource注解的方式加载值，那就要使用properties文件
如何使用
java -jar myapp.jar --spring.profiles.active=dev
```

# 错误页面

```java
方法一：Spring Boot 将所有的错误默认映射到/error， 实现ErrorController
@Controller
@RequestMapping(value = "error")
public class BaseErrorController implements ErrorController {
private static final Logger logger = LoggerFactory.getLogger(BaseErrorController.class);

	@Override
	public String getErrorPath() {
		logger.info("出错啦！进入自定义错误控制器");
		return "error/error";
	}

	@RequestMapping
	public String error() {
		return getErrorPath();
	}

}
添加自定义的错误页面
html静态页面：在resources/public/error/ 下定义
如添加404页面： resources/public/error/404.html页面，中文注意页面编码
2.2 模板引擎页面：在templates/error/下定义
如添加5xx页面： templates/error/5xx.ftl
注：templates/error/ 这个的优先级比较 resources/public/error/高


使用注解@ControllerAdvice

/**
	 * 统一异常处理
	 * 
	 * @param exception
	 *            exception
	 * @return
	 */
	@ExceptionHandler({ RuntimeException.class })
	@ResponseStatus(HttpStatus.OK)
	public ModelAndView processException(RuntimeException exception) {
		logger.info("自定义异常处理-RuntimeException");
		ModelAndView m = new ModelAndView();
		m.addObject("roncooException", exception.getMessage());
		m.setViewName("error/500");
		return m;
	}

	/**
	 * 统一异常处理
	 * 
	 * @param exception
	 *            exception
	 * @return
	 */
	@ExceptionHandler({ Exception.class })
	@ResponseStatus(HttpStatus.OK)
	public ModelAndView processException(Exception exception) {
		logger.info("自定义异常处理-Exception");
		ModelAndView m = new ModelAndView();
		m.addObject("roncooException", exception.getMessage());
		m.setViewName("error/500");
		return m;
	}

```

# 跨域请求

CORS 与 JSONP 相比 

1、 JSONP 只能实现 GET 请求，而 CORS 支持所有类型的 HTTP 请求。
 2、 使用 CORS，开发者可以使用普通的 XMLHttpRequest 发起请求和获得数据，比起 JSONP 有更好的 错误处理。
 3、 JSONP 主要被老的浏览器支持，它们往往不支持 CORS，而绝大多数现代浏览器都已经支持了 CORS 





在 spring MVC 中可以配置全局的规则，也可以使用@CrossOrigin 注解进行细粒度的配置

```java
@Configuration
public class CustomCorsConfiguration {
  @Bean
  public WebMvcConfigurer corsConfigurer() {
     return new WebMvcConfigurerAdapter() {
         @Override
         public void addCorsMappings(CorsRegistry registry) {
              registry.addMapping("/api/**").allowedOrigins("http://localhost:8080");
} };
} }




@RestController
@RequestMapping("/api")
public class ApiController {
  @RequestMapping(value = "/get")
  public HashMap<String, Object> get(@RequestParam String name) {
     HashMap<String, Object> map = new HashMap<String, Object>();
     map.put("title", "hello world");
     map.put("name", name);
     return map;
 } }

```

```js
测试 js:
$.ajax({
     url: "http://localhost:8081/api/get",
   type: "POST",
   data: {
name: "测试" },
   success: function(data, status, xhr) {
     console.log(data);
     alert(data.name);
} });
```

细粒度配置

```java
@RestController
@RequestMapping(value = "/api", method = RequestMethod.POST)
public class ApiController {

  @CrossOrigin(origins = "http://localhost:8080")
  @RequestMapping(value = "/get")
  public HashMap<String, Object> get(@RequestParam String name) {
     HashMap<String, Object> map = new HashMap<String, Object>();
     map.put("title", "hello world");
     map.put("name", name);
     return map;
} }
```

# 文件上传

一、Spring Boot 默认使用 springMVC 包装好的解析器进行上传 

二、添加代码

```html
 <form method="POST" enctype="multipart/form-data" action="/file/upload"> 

文件:<input type="file" name="roncooFile" /> <input type="submit" value="上传" /> 

</form> 
```

```java
@Controller
    @RequestMapping(value = "/file")
    public class FileController {
     private static final Logger logger = LoggerFactory.getLogger(FileController.class);
     @RequestMapping(value = "upload")
     @ResponseBody
     public String upload(@RequestParam("roncooFile") MultipartFile file) {
if (file.isEmpty()) { return "文件为空";
}
// 获取文件名
String fileName = file.getOriginalFilename(); logger.info("上传的文件名为:" + fileName);
// 获取文件的后缀名
String suffixName = fileName.substring(fileName.lastIndexOf(".")); logger.info("上传的后缀名为:" + suffixName);
// 文件上传路径
String filePath = "d:/roncoo/ttt/";
// 解决中文问题，liunx 下中文路径，图片显示问题 // fileName = UUID.randomUUID() + suffixName;
         File dest = new File(filePath + fileName);
// 检测是否存在目录
if (!dest.getParentFile().exists()) {
    dest.getParentFile().mkdirs();
         }
try { file.transferTo(dest); return "上传成功";
         } catch (IllegalStateException e) {
              e.printStackTrace();
         } catch (IOException e) {
              e.printStackTrace();
}
return "上传失败"; }
} 


```

三、配置

spring.http.multipart.enabled=true #默认支持文件上传.

 spring.http.multipart.file-size-threshold=0 #支持文件写入磁盘. 

spring.http.multipart.location= # 上传文件的临时目录 ,如果上传成功会存到真正的上传目录，如果失败，会存在这个目录里

spring.http.multipart.max-file-size=1Mb # 最大支持文件大小

 spring.http.multipart.max-request-size=10Mb # 最大支持请求大小

# JPA

```xml
<!-- 数据库 --> <dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-data-jpa</artifactId> </dependency>
<dependency>
<groupId>mysql</groupId> <artifactId>mysql-connector-java</artifactId> <scope>runtime</scope>
      </dependency>
```

```yaml
# JPA
spring.jpa.hibernate.ddl-auto= update
#显示 sql 语句 
spring.jpa.show-sql=true
```

实体类

```java
 @Entity
        public class RoncooUserLog {
         @Id
         @GeneratedValue
         private Integer id;
         @Column
         private Date createTime;
         @Column
         private String userName;
         @Column
         private String userIp;
        }
```

定义接口(继承 JpaRepository)

```java
public interface RoncooUserLogDao extends JpaRepository<RoncooUserLog, Integer>{ }
```

测试

```java
 @Autowired
         private RoncooUserLogDao roncooUserLogDao;
 @Test
public void insert() {
RoncooUserLog entity = new RoncooUserLog(); entity.setUserName("无境"); entity.setUserIp("192.168.0.1"); entity.setCreateTime(new Date()); roncooUserLogDao.save(entity);
}
         @Test
         public void delete() {
              roncooUserLogDao.delete(1);
         }
         @Test
         public void update() {
RoncooUserLog entity = new RoncooUserLog(); entity.setId(2);
entity.setUserName("无境 2"); entity.setUserIp("192.168.0.1"); entity.setCreateTime(new Date()); roncooUserLogDao.save(entity);
}
         @Test
         public void select() {
              RoncooUserLog result = roncooUserLogDao.findOne(1);
              System.out.println(result);
}
```

