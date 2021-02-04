pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.9.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.aishang</groupId>
    <artifactId>shiro</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>shiro</name>
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

        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring</artifactId>
            <version>1.3.2</version>
        </dependency>


        <!-- 添加servlet依赖模块 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <scope>provided</scope>
        </dependency>
        <!-- 添加jstl标签库依赖模块 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
        </dependency>
        <!--添加tomcat依赖模块.-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
            <version>9.0.24</version>
        </dependency>


        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
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

        <!--数据库-->
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
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.0.2</version>
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

properties

```properties

spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/shiro?useUnicode=true&characterEncoding=utf-8
spring.datasource.username=root
spring.datasource.password=password
spring.datasource.driverClassName=com.mysql.jdbc.Driver

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

mybatis.type-aliases-package=com.aishang.shiro.entity
mybatis.mapper-locations=classpath:mapper/*.xml



spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
```

# 入门

### 配置文件.ini

```ini
#定义一个用户和密码
[users]
root=123456
```

```java
package com.aishang.shiro.controller;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;

public class ShiroTest {
    public static void main(String[] args) {
        //全局设置，一次即可
        IniSecurityManagerFactory iniSecurityManagerFactory = new IniSecurityManagerFactory("classpath:shiro.ini");
        SecurityManager instance = iniSecurityManagerFactory.getInstance();
        SecurityUtils.setSecurityManager(instance);
        //subject:与当前系统交互的对象
        Subject subject = SecurityUtils.getSubject();
        UsernamePasswordToken token = new UsernamePasswordToken("root","00");
        //验证前  false
        System.out.println(subject.isAuthenticated());
        subject.login(token);
        //验证后 true
        System.out.println(subject.isAuthenticated());


    }
}

//如果密码错误 会抛出异常IncorrectCredentialsException
//用户不存在 UnknownAccountException
```

```ini
[users]
root=123456,vip
admin=111,admin
[roles]
vip=*
admin=show,add

#root用户拥有vip这个角色的所有权限
#admin用户拥有查看，添加权限

```



更改代码

```java
package com.aishang.shiro.controller;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;

public class ShiroTest {
    public static void main(String[] args) {
        //全局设置，一次即可
        IniSecurityManagerFactory iniSecurityManagerFactory = new IniSecurityManagerFactory("classpath:shiro.ini");
        SecurityManager instance = iniSecurityManagerFactory.getInstance();
        SecurityUtils.setSecurityManager(instance);
        //subject:与当前系统交互的对象
        Subject subject = SecurityUtils.getSubject();
        UsernamePasswordToken token = new UsernamePasswordToken("admin","123456");
        //验证前  false
        System.out.println(subject.isAuthenticated());
        try {
            subject.login(token);
            if (subject.isAuthenticated()){
                if (subject.hasRole("vip")){
                    System.out.println("该用户有VIP角色的权限");
                }else{
                    System.out.println("该用户没有VIP角色的权限");
                }
                if (subject.isPermitted("del")){
                    System.out.println("该用户有删除的权限");
                }else{
                    System.out.println("该用户没有有删除的权限");
                }
                if (subject.isPermitted("show")){
                    System.out.println("该用户有show的权限");
                }else{
                    System.out.println("该用户没有有show的权限");
                }
                if (subject.isPermittedAll("show","add")){
                    System.out.println("该用户有show,add的权限");
                }else{
                    System.out.println("该用户没有有show,add的权限");
                }
            }
        }catch (Exception e){
            System.out.println("登陆错误");
        }

        //验证后 true
        System.out.println(subject.isAuthenticated());


    }
}

```

# 自定义Realm

```java
package com.aishang.shiro.util;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

public class CustomRealm extends AuthorizingRealm {
    //设置realm的名称
    @Override
    public void setName(String name) {
        super.setName("CustomRealm");
    }

    //用于认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        //token是用户输入的


        //从token中取出用户的身份信息
        String userCode = (String) token.getPrincipal();
        //根据用户输入的userCode从数据库查询

        //模拟从数据库查询的密码是111111
        String password = "111111";
        //查询不到返回null

        //查询到返回认证信息AuthenticationInfo

        //身份信息principal  凭证信息credentials
        //用户输入的，跟数据库查到的
        SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(userCode,password,this.getName());
        return simpleAuthenticationInfo;
    }
    //用于授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }


}

```

