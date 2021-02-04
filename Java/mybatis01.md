# mybatis

### 工程目录

![eclipse-workspace_-_mybatis_config_mapper_User_xml_-_Eclipse_IDE](/Users/liujiang/Documents/Typora/imgs/eclipse-workspace_-_mybatis_config_mapper_User_xml_-_Eclipse_IDE.png)

### Log4j.properties

```properties
# Global logging configuration，建议开发环境中要用debug
log4j.rootLogger=DEBUG, stdout
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

### SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
<!-- 和spring整合后 environments将废除 -->
     <environments default="development">
         <environment id="development">
         <!-- 使用JDBC事务管理，事务控制由mybatis -->
             <transactionManager type="JDBC"/>
             <!-- 数据库连接池，由mybatis -->
             <dataSource type="POOLED">
                 <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                 <property name="url" value="jdbc:mysql://localhost:3306/1901?characterEncoding=utf-8"/>
                 <property name="username" value="root"/>
                 <property name="password" value="password"/>
             </dataSource>
         </environment>
     </environments>
     
     <!-- 加载mapper.xml -->
     <mappers>
     	<mapper  resource="mapper/User.xml" />
     </mappers>
 </configuration>
```

### UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">



<!-- namespace的命名空间 -->
<mapper namespace="test">


</mapper>
```

### 测试

```java
<!-- namespace的命名空间 -->
<mapper namespace="test">
	<!-- 根据id查询用户信息 -->
	<!-- id相当于 方法名
	#{变量名随便起}占位符，如果是简单类型，那么这个名称随意
	parameterType输入参数的类型
	结果：resultType 结果类型，不管是多条还是一条 最终映射的是pojo  User  是全限定类名
	-->
	<select id="findUserById" parameterType="int" resultType="com.aishang.domain.User">
		select * from user where id= #{id}
	</select>
</mapper>






package com.aishang.test;

import java.io.IOException;
import java.io.InputStream;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Before;
import org.junit.Test;

import com.aishang.domain.User;

public class Demo {
	SqlSessionFactory sqlSessionFactory;
	//创建工厂
	@Before
	public void init() throws IOException {
		//创建会话工厂
		String resource = "SqlMapConfig.xml";
		//加载配置文件到输入流
		InputStream inputStream = Resources.getResourceAsStream(resource);
		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		
	}
	@Test
	public void testFindUserById() {
		SqlSession sqlSession = sqlSessionFactory.openSession();
		
		try {
			//查一条
			User	user = sqlSession.selectOne("test.findUserById",1);

			System.out.println(user);
			//关闭sqlsession
		} catch (Exception e) {
			// TODO: handle exception
			e.printStackTrace();
		}finally {
			sqlSession.close();
		}
		
	}
}

