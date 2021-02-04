```java
 1 package gac.xdp.cookie;
 2 
 3 import java.io.IOException;
 4 import java.io.PrintWriter;
 5 import java.util.Date;
 6 import javax.servlet.ServletException;
 7 import javax.servlet.http.Cookie;
 8 import javax.servlet.http.HttpServlet;
 9 import javax.servlet.http.HttpServletRequest;
10 import javax.servlet.http.HttpServletResponse;
11 
12 /**
13  * @author gacl
14  * cookie实例：获取用户上一次访问的时间
15  */
16 public class CookieDemo01 extends HttpServlet {
17 
18     public void doGet(HttpServletRequest request, HttpServletResponse response)
19             throws ServletException, IOException {
20         //设置服务器端以UTF-8编码进行输出
21         response.setCharacterEncoding("UTF-8");
22         //设置浏览器以UTF-8编码进行接收,解决中文乱码问题
23         response.setContentType("text/html;charset=UTF-8");
24         PrintWriter out = response.getWriter();
25         //获取浏览器访问访问服务器时传递过来的cookie数组
26         Cookie[] cookies = request.getCookies();
27         //如果用户是第一次访问，那么得到的cookies将是null
28         if (cookies!=null) {
29             out.write("您上次访问的时间是：");
30             for (int i = 0; i < cookies.length; i++) {
31                 Cookie cookie = cookies[i];
32                 if (cookie.getName().equals("lastAccessTime")) {
33                     Long lastAccessTime =Long.parseLong(cookie.getValue());
34                     Date date = new Date(lastAccessTime);
35                     out.write(date.toLocaleString());
36                 }
37             }
38         }else {
39             out.write("这是您第一次访问本站！");
40         }
41         
42         //用户访问过之后重新设置用户的访问时间，存储到cookie中，然后发送到客户端浏览器
43         Cookie cookie = new Cookie("lastAccessTime", System.currentTimeMillis()+"");//创建一个cookie，cookie的名字是lastAccessTime
44         //将cookie对象添加到response对象中，这样服务器在输出response对象中的内容时就会把cookie也输出到客户端浏览器
45         response.addCookie(cookie);
46     }
47 
48     public void doPost(HttpServletRequest request, HttpServletResponse response)
49             throws ServletException, IOException {
50         doGet(request, response);
51     }
52 
53 }
```

```java
package gac.xdp.cookie;
 2 
 3 import java.io.IOException;
 4 
 5 import javax.servlet.ServletException;
 6 import javax.servlet.http.Cookie;
 7 import javax.servlet.http.HttpServlet;
 8 import javax.servlet.http.HttpServletRequest;
 9 import javax.servlet.http.HttpServletResponse;
10 
11 /**
12  * @author gacl
13  * 删除cookie
14  */
15 public class CookieDemo02 extends HttpServlet {
16 
17     public void doGet(HttpServletRequest request, HttpServletResponse response)
18             throws ServletException, IOException {
19         //创建一个名字为lastAccessTime的cookie
20         Cookie cookie = new Cookie("lastAccessTime", System.currentTimeMillis()+"");
21         //将cookie的有效期设置为0，命令浏览器删除该cookie
22         cookie.setMaxAge(0);
23         response.addCookie(cookie);
24     }
25 
26     public void doPost(HttpServletRequest request, HttpServletResponse response)
27             throws ServletException, IOException {
28         doGet(request, response);
29     }
30 }
```

```java
cookie中存取中文
　　要想在cookie中存储中文，那么必须使用URLEncoder类里面的encode(String s, String enc)方法进行中文转码，例如：

1 Cookie cookie = new Cookie("userName", URLEncoder.encode("孤傲苍狼", "UTF-8"));
2 response.addCookie(cookie);
　　在获取cookie中的中文数据时，再使用URLDecoder类里面的decode(String s, String enc)进行解码，例如：

1 URLDecoder.decode(cookies[i].getValue(), "UTF-8")
```

el表达式获取cookie

```jsp
<div class="form-group has-feedback">
                <input type="text" class="form-control" placeholder="用户名" name="username" value="${cookie.usernameremeber.value}">
                <span class="glyphicon glyphicon-user form-control-feedback"></span>
            </div>
            <div class="form-group has-feedback">
                <input type="password" class="form-control" placeholder="密码" name="password" value="${cookie.passwordremeber.value}">
                <span class="glyphicon glyphicon-lock form-control-feedback"></span>
            </div>
```

