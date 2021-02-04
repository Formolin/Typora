### properties

```properties
# 驱动配置信息
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.url = jdbc:mysql://127.0.0.1:3306/1901?useUnicode=true&characterEncoding=utf-8
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



mybatis.type-aliases-package=com.aishang.mybatis.test.domain
mybatis.mapper-locations=classpath:mapper/*.xml



#开启下划线转驼峰映射
mybatis.configuration.map-underscore-to-camel-case=true 
```

或者mybatis-config.xml

```xml
<configuration>
    <!-- 开启驼峰映射 ，为自定义的SQL语句服务-->
    <!--设置启用数据库字段下划线映射到java对象的驼峰式命名属性，默认为false-->  
    <settings>
      <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
 
</configuration>
```



### pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.aishang</groupId>
    <artifactId>mybatis-test</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>mybatis-test</name>
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
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
        </dependency>
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.0.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>RELEASE</version>
            <scope>compile</scope>
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

### UserController

```java
package com.aishang.mybatis.test.controller;

import com.aishang.mybatis.test.domain.User;
import com.aishang.mybatis.test.service.IUserService;
import org.springframework.util.DigestUtils;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;
import java.util.List;

@RestController
public class UserController {
    @Resource
    private IUserService userService;

    @RequestMapping(value = "/findAllUser",method = RequestMethod.GET)
    public String findAllUser(){
        List<User> all = userService.findAll();
        return all.get(1).toString();
    }


    @RequestMapping(value = "/insertUser",method = RequestMethod.GET)
    public String insertUser(){
        User user = new User();
        user.setDepID(1);
        user.setUserName("mybatis-002");
        user.setUserPwd(DigestUtils.md5DigestAsHex("123456".getBytes()));
        user.setUserCode("0123");
        user.setUserSex("男");
        user.setUserAge(18);
        user.setUserPower(1);
        int i = userService.insertUser(user);
        return i+"";
    }


    @RequestMapping(value = "/delUser",method = RequestMethod.GET)
    public String delUser(){
        int i = userService.delUser(6918);
        return i+"";
    }


    @RequestMapping(value = "/updateUser",method = RequestMethod.GET)
    public String updateUser(){
        User user = userService.getUserById(6909);
        user.setDepID(1);
        user.setUserName("111999");
        user.setUserPwd(DigestUtils.md5DigestAsHex("123456".getBytes()));
        user.setUserCode("0123");
        user.setUserSex("男");
        user.setUserAge(18);
        user.setUserPower(1);
        int i = userService.updateUser(user);
        return i+"";
    }

    @RequestMapping(value = "/likeUser",method = RequestMethod.GET)
    public String likeUser(){
        List<User> user = userService.findLikeAllUser("刘");
        return user.toString();
    }

    @RequestMapping(value = "/search",method = RequestMethod.GET)
    public String search(){
        User user = new User();
        user.setUserName("刘");
        user.setDepID(1);
        List<User> search = userService.searchUser(user);
        return search.toString();
    }
}

```

### UserServiceImpl

```java
package com.aishang.mybatis.test.service.impl;

import com.aishang.mybatis.test.dao.IUserDao;
import com.aishang.mybatis.test.domain.User;
import com.aishang.mybatis.test.service.IUserService;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;
import java.util.List;
@Service
public class UserServiceImpl implements IUserService {
    @Resource
    private IUserDao iUserDao;
    @Override
    public List<User> findAll() {
        return iUserDao.findAll();
    }

    @Override
    public int insertUser(User user) {
        return iUserDao.insertUser(user);
    }

    @Override
    public int delUser(int id) {
        return iUserDao.delUser(id);
    }

    @Override
    public User getUserById(int id) {
        return iUserDao.getUserById(id);
    }

    @Override
    public int updateUser(User user) {
        return iUserDao.updateUser(user);
    }

    @Override
    public List<User> findLikeAllUser(String name) {
        return iUserDao.findLikeAllUser(name);
    }

    @Override
    public List<User> searchUser(User user) {
        return iUserDao.searchUser(user);
    }
}

```

### IUserService

```java
package com.aishang.mybatis.test.service;

import com.aishang.mybatis.test.domain.User;

import java.util.List;

public interface IUserService {
    public List<User> findAll();

    public int insertUser(User user);

    public int delUser(int id);



    public User getUserById(int id);
    public int updateUser(User user);

    //模糊查询
    public List<User> findLikeAllUser(String name);

    public List<User> searchUser(User user);
}

```

### User

