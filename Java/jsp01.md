# jsp01

### 1、jsp概述

- JSP全称是Java Server Pages，它和servle技术一样。**JSP实际上就是Servlet。**
- 最大的特点在于，写jsp就像在写html，但它相比html而言，html只能为用户提供静态数据，而Jsp技术允许在页面中嵌套java代码，为用户提供动态数据。
- html+java片段+js+css+jsp标签
  - html做静态
  - java逻辑代码
  - jsp既有逻辑又有静态页面，那么就是动态网页
  - 原来的servlet的是java代码包含html代码，但是现实的html代码要大于java代码
  - jsp反过来了，html代码包含java代码

### 2、原理

- 创建一个jsp，但是这个jsp文件要保存到webcontent,右键new一个jsp file

- Index.jsp

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <!DOCTYPE html>
  <html>
  <head>
  <meta charset="UTF-8">
  <title>Insert title here</title>
  </head>
  <body>
  <h1>hello jsp</h1>
  </body>
  </html>
  ```

- 访问路径：/001/index.jsp

- 打开后,下面不就是html，那么上面是什么

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  
  ```

- 语言是java，编码集是utf-8

- 接着我在body写两个代码，记住我现在写的只是在jsp里，这是jsp的技术，java代码写在<%%>中，其他的就是html代码

  ```jsp
  <body>
  hello.jsp
  <%
  	Date date = new Date();
  %>
  </body>
  ```

- 报错了吧，正常在java中是不是需要导包，那么jsp想使用java代码，怎么导包？

  ```jsp
  <%@ page import="java.util.*" %>
  <%
  	Date date = new Date();
  	out.println(date);
  %>
  ```

- 那么怎么访问？

  - 启动服务器

  - http://localhost:8080/1901/demo.jsp

  - 原来我们访问的是servlet，现在直接访问的是jsp，并且我更改代码后不用重启，直接访问即可

    ```jsp
    <h1>现在的时间是：</h1>
    <%
    	Date date = new Date();
    	out.println(date);
    %>
    ```

