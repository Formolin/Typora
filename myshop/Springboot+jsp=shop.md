# springboot+jsp搭建的电商后台

idea中的shop项目

- 访问路径http://localhost:8080/

- pom.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <parent>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-parent</artifactId>
          <version>2.1.2.RELEASE</version>
          <relativePath/> <!-- lookup parent from repository -->
      </parent>
      <groupId>com.liujiang</groupId>
      <artifactId>shop</artifactId>
      <version>0.0.1-SNAPSHOT</version>
      <name>shop</name>
      <description>Demo project for Spring Boot</description>
  
      <properties>
          <java.version>1.8</java.version>
      </properties>
  
      <dependencies>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
  
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-test</artifactId>
              <scope>test</scope>
          </dependency>
  
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
          <!--jsp的支持-->
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
          <!-- servlet 依赖包 -->
          <dependency>
              <groupId>javax.servlet</groupId>
              <artifactId>javax.servlet-api</artifactId>
              <!-- <scope>provided</scope>-->
          </dependency>
          <!-- JSTL (JSP standard Tag Library) JSP 标准标签库 -->
          <dependency>
              <groupId>javax.servlet</groupId>
              <artifactId>jstl</artifactId>
          </dependency>
          <!-- Tomcat的支持 -->
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-tomcat</artifactId>
              <!--  <scope>provided</scope>-->
          </dependency>
          <dependency>
              <groupId>org.apache.tomcat.embed</groupId>
              <artifactId>tomcat-embed-jasper</artifactId>
              <!-- <scope>provided</scope>-->
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
              <plugin>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-maven-plugin</artifactId>
                  <configuration>
                      <fork>true</fork>
                  </configuration>
              </plugin>
          </plugins>
      </build>
  
  </project>
  
  ```

- 请求转发，model存到request

  ```java
  public String list(Model model){
      List<User> userList = userService.selectAll();
      model.addAttribute("user",userList)
      return "user_list"
  }
  ```

- 那么重定向怎么传参？只能存session，但时间长不合适，但是可以存到session一次生效，之后删除

  ```
  public String list(User user,RedirectAttributes redirectAttributes){
      List<User> userList = userService.selectAll();
      redirectAttributes.addAttribute("user",userList)
      return "redirect:/user_form"
  }
  ```

### 1、springboot接受form的参数自动绑定到实体类

```java
public String userform(User user) {
        System.out.println("POJO: " + user.getClass().getName() +
                ", hash code: " + user.hashCode() + ", " + user.toString()+"名字是"+user.getUsername());
        return "redirect:/user/list";
    }
```

```jsp
<form class="form-horizontal" method="post" action="/user/userform">
                <div class="box-body">
                    <div class="form-group">
                        <label for="inputEmail3" class="col-sm-2 control-label">用户名</label>

                        <div class="col-sm-10" style="width: 44%">
                            <input type="text" class="form-control" id="inputEmail3" placeholder="Username" name="username">
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputPassword3" class="col-sm-2 control-label">密码</label>

                        <div class="col-sm-10" style="width: 44%">
                            <input type="password" class="form-control" id="input1" placeholder="Password" name="password">
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputPassword3" class="col-sm-2 control-label">email</label>

                        <div class="col-sm-10" style="width: 44%">
                            <input type="email" class="form-control" id="inputPassword3" placeholder="Email" name="email">
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputPassword3" class="col-sm-2 control-label">电话</label>

                        <div class="col-sm-10" style="width: 44%">
                            <input type="number" class="form-control" id="input2" placeholder="Tel" name="phone">
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputPassword3" class="col-sm-2 control-label">地址</label>

                        <div class="col-sm-10"  style="width: 44%">
                            <input type="text" class="form-control" id="input3" placeholder="Addr" name="addr">
                        </div>
                    </div>

                </div>
                <!-- /.box-body -->
                <div class="box-footer" style="margin-left: 210px;width:480px;background-color: #EBF0F5">
                    <button type="submit" class="btn btn-info" style="margin-right: 20px">提交</button>
                    <button type="button" class="btn btn-danger" onclick="history.go(-1)">返回</button>
                </div>
                <!-- /.box-footer -->
            </form>
