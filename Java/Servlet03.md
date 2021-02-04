## Servlet第三课

### 一、多个页面共享数据

- 地址栏传递参数

  ```java
  public class SendRedirectDemo extends HttpServlet {
  	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		request.setCharacterEncoding("UTF-8");  //request转码
  		response.setContentType("text/html;charset=utf-8");	 //response转码
  		//?后传递参数,&连接多个参数：?参数名1=值1&参数2=值2&参数名3=值3...
  		response.sendRedirect("sendRedirectRequest?username=aishang&userpwd=123456");
  	}
  	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		this.doGet(request, response);
  	}
  }
  ```

  ```java
  @WebServlet("/sendRedirectRequest")
  public class SendRedirectRequest extends HttpServlet {
  	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		request.setCharacterEncoding("UTF-8"); 
  		response.setContentType("text/html;charset=utf-8");	 
  		String uname = request.getParameter("username");
  		String upwd = request.getParameter("userpwd");
  		System.out.println(uname);  //aishang
  		System.out.println(upwd);	//123456
  	}
  
  	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		this.doGet(request, response);
  	}
  }
  ```

- 隐藏表单提交（form） type=“hidden”

  ```java
  这是最常见的一种方式，也是最简单的，但有时该技巧非常管用，形如： 
  <form action=login>
  <input type=hidden name=a value=b>
  </form>
  通过隐藏表单，我们也可以将一个页面信息，传递给另外的页面。
  ```

  ```java
  @WebServlet("/FormHiddenDemo")
  public class FormHiddenDemo extends HttpServlet {
  	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		request.setCharacterEncoding("UTF-8");  //request转码
  		response.setContentType("text/html;charset=utf-8");	 //response转码
  		PrintWriter out = response.getWriter();
  		out.print("<html>");
  		out.print("<head><title>登录页</title></head>");
  		out.print("<body>");
  		out.print("<form action='FormHiddenRequest' method='get'>");
  		out.print("<table>");
  		out.print("<input type='hidden' name='flag' value='10'>");
  		out.print("<tr><td>用户名</td><td><input type='text' name='username' /></td></tr>");
  		out.print("<tr><td>密码</td><td><input type='password' name='userpwd' /></td></tr>");
  		out.print("<tr><td></td><td><input type='submit' name='sb' value='提交'  /></td></tr>");
  		out.print("</table>");
  		out.print("</form>");
  		out.print("</body>");
  	}
  
  
  @WebServlet("/FormHiddenRequest")
  public class FormHiddenRequest extends HttpServlet {
  	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		System.out.println(request.getParameter("flag"));  //value=10
  	} 
  	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		this.doGet(request, response);
  	}
  }
  
  ```

### 二、session

session属于客户端某一个浏览器的，占用服务器内存，浏览器与服务器的一次会话



- 例如，我登录163网站，登录后，无论跳到那个页面，都不用重新登录
- 如果我换个浏览器呢？session就不存在
- 如购物，我在这个浏览器买的东西，换个浏览器在购物，就需要重新登录，都不是一个浏览器了
- session的特点是一个浏览器
- 现在比如我的服务器在上海，一个人在哈尔滨访问我们的网站，那么就在服务器上开辟了一个session空间，别人在北京访问，也同样开辟空间
- 好处呢？
  - 既然在服务器里，那么我多个页面就可以共享这块区域，我想用的时候直接拿就可以
- 缺点
  - 占用服务器空间
  - 比如我存了一个照片？

#### 1、如何理解session？

- 可以把session看做一张表，这张表有两列，而表有多少行理论上没有限制，每一行就是session的一个属性，每个属性包含有两个部分，一个是该属性的名字String，另外一个是它的值Object
- 他的底层维护的就是map，map的特点，键不能重复
- 如果key重复了，值就被覆盖了
- 可以存储多个键值对

#### 2、session能做什么？

- 网上商城的购物车
  - 不用session，你一顿勾选，完了你一换页面就没了
  - 保存用户登录信息，换页面信息仍然存在
  - 将某一数据存到session中，供同一用户的各个方面使用
  - 防止非法登录到某个页面

#### 总结：

- 保存在服务器
- 属于一个浏览器
- 维护的一个map集合，不能重名

#### 3、如何创建session