配置shiro-realm.ini

```ini
[main]
CustomRealm=com.aishang.shiro.util.CustomRealm
securityManager.realms=$CustomRealm
```

测试

```java
package com.aishang.shiro.controller;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;

public class ShiroTest {
    public static void main(String[] args) {
        //全局设置，一次即可
        IniSecurityManagerFactory iniSecurityManagerFactory = new IniSecurityManagerFactory("classpath:shiro-realm.ini");
        SecurityManager instance = iniSecurityManagerFactory.getInstance();
        SecurityUtils.setSecurityManager(instance);
        //subject:与当前系统交互的对象
        Subject subject = SecurityUtils.getSubject();
        UsernamePasswordToken token = new UsernamePasswordToken("admin","123456");
        //验证前  false
        System.out.println(subject.isAuthenticated());
        try {
            subject.login(token);
            if (subject.isAuthenticated()){
                System.out.println("登陆成功");
            }
        }catch (Exception e){
            System.out.println("登陆错误");
        }

        //验证后 true
        System.out.println(subject.isAuthenticated());


    }
}

```

# 授权

```ini
[users]
#用户zhang的密码是123，此用户具有role1和role2两个角色
zhang=123,role1,role2
wang=123,role2

[roles]
#角色role1对资源user拥有create、update权限
role1=user:create,user:update
#角色role2对资源user拥有create、delete权限
role2=user:create,user:delete
#角色role3对资源user拥有create权限
role3=user:create
```

权限字符串的规则是：“资源标识符：操作：资源实例标识符”，意思是对哪个资源的哪个实例具有什么操作，“:”是资源/操作/实例的分割符，权限字符串也可以使用*通配符。

 

例子：

用户创建权限：user:create，或user:create:*

用户修改实例001的权限：user:update:001

用户实例001的所有权限：user：*：001

```java
package com.aishang.shiro.controller;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.subject.Subject;

import java.lang.reflect.Array;
import java.util.ArrayList;
import java.util.Arrays;

public class ShiroTest {
    public static void main(String[] args) {
        //全局设置，一次即可
        IniSecurityManagerFactory iniSecurityManagerFactory = new IniSecurityManagerFactory("classpath:shiro-permission.ini");
        SecurityManager instance = iniSecurityManagerFactory.getInstance();
        SecurityUtils.setSecurityManager(instance);
        //subject:与当前系统交互的对象
        Subject subject = SecurityUtils.getSubject();
        UsernamePasswordToken token = new UsernamePasswordToken("zhang","123");
        try {
            subject.login(token);
            boolean role1 = subject.hasRole("role1");
            System.out.println("有role1权限"+role1);//有role1权限true

            boolean hasAllRoles = subject.hasAllRoles(Arrays.asList("role1","role2"));
            System.out.println("多个权限"+hasAllRoles);//多个权限true


            boolean hasAllRoles1 = subject.hasAllRoles(Arrays.asList("role1","role112"));
            System.out.println("多个权限"+hasAllRoles1);//多个权限false


            boolean permitted = subject.isPermitted("user:create");
            System.out.println(permitted+"拥有user:create权限");//true拥有user:create权限

            boolean permitted1 = subject.isPermittedAll("user:create","user:update");
            System.out.println(permitted1+"拥有user:create,user:update权限");//true拥有user:create,user:update权限

            boolean permitted2 = subject.isPermittedAll("user:create","user:update","user:get");
            System.out.println(permitted2+"拥有user:create,user:update,user:get");//false拥有user:create,user:update,user:get
        }catch (Exception e){
            System.out.println("登陆错误");
        }



    }
}

```

自定义授权