```

结果：

```java
POJO: com.liujiang.shop.pojo.User, hash code: 536453125, User{uid=null, username='liujiangadf', password='adf', name='null', email='2131230@163.com', phone='123', addr='123', state=null, code='null'}名字是liujiangadf
```

### 2、关于sql语句的写法

- @Select 是查询类的注解，所有的查询均使用这个

- @Result 修饰返回的结果集，关联实体类属性和数据库字段一一对应，如果实体类属性和数据库属性名保持一致，就不需要这个属性来修饰。

- @Insert 插入数据库使用，直接传入实体类会自动解析属性到对应的值

- @Update 负责修改，也可以直接传入对象

- @delete 负责删除

  > 注意，使用#符号和$符号的不同：

  ```java
  // This example creates a prepared statement, something like select * from teacher where name = ?;
  @Select("Select * from teacher where name = #{name}")
  Teacher selectTeachForGivenName(@Param("name") String name);
  
  // This example creates n inlined statement, something like select * from teacher where name = 'someName';
  @Select("Select * from teacher where name = '${name}'")
  Teacher selectTeachForGivenName(@Param("name") String name);
  ```

```java
public interface UserMapper {

    @Select("SELECT * FROM users")
    @Results({
        @Result(property = "userSex",  column = "user_sex", javaType = UserSexEnum.class),
        @Result(property = "nickName", column = "nick_name")
    })
    List<UserEntity> getAll();

    @Select("SELECT * FROM users WHERE id = #{id}")
    @Results({
        @Result(property = "userSex",  column = "user_sex", javaType = UserSexEnum.class),
        @Result(property = "nickName", column = "nick_name")
    })
    UserEntity getOne(Long id);

    @Insert("INSERT INTO users(userName,passWord,user_sex) VALUES(#{userName}, #{passWord}, #{userSex})")
    void insert(UserEntity user);

    @Update("UPDATE users SET userName=#{userName},nick_name=#{nickName} WHERE id =#{id}")
    void update(UserEntity user);

    @Delete("DELETE FROM users WHERE id =#{id}")
    void delete(Long id);

}
```

代码：

User

```java
package com.liujiang.shop.pojo;

public class User {
    private Integer uid;
    private String username;
    private String password;
    private String name;
    private String email;
    private String phone;
    private String addr;
    private Integer state;
    private String code;

    public Integer getUid() {
        return uid;
    }

    public void setUid(Integer uid) {
        this.uid = uid;
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

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    public String getAddr() {
        return addr;
    }

    public void setAddr(String addr) {
        this.addr = addr;
    }

    public Integer getState() {
        return state;
    }

    public void setState(Integer state) {
        this.state = state;
    }

    public String getCode() {
        return code;
    }

    public void setCode(String code) {
        this.code = code;
    }

    @Override
    public String toString() {
        return "User{" +
                "uid=" + uid +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                ", name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", phone='" + phone + '\'' +
                ", addr='" + addr + '\'' +
                ", state=" + state +
                ", code='" + code + '\'' +
                '}';
    }
}

```



Mapper:注意#{username}的参数必须和pojo参数名一致

```java
    //添加用户
    @Insert("insert into user(username,password,email,phone,addr) values(#{username},#{password},#{email},#{phone},#{addr})")
    public void addUser(User user);
```

### 3、dto

数据传输对象com.liujiang.shop.dto

自定义返回结果集

```java
package com.liujiang.shop.dto;

import java.io.Serializable;

/**
 * 自定义返回结果
 */
public class BaseResult implements Serializable {
    public static final int STATUS_SUCCESS = 200;
    public static final int STATUS_fail = 500;
    private int status;
    private String message;

    public static BaseResult success() {
        return BaseResult.createResult(STATUS_SUCCESS,"成功");
    }
    public static BaseResult success(String message) {
        return BaseResult.createResult(STATUS_SUCCESS,message);
    }

    public static BaseResult fail() {
        return BaseResult.createResult(STATUS_fail,"失败");
    }
    public static BaseResult fail(String message) {
        return BaseResult.createResult(STATUS_fail,message);
    }
    public static BaseResult fail(int status,String message) {
        return BaseResult.createResult(status,message);
    }

    public static BaseResult createResult(int status, String message) {
        BaseResult baseResult = new BaseResult();
        baseResult.setStatus(status);
        baseResult.setMessage(message);
        return baseResult;
    }

    public int getStatus() {
        return status;
    }