- 创建session

  - session所属了类是HttpSession，是new吗？你从设计上考虑，session能让你自己创建吗？

  - HttpSession是接口，不能new，如果是普通的类，可不可以new

  - 如果你是开发者，你让客户可不可以随便new

  - 如果可以随便new，那么随便创建，服务器不就宕机了

  - 那他怎么实现呢？

    - 我们之前学过request，response两个对象，你用哪个？
    - request是接收
    - 返回的是session的实现类

    ```java
    HeepSession session = request.getSession(true)
    ```

    - 这里面有参数，你不写默认是true
      - true：如果这个浏览器，创建了session则不再创建，使用原session，没有我在创建，保证了session的唯一
      - false：如果浏览器创建了session，则不创建，如果没有，则返回null

  - 将数据保存到session中

    ```java
    session.setAttribute("name",username);
    ```

    session可以保存对象，第一个参数是String，第二个参数是Object

    ，那么就可以保存对象

    - 相当于把数据存到服务器了

  - 登录成功跳转到主页面

    - 地址栏没有值
    - 也没用隐藏表单

#### 4、非法登录

- 现在我登录到主页面了,那我下次就不登录，我记住这个登陆后的页面地址了，我下次直接访问这个地址。

- 我换个浏览器，试试访问这个地址，进入后用户名密码为空，因为换浏览器了，创建了新的session

- 怎么防止用户非法登录？判断

  ```java
  if(session.getAttribute("name")==null) {
  			response.sendRedirect("/1901/day03");
      return
  		}
  		
  			String name = (String)session.getAttribute("name");
  			String pwd = (String)session.getAttribute("pwd");
  			out.write("欢迎登录主页面");
  			out.write("用户名："+name);
  			out.write("密码："+pwd);
  ```

- 那么session能一直保存吗？

#### 5、session的生命周期

- 默认时间是30min，你也可修改它存在的时间：发呆时间
  - 修改tomcat的web.xml    
  - 修改项目的web.xml
  - 在程序中修改



- 上面说的这个30min指的是用户的发呆时间，而不是累计时间  

  - 时间太长，信息容易泄露

  - 太短，体验不好

    ```java
    session.setMaxInactiveInterval(10*60);//发呆时间是秒
    ```

- 如何销毁session

```java
	 session.invalidate() 销毁 注销 
```

- 演示，当我登陆后，点击注销，别的页面目前有登录账号，当我刷新页面时就会退出

- 移除session中的部分数据

  ```java
  session.removeAttribute("name");
  ```

【编写示例三】演示session的作用域，生存周期，及如何存值、取值，销毁

```java
//获取session
		HttpSession session = request.getSession();
		//设置session的有效时间
		//如果设置为0或负数，则表示会话将永远不会超时。常用于设置当前会话时间。
		session.setMaxInactiveInterval(60); //设置有效60秒
		//输出session的id
		System.out.println(session.getId());
		//销毁 session
		session.invalidate();

@WebServlet("/SessionDemo1")
public class SessionDemo1 extends HttpServlet {
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//获取session
		HttpSession session = request.getSession();
		//输出session的id
		System.out.println(session.getId());
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		this.doGet(request, response);
	}
}
```