```java
package com.aishang.shiro.util;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.subject.Subject;
import org.junit.jupiter.api.Test;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class CustomRealm extends AuthorizingRealm {
    //设置realm的名称
    @Override
    public void setName(String name) {
        super.setName("CustomRealm");
    }

    //用于认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        //token是用户输入的


        //从token中取出用户的身份信息
        String userCode = (String) token.getPrincipal();
        //根据用户输入的userCode从数据库查询

        //模拟从数据库查询的密码是111111
        String password = "111111";
        //查询不到返回null

        //查询到返回认证信息AuthenticationInfo

        //身份信息principal  凭证信息credentials
        //用户输入的，跟数据库查到的
        SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(userCode,password,this.getName());
        return simpleAuthenticationInfo;
    }
    //用于授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        //principalCollection获取主身份信息
        String userCode = (String) principalCollection.getPrimaryPrincipal();
        //模拟数据库
        List<String> list= new ArrayList<>();
        list.add("user:create");
        list.add("product:add");

        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();

        simpleAuthorizationInfo.addStringPermissions(list);


        return simpleAuthorizationInfo;
    }

    @Test
    public void fun(){
        //全局设置，一次即可
        IniSecurityManagerFactory iniSecurityManagerFactory = new IniSecurityManagerFactory("classpath:shiro-realm.ini");
        SecurityManager instance = iniSecurityManagerFactory.getInstance();
        SecurityUtils.setSecurityManager(instance);
        //subject:与当前系统交互的对象
        Subject subject = SecurityUtils.getSubject();
        UsernamePasswordToken token = new UsernamePasswordToken("zhang","111111");
        try {
            subject.login(token);


            boolean permitted = subject.isPermitted("user:create");
            System.out.println(permitted+"拥有user:create权限");//true拥有user:create权限

            boolean permitted1 = subject.isPermittedAll("user:create","user:update");
            System.out.println(permitted1+"拥有user:create,user:update权限");//true拥有user:create,user:update权限

            boolean permitted2 = subject.isPermittedAll("user:create","user:update","user:get");
            System.out.println(permitted2+"拥有user:create,user:update,user:get");//false拥有user:create,user:update,user:get
        }catch (Exception e){
            System.out.println("登陆错误");
        }
    }
}

```

# 整合

```java
package com.aishang.shiro.controller;

import com.aishang.shiro.entity.User;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationException;
import org.apache.shiro.authz.annotation.RequiresPermissions;
import org.apache.shiro.authz.annotation.RequiresRoles;
import org.apache.shiro.subject.Subject;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;
import javax.servlet.http.HttpServletRequest;

@Controller
public class IndexController {


    @RequestMapping("/user/add")
    public String add() {
        return "user/add";
    }

    @RequestMapping("/user/update")
    public String update() {
        return "user/update";
    }
    @RequestMapping("/main")
    public String main() {
        return "main";
    }

    @RequestMapping("/tologin")
    public String login(User user) {
        return "login";
    }
    //注解验角色和权限
    @RequestMapping("/index")
    public String index() {
        return "index";
    }
}

```

```java
package com.aishang.shiro.config;

import com.aishang.shiro.util.CustomRealm;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.util.LinkedHashMap;
import java.util.Map;

@Configuration
public class ShiroConfig {

    //将自己的验证方式加入容器
    @Bean(name = "myShiroRealm")
    public CustomRealm myShiroRealm() {
        return new CustomRealm();
    }

    //权限管理，配置主要是Realm的管理认证
    @Bean(name = "securityManager")
    public DefaultWebSecurityManager securityManager(@Qualifier("myShiroRealm")CustomRealm myShiroRealm) {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(myShiroRealm);
        return securityManager;
    }

    //Filter工厂，设置对应的过滤条件和跳转条件
    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(@Qualifier("securityManager")DefaultWebSecurityManager securityManager) {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        /*
        shiro内置过滤器
        常用过滤器：
            anon:无需认证（登陆）即可访问
            authc：必须认证才可以访问
            user：如果使用rememberMe的功能可以直接访问
            perms：该资源必须得到资源权限才可以访问
            role：该资源必须得到角色权限才可以访问
         */

        Map<String, String> map = new LinkedHashMap<>();
        //登出
        map.put("/logout", "logout");
        //对所有用户认证
        map.put("/user/add", "authc");
        map.put("/user/update", "authc");
       


//       没有认证，要跳转的页面
        shiroFilterFactoryBean.setLoginUrl("/tologin");


        shiroFilterFactoryBean.setFilterChainDefinitionMap(map);
        return shiroFilterFactoryBean;
    }



}

```

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8" %>
<!DOCTYPE HTML>
<html>
<head>

    <title>shiro</title>
</head>
<body>
<h1>首页index</h1>
<a href="${pageContext.request.contextPath}/logout">注销</a>
</body>
</html>