    public void setStatus(int status) {
        this.status = status;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}

```

### 4、添加用户

- 后台进行验证，密码md5

  - ```java
    user.setPassword(DigestUtils.md5DigestAsHex(user.getPassword().getBytes()));
    ```

- @ModelAttribute

  - 返回的是对象，放在方法上，会在所有的@RequestMapping之前执行

### 5、JqueryValidation

引入是三个js文件，在include的footer里![截屏2019_2_20_21_45](https://ws1.sinaimg.cn/large/006tKfTcly1g0d84hw83yj30kj08vmzx.jpg)

```html
//模板样式
<div class="form-group has-error">
                  <label class="control-label" for="inputError"><i class="fa fa-times-circle-o"></i> Input with
                    error</label>
                  <input type="text" class="form-control" id="inputError" placeholder="Enter ...">
                  <span class="help-block">Help block with error</span>
                </div>
```

封装：通用的,创建一个app文件夹，validation.js,之后再footer中引用,之后再userform.jsp中初始化,并且所有的表单给个id=inputForm

```js
//函数对象:解决闭包等问题，js的面向对象语法
var Validate = function () {
    var handlerInitValidate = function () {
        //这里是私有方法
        $.validator.addMethod("mobile", function (value, element) {
            var length = value.length;
            var mobile = /^(((13[0-9]{1})|(15[0-9]{1}))+\d{8})$/;
            return this.optional(element)||(length==11&&mobile.test(value));
        },"手机号码格式错误")

        $("#inputForm").validate({
            errorElement:"span",
            errorClass:"help-block",
            errorPlacement:function (error, element) {
                element.parent().parent().attr("class","form-group has-error");
                error.insertAfter(element);
            }
        })
    }

    return {
        //这里是公共方法
        init: function () {
            handlerInitValidate();
        }
    }
}();

$(function () {
    //引入后直接初始化
    Validate.init();
})
```

```html
 <form class="form-horizontal" id="inputForm" method="post" action="/user/userform">
                <div class="box-body">
                    <div class="form-group">
                        <label for="inputEmail3" class="col-sm-2 control-label">用户名</label>
                        <div class="col-sm-10" style="width: 44%">
                            <input type="text" class="form-control required" id="inputEmail3" placeholder="Username"
                            name="username" value="${user.username}">
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputPassword3" class="col-sm-2 control-label ">密码</label>

                        <div class="col-sm-10" style="width: 44%">
                            <input type="password" class="form-control required" id="input1" placeholder="Password"
                                   name="password" value="${user.password}">
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputPassword3" class="col-sm-2 control-label">email</label>

                        <div class="col-sm-10" style="width: 44%">
                            <input type="email" class="form-control required email" id="inputPassword3" placeholder="Email"
                                   name="email" value="${user.email}">
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputPassword3" class="col-sm-2 control-label">电话</label>

                        <div class="col-sm-10" style="width: 44%">
                            <input type="number" class="form-control  required mobile" id="input2" placeholder="Tel" name="phone" value="${user.phone}">
                        </div>
                    </div>
                    <div class="form-group">
                        <label for="inputPassword3" class="col-sm-2 control-label ">地址</label>

                        <div class="col-sm-10" style="width: 44%">
                            <input type="text" class="form-control required" id="input3" placeholder="Addr" name="addr" value="${user.addr}">
                        </div>
                    </div>

                </div>
                <!-- /.box-body -->
                <div class="box-footer" style="margin-left: 210px;width:480px;background-color: #EBF0F5">
                    <button type="submit" class="btn btn-info" style="margin-right: 20px">提交</button>
                    <button type="button" class="btn btn-danger" onclick="history.go(-1)">返回</button>
                </div>
            </form>
```

### 6、spring-boot+mybatis动态sql

在讲动态sql前 对mybatis常用的查询注解做一个简单的回顾

实体类

```java

public class User {
 
    private Integer id;
 
    private String name;
 
    private Integer age;
 