```java
package com.aishang.mybatis.test.domain;

import lombok.Data;

@Data
public class User {
    private Integer id;
    private Integer depID;
    private String userName;
    private String userPwd;
    private String userCode;
    private String userSex;
    private Integer userAge;
    private Integer userPower;


}

```



### IUserDao

```java
package com.aishang.mybatis.test.dao;

import com.aishang.mybatis.test.domain.User;
import org.apache.ibatis.annotations.*;

import java.util.List;

@Mapper
public interface IUserDao {
    //查询所有
    //@Select("select * from tb_users")
    public List<User> findAll();
    //添加
    //@Insert("insert into tb_users values(default,#{depID},#{userName},#{userPwd},#{userCode},#{userSex},#{userAge},#{userPower})")
    public int insertUser(User user);
    //删除
    //@Delete("delete from tb_users where id=#{id}")
    public int delUser(int id);


    //根据id查询
    //@Select("select * from tb_users where id = #{id}")
    public User getUserById(int id);
    //修改
    //@Update("update tb_users set userName=#{userName},userAge=#{userAge} where id = #{id}")
    public int updateUser(User user);


    //模糊查询
//    @Select("select * from tb_users where userName like concat('%',#{userName},'%')")
    public List<User> findLikeAllUser(String name);
}

```

### userMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.aishang.mybatis.test.dao.IUserDao">
    <!--公有部分抽取出sql片段-->
    <sql id="tb_users_col">
        id,
        depID,
        userName,
        userPwd,
        userCode,
        userSex,
        userAge,
        userPower
    </sql>
    <select id="findAll" resultType="User">
        select * from tb_users
    </select>

    <insert id="insertUser">
        insert into tb_users values(default,#{depID},#{userName},#{userPwd},#{userCode},#{userSex},#{userAge},#{userPower})
    </insert>


    <delete id="delUser">
        delete from tb_users where id=#{id}
    </delete>

    <select id="getUserById" resultType="User">
        select
        <include refid="tb_users_col"/>
        from tb_users where id = #{id}
    </select>

    <update id="updateUser">
        update tb_users set userName=#{userName},userAge=#{userAge} where id = #{id}
    </update>

    <!--模糊查询-->
    <select id="findLikeAllUser" resultType="User">
        select * from tb_users where userName like concat('%',#{userName},'%')
    </select>
    或者
     <select id="findLikeAllUser" resultType="User">
        select * from tb_users where userName like "%"#{username}"%"
    </select>
</mapper>


```

### 复合查询

```xml
 <!--复合查询-->
    <select id="searchUser" resultType="User">
        select * from tb_users
        <where>
            <if test="userName !=null and userName !='' ">
                and userName like concat('%',#{userName},'%')
            </if>
            <if test="depID!=null and depID !=0">
                and depID = #{depID}
            </if>
        </where>
    </select>
```



### 批量删除

![069_项目实战-MyShop-用户管理功能-使用动态_SQL_实现搜索功能_mp4](/Users/liujiang/Documents/Typora/imgs/006y8mN6ly1g73jcimq9lj311g0k0wm0.jpg)

![069_项目实战-MyShop-用户管理功能-使用动态_SQL_实现搜索功能_mp4](/Users/liujiang/Documents/Typora/imgs/006y8mN6ly1g73jduxqz7j314w0lg49i.jpg)

```xml
 <!--批量删除-->
    <delete id="delBatch">
        delete from tb_users
            <if test="array !=null and array.length>0">
                where id in
                <foreach collection="array" open="(" close=")" separator="," item="item">
                    #{item}
                </foreach>
            </if>
    </delete>



<!--批量删除-->
    <delete id="delBatch">
        delete from uu
        <choose>
            <when test="array !=null and array.length>0">
                where id in
                <foreach collection="array" open="(" close=")" separator="," item="item">
                    #{item}
                </foreach>
            </when>
            <otherwise>
                where id = -1
            </otherwise>
        </choose>
    </delete>
```

### 分页+复合查询

- 查询记录数

```java
 @RequestMapping(value = "/selectCount",method = RequestMethod.GET)
    public String selectCount(){
        User user = new User();
        user.setUserName("ad");
        user.setDepID(4);
        int i = userService.selectCount(user);
        return i+"";
    }




  <!--复合查询+记录数-->
    <select id="selectCount" resultType="java.lang.Integer">
        select count(*) from tb_users
        <where>
            <if test="userName!=null and userName !=''">
                and userName like concat('%',#{userName},'%')
            </if>
            <if test="depID!=null and depID !=0">
                and depID = #{depID}
            </if>
        </where>
    </select>
```

- 复合查询+分页

```java
 @RequestMapping(value = "/compoundQuery",method = RequestMethod.GET)
    public String compoundQuery(){
        System.out.println("1111");
        User user = new User();
        user.setUserName("ad");
        user.setDepID(4);
        int pageNow = 1;
        int pageSize = 5;
        List<User> users = userService.compoundQuery(user, pageNow, pageSize);
        for (User user1 : users) {
            System.out.println(user1);
        }
        return users.toString();
    }






@Override
    public List<User> compoundQuery(User user, int pageNow, int pageSize) {
        Map<String,Object> map = new HashMap<>();
        map.put("user",user);
        map.put("start",pageSize*(pageNow-1));
        map.put("pageSize",pageSize);
        System.out.println(map);
        return iUserDao.compoundQuery(map);
    }





<!--复合查询+分页-->
    <select id="compoundQuery" resultType="User" parameterType="java.util.Map">
        select * from tb_users
        <where>
            <if test="user.userName!=null and user.userName !=''">
                and userName like concat('%',#{user.userName},'%')
            </if>
            <if test="user.depID!=null and user.depID !=0">
                and depID = #{user.depID}
            </if>
        </where>
        limit #{start},#{pageSize}
    </select>
    
      
      #{start}直接获取map的key的value值，
```

### 添加后返回id

```xml
@RequestMapping(value = "/insertUserGetId",method = RequestMethod.GET)
    public String insertUserGetId(){
        User user = new User();
        user.setDepID(1);
        user.setUserName("mybatis-102");
        user.setUserPwd(DigestUtils.md5DigestAsHex("123456".getBytes()));
        user.setUserCode("0123");
        user.setUserSex("男");
        user.setUserAge(18);
        user.setUserPower(1);
        int i = userService.insertUserGetId(user);
        System.out.println(i);
        System.out.println("添加后的id："+user.getId());
        return i+"";
    }



<!--添加后返回id,对于支持主键自增的数据库，只需要设置两个属性
    useGeneratedKeys="true"
    keyProperty="实体类中的某个属性"
    -->
    <insert id="insertUserGetId" useGeneratedKeys="true" keyProperty="id" >
        insert into tb_users values(default,#{depID},#{userName},#{userPwd},#{userCode},#{userSex},#{userAge},#{userPower})
    </insert>
```

### 多个参数

```xml
  @RequestMapping(value = "/login",method = RequestMethod.GET)
    public List<User> login(){
        List<User> login = userService.login("李跃", "123456");
        return login;
    }

    //登陆，多个参数
    public List<User> login(@Param("name") String username,@Param("password") String password);


<select id="login" resultType="User">
        select * from tb_users where userName = #{name} and userPwd = #{password}
    </select>
```

### 查询多条数据返回一个Map

```java
 @RequestMapping(value = "/loginMap",method = RequestMethod.GET)
    public Map<Integer, User> loginMap(){
        Map<Integer, User> login = userService.loginMap("李跃", "123456");
        return login;
    }


//告诉mybatis封装Map的时候，用User中的id属性当成key
    @MapKey("id")
    public Map<Integer, User> loginMap(@Param("name") String username,@Param("password") String password);


  <select id="loginMap" resultType="User">
        select * from tb_users where userName = #{name} and userPwd = #{password}
    </select>
```

### 多表联合查询

Controller

```java
 @RequestMapping(value = "/getCAndCS",method = RequestMethod.GET)
    public List<CategoryAndCS>  getCAndCS(){
        List<CategoryAndCS> cAndCS = userService.getCAndCS();
        return cAndCS;
    }
```

dao

```java
public List<CategoryAndCS> getCAndCS();
```

category

```java
package com.aishang.mybatis.test.domain;

import lombok.Data;

@Data
public class Category {
    private Integer cid;
    private String cname;
    private Integer cstate;
}

```



categorySecond

```java
package com.aishang.mybatis.test.domain;

import lombok.Data;

@Data
public class CategorySecond {
    private Integer csid;
    private String csname;
    private Integer cid;
    private Integer csstate;
}

```

categoryAndCS

```java
package com.aishang.mybatis.test.domain;

import lombok.Data;

@Data
public class CategoryAndCS {
    private Category category;
    private CategorySecond categorySecond;
}

```

mapper

```xml
 <!--多表查询-->
    <select id="getCAndCS" resultType="CategoryAndCS">
        SELECT
        c.cname as "category.cname",
        cs.csname as "categorySecond.csname"
        from
        category c,categorysecond cs
        where c.cid = cs.cid
    </select>
```

### resultMap

```xml
    <!--自定义映射-->
    <resultMap id="allProduct" type="com.aishang.mybatis.test.domain.Product">
        <!--column指定数据库的字段  property   pojo的属性-->
        <id column="pid" property="pid"/>
        <result column="pname" property="pname"></result>
        <result column="market_price" property="marketPrice"></result>
        <result column="shop_price" property="shopPrice"></result>
        <result column="pdesc" property="pdesc"></result>
        <result column="pdate" property="pdate"></result>
        <result column="csid" property="csid"></result>
        <result column="pstate" property="pstate"></result>
    </resultMap>
    <select id="getAllProduct" resultMap="allProduct">
        select * from product
    </select>
```

```java
@Data
public class Product {
    private int pid;
    private String pname;
    private double marketPrice;
    private double shopPrice;
    private String pdesc;
    private String pdate;
    private int csid;
    private int pstate;
}

@Data
public class ProductExt extends Product{
    private CategorySecond categorySeconds;
}

```



```xml
 <!--查询商品及所属分类-->
    <select id="getAllProAndCate" resultType="ProductExt">
        select
            p.pname,
            c.csname as "categorySeconds.csname"
        from
            product p,categorysecond c
        where p.csid=c.csid
    </select>
```

```xml
 <!--级联方式-->  
<resultMap id="allProAndCate" type="ProductExt">
        <id column="pid" property="pid"/>
        <result column="csname" property="categorySeconds.csname"/>
    </resultMap>
    <select id="getAllProAndCate" resultMap="allProAndCate">
        select
            p.pname,
            c.csname
        from
            product p,categorysecond c
        where p.csid=c.csid
    </select>
```

```xml
<!--平铺映射--> 
<resultMap id="allProAndCate" type="ProductExt">
        <id column="pid" property="pid"/>
        <result column="pname" property="pname"/>
        <association property="categorySeconds" javaType="CategorySecond">
            <id column="csid" property="csid"/>
            <result column="csname" property="csname"/>
        </association>
    </resultMap>
    <select id="getAllProAndCate" resultMap="allProAndCate">
        select
            p.pname,
            c.csname
        from
            product p,categorysecond c
        where p.csid=c.csid
    </select>
```

```java
@Data
public class CategorySecond {
    private Integer csid;
    private String csname;
    private Integer cid;
    private Integer csstate;
}


@Data
public class CategorySecondExt extends  CategorySecond{
    private List<Product> products;
}
```



```xml
 <!--一对多-->
    <resultMap id="allCategorySecondAndProduct" type="CategorySecondExt">
        <id column="csid" property="csid"/>
        <result column="csname" property="csname"/>
        <collection property="products" ofType="Product">
            <id column="pid" property="pid"/>
            <result property="pname" column="pname"/>
        </collection>
    </resultMap>
    <select id="getAllCategorySecondAndProduct" resultMap="allCategorySecondAndProduct">
        select c.csname,
               p.pname
        from product p
                 right join categorysecond c
                            on p.csid = c.csid
    </select>
```

```xml
<!-- 7.四表查询 -->
	<select id="getOrders_Four" resultMap="Orders_Four">
		SELECT o.id as oid,o.createtime,o.paystate,o.receiverinfo,o.totalprice,
			 u.id as uid,u.active,u.nickname,u.`password`,u.role,u.tel,
			 i.order_id as io,i.product_id as ip,i.buynum,
			 p.id as pid,p.category,p.description,p.imgurl,p.marketprice,p.pnum,p.pro_name
		from orders o
		left JOIN `user` u on o.user_id=u.id
		left JOIN orderitem i on i.order_id=o.id
		LEFT JOIN product p on i.product_id=p.id
		where o.id=#{id}
	</select>
	<!-- Order表及其主键的对应关系 -->
	<resultMap type="com.bd.domain.Orders" id="Orders_Four">
		<id column="oid" property="id" />
		
		<!-- User表及其主键的对应关系 -->
		<association property="user" javaType="com.bd.domain.User" autoMapping="true">
			<id column="uid" property="id"/>
		</association>
		
		<!-- Orderlist表及其主键的对应关系 -->
		<collection property="orderitem" ofType="com.bd.domain.Orderitem" autoMapping="true">
			<id column="io" property="order_id"/>
			<id column="ip" property="product_id"/>
			
			<!-- Product表 -->
			<association property="product" javaType="com.bd.domain.Product" autoMapping="true">	
			</association>
		</collection>
	</resultMap>
```