```

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8" %>
<!DOCTYPE HTML>
<html>
<head>

    <title>shiro</title>
</head>
<body>
<h1>main</h1>
<a href="${pageContext.request.contextPath}/user/add">添加页面</a>
<a href="${pageContext.request.contextPath}/user/update">更新页面</a>
</body>
</html>

```

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8" %>
<!DOCTYPE HTML>
<html>
<head>

    <title>shiro</title>
</head>
<body>
<form action="${pageContext.request.contextPath}/tologin" method="post">
    用户名：<input type="text" name="username">
    <br>
    密码：<input type="password" name="password">
    <br>
    <input type="submit" value="login">
</form>
</body>
</html>

```

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8" %>
<!DOCTYPE HTML>
<html>
<head>

    <title>shiro</title>
</head>
<body>
<h1>add页面</h1>
</body>
</html>

```

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8" %>
<!DOCTYPE HTML>
<html>
<head>

    <title>shiro</title>
</head>
<body>
<h1>更新页面</h1>
</body>
</html>

```

![image-20191010214929965](/Users/liujiang/Documents/Typora/imgs/image-20191010214929965.png)

此时访问，都会跳转到登陆页面,也可更改代码

```java
package com.aishang.shiro.config;

import com.aishang.shiro.util.CustomRealm;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.util.LinkedHashMap;
import java.util.Map;

@Configuration
public class ShiroConfig {

    //将自己的验证方式加入容器
    @Bean(name = "myShiroRealm")
    public CustomRealm myShiroRealm() {
        return new CustomRealm();
    }

    //权限管理，配置主要是Realm的管理认证
    @Bean(name = "securityManager")
    public DefaultWebSecurityManager securityManager(@Qualifier("myShiroRealm")CustomRealm myShiroRealm) {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(myShiroRealm);
        return securityManager;
    }

    //Filter工厂，设置对应的过滤条件和跳转条件
    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(@Qualifier("securityManager")DefaultWebSecurityManager securityManager) {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        /*
        shiro内置过滤器
        常用过滤器：
            anon:无需认证（登陆）即可访问
            authc：必须认证才可以访问
            user：如果使用rememberMe的功能可以直接访问
            perms：该资源必须得到资源权限才可以访问
            role：该资源必须得到角色权限才可以访问
         */

        Map<String, String> map = new LinkedHashMap<>();
        //登出
        map.put("/logout", "logout");
        //对所有用户认证
        map.put("/user/add", "authc");
        map.put("/user/update", "authc");
        map.put("/main", "anon");
        //下面这句话必须放在最后，要不所有都拦截
        map.put("/**", "authc");


//       没有认证，要跳转的页面
        shiroFilterFactoryBean.setLoginUrl("/tologin");


        shiroFilterFactoryBean.setFilterChainDefinitionMap(map);
        return shiroFilterFactoryBean;
    }



}

```

# 登陆

user

```java
package com.aishang.shiro.entity;

public class User {
    private int id;
    private String username;
    private String password;

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}

```



role

```java
package com.aishang.shiro.entity;

import java.util.HashSet;
import java.util.Set;

public class Role {
    private int id;
    private String name;
    private Set<Permission> permissionSet = new HashSet<>();
    private Set<User> userSet = new HashSet<>();

    @Override
    public String toString() {
        return "Role{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", permissionSet=" + permissionSet +
                ", userSet=" + userSet +
                '}';
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Set<Permission> getPermissionSet() {
        return permissionSet;
    }

    public void setPermissionSet(Set<Permission> permissionSet) {
        this.permissionSet = permissionSet;
    }

    public Set<User> getUserSet() {
        return userSet;
    }

    public void setUserSet(Set<User> userSet) {
        this.userSet = userSet;
    }
}

```



permission

```java
package com.aishang.shiro.entity;

/**
 * 权限
 */
public class Permission {
 private int id;
 private String name;
 private String url;

    @Override
    public String toString() {
        return "Permission{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", url='" + url + '\'' +
                '}';
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }
}

```

```java
package com.aishang.shiro.config;

import com.aishang.shiro.util.CustomRealm;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.util.LinkedHashMap;
import java.util.Map;

@Configuration
public class ShiroConfig {

    //将自己的验证方式加入容器
    @Bean(name = "myShiroRealm")
    public CustomRealm myShiroRealm() {
        return new CustomRealm();
    }

