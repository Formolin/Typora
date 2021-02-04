## 概述

Thymeleaf 是一个跟 Velocity、FreeMarker 类似的模板引擎，它可以完全替代 JSP 。相较与其他的模板引擎，它有如下三个极吸引人的特点

- Thymeleaf 在有网络和无网络的环境下皆可运行，即它可以让美工在浏览器查看页面的静态效果，也可以让程序员在服务器查看带数据的动态页面效果。这是由于它支持 html 原型，然后在 html 标签里增加额外的属性来达到模板 + 数据的展示方式。浏览器解释 html 时会忽略未定义的标签属性，所以 thymeleaf 的模板可以静态地运行；当有数据返回到页面时，Thymeleaf 标签会动态地替换掉静态内容，使页面动态显示。
- Thymeleaf 开箱即用的特性。它提供标准和 Spring 标准两种方言，可以直接套用模板实现 JSTL、 OGNL 表达式效果，避免每天套模板、改 JSTL、改标签的困扰。同时开发人员也可以扩展和创建自定义的方言。
- Thymeleaf 提供 Spring 标准方言和一个与 SpringMVC 完美集成的可选模块，可以快速的实现表单绑定、属性编辑器、国际化等功能。


如果希望以 Jar 形式发布模块则尽量不要使用 JSP 相关知识，这是**因为 JSP 在内嵌的 Servlet 容器上运行有一些问题 (内嵌 Tomcat、 Jetty 不支持 Jar 形式运行 JSP**，Undertow 不支持 JSP)。

Spring Boot 中推荐使用 Thymeleaf 作为模板引擎，因为 Thymeleaf 提供了完美的 Spring MVC 支持

Spring Boot 提供了大量模板引擎，包括：

- FreeMarker
- Groovy
- Mustache
- **Thymeleaf**
- Velocity
- **Beetl**

## 引入依赖


主要增加 `spring-boot-starter-thymeleaf` 和 `nekohtml` 这两个依赖

- `spring-boot-starter-thymeleaf`：Thymeleaf 自动配置
- `nekohtml`：允许使用非严格的 HTML 语法

完整的 `pom.xml` 如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.funtl</groupId>
    <artifactId>hello-spring-boot</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>hello-spring-boot</name>
    <description></description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
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

        <dependency>
            <groupId>net.sourceforge.nekohtml</groupId>
            <artifactId>nekohtml</artifactId>
            <version>1.9.22</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.hello.spring.boot.HelloSpringBootApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
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

## 创建测试用 JavaBean

创建一个测试效果的 JavaBean，简单封装一下即可

```java
package com.funtl.hello.spring.boot.entity;

import java.io.Serializable;

public class PersonBean implements Serializable {

    private String name;
    private Integer age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```

## 创建测试用 Controller

创建一个 Controller，造一些测试数据并设置跳转

```java
package com.funtl.hello.spring.boot.controller;

import com.funtl.hello.spring.boot.entity.PersonBean;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import java.util.ArrayList;
import java.util.List;

@Controller
@RequestMapping(value = "thymeleaf")
public class IndexController {

    @RequestMapping(value = "index", method = RequestMethod.GET)
    public String index(Model model) {
        PersonBean person = new PersonBean();
        person.setName("张三");
        person.setAge(22);

        List<PersonBean> people = new ArrayList<>();
        PersonBean p1 = new PersonBean();
        p1.setName("李四");
        p1.setAge(23);
        people.add(p1);

        PersonBean p2 = new PersonBean();
        p2.setName("王五");
        p2.setAge(24);
        people.add(p2);

        PersonBean p3 = new PersonBean();
        p3.setName("赵六");
        p3.setAge(25);
        people.add(p3);

        model.addAttribute("person", person);
        model.addAttribute("people", people);

        return "index";
    }
}
```

## 创建测试页面

在 `templates` 目录下创建 `index.html` 文件，代码如下：

```html
<!DOCTYPE html SYSTEM "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-spring4-4.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Hello Thymeleaf</title>
</head>
<body>
    <div>
        <span>访问 Model：</span><span th:text="${person.name}"></span>
    </div>
    <div>
        <span>访问列表</span>
        <table>
            <thead>
                <tr>
                    <th>姓名</th>
                    <th>年龄</th>
                </tr>
            </thead>
            <tbody>
                <tr th:each="human : ${people}">
                    <td th:text="${human.name}"></td>
                    <td th:text="${human.age}"></td>
                </tr>
            </tbody>
        </table>
    </div>
</body>
</html>
```

修改 html 标签用于引入 thymeleaf 引擎，这样才可以在其他标签里使用 `th:*` 语法，声明如下：

```html
<!DOCTYPE html SYSTEM "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-spring4-4.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
```

## 测试访问

启动成功后，访问：http://localhost:8080/thymeleaf/index 即可看到效果