- 那么看看到servlet的东西了吗？我们看下原理![image-20190219091741193](https://ws2.sinaimg.cn/large/006tKfTcly1g0bgviswz9j30aj04x0t8.jpg)

- 是jsp引擎，将jsp翻译成java，java又编译成class

  - 我想访问1.jsp，首先服务器得有这个资源，那么先翻译成java文件，那么运行的是什么？运行的是class文件，由谁翻译，由谁编译，还有我这是class文件吗？不是，那么我们项目是由谁发布的？是tomcat，那么我们就去看下tomcat的目录webapps
  - 没有访问的时候，文件夹是空的，第一次访问的时候，生成上述文件![jsp](https://ws3.sinaimg.cn/large/006tKfTcly1g0bhcmj2obj30jq00omx2.jpg)


### 3、最佳实践

- 我们用jsp做什么？

  - 分层
    - view
    - controller
    - service
    - dao
  - jsp写哪个层比较好？理论上都可以
    - 能不能做controller层，controlle层的作用
    - 最好的是替换原来的view层

  ```jsp
  	<form action="/1901/demo.jsp" method="post">
  		用户名：<input type="text" name="username"/><br/>
  		密码：<input type="password" name="password"/><br/>
  		<input type="submit" value="登录"/><br/>
  	</form>
  ```

  - 我提交到另外一个jsp里

  - 那么另一个jsp干什么了？模拟controller层

    - 获取表单数据
    - 处理业务逻辑
    - 分发转向

  - 那为什么写在jsp了，我是让你看下这是错误的案列！

    ```jsp
    <%
    	Date date = new Date();
    	out.println(date);
    	request.getSession();
    	/* 获取数据 */
    	String name = request.getParameter("username");
    	String password = request.getParameter("password");
    	/* 业务逻辑 */
    	if("tom".equals(name)&&"123".equals(password)){
    		request.getRequestDispatcher("/success.jsp").forward(request, response);
    	}else {
    		response.sendRedirect("/1901/login.jsp");
    	}
    	/* 分发转向*/
    	
    %>
    ```

  - 前端的页面，展示页面，如果后台要进行处理，都可以用jsp

### 4、基本语法

```jsp
<% %>
<%= %>
可能还有别的，但是不关键，就这两个实际开发常用

<%
	int i = 10;
	out.print(i);
%>
看一下java源码


out.write("\n");
      out.write("<!DOCTYPE html PUBLIC \"-//W3C//DTD HTML 4.01 Transitional//EN\" \"http://www.w3.org/TR/html4/loose.dtd\">\n");
      out.write("<html>\n");
      out.write("<head>\n");
      out.write("<meta http-equiv=\"Content-Type\" content=\"text/html; charset=UTF-8\">\n");
      out.write("<title>Insert title here</title>\n");
      out.write("</head>\n");
      out.write("<body>\n");

	int i = 10;
	out.print(i);

      out.write("\n");
      out.write("</body>\n");
      out.write("</html>");

servlet！

那我们看下<%=%>

<%
	int i = 10;
	out.print(i);
%>
<%=i %>

输出两个10

说明跟out.println()是等价的

 out.write('\n');
      out.print(i );
      out.write("\n");
      out.write("\n");

看源码，那么我能不能写分号，能不能再写别的，不能！
他就是输出的i+i可以，java小脚本

如果想了解还有一个<%! %>可以自己看，没讲是真不常用！



	<%
		int a = 10;
		out.print(a);

		for (int i = 0; i < 10; i++) {
	%>
			<h1>我是1.jsp</h1>
	<%
		}
	%>
```

- jsp的注释

  ```jsp
  <!-- 这是html注释 -->
  浏览器打开，右键源代码能看见，所以不要用这种方式，不安全
  <%-- 这是java注释 --%>
  java的注释，直接就不编译了
  ```

  

### 5、3大指令（了解）

- page就是一个指令

  ```jsp
  <%@ page%>
  <%@ include%>
  <%@ tablib%>
  ```

- <%@ page **errorPage**="**errorpage**.**jsp**"%>

  - 页面发生错误跳转到指定页面

    ```jsp
    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@page import="java.util.*" %>
        
    <%@page errorPage="err.jsp" %>
    或者
    <%@page errorPage="/err.jsp" %>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
    <h1>hello jsp aaaa</h1>
    <%
    	int a = 10;
      
      
    	out.println(a/0);
    	
    	Date date = new Date();
    	out.println(date.toLocaleString());
    	out.println(date);
    %>
    
    <%=a %>
    <%=new Date() %>
    
    <!-- <h1>我是html注释</h1> -->
    <h1>我是java注释</h1>
    <%--<h1>我是jsp注释</h1>--%>
    
    </body>
    </html>
    ```

    在处理异常的网页中，应该通过如下语句将该网页生命为异常处理网页
    　　<%@ page isErrorPage="true"%>

  - 在错误页面使用isErrorPage的区别，如果不加，属于转发，状态码还是200，使用isErrorPage状态码为500，并且可以使用exception域对象。

  - 即跳转到自定义页面，还在可以在这个页面上显示错误信息

    ```jsp
    <%@ page language="java" contentType="text/html; charset=UTF-8"
    	pageEncoding="UTF-8" import="java.util.*" isErrorPage="true"%>
    <img src='images/1.jpg'/>	
    <%
    
    	exception.printStackTrace(response.getWriter());
    %>
    
    但是体验不好，用户看不懂错误信息
    ```

  - 在web.xml中配置错误页面

    ```xml
    <error-page>
     <error-code>404</error-code>
     <location>/err404.jsp</location>
    </error-page>
      
    <error-page>
     <error-code>500</error-code>
     <location>/err500.jsp</location>
    </error-page>
    
    <error-page>
     <exception-type>java.lang.RuntimeException</exception-type>
     <location>/err.jsp</location>
    </error-page>
    
    
    ```

    

- 先了解，等下面学jstl时候在说

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  
  page指令，language属性 =后面是属性值
  language 可以选择其他语言的语法，他想使用别人的，但是别人不吊他，所以这个就废了
  
  contentType="text/html; charset=UTF-8"相当于
  response.setContentType("text/html;charset=utf-8");
  也可以干掉，因为pageEncoding就包含了，但是还多了一个功能，多了翻译的时候也采用utf-8
  ```

​	 导包的指令，之前说过，我的用的不熟练，你知道意味着什么？或者说不出123，更多的经验没告诉你们，是因为我也不常用，我就知道有这个东西就行，有这时间不如干点别的，算法，逻辑能力等

- session.setAttribute("p", "pp");我在脚本里写这个session能不能用，能！不能用不久报错了，在一个看源码，是不是编译了，那么看这个指令，我不写默认是true

  ```jsp
  <%@ page session="false" pageEncoding="UTF-8"%>
  相当于把开关关了，就用不了了
  
  <%
  	HttpSession session = request.getSession();
  	session.setAttribute("p", "pp");
  	我可以自己整一个
  %>
  
  关闭session后，我们在看源代码，就没有初始化的session了
  ```

- 然而并没什么卵用，我只是告诉这个指令跟的属性都能干什么，了解下，后面的自己可以看下，我如果都讲完，你们在问我有啥用，我觉得还是没啥用，但你得知道有这么个东西

- 传智播客愿意这么干，所有的都讲，得一天，结果开发的时候就知道了

- <%@ include%>静态包含

  ```jsp
  //j.jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>Insert title here</title>
  </head>
  <body>
  111111111111
      <%@ include file="/2.jsp" %>
  </body>
  </html>
  //2.jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>Insert title here</title>
  </head>
  <body>
  22222222222222
  </body>
  </html>
  ```

  执行，结果显示出来了，但是右键查看源代码，发现他就是包含，并且全部输出了，是不是重复了？

  再看，tomcat路径，他并没有生成2.jsp这个文件，说明在翻译的时候，他就包含了

  - 那么我们怎么改这个代码

    ```jsp
    //1.jsp
    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
    
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
    111111111111
    <%@ include file="/2.jsp" %>
    //2.jsp
        <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
    
    22222222222222
    </body>
    </html>
    ```

    特点：翻译的时候，就把两个页面整个在一起，以后网页的头部和尾部都一样的，那么就可以写一遍，之后用这个包含就完事了

  - 动态包含：tablib先不讲，看6大动作

### 6、6大动作（了解）

- <jsp:include >动态包含

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>Insert title here</title>
  </head>
  <body>
  111111111111
  <jsp:include page="/2.jsp"></jsp:include>
  
  ```

  使用动态包含，结果一样

  看源码：org.apache.jasper.runtime.JspRuntimeLibrary.include(request, response, "/2.jsp", out, false);

  翻译的时候不合并，运行的时候在合并

- <jsp:forward >请求转发

  ```jsp
  <h1>我是1.jsp</h1>
  <jsp:forward page="2.jsp"></jsp:forward>
  ```

  传参

  ```jsp
  <h1>我是2.jsp</h1>
  <%=request.getParameter("name") %>
  
  
  <h1>我是1.jsp</h1>
  <jsp:forward page="2.jsp?name=tom"></jsp:forward>
  ```

- 赋值这些都应该控制层做的事，jsp就是显示层

  都什么意思？开发中一个都用不到

- 以后再jsp页面不要出现java代码，因为前端的人也看不懂，不会有脚本和表达式了

### 7、9大内置对象（掌握）

- request
- response
- session
- application
- pageContext
- page
- out
- exception
- config

### 8、四大域对象：实际开发

PageContext : pageConext 存放的数据在当前页面有效。开发时使用较少。能获取其他域对象 findAttribute();

ServletRequest: request  存放的数据在一次请求（转发）内有效。使用非常多。针对用户的一次请求转发

HttpSession: session 存放的数据在一次会话中有效。使用的比较多。如：存放用户的登录信息，购物车功能。

ServletContext: application 存放的数据在整个应用范围内都有效。因为范围太大，应尽量少用。

 

到此为止，web开发接触到了4个域对象,这4个域对象是学习web的重点，也是笔试经常考察的知识点

l pageContext（称之为page域） 

l request（称之为request域）

l session（称之为session域）

l servletContext（称之为application域）

明确如下问题：

#### 什么是域？

这4个对象的生命周期？

哪种情况下用哪种域对象。

l 1、request：如果客户向服务器发请求，产生的数据，用户看完就没用了，像这样的数据就存在request域,像新闻数据，属于用户看完就没用的

l 2、session：如果客户向服务器发请求，产生的数据，用户用完了等一会儿还有用，像这样的数据就存在session域中，像购物数据，用户需要看到自己购物信息，并且等一会儿，还要用这个购物数据结帐

l 3、servletContext：如果客户向服务器发请求，产生的数据，用户用完了，还要给其它用户用，像这样的数据就存在servletContext域中，像聊天数据

1.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>我是1.jsp</h1>

<%
	pageContext.setAttribute("str", "我是pageContext");
	request.setAttribute("str", "我是request");
	session.setAttribute("str", "我是session");
	application.setAttribute("str", "我是application");
%>
	
	
	
<jsp:forward page="2.jsp"></jsp:forward>
</body>
</html>
```

2.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<h1>我是2.jsp</h1>

<%=pageContext.getAttribute("str") %>
<%=request.getAttribute("str")%>
<%=session.getAttribute("str") %>
<%=application.getAttribute("str") %>
</body>
</html>
```

结果发现pageContext获取不到，因为只能在当前页面获取到

测试在当前页面获取这些数据

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>我是1.jsp</h1>

<%
	pageContext.setAttribute("str", "我是pageContext");
	request.setAttribute("str", "我是request");
	session.setAttribute("str", "我是session");
	application.setAttribute("str", "我是application");
%>
	
<%=pageContext.getAttribute("str") %>
<%=request.getAttribute("str")%>
<%=session.getAttribute("str") %>
<%=application.getAttribute("str") %>
	
<%-- <jsp:forward page="2.jsp"></jsp:forward> --%>
</body>
</html>
```

都能获取到

```jsp

	<%
		pageContext.setAttribute("str1", "我是pageContext", PageContext.PAGE_SCOPE);
		pageContext.setAttribute("str", "我是request", PageContext.REQUEST_SCOPE);
		pageContext.setAttribute("str", "我是session", PageContext.SESSION_SCOPE);
		pageContext.setAttribute("str", "我是application", PageContext.APPLICATION_SCOPE);
	%>

	<%=pageContext.getAttribute("str")%>
	<%=request.getAttribute("str")%>
	<%=session.getAttribute("str")%>
	<%=application.getAttribute("str")%>


	<%=pageContext.findAttribute("str")%>
```





### 三、el表达式



EL表达式：expression language 表达式语言

 要简化jsp中java代码开发。

 它不是一种开发语言，是jsp中**获取数据**的一种规范

- 功能

  - 获取数据

    - 一个jsp页面存储对象后，另一个页面除了用getAttribute还有没有没得方法？
    - el表达式
    - 根据setget方法的名字，去掉setget，首字母小写

  - 案列

    ```jsp
    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
    <%@ page import="com.aishang.pojo.*" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
    <%
    	User user = new User();
    	user.setName("tom");
    	user.setAge(13);
    	request.setAttribute("user", user);
    	request.getRequestDispatcher("/5.jsp").forward(request, response);
    %>
    </body>
    </html>
    
    
    ${user.name}
    
    调用的是getter、setter方法，因为成员变量是私有的，只能访问共有的属性
    ${u.name} == u.getName()方法
    ```

    ![image-20190410223149897](http://ww4.sinaimg.cn/large/006y8mN6ly1g6t6ncg4u0j30ni0c075q.jpg)

  - 案例二

    .属性导航，作用，一个实体类里封装了另一个实体类的情况

    student类包含了city类

    ```java
    package com.aishang.pojo;
    
    public class Student {
    	private String name;
    	private City city;
    	public String getName() {
    		return name;
    	}
    	public void setName(String name) {
    		this.name = name;
    	}
    	public City getCity() {
    		return city;
    	}
    	public void setCity(City city) {
    		this.city = city;
    	}
    	
    }
    
    
    package com.aishang.pojo;
    
    public class City {
    	private String cityName;
    
    	public String getCityName() {
    		return cityName;
    	}
    
    	public void setCityName(String cityName) {
    		this.cityName = cityName;
    	}
    }
    ${u.city.cityName}
    ```

  - []运算符，点能做的，他也能做，它能做的，点不一定能做

    ```java
    <%
    	List<String> list = new ArrayList<String>();
    	list.add("1");
    	list.add("2");
    	list.add("3");
    	request.setAttribute("list", list);
    %>
    ${list[1] }
    ```

    ```java
    <%
    	Map map = new HashMap();
    	map.put("a", "1");
    	map.put("b", "2");
    	map.put("c", "3");
    	request.setAttribute("map", map);
    %>
    ${map['a'] }==${map["a"] }==${map.a }
    ```

    

  - empty看课件

  - el的三元运算符—单选框案列

    ```jsp
    <body>
    <%
    	request.setAttribute("num", 1);
    %>
    	<input type="radio" name="sex" value="0" ${num==0?"checked=checked":"" }/>男	
    	<input type="radio" name="sex" value="1" ${num==1?"checked=checked":"" }/>女
    </body>
    ```

- 隐式对象

  ```jsp
  <%
  	pageContext.setAttribute("p", "page",PageContext.SESSION_SCOPE);
  	request.setAttribute("p", "request");
  	session.setAttribute("p", "session");
  	application.setAttribute("p", "application");
  %>
  
  ${p }==request
  ```

  从指定作用域取

  - ${requestScope.p}
  - ${sessionScope.p}

- 获取当前项目名称

  - 我们项目在完成的时候，要发布上线，运维取处理，但是他觉得你的项目名不规范，要是改了项目名，就报错了

    ```jsp
    <form action="/1901/demo.jsp">
    		<input type="text" name="username"/>用户名
    		<input type="submit" value="提交"/>
    	</form>
    ```

    正常我们在获取项目名称用request.getContextPath()

    el呢？

    ```jsp
    	<form action="${pageContext.request.contextPath }/demo.jsp">
    		<input type="text" name="username"/>用户名
    		<input type="submit" value="提交"/>
    	</form>
    
    //另一个页面
    ${param.username }
    ```

- 案例

  - 获取重定向地址栏参数

    ```jsp
    <%
    	response.sendRedirect("/001/2.jsp?name=tom&password=123");
    %>
    
    
    
    <h1>我是2.jsp</h1>
    ${param.name }
    ${param.password }
    ```

    

  - 获取数组

    ```jsp
      	<h1>我是1.jsp</h1>
      
    <%
    	int arr[] = {1,2,3};
    	request.setAttribute("arr", arr);
    %>
    <jsp:forward page="2.jsp"></jsp:forward>
    
    
    
    
    
    ${arr[0] }
    
    <c:forEach  var="arr" items="${arr }">
    ${arr }
    </c:forEach>
    ```

    

  - 获取对象

    ```jsp
    	<h1>我是1.jsp</h1>
    
    <%
    	Person p = new Person();
    	p.setName("tom");
    	p.setAge(18);
    	Department department = new Department();
    	department.setDepName("市场部");
    	p.setDepartment(department);
    	
    	request.setAttribute("person", p);
    %>
    <jsp:forward page="2.jsp"></jsp:forward>
    ```

  - 获取对象中对象

    ```jsp
    <%
    	Person p = (Person)request.getAttribute("person");
    %>
    <%=p.getName() %>
    <%=p.getAge() %>
    <%=p.getDepartment().getDepName() %>
    
    
    
    ${person }
    ${person.name }
    ${person.age }
    ${person.department.depName }
    ```

    

  - 获取List

    ```jsp
    <%
     List<String> list = new ArrayList<String>();
    list.add("tom");
    list.add("abc");
    
    request.setAttribute("list", list);
    %>
    <jsp:forward page="2.jsp"></jsp:forward>
    
    
    
    
    <h1>我是2.jsp</h1>
    ${list }
    ${list[0] }
    ```

  - 获取list的对象

    ```jsp
    <%
     List<Person> list = new ArrayList<Person>();
    Person person = new Person();
    person.setName("tom");
    Department department =new Department();
    department.setDepName("市场部");
    person.setDepartment(department);
    list.add(person);
    
    request.setAttribute("list", list);
    %>
    <jsp:forward page="2.jsp"></jsp:forward>
    
    
    
    
    <h1>我是2.jsp</h1>
    ${list }
    <br>
    ${list[0].name }
    <br>
    ${list[0].department.depName }
    ```

  - 获取Map

  - 获取map中的对象

  - 获取cookie

  

### 四、jstl

导包，2个依赖

```jsp
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
```

- set
- out
- remove
- if
- choose
- foreach
  - 便利数组、集合、map
  - 集合对象



```java
	<form action='${pageContext.request.contextPath }/2.jsp' method="post">
		<input type="checkbox" name="hobbys" value="篮球">🏀
		<input type="checkbox" name="hobbys" value="足球">⚽️
		<input type="checkbox" name="hobbys" value="我们">🏓
		<input type="checkbox" name="hobbys" value="是">🏸
		<input type='text' name='vv' value='啊'/>
		<input type="submit" value="提交">
	</form>
```



```jsp
<%
	Map<String,Person> map = new HashMap<String,Person>();
	map.put("1",new Person("tom","123",new Department("市场部")));
	pageContext.setAttribute("map", map);
%>
<table>
<tr>
		<td>姓名</td>
		<td>密码</td>
		<td>部门</td>
	</tr>
<c:forEach var="map" items="${map }">
	<tr>
		<td>${map.key }</td>
		<td>${map.value.password }</td>
		<td>${map.value.department.depName }</td>
	</tr>
</c:forEach>

```





## js代码中使用EL表达式

```js
console.log("${param.id}")

```





### Cookie

```jsp
<%
	Cookie c = new Cookie("name","liu");
	response.addCookie(c);
%>
<h1>${cookie.name.value }</h1>
	

```

js中获取cookie，使用js的urldecode解决中文乱码







**解决方法1：**

使用Long类型作为Map中的键值类型，本例中则是Map<Long，Boolean>，在JSP页面上进行遍历处理时，我们可以使用如下<c:if test="${roleReourceMap[resource.id+0]}">checked="true"</c:if>的方 式进行判断，注意这是使用了+0运算，具体原理不是很清楚，可能是将key值转换成了long类型，不过确实解决了问题。

当然，也可以通过

```java
<c:set value="${resource.id + 0}" var="longKey"></c:set>首先设置一个long类型的key，之后可以通过<c:if test="${roleReourceMap[longkey]}">checked="true"</c:if>进行判断处理。

```





使服务器端不需要处理map集合的泛型，依旧使用Map<Integer,Boolean>，不过不能再通过map[key]这种方式获取value值，我们可以通过map.entry，如下：

```
<c:forEach var="resource" items="resources">
    <input type="checkbox"
        <c:forEach items="${roleResourceMap}" var="entry">
            <c:if test="${entry.key==resource.id}">checked="true"</c:if>
        </c:forEach>
    >${resource.name}</input>
</c:forEach>

```

我们在菜单遍历的过程中多使用了一层循环，遍历了map集合，通过entry拿到菜单的ID与系统菜单的ID进行比较，实现勾选。

paramValues乱码

```jsp
<%
	request.setCharacterEncoding("utf-8");
%>

```





















#### 请求转发和重定向的路径问题

- 请求转发的/代表的根目录，相当于http://localhost:8080/1901，/就直接是webContent文件夹
- 重定向/1901/demo.jsp，因为回到客户端，客户端不知道你要访问那个项目，你就需要写上项目名
- 差别就是加个项目名

- 那我存到session里，能不能获取到？肯定的

  ```jsp
  pageContext.setAttribute("p", "我是pageContext的值，我想存到request域",PageContext.SESSION_SCOPE);
  
  String s = (String)session.getAttribute("p");
  ```

- session 一次会话（每个用户访问自己的）

- ServletContext application 全局的，只要tomcat不关闭，他就一直存在

  - 网站计数器（自己做，数据库的表）

  虽然目前你写代码，需要重启，但是一旦项目发布上线，你会重启吗？

  - 维护的key，value

  ```jsp
  application.setAttribute("p", "application");
  String s = (String)application.getAttribute("p");
  ```

- 看代码，我现在存了4个值，在四个作用域里,那么我能不能获取里面的值

  ```jsp
  <%
  		pageContext.setAttribute("p", "pageContext", PageContext.PAGE_SCOPE);
  		pageContext.setAttribute("p", "request", PageContext.REQUEST_SCOPE);
  		pageContext.setAttribute("p", "application", PageContext.APPLICATION_SCOPE);
  		pageContext.setAttribute("p", "session", PageContext.SESSION_SCOPE);
  		request.getRequestDispatcher("demo.jsp").forward(request, response);
  	%>
  ```

  - 除了pageContext获取不到，其他都可以。但是，有一个方法，大家要知道

    ```jsp
    String str = (String)pageContext.findAttribute("p");
    ```

    他得到的是什么？他会从小到大去找pageContext->request->session->application

  - 那我要是重定向呢？什么结果？

    - pageContext,request肯定获取不到
    - session,application没问题

 

### 