    //权限管理，配置主要是Realm的管理认证
    @Bean(name = "securityManager")
    public DefaultWebSecurityManager securityManager(@Qualifier("myShiroRealm")CustomRealm myShiroRealm) {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(myShiroRealm);
        return securityManager;
    }

    //Filter工厂，设置对应的过滤条件和跳转条件
    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(@Qualifier("securityManager")DefaultWebSecurityManager securityManager) {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        /*
        shiro内置过滤器
        常用过滤器：
            anon:无需认证（登陆）即可访问
            authc：必须认证才可以访问
            user：如果使用rememberMe的功能可以直接访问
            perms：该资源必须得到资源权限才可以访问
            role：该资源必须得到角色权限才可以访问
         */

        //       没有认证，要跳转的页面
        shiroFilterFactoryBean.setLoginUrl("/tologin");
        shiroFilterFactoryBean.setUnauthorizedUrl("/unauthc");

        Map<String, String> map = new LinkedHashMap<>();
        //登出
        map.put("/logout", "logout");
        //对所有用户认证
        map.put("/user/add", "authc");
        map.put("/user/update", "authc");
        map.put("/main", "anon");
        map.put("/loginOk", "anon");
        //下面这句话必须放在最后，要不所有都拦截
        map.put("/**", "authc");





        shiroFilterFactoryBean.setFilterChainDefinitionMap(map);
        return shiroFilterFactoryBean;
    }



}

```

```java
package com.aishang.shiro.controller;

import com.aishang.shiro.entity.User;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationException;
import org.apache.shiro.authz.annotation.RequiresPermissions;
import org.apache.shiro.authz.annotation.RequiresRoles;
import org.apache.shiro.subject.Subject;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

@Controller
public class IndexController {


    @RequestMapping("/user/add")
    public String add() {
        return "user/add";
    }

    @RequestMapping("/user/update")
    public String update() {
        return "user/update";
    }
    @RequestMapping("/main")
    public String main() {
        return "main";
    }

    @RequestMapping("/tologin")
    public String login(User user) {
        return "login";
    }
    @RequestMapping("/index")
    public String index() {
        return "index";
    }