    /**此处省略了get/set**/
 
}

```

增加

```java
 //通过Options获取主键
    @Insert("INSERT INTO user(name,age) VALUES (#{name}, #{age}) ")
    @Options(useGeneratedKeys= true, keyProperty="id")
    int insert(User user);
```



删除

```java
 @Delete("DELETE FROM user WHERE id　= #{id}")
    int delete(@Param("id") Integer id);
```



修改

```java
 @Update("UPDATE user SET name = #{name}, age = #{age} WHERE id = #{id}")
    int update(User user);
```

查询

```java
 @Select("SELECT id, name, age FROM user WHERE id = #{id}")
    User findById(Integer id);
```

铺垫了这么多 下面开始主题——动态sql 
有时候我们需要根据输入 的条件 动态地构建 SQL语句。 
MyBatis 为我们提供了各种注解 如：

```java
@InsertProvider,@UpdateProvider,@DeleteProvider,@SelectProvider
```

这里我们以@SelectProvider注解为例（type 为方法所在的类 ，method 为方法名称）

```java
 // 动态生成sql 
    @SelectProvider(type = UserMapperProvider.class, method = "findByName")
    List<User> findByName(String name);
```

```java
// 动态生成sql
    public String findByName(String name) {
        String sql = "SELECT * FROM user";
        if (StringUtils.isEmpty(name)) {
            return sql;
        }
        sql += " WHERE name LIKE '%" + name + "%'";
        return sql;
    }

```

上面的findByName(String name)方法 在条件少的时侯好像读起来还很容易 但是随着条件的增多 各种关系就变的比较难以理清 如果能有一种类似于 hibernate中hql的语法就好了 mybatis 为我们提供了 new SQL()
###### 现在我们可以将findByName(String name)进行重构一下
```java
 // 使用工具类来准备相同的 SQL 语句
    public String findByName(String name) {
 
        return new SQL() {
            {
                SELECT("id, name, age");
                FROM("user");
                WHERE("name LIKE '%" + name + "%'");
            }
        }.toString();
    }
```

关系变得明朗了 这里对new SQL（）使用就不再去探究了 更多的方法可以去阅读源码或查找对应的文档

这是对于单个参数 对于 多个参数我们该怎么做呢 
###### 如果映射器 Mapper接口 有多个输入参数，我们可以使用 参数类型为，util.Map的方法作为 SQL provider方 法。然后 映射器 Mapper接口 方法 所有 的输入参数将会 被放到 map中，以 param1,param2等等作 为 key，将输入参数按 序作为 value。你也可以 使用 0，1，2等作为 key值来 取的输入参数。——引自 java Persistence with MyBatis3

```java
 //多参使用map
    @SelectProvider(type = UserMapperProvider.class, method = "findByNameAndAge")
    List<User> findByNameAndAge(String name, Integer age);
```

```java
public String findByNameAndAge(Map<String, Object> map) {
 
        String name = (String) map.get("param1");
        Integer age = (Integer) map.get("param2");
 
        return new SQL() {
            {
                SELECT("id, name, age");
                FROM("user");
                WHERE("name LIKE '%" + name + "%'");
                AND();
                WHERE("age = " + age);
            }
        }.toString();
 
    }

```

Mapper

```java
@SelectProvider(type = UserMapperProvider.class, method = "findByUsernameAndEmailAndPhone")
```

新创建的类UserMapperProvider.java

```java
package com.liujiang.shop.dao;


import com.liujiang.shop.pojo.User;
import org.apache.ibatis.jdbc.SQL;

import java.util.Map;

public class UserMapperProvider {
    public String findByUsernameAndEmailAndPhone(User user) {

//         String username = (String) map.get("param1");
//        String email = (String) map.get("param2");
//        String phone = (String) map.get("param3");
//        System.out.println("sql-username===" + user.getUsername() + "--email-" + user.getEmail() + "--phone--" + user.getPhone());
        System.out.println("sql--复合查询"+user.toString());
        return new SQL() {
            {
                SELECT("username, email, phone");
                FROM("user");
                WHERE("1=1");
                if(user.getUsername()!=null&&user.getUsername()!=""){
                    AND();
                    WHERE("username like CONCAT('%',#{username},'%')");
                }
                if(user.getEmail()!=null&&user.getEmail()!=""){
                    
                    AND();
                    WHERE("email like CONCAT('%',#{email},'%')");
                }
                if(user.getPhone()!=null&&user.getPhone()!=""){
                    
                    AND();
                    WHERE("phone like CONCAT('%',#{phone},'%')");
                }

            }
        }.toString();
//        return new SQL() {{
//            SELECT("*");
//            FROM("user");
//            if (user.getUsername() != null) {
//                System.out.println("----#{username}---"+user.toString());
//                WHERE("username like CONCAT('%',#{username},'%')");
//            }
//            if (user.getEmail() != null) {
//                System.out.println("----#{email}---"+user.toString());
//                WHERE("email=#{email}");
//            }
//            if (user.getPhone() != null) {
//                System.out.println("----#{phone}---"+user.toString());
//                WHERE("phone=#{phone}");
//            }
//        }}.toString();

    }
}
//注意：这里提交表单的时候，如果没有查询条件，会把对象里的null变成“”
```

### 7、复合查询样式

```html
 <%--复合查询--%>
                            <div class="row" style="margin-top: 30px">
                                <form action="/user/search" class="form-horizontal" method="post">
                                    <div class="row">
                                        <div class="col-xs-12">
                                            <div class="col-xs-3">
                                                <div class="form-group">
                                                    <label for="username" class="col-sm-3 control-label">姓名</label>
                                                    <div class="col-sm-9" >
                                                        <input type="text" name="username" id="username" class="form-control"
                                                               placeholder="姓名" >
                                                    </div>
                                                </div>
                                            </div>
                                            <div class="col-xs-3">
                                                <div class="form-group">
                                                    <label for="email" class="col-sm-3 control-label">邮箱</label>
                                                    <div class="col-sm-9" >
                                                        <input type="text" name="email" id="email" class="form-control"
                                                               placeholder="邮箱">
                                                    </div>
                                                </div>
                                            </div>
                                            <div class="col-xs-3">
                                                <div class="form-group">
                                                    <label for="phone" class="col-sm-3 control-label">手机</label>
                                                    <div class="col-sm-9" >
                                                        <input type="text" name="phone" id="phone" class="form-control"
                                                               placeholder="手机号">
                                                    </div>
                                                </div>
                                            </div>
                                        </div>
                                    </div>