![img](https://ws2.sinaimg.cn/large/006tNc79ly1fzl28d0kwxj30gt08pq2v.jpg)

# Thymeleaf 常用语法

## 引入 Thymeleaf

修改 html 标签用于引入 thymeleaf 引擎，这样才可以在其他标签里使用 `th:*` 语法，这是下面语法的前提。

```text
<!DOCTYPE html SYSTEM "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-spring4-4.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
```

## 获取变量值

```text
<p th:text="'Hello！, ' + ${name} + '!'" >name</p>
```

可以看出获取变量值用 `$` 符号,对于javaBean的话使用 `变量名.属性名` 方式获取,这点和 `EL` 表达式一样.

另外 `$` 表达式只能写在th标签内部,不然不会生效,上面例子就是使用 `th:text` 标签的值替换 `p` 标签里面的值,至于 `p` 里面的原有的值只是为了给前端开发时做展示用的.这样的话很好的做到了前后端分离.

## 引入 URL

Thymeleaf 对于 URL 的处理是通过语法 `@{…}` 来处理的

```text
<a th:href="@{http://www.baidu.com}">绝对路径</a>
<a th:href="@{/}">相对路径</a>
<a th:href="@{css/bootstrap.min.css}">Content路径,默认访问static下的css文件夹</a>
```

类似的标签有:`th:href` 和 `th:src`

## 字符串替换

很多时候可能我们只需要对一大段文字中的某一处地方进行替换，可以通过字符串拼接操作完成：

```text
<span th:text="'Welcome to our application, ' + ${user.name} + '!'">
```

一种更简洁的方式是：

```text
<span th:text="|Welcome to our application, ${user.name}!|">
```

当然这种形式限制比较多，|…|中只能包含变量表达式${…}，不能包含其他常量、条件表达式等。

## 运算符

在表达式中可以使用各类算术运算符，例如+, -, *, /, %

```text
th:with="isEven=(${prodStat.count} % 2 == 0)"
```

逻辑运算符>, <, <=,>=，==,!=都可以使用，唯一需要注意的是使用<,>时需要用它的HTML转义符：

```text
th:if="${prodStat.count} &gt; 1"
th:text="'Execution mode is ' + ( (${execMode} == 'dev')? 'Development' : 'Production')"
```

## 条件

### if/unless

Thymeleaf 中使用 `th:if` 和 `th:unless` 属性进行条件判断，下面的例子中，标签只有在 `th:if` 中条件成立时才显示：

```text
<a th:href="@{/login}" th:unless=${session.user != null}>Login</a>
```

`th:unless` 于 `th:if` 恰好相反，只有表达式中的条件不成立，才会显示其内容。

### switch

Thymeleaf 同样支持多路选择 Switch 结构：

```text
<div th:switch="${user.role}">
  <p th:case="'admin'">User is an administrator</p>
  <p th:case="#{roles.manager}">User is a manager</p>
</div>
```

默认属性 default 可以用 * 表示：

```text
<div th:switch="${user.role}">
  <p th:case="'admin'">User is an administrator</p>
  <p th:case="#{roles.manager}">User is a manager</p>
  <p th:case="*">User is some other thing</p>
</div>
```

## 循环

渲染列表数据是一种非常常见的场景，例如现在有 n 条记录需要渲染成一个表格，该数据集合必须是可以遍历的，使用 `th:each` 标签：

```text
<body>
  <h1>Product list</h1>

  <table>
    <tr>
      <th>NAME</th>
      <th>PRICE</th>
      <th>IN STOCK</th>
    </tr>
    <tr th:each="prod : ${prods}">
      <td th:text="${prod.name}">Onions</td>
      <td th:text="${prod.price}">2.41</td>
      <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
    </tr>
  </table>

  <p>
    <a href="../home.html" th:href="@{/}">Return to home</a>
  </p>
</body>
```

可以看到，需要在被循环渲染的元素（这里是）中加入 `th:each` 标签，其中 `th:each="prod : ${prods}"` 意味着对集合变量 `prods` 进行遍历，循环变量是 `prod` 在循环体中可以通过表达式访问。

# Thymeleaf 参考手册

## 声明

修改 html 标签用于引入 thymeleaf 引擎，这样才可以在其他标签里使用 `th:*` 语法。

```text
<!DOCTYPE html SYSTEM "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-spring4-4.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
```

## 使用文本

| 语法                     | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| `{home.welcome}`         | 使用国际化文本,国际化传参直接追加 (value…)                   |
| `${user.name}`           | 使用会话属性                                                 |
| `@{}` 表达式中使用超链接 | `<link rel="stylesheet" type="text/css" media="all"href="../../css/gtvg.css" th:href="@{/css/gtvg.css}" />` |
| -                        | -                                                            |
| `${}`                    | 表达式中基本对象                                             |
| param                    | 获取请求参数，比如 `${param.name}`, `http://localhost:8080?name=jeff` |
| session                  | 获取 session 的属性                                          |
| application              | 获取 application 的属性                                      |
| execInfo                 | 有两个属性 templateName 和 now (是 java 的 Calendar 对象)    |
| ctx                      |                                                              |
| vars                     |                                                              |
| locale                   |                                                              |
| httpServletRequest       |                                                              |
| httpSession              |                                                              |
| -                        | -                                                            |
| th 扩展标签              |                                                              |
| `th:text`                | 普通字符串                                                   |
| `th:utext`               | 转义文本                                                     |
| `th:href`                | 链接                                                         |
| `th:attr` 设置元素属性   | `<img src="../../images/gtvglogo.png" th:attr="src=@{/images/gtvglogo.png},title=#{logo},alt=#{logo}" />` |
| `th:with`                | 定义常量                                                     |
| `th:attrappend`          | 追加属性                                                     |
| `th:classappend`         | 追加类样式                                                   |
| `th:styleappend`         | 追加样式                                                     |

## 其他标签

| 语法                 | 说明 |
| -------------------- | ---- |
| `th:abbr`            |      |
| `th:accept`          |      |
| `th:accept-charset`  |      |
| `th:accesskey`       |      |
| `th:action`          |      |
| `th:align`           |      |
| `th:alt`             |      |
| `th:archive`         |      |
| `th:audio`           |      |
| `th:autocomplete`    |      |
| `th:axis`            |      |
| `th:background`      |      |
| `th:bgcolor`         |      |
| `th:border`          |      |
| `th:cellpadding`     |      |
| `th:cellspacing`     |      |
| `th:challenge`       |      |
| `th:charset`         |      |
| `th:cite`            |      |
| `th:class`           |      |
| `th:classid`         |      |
| `th:codebase`        |      |
| `th:codetype`        |      |
| `th:cols`            |      |
| `th:colspan`         |      |
| `th:compact`         |      |
| `th:content`         |      |
| `th:contenteditable` |      |
| `th:contextmenu`     |      |
| `th:data`            |      |
| `th:datetime`        |      |
| `th:dir`             |      |
| `th:draggable`       |      |
| `th:dropzone`        |      |
| `th:enctype`         |      |
| `th:for`             |      |
| `th:form`            |      |
| `th:formaction`      |      |
| `th:formenctype`     |      |
| `th:formmethod`      |      |
| `th:formtarget`      |      |
| `th:frame`           |      |
| `th:frameborder`     |      |
| `th:headers`         |      |
| `th:height`          |      |
| `th:high`            |      |
| `th:href`            |      |
| `th:hreflang`        |      |
| `th:hspace`          |      |
| `th:http-equiv`      |      |
| `th:icon`            |      |
| `th:id`              |      |
| `th:keytype`         |      |
| `th:kind`            |      |
| `th:label`           |      |
| `th:lang`            |      |
| `th:list`            |      |
| `th:longdesc`        |      |
| `th:low`             |      |
| `th:manifest`        |      |
| `th:marginheight`    |      |
| `th:marginwidth`     |      |
| `th:max`             |      |
| `th:maxlength`       |      |
| `th:media`           |      |
| `th:method`          |      |
| `th:min`             |      |
| `th:name`            |      |
| `th:optimum`         |      |
| `th:pattern`         |      |
| `th:placeholder`     |      |
| `th:poster`          |      |
| `th:preload`         |      |
| `th:radiogroup`      |      |
| `th:rel`             |      |
| `th:rev`             |      |
| `th:rows`            |      |
| `th:rowspan`         |      |
| `th:rules`           |      |
| `th:sandbox`         |      |
| `th:scheme`          |      |
| `th:scope`           |      |
| `th:scrolling`       |      |
| `th:size`            |      |
| `th:sizes`           |      |
| `th:span`            |      |
| `th:spellcheck`      |      |
| `th:src`             |      |
| `th:srclang`         |      |
| `th:standby`         |      |
| `th:start`           |      |
| `th:step`            |      |
| `th:style`           |      |
| `th:summary`         |      |
| `th:tabindex`        |      |
| `th:target`          |      |
| `th:title`           |      |
| `th:type`            |      |
| `th:usemap`          |      |
| `th:value`           |      |
| `th:valuetype`       |      |
| `th:vspace`          |      |
| `th:width`           |      |
| `th:wrap`            |      |
| `th:xmlbase`         |      |
| `th:xmllang`         |      |
| `th:xmlspace`        |      |
| `th:alt-title`       |      |
| `th:lang-xmllang`    |      |

## 循环

```text
<tr th:each="prod : ${prods}">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
</tr>

<table> 
    <tr>
        <th>NAME</th>
        <th>PRICE</th>
        <th>IN STOCK</th>
    </tr>
    <tr th:each="prod,iterStat : ${prods}" th:class="${iterStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
  </tr>
</table>
```

迭代器的状态：

- index: 当前的索引，从0开始
- count: 当前的索引，从1开始
- size：总数
- current:
- even/odd:
- first
- last

## 判断

### if

```text
<a href="comments.html" th:href="@{/product/comments(prodId=${prod.id})}" th:if="${not #lists.isEmpty(prod.comments)}">view</a>
```

### unless

```text
<a href="comments.html" th:href="@{/comments(prodId=${prod.id})}" th:unless="${#lists.isEmpty(prod.comments)}">view</a>
```

### switch

```text
<div th:switch="${user.role}">
    <p th:case="'admin'">User is an administrator</p> <p th:case="#{roles.manager}">User is a manager</p>
</div>

<div th:switch="${user.role}">
    <p th:case="'admin'">User is an administrator</p> <p th:case="#{roles.manager}">User is a manager</p> <p th:case="*">User is some other thing</p>
</div>
```

## th:block

```text
<table>
    <th:block th:each="user : ${users}">
    <tr>
        <td th:text="${user.login}">...</td> <td th:text="${user.name}">...</td>
    </tr>
    <tr>
        <td colspan="2" th:text="${user.address}">...</td> 
    </tr>
    </th:block>
</table>
```

推荐下面写法（编译前看不见）

```text
<table>
    <tr>
        <td th:text="${user.login}">...</td>
        <td th:text="${user.name}">...</td> </tr>
        <tr>
        <td colspan="2" th:text="${user.address}">...</td>
    </tr>
    <!--/*/ </th:block> /*/--> 
</table>
```

## th:inline

### `th:inline` 用法

th:inline 可以等于 text , javascript(dart) , none

### `text: [[…]]`

```text
<p th:inline="text">Hello, [[#{test}]]</p>
```

### `javascript: /[[…]]/`

```text
<script th:inline="javascript">
    var username = /*[[
        #{test}
    ]]*/;
    var name = /*[[
        ${param.name[0]}+${execInfo.templateName}+'-'+${#dates.createNow()}+'-'+${#locale}
    ]]*/;
</script>
```

```text
<script th:inline="javascript">

/*<![CDATA[*/

    var username = [[#{test}]];

    var name = [[${param.name[0]}+${execInfo.templateName}+'-'+${#dates.createNow()}+'-'+${#locale}]];

/*]]>*/

</script>
```

### `adding code: /* [+…+]*/`

```text
var x = 23;
/*[+
var msg = 'Hello, ' + [[${session.user.name}]]; +]*/
var f = function() {
...
```

### `removind code: /[- / and /* -]*/`

```text
var x = 23;
/*[- */
var msg = 'This is a non-working template'; /* -]*/
var f = function() {
...
```

# Thymeleaf 表达式语法

## Message 表达式

```
#{...}
<p th:utext="#{home.welcome(${session.user.name})}"> Welcome to our grocery store, Sebastian Pepper!</p>
<p th:utext="#{${welcomeMsgKey}(${session.user.name})}"> Welcome to our grocery store, Sebastian Pepper!</p>
```

## 变量表达式

```
${}
ongl 标准语法，方法也可以被调用
```

## 选择变量表达式

```
*{}
<div th:object="${session.user}">
    <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
    <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p> 
    <p>Nationality: <span th:text={nationality}">Saturn</span>.</p>
</div> 

等价于

<div>
    <p>Name: <span th:text="${session.user.firstName}">Sebastian</span>.</p> 
    <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p> 
    <p>Nationality: <span th:text="${session.user.nationality}">Saturn</span>.</p>
</div>

当然了，这两者可以混合使用
还有一种方式

<div>
    <p>Name: <span th:text="*{session.user.name}">Sebastian</span>.</p> 
    <p>Surname: <span th:text="*{session.user.surname}">Pepper</span>.</p> 
    <p>Nationality: <span th:text="*{session.user.nationality}">Saturn</span>.</p>
</div>  
```

## 链接 URL 表达式

```
@{}
<!-- Will produce 'http://localhost:8080/gtvg/order/details?orderId=3' (plus rewriting) --> <a href="details.html"

th:href="@{http://localhost:8080/gtvg/order/details(orderId=${o.id})}">view</a> <!-- Will produce '/gtvg/order/details?orderId=3' (plus rewriting) -->

<a href="details.html" th:href="@{/order/details(orderId=${o.id})}">view</a>

<!-- Will produce '/gtvg/order/3/details' (plus rewriting) -->

<a href="details.html" th:href="@{/order/{orderId}/details(orderId=${o.id})}">view</a>
```

## 变量

| 分类     | 实例                            |
| -------- | ------------------------------- |
| 文本     | `one text`，`Another one!`，... |
| 数字     | 0 , 34 , 3.0 , 12.3 ,…          |
| 真假     | true , false                    |
| 文字符号 | one , sometext , main ,…        |

## 算数运算

| 分类          | 实例               |
| ------------- | ------------------ |
| +, -, *, /, % | 二元运算符         |
| -             | 减号（一元运算符） |

## 真假运算

| 分类     | 实例               |
| -------- | ------------------ |
| and , or | 二元运算符         |
| ! , not  | 否定（一元运算符） |

## 比较运算

| 分类                          | 实例 |
| ----------------------------- | ---- |
| >, <, >=, <= (gt, lt, ge, le) | 比较 |
| == , != ( eq , ne )           | 平等 |

## 条件运算

| 分类         | 实例                      |
| ------------ | ------------------------- |
| if-then      | (if) ? (then)             |
| if-then-else | (if) ? (then) : (else)    |
| Default      | (value) ?: (defaultvalue) |

# Thymeleaf 内置对象

## `#dates`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Dates
* ======================================================================
*/
/*
* Format date with the standard locale format
* Also works with arrays, lists or sets
*/
${#dates.format(date)}
${#dates.arrayFormat(datesArray)}
${#dates.listFormat(datesList)}
${#dates.setFormat(datesSet)}
/*
* Format date with the ISO8601 format
* Also works with arrays, lists or sets
*/
${#dates.formatISO(date)}
${#dates.arrayFormatISO(datesArray)}
${#dates.listFormatISO(datesList)}
${#dates.setFormatISO(datesSet)}
/*
* Format date with the specified pattern
* Also works with arrays, lists or sets
*/
${#dates.format(date, 'dd/MMM/yyyy HH:mm')}
${#dates.arrayFormat(datesArray, 'dd/MMM/yyyy HH:mm')}
${#dates.listFormat(datesList, 'dd/MMM/yyyy HH:mm')}
${#dates.setFormat(datesSet, 'dd/MMM/yyyy HH:mm')}
/*
* Obtain date properties
* Also works with arrays, lists or sets
*/
${#dates.day(date)} // also arrayDay(...), listDay(...), etc.
${#dates.month(date)} // also arrayMonth(...), listMonth(...), etc.
${#dates.monthName(date)} // also arrayMonthName(...), listMonthName(...), etc.
${#dates.monthNameShort(date)} // also arrayMonthNameShort(...), listMonthNameShort(...), etc.
${#dates.year(date)} // also arrayYear(...), listYear(...), etc.
${#dates.dayOfWeek(date)} // also arrayDayOfWeek(...), listDayOfWeek(...), etc.
${#dates.dayOfWeekName(date)} // also arrayDayOfWeekName(...), listDayOfWeekName(...), etc.
${#dates.dayOfWeekNameShort(date)} // also arrayDayOfWeekNameShort(...), listDayOfWeekNameShort(...), etc.
${#dates.hour(date)} // also arrayHour(...), listHour(...), etc.
${#dates.minute(date)} // also arrayMinute(...), listMinute(...), etc.
${#dates.second(date)} // also arraySecond(...), listSecond(...), etc.
${#dates.millisecond(date)} // also arrayMillisecond(...), listMillisecond(...), etc.
/*
* Create date (java.util.Date) objects from its components
*/
${#dates.create(year,month,day)}
${#dates.create(year,month,day,hour,minute)}
${#dates.create(year,month,day,hour,minute,second)}
${#dates.create(year,month,day,hour,minute,second)}
${#dates.create(year,month,day,hour,minute,second,millisecond)}
/*
* Create a date (java.util.Date) object for the current date and time
*/
${#dates.createNow()}
/*
* Create a date (java.util.Date) object for the current date (time set to 00:00)
*/
${#dates.createToday()}
```

## `#Calendars`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Calendars
* ======================================================================
*/
/*
* Format calendar with the standard locale format
* Also works with arrays, lists or sets
*/
${#calendars.format(cal)}
${#calendars.arrayFormat(calArray)}
${#calendars.listFormat(calList)}
${#calendars.setFormat(calSet)}
/*
* Format calendar with the ISO8601 format
* Also works with arrays, lists or sets
*/
${#calendars.formatISO(cal)}
${#calendars.arrayFormatISO(calArray)}
${#calendars.listFormatISO(calList)}
${#calendars.setFormatISO(calSet)}
/*
* Format calendar with the specified pattern
* Also works with arrays, lists or sets
*/
${#calendars.format(cal, 'dd/MMM/yyyy HH:mm')}
${#calendars.arrayFormat(calArray, 'dd/MMM/yyyy HH:mm')}
${#calendars.listFormat(calList, 'dd/MMM/yyyy HH:mm')}
${#calendars.setFormat(calSet, 'dd/MMM/yyyy HH:mm')}
/*
* Obtain calendar properties
* Also works with arrays, lists or sets
*/
${#calendars.day(date)} // also arrayDay(...), listDay(...), etc.
${#calendars.month(date)} // also arrayMonth(...), listMonth(...), etc.
${#calendars.monthName(date)} // also arrayMonthName(...), listMonthName(...), etc.
${#calendars.monthNameShort(date)} // also arrayMonthNameShort(...), listMonthNameShort(...), etc.
${#calendars.year(date)} // also arrayYear(...), listYear(...), etc.
${#calendars.dayOfWeek(date)} // also arrayDayOfWeek(...), listDayOfWeek(...), etc.
${#calendars.dayOfWeekName(date)} // also arrayDayOfWeekName(...), listDayOfWeekName(...), etc.
${#calendars.dayOfWeekNameShort(date)} // also arrayDayOfWeekNameShort(...), listDayOfWeekNameShort(...), etc.
${#calendars.hour(date)} // also arrayHour(...), listHour(...), etc.
${#calendars.hour(date)} // also arrayHour(...), listHour(...), etc.
${#calendars.minute(date)} // also arrayMinute(...), listMinute(...), etc.
${#calendars.second(date)} // also arraySecond(...), listSecond(...), etc.
${#calendars.millisecond(date)} // also arrayMillisecond(...), listMillisecond(...), etc.
/*
* Create calendar (java.util.Calendar) objects from its components
*/
${#calendars.create(year,month,day)}
${#calendars.create(year,month,day,hour,minute)}
${#calendars.create(year,month,day,hour,minute,second)}
${#calendars.create(year,month,day,hour,minute,second,millisecond)}
/*
* Create a calendar (java.util.Calendar) object for the current date and time
*/
${#calendars.createNow()}
/*
* Create a calendar (java.util.Calendar) object for the current date (time set to 00:00)
*/
${#calendars.createToday()}
```

## `#numbers`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Numbers
* ======================================================================
*/
/*
* ==========================
* Formatting integer numbers
* ==========================
*/
/*
* Set minimum integer digits.
* Also works with arrays, lists or sets
*/
${#numbers.formatInteger(num,3)}
${#numbers.arrayFormatInteger(numArray,3)}
${#numbers.listFormatInteger(numList,3)}
${#numbers.setFormatInteger(numSet,3)}
/*
* Set minimum integer digits and thousands separator:
* 'POINT', 'COMMA', 'WHITESPACE', 'NONE' or 'DEFAULT' (by locale).
* Also works with arrays, lists or sets
*/
${#numbers.formatInteger(num,3,'POINT')}
${#numbers.arrayFormatInteger(numArray,3,'POINT')}
${#numbers.listFormatInteger(numList,3,'POINT')}
${#numbers.setFormatInteger(numSet,3,'POINT')}
/*
* ==========================
* Formatting decimal numbers
* ==========================
*/
/*
* Set minimum integer digits and (exact) decimal digits.
* Also works with arrays, lists or sets
*/
${#numbers.formatDecimal(num,3,2)}
${#numbers.arrayFormatDecimal(numArray,3,2)}
${#numbers.listFormatDecimal(numList,3,2)}
${#numbers.setFormatDecimal(numSet,3,2)}
/*
* Set minimum integer digits and (exact) decimal digits, and also decimal separator.
* Also works with arrays, lists or sets
*/
${#numbers.formatDecimal(num,3,2,'COMMA')}
${#numbers.arrayFormatDecimal(numArray,3,2,'COMMA')}
${#numbers.listFormatDecimal(numList,3,2,'COMMA')}
${#numbers.setFormatDecimal(numSet,3,2,'COMMA')}
/*
* Set minimum integer digits and (exact) decimal digits, and also thousands and
* decimal separator.
* Also works with arrays, lists or sets
*/
${#numbers.formatDecimal(num,3,'POINT',2,'COMMA')}
${#numbers.arrayFormatDecimal(numArray,3,'POINT',2,'COMMA')}
${#numbers.listFormatDecimal(numList,3,'POINT',2,'COMMA')}
${#numbers.setFormatDecimal(numSet,3,'POINT',2,'COMMA')}
/*
* ==========================
* Utility methods
* ==========================
*/
/*
* Create a sequence (array) of integer numbers going
* from x to y
*/
${#numbers.sequence(from,to)}
${#numbers.sequence(from,to,step)}
```

## `#strings`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Strings
* ======================================================================
*/
/*
* Null-safe toString()
*/
${#strings.toString(obj)} // also array*, list* and set*
/*
* Check whether a String is empty (or null). Performs a trim() operation before check
* Also works with arrays, lists or sets
* * Also works with arrays, lists or sets
*/
${#strings.isEmpty(name)}
${#strings.arrayIsEmpty(nameArr)}
${#strings.listIsEmpty(nameList)}
${#strings.setIsEmpty(nameSet)}
/*
* Perform an 'isEmpty()' check on a string and return it if false, defaulting to
* another specified string if true.
* Also works with arrays, lists or sets
*/
${#strings.defaultString(text,default)}
${#strings.arrayDefaultString(textArr,default)}
${#strings.listDefaultString(textList,default)}
${#strings.setDefaultString(textSet,default)}
/*
* Check whether a fragment is contained in a String
* Also works with arrays, lists or sets
*/
${#strings.contains(name,'ez')} // also array*, list* and set*
${#strings.containsIgnoreCase(name,'ez')} // also array*, list* and set*
/*
* Check whether a String starts or ends with a fragment
* Also works with arrays, lists or sets
*/
${#strings.startsWith(name,'Don')} // also array*, list* and set*
${#strings.endsWith(name,endingFragment)} // also array*, list* and set*
/*
* Substring-related operations
* Also works with arrays, lists or sets
*/
${#strings.indexOf(name,frag)} // also array*, list* and set*
${#strings.substring(name,3,5)} // also array*, list* and set*
${#strings.substringAfter(name,prefix)} // also array*, list* and set*
${#strings.substringBefore(name,suffix)} // also array*, list* and set*
${#strings.replace(name,'las','ler')} // also array*, list* and set*
/*
* Append and prepend
* Also works with arrays, lists or sets
*/
${#strings.prepend(str,prefix)} // also array*, list* and set*
${#strings.append(str,suffix)} // also array*, list* and set*
/*
* Change case
* Also works with arrays, lists or sets
*/
${#strings.toUpperCase(name)} // also array*, list* and set*
${#strings.toLowerCase(name)} // also array*, list* and set*
/*
* Split and join
*/
${#strings.arrayJoin(namesArray,',')}
${#strings.listJoin(namesList,',')}
${#strings.setJoin(namesSet,',')}
${#strings.arraySplit(namesStr,',')} // returns String[]
${#strings.listSplit(namesStr,',')} // returns List<String>
${#strings.setSplit(namesStr,',')} // returns Set<String>
/*
* Trim
* Also works with arrays, lists or sets
*/
${#strings.trim(str)} // also array*, list* and set*
/*
* Compute length
* Also works with arrays, lists or sets
*/
${#strings.length(str)} // also array*, list* and set*
/*
* Abbreviate text making it have a maximum size of n. If text is bigger, it
* will be clipped and finished in "..."
* Also works with arrays, lists or sets
*/
${#strings.abbreviate(str,10)} // also array*, list* and set*
/*
* Convert the first character to upper-case (and vice-versa)
*/
${#strings.capitalize(str)} // also array*, list* and set*
${#strings.unCapitalize(str)} // also array*, list* and set*
/*
* Convert the first character of every word to upper-case
*/
${#strings.capitalizeWords(str)} // also array*, list* and set*
${#strings.capitalizeWords(str,delimiters)} // also array*, list* and set*
/*
* Escape the string
*/
${#strings.escapeXml(str)} // also array*, list* and set*
${#strings.escapeJava(str)} // also array*, list* and set*
${#strings.escapeJavaScript(str)} // also array*, list* and set*
${#strings.unescapeJava(str)} // also array*, list* and set*
${#strings.unescapeJavaScript(str)} // also array*, list* and set*
/*
* Null-safe comparison and concatenation
*/
${#strings.equals(first, second)}
${#strings.equalsIgnoreCase(first, second)}
${#strings.concat(values...)}
${#strings.concatReplaceNulls(nullValue, values...)}
/*
* Random
*/
${#strings.randomAlphanumeric(count)}
```

## `#objects`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Objects
* ======================================================================
*/
/*
* Return obj if it is not null, and default otherwise
* Also works with arrays, lists or sets
*/
${#objects.nullSafe(obj,default)}
${#objects.arrayNullSafe(objArray,default)}
${#objects.listNullSafe(objList,default)}
${#objects.setNullSafe(objSet,default)}
```

## `#bools`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Bools
* ======================================================================
*/
/*
* Evaluate a condition in the same way that it would be evaluated in a th:if tag
* (see conditional evaluation chapter afterwards).
* Also works with arrays, lists or sets
*/
${#bools.isTrue(obj)}
${#bools.arrayIsTrue(objArray)}
${#bools.listIsTrue(objList)}
${#bools.setIsTrue(objSet)}
/*
* Evaluate with negation
* Also works with arrays, lists or sets
*/
${#bools.isFalse(cond)}
${#bools.arrayIsFalse(condArray)}
${#bools.listIsFalse(condList)}
${#bools.setIsFalse(condSet)}
/*
* Evaluate and apply AND operator
* Receive an array, a list or a set as parameter
*/
${#bools.arrayAnd(condArray)}
${#bools.listAnd(condList)}
${#bools.setAnd(condSet)}
/*
* Evaluate and apply OR operator
* Receive an array, a list or a set as parameter
*/
${#bools.arrayOr(condArray)}
${#bools.listOr(condList)}
${#bools.setOr(condSet)}
```

## `#arrays`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Arrays
* ======================================================================
*/
/*
* Converts to array, trying to infer array component class.
* Note that if resulting array is empty, or if the elements
* of the target object are not all of the same class,
* this method will return Object[].
*/
${#arrays.toArray(object)}
/*
* Convert to arrays of the specified component class.
*/
${#arrays.toStringArray(object)}
${#arrays.toIntegerArray(object)}
${#arrays.toLongArray(object)}
${#arrays.toDoubleArray(object)}
${#arrays.toFloatArray(object)}
${#arrays.toBooleanArray(object)}
/*
* Compute length
*/
${#arrays.length(array)}
/*
* Check whether array is empty
*/
${#arrays.isEmpty(array)}
/*
* Check if element or elements are contained in array
*/
${#arrays.contains(array, element)}
${#arrays.containsAll(array, elements)}
```

## `#lists`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Lists
* ======================================================================
*/
/*
* Converts to list
*/
${#lists.toList(object)}
/*
* Compute size
*/
${#lists.size(list)}
/*
* Check whether list is empty
*/
${#lists.isEmpty(list)}
/*
* Check if element or elements are contained in list
*/
${#lists.contains(list, element)}
${#lists.containsAll(list, elements)}
/*
* Sort a copy of the given list. The members of the list must implement
* comparable or you must define a comparator.
*/
${#lists.sort(list)}
${#lists.sort(list, comparator)}
```

## `#sets`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Sets
* ======================================================================
*/
/*
* Converts to set
*/
${#sets.toSet(object)}
/*
* Compute size
*/
${#sets.size(set)}
/*
* Check whether set is empty
*/
${#sets.isEmpty(set)}
/*
* Check if element or elements are contained in set
*/
${#sets.contains(set, element)}
${#sets.containsAll(set, elements)}
```

## `#maps`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Maps
* ======================================================================
*/
/*
* Compute size
*/
${#maps.size(map)}
/*
* Check whether map is empty
*/
${#maps.isEmpty(map)}
/*
* Check if key/s or value/s are contained in maps
*/
${#maps.containsKey(map, key)}
${#maps.containsAllKeys(map, keys)}
${#maps.containsValue(map, value)}
${#maps.containsAllValues(map, value)}
```

## `#aggregates`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Aggregates
* ======================================================================
*/
/*
* Compute sum. Returns null if array or collection is empty
*/
${#aggregates.sum(array)}
${#aggregates.sum(collection)}
/*
* Compute average. Returns null if array or collection is empty
*/
${#aggregates.avg(array)}
${#aggregates.avg(collection)}
```

## `#messages`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Messages
* ======================================================================
*/
/*
* Obtain externalized messages. Can receive a single key, a key plus arguments,
* or an array/list/set of keys (in which case it will return an array/list/set of
* externalized messages).
* If a message is not found, a default message (like '??msgKey??') is returned.
*/
${#messages.msg('msgKey')}
${#messages.msg('msgKey', param1)}
${#messages.msg('msgKey', param1, param2)}
${#messages.msg('msgKey', param1, param2, param3)}
${#messages.msgWithParams('msgKey', new Object[] {param1, param2, param3, param4})}
${#messages.arrayMsg(messageKeyArray)}
${#messages.listMsg(messageKeyList)}
${#messages.setMsg(messageKeySet)}
/*
* Obtain externalized messages or null. Null is returned instead of a default
* message if a message for the specified key is not found.
*/
${#messages.msgOrNull('msgKey')}
${#messages.msgOrNull('msgKey', param1)}
${#messages.msgOrNull('msgKey', param1, param2)}
${#messages.msgOrNull('msgKey', param1, param2, param3)}
${#messages.msgOrNullWithParams('msgKey', new Object[] {param1, param2, param3, param4})}
${#messages.arrayMsgOrNull(messageKeyArray)}
${#messages.listMsgOrNull(messageKeyList)}
${#messages.setMsgOrNull(messageKeySet)}
```

## `#ids`

```text
/*
* ======================================================================
* See javadoc API for class org.thymeleaf.expression.Ids
* ======================================================================
*/
/*
* Normally used in th:id attributes, for appending a counter to the id attribute value
* so that it remains unique even when involved in an iteration process.
*/
${#ids.seq('someId')}
/*
* Normally used in th:for attributes in <label> tags, so that these labels can refer to Ids
* generated by means if the #ids.seq(...) function.
*
* Depending on whether the <label> goes before or after the element with the #ids.seq(...)
* function, the "next" (label goes before "seq") or the "prev" function (label goes after
* "seq") function should be called.
*/
${#ids.next('someId')}
${#ids.prev('someId')}
```

# Thymeleaf 模板布局

这节主要介绍模板的引入。及如何在不改变前端人员的 HTML 显示结果的情况下设计模板（通过属性配置动态时不显示的部分）。

## [#](http://www.funtl.com/zh/spring-boot-thymeleaf/Thymeleaf-%E6%A8%A1%E6%9D%BF%E5%B8%83%E5%B1%80.html#%E6%A8%A1%E6%9D%BF%E6%A8%A1%E5%9D%97%E5%AF%BC%E5%85%A5)模板模块导入

首先定义一个 `/resources/templates/footer.html` 文件：

```text
<!DOCTYPE html SYSTEM "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-4.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
    <body>
        <div th:fragment="copy">
            &copy; 2018 Copyright by Lusifer.
        </div>
    </body>
</html>
```



上面的代码定义了一个片段称为 `copy`，我们可以很容易地使用 `th:include` 或者 `th:replace` 属性包含在我们的主页上

```text
<body>
...
<div th:include="footer :: copy"></div>
</body>
```



`include` 的表达式想当简洁。这里有三种写法：

- `templatename::domselector` 或者 `templatename::[domselector]` 引入模板页面中的某个模块
- `templatename` 引入模板页面
- `::domselector` 或者 `this::domselector` 引入自身模板的模块

上面所有的 `templatename` 和 `domselector` 的写法都支持表达式写法：

```text
<div th:include="footer :: (${user.isAdmin}? #{footer.admin} : #{footer.normaluser})"></div>
```

## 不使用 `th:fragment` 来引用模块

```text
<div id="copy-section">
&copy; 2018 Copyright by Lusifer.
</div>
```



我们可以用 CSS 的选择器写法来引入

```text
<body>
...
<div th:include="footer :: #copy-section"></div>
</body>
```

## `th:include` 和 `th:replace` 的区别

`th:include` 和 `th:replace` 都可以引入模块，两者的区别在于：

- `th:include`：引入子模块的 children，依然保留父模块的 tag
- `th:replace`：引入子模块的所有，不保留父模块的 tag

### 举个例子

```text
<footer th:fragment="copy">
&copy; 2018 Copyright by Lusifer.
</footer>
```

### 引入界面

```text
<body>
...
<div th:include="footer :: copy"></div>
<div th:replace="footer :: copy"></div>
</body>
```

### 显示结果

```text
<body>
...
<div>
&copy; 2018 Copyright by Lusifer.
</div>
<footer>
&copy; 2018 Copyright by Lusifer.
</footer>
</body>
```