    @RequestMapping("/loginOk")
    public String loginOk(User user, HttpSession session, Model model) {
        UsernamePasswordToken usernamePasswordToken = new UsernamePasswordToken(user.getUsername(), user.getPassword());
        Subject subject = SecurityUtils.getSubject();
        try{
            subject.login(usernamePasswordToken);
            session.setAttribute("user",user);
            System.out.println("登陆成功");
            return "main";
        }catch (Exception e){
            e.printStackTrace();
            System.out.println("登录失败");
            session.setAttribute("err","用户名密码错误");
            return "redirect:login";
        }
    }
}

```

```sh
SELECT
 u.*,r.*,p.*
FROM user u
inner join user_role ur on u.uid=ur.uid
inner join role r on r.rid = ur.rid
inner join permission_role pr on pr.rid=r.rid
inner join permission p on p.pid = pr.pid
where u.username = 'admin'







1	admin	123		1	vip		1	add	
1	admin	123		1	vip		2	delete	
1	admin	123		1	vip		3	update	
1	admin	123		1	vip		4	query	
1	admin	123		2	normal	1	add	
1	admin	123		2	normal	4	query	




User(uid=1, username=admin, password=123, userRoleSet=[Role(rid=2, rname=normal, permissionSet=[Permission(pid=4, pname=query, url=null), Permission(pid=1, pname=add, url=null)], userSet=[]), Role(rid=1, rname=vip, permissionSet=[Permission(pid=4, pname=query, url=null), Permission(pid=1, pname=add, url=null), Permission(pid=3, pname=update, url=null), Permission(pid=2, pname=delete, url=null)], userSet=[])])






```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">


<!-- namespace的命名空间 -->
<mapper namespace="com.aishang.shiro.dao.UserDao">
    <resultMap id="userMap" type="User">
        <id property="uid" column="uid"></id>
        <result property="username" column="username"></result>
        <result property="password" column="password"></result>
        <collection property="userRoleSet" ofType="com.aishang.shiro.entity.Role">
            <id property="rid" column="rid"></id>
            <result property="rname" column="rname"></result>
            <collection property="permissionSet" ofType="com.aishang.shiro.entity.Permission">
                <id property="pid" column="pid"></id>
                <result property="pname" column="pname"></result>
                <result property="url" column="url"></result>
            </collection>
        </collection>
    </resultMap>
    <select id="getUserByName" parameterType="String" resultMap="userMap">
            SELECT
            u.*,r.*,p.*
            from user u
            left join user_role ur on u.uid=ur.uid
            left join role r on r.rid = ur.rid
            left join permission_role pr on pr.rid=r.rid
            left join permission p on p.pid = pr.pid
            where u.username = #{username}
     </select>
</mapper>





```

# 权限标签

```jsp
<shiro:guest>
    游客访问 <a href = "login.jsp"></a>
</shiro:guest>
 
user 标签：用户已经通过认证\记住我 登录后显示响应的内容
<shiro:user>
    欢迎[<shiro:principal/>]登录 <a href = "logout">退出</a>
</shiro:user>
 
authenticated标签：用户身份验证通过，即 Subjec.login 登录成功 不是记住我登录的
<shiro:authenticted>
    用户[<shiro:principal/>] 已身份验证通过
</shiro:authenticted>
 
notAuthenticated标签：用户未进行身份验证，即没有调用Subject.login进行登录,包括"记住我"也属于未进行身份验证
<shiro:notAuthenticated>
    未身份验证(包括"记住我")
</shiro:notAuthenticated>
 
 
principal 标签：显示用户身份信息，默认调用
Subjec.getPrincipal()获取，即Primary Principal
<shiro:principal property = "username"/>
 
hasRole标签：如果当前Subject有角色将显示body体内的内容
<shiro:hashRole name = "admin">
    用户[<shiro:principal/>]拥有角色admin
</shiro:hashRole>
 
hasAnyRoles标签：如果Subject有任意一个角色(或的关系)将显示body体里的内容
<shiro:hasAnyRoles name = "admin,user">
    用户[<shiro:pricipal/>]拥有角色admin 或者 user
</shiro:hasAnyRoles>
 
lacksRole:如果当前 Subjec没有角色将显示body体内的内容
<shiro:lacksRole name = "admin">
    用户[<shiro:pricipal/>]没有角色admin
</shiro:lacksRole>
 
hashPermission:如果当前Subject有权限将显示body体内容
<shiro:hashPermission name = "user:create">
    用户[<shiro:pricipal/>] 拥有权限user:create
</shiro:hashPermission>
 
lacksPermission:如果当前Subject没有权限将显示body体内容
<shiro:lacksPermission name = "org:create">
    用户[<shiro:pricipal/>] 没有权限org:create
</shiro:lacksPermission>
```

```jsp
<%@ taglib prefix="shiro" uri="http://shiro.apache.org/tags" %>
```

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8" %>
<%@ taglib prefix="shiro" uri="http://shiro.apache.org/tags" %>
<!DOCTYPE HTML>
<html>
<head>

    <title>shiro</title>
</head>
<body>
<h1>main</h1>
<a href="${pageContext.request.contextPath}/user/add">添加页面</a><br>
<a href="${pageContext.request.contextPath}/user/update">更新页面</a><br>
<shiro:hasPermission name="add">
    <a href="${pageContext.request.contextPath}/admin">访问admin页面，必须有vip的权限</a><br>
</shiro:hasPermission>
<shiro:hasRole name="vip">
    <a href="${pageContext.request.contextPath}/user/del">访问del，注解有vip的权限</a><br>
</shiro:hasRole>

<a href="${pageContext.request.contextPath}/logout">注销</a><br>
</body>
</html>

```

```java
package com.aishang.shiro.util;

import com.aishang.shiro.entity.Permission;
import com.aishang.shiro.entity.Role;
import com.aishang.shiro.entity.User;
import com.aishang.shiro.service.UserService;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

import javax.annotation.Resource;
import java.util.*;

public class CustomRealm extends AuthorizingRealm {
    @Resource
    UserService userService;
    //授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
//        User user1 = (User) principalCollection.fromRealm(this.getClass().getName()).iterator().next();
        User user = (User) principalCollection.getPrimaryPrincipal();
        List<String> permissionList = new ArrayList<>();
        List<String> rolesList = new ArrayList<>();
        Set<Role> roleSet = user.getUserRoleSet();
        if (roleSet!=null){
            for (Role role : roleSet) {
                rolesList.add(role.getRname());
                Set<Permission> permissionSet = role.getPermissionSet();
                if (permissionSet!=null){
                    for (Permission permission : permissionSet) {
                        permissionList.add(permission.getPname());
                    }
                }
            }
        }
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        info.addStringPermissions(permissionList);
        info.addRoles(rolesList);
        return info;
    }