                                    <div class="row">
                                        <div class="col-xs-12">
                                            <button type="submit" class="btn btn-info pull-right" style="margin-right: 20px">搜索</button>
                                        </div>
                                    </div>
                                </form>
                            </div>


                        </div>
```

### 8、批量删除-jquery-icheck的插件



引入

```js
<%--icheck--%>
<script src="/assets/plugins/iCheck/icheck.min.js"></script>

<%--icheck--%>
<link rel="stylesheet" href="/assets/plugins/iCheck/all.css">
```

激活：

- css部分

```html
<input type="checkbox" class="minimal" />
```

- js部分

  ```js
  //iCheck for checkbox and radio inputs
      $('input[type="checkbox"].minimal, input[type="radio"].minimal').iCheck({
        checkboxClass: 'icheckbox_minimal-blue',
        radioClass   : 'iradio_minimal-blue'
      })
  ```

```html
var app = function () {
//    私有方法
    var checkbox = function () {
        $('input[type="checkbox"].minimal, input[type="radio"].minimal').iCheck({
            checkboxClass: 'icheckbox_minimal-blue',
            radioClass: 'iradio_minimal-blue'
        })
    }

    return {
        inin:function(){
            initCheckbox()
        },

    }
}();

$(function () {
    app.inin();
})
```

- 全选反选

  ```js
  var app = function () {
  //    私有
  
      var icheckmaster;//主要的
      var checkboxs ;//其他的
  
  
  
      var checkbox = function () {
          $('input[type="checkbox"].minimal, input[type="radio"].minimal').iCheck({
              checkboxClass: 'icheckbox_minimal-blue',
              radioClass: 'iradio_minimal-blue'
          })
  
          icheckmaster = $('input[type="checkbox"].minimal.icheckmaster');//主要的
          checkboxs = $('input[type="checkbox"].minimal');//其他的
      }
  
      var checkboxSelect = function () {
          icheckmaster.on("ifClicked",function (e) {
          //    返回true 表示未选中
              if(e.target.checked){
                  checkboxs.iCheck("uncheck");
              }else {
                  checkboxs.iCheck("check");
              }
          })
      }
  
  
      return {
          init:function(){
              checkbox();
              checkboxSelect();
          },
  
      }
  }();
  