```

根据用户名称查询用户信息

```xml
@Test
	public void testFindUserByName() {
		SqlSession sqlSession = sqlSessionFactory.openSession();
		try {
			//查一条
			List<User> selectList = sqlSession.selectList("test.findUserByName","%小明%");
			System.out.println(selectList);
			//关闭sqlsession
		} catch (Exception e) {
			// TODO: handle exception
			e.printStackTrace();
		}finally {
			sqlSession.close();
		}
	}
        
        
        
        
   <!-- 根据用户名称查询用户信息 ${}表示sql的拼接,不能防止sql注入-->
	
	<select id="findUserByName" parameterType="String" resultType="com.aishang.domain.User">
		select * from user where username like '%${value}%'
	</select>
        
        
        select * from user where username like concat(concat('%',#{username}),'%')
```



```xml
<select id="findUserById" parameterType="int" resultType="com.aishang.domain.User">
	select * from tb_user where id=#{id}
</select>

id相当于方法名，parameterType相当于参数类型。resultType返回类型结果,#{内容随便写}
```

返回值是集合

```xml
通过用户名名模糊查找用户
<select id="findUserByName" parameterType="String" resultType="com.aishang.domain.User">
	select * from tb_user where like '%${value}%'
</select>

模糊查找返回集合，如果返回值是一个集合，则resultType类型是用这个集合的泛型,'%${value}%'固定写法，相当于连接符


1.  参数中直接加入%%

　　param.setUsername("%CD%");
      param.setPassword("%11%");

	<select  id="selectPersons" resultType="person" parameterType="person">
		select id,sex,age,username,password from person where true 
			<if test="username!=null"> AND username LIKE #{username}</if>
			<if test="password!=null">AND password LIKE #{password}</if>
	
	</select>
2.  bind标签

<select id="selectPersons" resultType="person" parameterType="person">
  <bind name="pattern" value="'%' + _parameter.username + '%'" />
  select id,sex,age,username,password 
  from person
  where username LIKE #{pattern}
</select>
 

 

3. CONCAT

where username LIKE concat(concat('%',#{username}),'%')
```

统计所有用户的数量

```xml
<select id="getUserCount" resultType="int">
	select count(*) from tb_user
</select>
```

插入用户

```xml
<insert id="addUser" parameterType="com.aishang.domain.User">
	insert into tb_user values(default,#{username},#{password})
</insert>

对应po包的User类的成员变量而不是get方法
```

获取添加后的id值

```xml
select last_insert_id()
必须上面是insert语句

<insert id="addUser" parameterType="com.aishang.domain.User">
  <selectKey keyProperty="id" resultType="int" order="AFTER">
  	select last_insert_id()
  </selectKey>
	insert into tb_user values(default,#{username},#{password})
</insert>
```

![eclipse-workspace_-_mybatis_src_com_aishang_mapper_UserMapper_java_-_Eclipse_IDE](/Users/liujiang/Documents/Typora/imgs/eclipse-workspace_-_mybatis_src_com_aishang_mapper_UserMapper_java_-_Eclipse_IDE.png)

UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">



<!-- namespace的命名空间 -->
<mapper namespace="com.aishang.mapper.UserMapper">
	<select id="findUserById" parameterType="int" resultType="com.aishang.domain.User">
		select * from user where id= #{id}
	</select>
  对应接口public User findUserById(int id);
</mapper>




```

sqlmappercongif

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
<!-- 和spring整合后 environments将废除 -->
     <environments default="development">
         <environment id="development">
         <!-- 使用JDBC事务管理，事务控制由mybatis -->
             <transactionManager type="JDBC"/>
             <!-- 数据库连接池，由mybatis -->
             <dataSource type="POOLED">
                 <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                 <property name="url" value="jdbc:mysql://localhost:3306/1901?characterEncoding=utf-8"/>
                 <property name="username" value="root"/>
                 <property name="password" value="password"/>
             </dataSource>
         </environment>
     </environments>
     
     <!-- 加载mapper.xml -->
     <mappers>
     	<mapper resource="mapper/User.xml" />
       	<mapper resource="mapper/UserMapper.xml" />
     </mappers>
 </configuration>
```

接口+映射文件开发方式

![Windows_7](https://ws4.sinaimg.cn/large/006tNc79ly1g2ttl6w01lj307k0dodgs.jpg)

namespace=接口的全限定类名

- 接口方法的名称要和映射文件的id一致
- 参数类型要与映射文件的param一致
- 返回值类型一致

映射文件

- parameterType：入参类型
  - 简单类型：8中基本数据类型+string  #{}
  - pojo：实体类
  - wapper：包装类型
- resultType：返回值类型
  - 简单类型：8中基本数据类型+string  #{}
  - pojo：实体类
  - wapper：包装类型

### Wapper

```java
class userWapper {
	private User user;
	private Product product;
}
```

![Windows_7](https://ws2.sinaimg.cn/large/006tNc79ly1g2uxyx937qj30z60lutd7.jpg)

![Java秒杀项目_哔哩哔哩__゜-゜_つロ_干杯_-bilibili](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3cans34ebj31b00lutqt.jpg)

![01_-_新增商品分类信息_avi](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3mr78z4a1j30xg0ditcc.jpg)

```
<!-- foreach标签 -->
<select id="foreacharray" resultType="User">
    select * from user where id in(
    <!--foreach用来遍历数组
    collection指定数据的类型
    separator拼接sql使用的分隔符
    item遍历时数据的别名  -->
        <foreach collection="array"
        separator=","
        item="o">
            #{o}
        </foreach>
    )
</select>
```

![image-20191018141044609](/Users/liujiang/Documents/Typora/imgs/image-20191018141044609.png)

数据库和实体类字段对应不上，使用上面方式

![image-20191018141349666](/Users/liujiang/Documents/Typora/imgs/image-20191018141349666.png)

如果value的值只有一个，可以省略

```java
@ResultMap("userMap")
```