    /**
     * 这里可以注入userService,为了方便演示，我就写死了帐号了密码
     * private UserService userService;
     * <p>
     * 获取即将需要认证的信息
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("-------身份认证方法--------");
        //从token中取出用户的身份信息 ,包含username和password
        UsernamePasswordToken usernamePasswordToken = (UsernamePasswordToken) authenticationToken;
        //获取用户输入的用户名
        String username = usernamePasswordToken.getUsername();

        //根据用户名获取数据库中对象
        User user = userService.getUserByName(username);

        //判断用户名
        if (user==null){
            return null;
        }



        //身份信息principal  凭证信息credentials
        //用户输入的，跟数据库查到的
        //判断密码
        SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(user,user.getPassword(),getName());
        System.out.println(simpleAuthenticationInfo);
        return simpleAuthenticationInfo;
    }

}

```

![image-20191013212141824](/Users/liujiang/Documents/Typora/imgs/image-20191013212141824.png)

```java
package cn.itcast.oa.test.tree;

import java.util.ArrayList;
import java.util.Collection;
import java.util.LinkedHashSet;
import java.util.List;
import java.util.Set;

import org.junit.Test;

import cn.itcast.oa.domain.Department;

/**
 * 说明：不能使用多层循环的方式，因为需要能支持任意层。
 */
public class TreeViewPractice {
	/**
	 * 结构如下：
	 * 
	 * <pre>
	 * ┣市场部
	 *    ┣宣传部
	 *    ┣业务部
	 *       ┣业务一部
	 *       ┣业务二部
	 * ┣开发部
	 *    ┣开发一部
	 *    ┣开发二部
	 * </pre>
	 * 
	 * @return 所有最顶层的部门的列表
	 */
	public static List<Department> findTopLevelDepartmentList() {
		Department dept_1_1 = new Department();
		dept_1_1.setId(new Long(11));
		dept_1_1.setName("宣传部");

		Department dept_1_2 = new Department();
		dept_1_2.setId(new Long(12));
		dept_1_2.setName("业务部");

		Department dept_1_2_1 = new Department();
		dept_1_2_1.setId(new Long(121));
		dept_1_2_1.setName("业务一部");

		Department dept_1_2_2 = new Department();
		dept_1_2_2.setId(new Long(122));
		dept_1_2_2.setName("业务二部");

		dept_1_2_1.setParent(dept_1_2);
		dept_1_2_2.setParent(dept_1_2);
		Set<Department> children_0 = new LinkedHashSet<Department>();
		children_0.add(dept_1_2_1);
		children_0.add(dept_1_2_2);
		dept_1_2.setChildren(children_0);

		// ================================

		Department dept_1 = new Department();
		dept_1.setId(new Long(1));
		dept_1.setName("市场部");

		dept_1_1.setParent(dept_1);
		dept_1_2.setParent(dept_1);
		Set<Department> children_1 = new LinkedHashSet<Department>();
		children_1.add(dept_1_1);
		children_1.add(dept_1_2);
		dept_1.setChildren(children_1);

		// ---

		Department dept_2_1 = new Department();
		dept_2_1.setId(new Long(21));
		dept_2_1.setName("开发一部");

		Department dept_2_2 = new Department();
		dept_2_2.setId((new Long(22)));
		dept_2_2.setName("开发二部");

		Department dept_2 = new Department();
		dept_2.setId(new Long(2));
		dept_2.setName("开发部");

		dept_2_1.setParent(dept_2);
		dept_2_2.setParent(dept_2);
		Set<Department> children_2 = new LinkedHashSet<Department>();
		children_2.add(dept_2_1);
		children_2.add(dept_2_2);
		dept_2.setChildren(children_2);

		// ---

		List<Department> depts = new ArrayList<Department>();
		depts.add(dept_1);
		depts.add(dept_2);
		return depts;
	}

}

```