  $(function () {
      app.init();
  })
  ```

- 原理，选中之后，要获取id

- 判断有没有选中

  

### 9、高级搜索显示、隐藏

```java
<button class="btn btn-warning" onclick="$('.box-search').css('display')=='none'?$('.box-search').show('fast'):$('.box-search').hide('fast')">
<i class="glyphicon glyphicon-search"></i> 搜索
</button>
```

### 10、自定义标签

- 目录必须在WEB-INF下-tags-sys

```java
<%@ taglib prefix="sys" tagdir="/WEB-INF/tags/sys" %>
```

```java
<%@ tag language="java" pageEncoding="utf-8" %>
<%@attribute name="title" type="java.lang.String" required="false" description="模态框标题" %>
<%@attribute name="msg" type="java.lang.String" required="true" description="模态框消息" %>

<div class="modal fade" id="myModal">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                            <span aria-hidden="true"
                                                  onclick="javascript:document.getElementById('playmp3').pause()">&times;</span>
                </button>
                <h4 class="modal-title">${title==null?"温馨提示":title}</h4>
            </div>
            <div class="modal-body">
                <p>${msg}&hellip;</p>
            </div>
            <div class="modal-footer">
                <button type="button"
                        onclick="javascript:document.getElementById('playmp3').pause()"
                        class="btn btn-default pull-left" data-dismiss="modal">关闭
                </button>
                <button type="button" onclick="location.reload()" class="btn btn-primary">继续
                </button>
            </div>
        </div>
        <!-- /.modal-content -->
    </div>
    <!-- /.modal-dialog -->
</div>
```

```java
<%--批量删除弹框--%>
<sys:modal msg="你没有选中要删除的数据"></sys:modal>
```

### 11、@ResponseBody

返回数据，在直接解析成json

#### 1、json依赖

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.9.8</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.8</version>
</dependency>
```

### 12、批量删除代码

```java
//批量删除
    public String deleteBatch(Map<String, Object> map) {
        String[] array = (String[]) map.get("array");
        StringBuilder sb = new StringBuilder();
        sb.append("DELETE FROM user WHERE uid IN (");
        for (int i = 0; i < array.length; i++) {
            sb.append("'").append(array[i]).append("'");
            if (i < array.length - 1)
                sb.append(",");
        }
        sb.append(")");
        return sb.toString();

    }
```

### 13、分页DataTables

是一款 jQuery 表格插件。它是一个高度灵活的工具，可以将任何HTML表格添加高级的交互功能。

http://www.datatables.club/

CSS 部分

```text
<!-- DataTables -->
<link rel="stylesheet" href="/static/assets/bower_components/datatables.net-bs/css/dataTables.bootstrap.min.css">
```

JS 部分

```text
<!-- DataTables -->
<script src="/static/assets/bower_components/datatables.net/js/jquery.dataTables.min.js"></script>
<script src="/static/assets/bower_components/datatables.net-bs/js/dataTables.bootstrap.min.js"></script>
```

使用，启用 0 配置模式

```text
$('#dataTable').DataTable();
```

```html
<table id="table_id" class="display">
    <thead>
        <tr>
            <th>Column 1</th>
            <th>Column 2</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Row 1 Data 1</td>
            <td>Row 1 Data 2</td>
        </tr>
        <tr>
            <td>Row 2 Data 1</td>
            <td>Row 2 Data 2</td>
        </tr>
    </tbody>
</table>
```

### 14、DT自动请求的参数(Sent parameters)

当开启了 **服务器模式**时，DataTables 会发送如下参数到服务器

ps:需要说明的是

- 如果你是 Java 开发者，那么使用struts2的需要注意，会有错误抛出，因为处理不了类似 `columns[i][search][regex]`的变量
- 如果是你 .net 开发者，那么可能会遇到 maxQueryStringLength 的错误
- 如果是你 php 开发者，那么恭喜你，php天生支持以上参数的解析，自动转为数组，好不公平啊



| 名称                        | 类型                                                        | 描述                                                         |
| --------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------ |
| `draw`                      | [`integerJS`](http://datatables.net/reference/type/integer) | 绘制计数器。这个是用来确保Ajax从服务器返回的是对应的（Ajax是异步的，因此返回的顺序是不确定的）。 要求在服务器接收到此参数后再返回（具体看 [下面](http://www.datatables.club/manual/server-side.html#returndata)） |
| `start`                     | [`integerJS`](http://datatables.net/reference/type/integer) | 第一条数据的起始位置，比如0代表第一条数据                    |
| `length`                    | [`integerJS`](http://datatables.net/reference/type/integer) | 告诉服务器每页显示的条数，这个数字会等于返回的 `data`集合的记录数，可能会大于因为服务器可能没有那么多数据。这个也可能是-1，代表需要返回全部数据(尽管这个和服务器处理的理念有点违背) |
| `search[value]`             | [`stringJS`](http://datatables.net/reference/type/string)   | 全局的搜索条件，条件会应用到每一列（ `searchable`需要设置为 `true` ） |
| `search[regex]`             | [`booleanJS`](http://datatables.net/reference/type/boolean) | 如果为 `true`代表全局搜索的值是作为正则表达式处理，为 `false`则不是。 注意：通常在服务器模式下对于大数据不执行这样的正则表达式，但这都是自己决定的 |
| `order[i][column]`          | [`integerJS`](http://datatables.net/reference/type/integer) | 告诉后台那些列是需要排序的。 `i`是一个数组索引，对应的是 `columns`配置的数组，从0开始 |
| `order[i][dir]`             | [`stringJS`](http://datatables.net/reference/type/string)   | 告诉后台列排序的方式， `desc` 降序 `asc`升序                 |
| `columns[i][data]`          | [`stringJS`](http://datatables.net/reference/type/string)   | columns 绑定的数据源，由 [`columns.dataOption` ](https://datatables.net/reference/option/columns.data)定义。 |
| `columns[i][name]`          | [`stringJS`](http://datatables.net/reference/type/string)   | columns 的名字，由 [`columns.nameOption` ](http://www.datatables.club/reference/option/columns.name.html)定义。 |
| `columns[i][searchable]`    | [`booleanJS`](http://datatables.net/reference/type/boolean) | 标记列是否能被搜索,为`true`代表可以，否则不可以，这个是由[`columns.searchableOption` ](http://www.datatables.club/reference/option/columns.searchable.html)控制 |
| `columns[i][orderable]`     | [`booleanJS`](http://datatables.net/reference/type/boolean) | 标记列是否能排序,为 `true`代表可以，否则不可以，这个是由[`columns.orderableOption` ](http://www.datatables.club/reference/option/columns.orderable.html)控制 |
| `columns[i][search][value]` | [`stringJS`](http://datatables.net/reference/type/string)   | 标记具体列的搜索条件                                         |
| `columns[i][search][regex]` | [`booleanJS`](http://datatables.net/reference/type/boolean) | 特定列的搜索条件是否视为正则表达式， 如果为 `true`代表搜索的值是作为正则表达式处理，为 `false`则不是。 注意：通常在服务器模式下对于大数据不执行这样的正则表达式，但这都是自己决定的 |

`order[i]`和 `columns[i]`参数发送到服务器是数组信息：

- `order[i]` - 是一个数组，定义了多少列要被排序。比如，数组长度为1，那么就 只有一个列被排序，否则就是多个列被排序
- `columns[i]` - 是一个数组，定了这个表格里所有的列

```java
//    分页
    @Select("select * from user order by uid desc limit #{start},#{length}")
    public List<User> page(@Param("start") int start,@Param("length") int length);

//    总记录数
    @Select("select count(*) from user")
    public Integer count();

```

```java
//    分页
    @ResponseBody
    @RequestMapping("/user/page")
    public Map<String,Object> page(HttpServletRequest request){
        Map<String,Object> map = new HashMap<String, Object>();
        String strDraw = request.getParameter("draw");
        String strStart = request.getParameter("start");
        String strLength = request.getParameter("length");
        int draw = strDraw==null?0:Integer.parseInt(strDraw);
        int start = strDraw==null?0:Integer.parseInt(strStart);
        int length = strDraw==null?0:Integer.parseInt(strLength);
//        封装datatables的结果
        List<User> userList = userService.page(start, length);
        int count = userService.count();
        map.put("draw",draw);
        map.put("recordsTotal",count);
        map.put("recordsFiltered",count);
        map.put("data",userList);
        map.put("error","");
        System.out.println("draw="+draw+"start="+start+"length="+length);
//        Enumeration<String> parameterNames = request.getParameterNames();
//        while(parameterNames.hasMoreElements()){
//            String s = parameterNames.nextElement();
//            System.out.println(String.format("key:%s  value:%s",s,request.getParameter(s)));
//        }
        return map;
    }
```



```js
$(document).ready(function () {
        $('#table_id').DataTable({
            "ordering": false,
            "searching": false,
            "processing": true,
            "serverSide": true,
            "deferRender": true,
            "lengthChange": false,
            "ajax": {
                "url": "/user/page",
                // "data": {
                //     "user_id": 451
                // }
            },
            columns: [
                {
                    data: function (row, type, val, meta) {
                        // console.log(row);
                        // console.log(type);
                        // console.log(val);
                        // console.log(meta);
                        return '<input id="' + row.uid + '" type="checkbox" class="minimal"/>'
                    }
                },
                {data: 'uid'},
                {data: 'username'},
                {data: 'email'},
                {data: 'phone'},
                {data: 'time'},
                {
                    data: function (row, type, val, meta) {
                        return '<span class="label label-success status">Approved</span>'

                    }
                },
                {
                    data: function (row, type, val, meta) {
                        return '<a class="btn bg-purple" id="'+row.uid+'">\n' +
                            '                <i class="glyphicon glyphicon-search"></i> 查看\n' +
                            '                </a>\n' +
                            '                <a class="btn  btn-info" id="'+row.uid+'">\n' +
                            '                <i class="fa fa-edit"></i> 编辑\n' +
                            '                </a>\n' +
                            '                <a class="btn  btn-danger" id="'+row.uid+'">\n' +
                            '                <i class="glyphicon glyphicon-trash"></i> 删除\n' +
                            '                </a>'

                    }
                },

            ],
            language: {
                "sProcessing": "处理中...",
                "sLengthMenu": "显示 _MENU_ 项结果",
                "sZeroRecords": "没有匹配结果",
                "sInfo": "显示第 _START_ 至 _END_ 项结果，共 _TOTAL_ 项",
                "sInfoEmpty": "显示第 0 至 0 项结果，共 0 项",
                "sInfoFiltered": "(由 _MAX_ 项结果过滤)",
                "sInfoPostFix": "",
                "sSearch": "搜索:",
                "sUrl": "",
                "sEmptyTable": "表中数据为空",
                "sLoadingRecords": "载入中...",
                "sInfoThousands": ",",
                "oPaginate": {
                    "sFirst": "首页",
                    "sPrevious": "上页",
                    "sNext": "下页",
                    "sLast": "末页"
                },
                "oAria": {
                    "sSortAscending": ": 以升序排列此列",
                    "sSortDescending": ": 以降序排列此列"
                }
            },
            drawCallback: function( settings ) {
                // alert( '表格重绘了,回调函数' );
                app.init();
            }

        });
    });
```

### 15、复合查询解决datatables数据传输

```java
 public String pageSearch(Map<String, Object> map) {
        int start = (int) map.get("start");
        int length = (int) map.get("length");
        String username = (String) map.get("username");
        String phone = (String) map.get("phone");
        String email = (String) map.get("email");
        return new SQL() {
            {
                SELECT("*");
                FROM("user");
                WHERE("1=1");
                if (username != null && username != "") {
                    AND();
                    WHERE("username like CONCAT('%',#{username},'%')");
                }
                if (email != null && email != "") {
                    AND();
                    WHERE("email like CONCAT('%',#{email},'%')");
                }
                if (phone != null && phone != "") {
                    AND();
                    WHERE("phone like CONCAT('%',#{phone},'%')");
                }

            }
        }.toString();
    }


//    分页
    @SelectProvider(type = UserMapperProvider.class, method = "pageSearch")
    public List<User> page(Map<String,Object> map);
```

```java
@ResponseBody
    @RequestMapping("/user/page")
    public Map<String, Object> page(HttpServletRequest request) {
        Map<String, Object> map = new HashMap<String, Object>();
        String strDraw = request.getParameter("draw");
        String strStart = request.getParameter("start");
        String strLength = request.getParameter("length");
        String username = request.getParameter("username");
        String email = request.getParameter("email");
        String phone = request.getParameter("phone");
        System.out.println("username="+username);
        int draw = strDraw == null ? 0 : Integer.parseInt(strDraw);
        int start = strDraw == null ? 0 : Integer.parseInt(strStart);
        int length = strDraw == null ? 0 : Integer.parseInt(strLength);
        map.put("username",username);
        map.put("phone",phone);
        map.put("email",email);
        map.put("start",start);
        map.put("length",length);
//        封装datatables的结果
        List<User> userList = userService.page(map);
        int count = userService.count();
        map.put("draw", draw);
        map.put("recordsTotal", count);
        map.put("recordsFiltered", count);
        map.put("data", userList);
        map.put("error", "");
        System.out.println("draw=" + draw + "start=" + start + "length=" + length);
//        Enumeration<String> parameterNames = request.getParameterNames();
//        while(parameterNames.hasMoreElements()){
//            String s = parameterNames.nextElement();
//            System.out.println(String.format("key:%s  value:%s",s,request.getParameter(s)));
//        }
        return map;
    }
```

```js

```