name是获取的cookie的名称，value获取的是cookie的值

### 中文乱码

```java
Cookie usernameCookie = new Cookie("usernameremeber",URLDecoder.decode(username,"utf-8"));
Cookie passwordCookie = new Cookie("passwordremeber",URLDecoder.decode(password,"utf-8"));
```

### 直接在后台解码就可以

```java
 Cookie("passwordremeber",URLDecoder.decode(password,"utf-8"));
```

### 解决cookie不能存空格问题

利用el的fn函数

```java
 //获取当前时间
                SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd_HH:mm:ss");
                String currentTime = simpleDateFormat.format(new Date());
                Cookie last = new Cookie("last", currentTime);
                response.addCookie(last);
```

前台按_切割，之后再空格拼接

```jsp
 <small>上次登录时间：${fn:join(fn:split(cookie.last.value, "_"), " ")}</small>
```



### 时间格式化问题

fmt标签



# 三种方法

cookie接收中文乱码的原因:默认的使用IOS-8859-1的编码存储数据,不支持中文,存储中文时报错:Control character in cookie value or attribute

乱码的原因要么编码不支持,要么就是存储端解码和接收端编码使用的编码集不一样.

第一步:先解决cookie存入时不支持中文的乱码报错问题

cookie(String name,URLEncode("String value","UTF-8"));存储前先使用UTF-8解码

第二步解决接收端的乱码问题:保证存储和接收编码集一致

URLDecoder.decode(cookies[i].getValue(), "UTF-8"),接收的数据用UTF-8编码

//上述两步可以用于解决过滤器中的自动登录问题,将用户名和密码存储到cookie中,通过过滤器接收判断实现自动 登录

如果在JSP中接收cookie的值如何解码(可以用于记录用户名的问题)

提供两种方式:

1.使用java代码实现

```java
<%
Cookie[] cookies = request.getCookies();
String value="";
for(int i=0;cookies!=null&&i<cookies.length;i++){
if("remname".equals(cookies[i].getName())){
value=URLDecoder.decode(cookies[i].getValue(), "UTF-8");
}
}
 %>
```



2.使用JS代码实现

```java
window.onload=function(){
var remember = document.getElementById("remember");//获取对象
var username = decodeURIComponent("${cookie.remname.value}");//如果为空,使用解码后为空字符串
if(!username.length==0){
remember.value=username;
}
};
```



3.使用自定义的EL方法进行解码

```java
①自定义一个工具类,实现需要的功能

package mytag;
import java.io.UnsupportedEncodingException;
import java.net.URLDecoder;

public class ElTag {
public static String urlDecode(String value){

try {
value= URLDecoder.decode(value, "UTF-8");
} catch (UnsupportedEncodingException e) {
e.printStackTrace();
}
return value;
}
}

②创建一个对应的标签文件TDL文件,放在WEB-INF下

<?xml version="1.0" encoding="GBK"?>  
<taglib xmlns="http://java.sun.com/xml/ns/j2ee"       
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"       
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee   
    http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd"       
    version="2.0">     
    <!-- 定义函数版本 -->  
    <tlib-version>1.0</tlib-version>  
    <!-- 定义函数名称 这个是随意的,但是最好和调用函数时定义一致-->  
    <short-name>el</short-name>  
    <!-- 定义第一个函数 -->  

   <!-- 上面基本是固定的格式,下面也是通用格式,根据自己需求更改内容-->  
    <function>  
        <!-- 定义第一个函数：reverse -->  
        <name>urlDecode</name>  
        <!-- 定义函数处理类 写类的全路径 -->  
        <function-class>mytag.ElTag</function-class>  
        <!-- 定义函数的对应方法 -->  
        <function-signature>  
            java.lang.String urlDecode(java.lang.String)  
        </function-signature>  
    </function>  
      
</taglib>

③.在web.xml中配置该TDL的导入路径

<jsp-config>  
        <taglib>  
            <!-- 配置标签的引用地址 JSP页面中引用时使用-->  
            <taglib-uri>/eltag</taglib-uri>  
            <!-- 配置标签的TLD文件地址 -->  
            <taglib-location>/WEB-INF/ElTag.tld</taglib-location>  
        </taglib>  
    </jsp-config> 
```



引入方式:   <%@ taglib uri="/eltag" prefix="el" %>

##### 调用方式:  ${el:urlDecode(cookie.username.value)}