![image-20190215215115682](https://ws2.sinaimg.cn/large/006tKfTcly1g07g6dylwuj30ek02mdhg.jpg)

```java
@WebServlet("/SessionDemo2")
public class SessionDemo2 extends HttpServlet {
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		HttpSession session = request.getSession();
		//输出的sessionid仍为SessionDemo1的sessionid
		//说明session能在不同页面共享数据
		System.out.println(session.getId());
	}
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		this.doGet(request, response);
	}
}
```

![image-20190215215331228](https://ws4.sinaimg.cn/large/006tKfTcly1g07g8qoortj30ek03hgnr.jpg)

```java
@WebServlet("/SessionDemo1")
public class SessionDemo1 extends HttpServlet {
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		HttpSession session = request.getSession();
		//通过setAttribute方法给 username赋值
		session.setAttribute("username","aishang");
	}
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		this.doGet(request, response);
	}
}
@WebServlet("/SessionDemo2")
public class SessionDemo2 extends HttpServlet {
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		HttpSession session = request.getSession();
		//说明session能在不同页面共享数据
		System.out.println(session.getAttribute("username"));
	}
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		this.doGet(request, response);
	}
}
```

### 三、请求转发与重定向

```java
String contextPath = request.getContextPath();
String requestURI = request.getRequestURI();
StringBuffer requestURL = request.getRequestURL();
System.out.println("contextPath="+contextPath);
System.out.println("requestURI="+requestURI);
System.out.println("requestURL="+requestURL);
//------------
contextPath=/1901
requestURI=/1901/page1
requestURL=http://localhost:8080/1901/page1  
```

#### 1、重定向

- 浏览器2次请求
- 属于get请求
- 地址栏改变
- 能否传数据-可以
- 能否传对象-不可以
  - 为什么能传字符串，不能传对象
  - ![截屏2019_2_24_08_26](https://ws2.sinaimg.cn/large/006tKfTcly1g0h7hjq89jj30r00bign3.jpg)



- 回到客户端，对象是在服务器内存的堆里，客户端有tomcat吗？有jdk吗？他俩都不是一个屋，怎么能访问到？

- 字符串为什么能，地址栏传过去的

- 对象能序列化之后传递（了解）

- 案例1-简单的页面传参跳转

- 案列2

  ```java
  System.out.println("我是page1");
  request.setCharacterEncoding("utf-8");
  response.setContentType("text:html;charset=utf-8");
  int contentLength = request.getContentLength();
  String contextPath = request.getContextPath();
  String requestURI = request.getRequestURI();
  StringBuffer requestURL = request.getRequestURL();
  System.out.println("contentLength="+contentLength);
  System.out.println("contextPath="+contextPath);
  System.out.println("requestURI="+requestURI);
  System.out.println("requestURL="+requestURL);
  response.sendRedirect("/1901/page2");
  System.out.println("又回到page1");
  //----
  我是page1
  contentLength=-1
  contextPath=/1901
  requestURI=/1901/page1
  requestURL=http://localhost:8080/1901/page1
  又回到page1
  我是 page2
  ```

  - 重定向后，后面的程序继续执行，我告诉客户端去找别人了，剩下的事跟我有关了吗？我程序还没执行完

#### 2、请求转发

- 没有回到客户端

- 浏览器1次请求

- 地址栏不变，服务器内部去跳转

- 案列：重定向的案例换成转发，结果

  ```java
  System.out.println("我是page1");
  request.setCharacterEncoding("utf-8");
  response.setContentType("text:html;charset=utf-8");
  int contentLength = request.getContentLength();
  String contextPath = request.getContextPath();
  String requestURI = request.getRequestURI();
  StringBuffer requestURL = request.getRequestURL();
  System.out.println("contentLength="+contentLength);
  System.out.println("contextPath="+contextPath);
  System.out.println("requestURI="+requestURI);
  System.out.println("requestURL="+requestURL);
  //		response.sendRedirect("/1901/page2");
  request.getRequestDispatcher("page2").forward(request, response);
  System.out.println("又回到page1");
  //----------
  我是page1
  contentLength=-1
  contextPath=/1901
  requestURI=/1901/page1
  requestURL=http://localhost:8080/1901/page1
  我是 page2
  又回到page1
  ```

  - 当我执行带转发时，他去给我找，找到后，在返回，程序继续执行

- 能否传递参数

  - 地址栏传递-字符串
  - 传对象

可以利用request域对象传递数据：

request.setAttribute(key, value);

在接收端：

request.getAttribute("key");

注：使用request传递数据只能使用请求转发，只有一次请求有效

![3_多个页面共享数据_session_docx_-_WPS_Office_2019_for_Mac](https://ws1.sinaimg.cn/large/006tKfTcly1g07galkiltj30hw0ev0tw.jpg)

- ### 请求转发

  - 服务器内部的跳转
  - 只要请求链不断，可以通过request域对象传递数据
  - 地址栏不发生改变
  - 浏览器的几次访问（1次）

- ### 重定向

  - 浏览器的跳转
  - 不可以传递对象
  - 地址栏改变(属于get请求)
  - 浏览器的2次访问
  - response域对象

  重定向：会在当前页面代码执行完毕后，跳转到指定的页面执行其他代码。 
  转 发：在本页面代码执行到转发语句后，即跳转到指定的页面执行其他代码，执行完毕后返回接着执行转发语句后的代码。

#### 3、重定向和请求转发选哪个？

- 因为是一次请求，请求转发效率高，所以能用转发就用转发
- 如果需要地址栏改变，那么就需要用重定向

## 作业

- 注册页面，成功后跳转到登陆页面，并且回显注册信息
- 登陆成功后，需判断是否是普通用户和vip用户
- 普通用户，显示欢迎普通用户+用户名界面
- vip用户，显示欢迎vip用户+用户名界面
- 要求普通用户和vip用户写在一个servlet中
- 数据库表中增加一个字段，普通用户为0，vip用户为1
- 显示欢迎页面增加一个安全退出的链接，退出时清空session
- 非法登录验证
- 在显示页面只显示当前用户的，姓名、密码、地址(数据库)
- 增加一个修改的连接，跳到修改该用户信息页面，制作form表单
- 表单中可以修改信息
- 当我点击提交时，为修改功能，根据form表单中的数据，修改用户信息
- 修改成功后跳回到显示页面