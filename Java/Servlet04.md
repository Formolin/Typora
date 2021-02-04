# Servlet第四课

### 一、页面显示图片

（1）在你的webContent下建立一个文件夹，比如images，然后将图片拷贝到该文件夹中

（2）在servlet中添加：<img src=*.images/图片名/>即可。 注：如果是上级文件夹用../代替。最后会用request.getServletContextPath()代替，request.getContextPath()能够获得当前工程路径,

如果“”双引号中仍然存在“”双引号，则双引号用‘’单引号代替，或使用转义字符(/反斜杠)实现。转义实现：out.println(“<img src=/”images/logo.jpg/"/>”);

- 注意，需要写response.setContentType("text/html;charset=utf-8");，如果不写，解析成字符串在浏览器

  ```java
  out.println("<img src='../images/1.jpg'/>");
  //http://localhost:8080/images/1.jpg 
  
  out.println("<img src='/1901/images/1.jpg'/>");
  //项目名写死了
  
  out.println("<img src='"+req.getContextPath()+"/images/1.jpg'/>");
  ```

  

### 二、分页实现

（1） 定义四个变量

int pageSize：每页显示多少条记录 程序员自己定义的 

int pageNow：希望显示第几页 初始赋值，后期传递 

int pageCount：一共有多少页计算出来的 

int rowCount：一共有多少条记录 select count(*) from 表名 

（2） pageCount获得算法：

总记录数余每页显示记录入，如取余结果为0，则代表整除了，因此pageCount=rowCount/pageSize; 否则pageCount=rowCount/pageSize+1;

例如总记录数为20天，每页显示10天，则pageCount=20/10=2页，如每页显示7条，则pageCount=(20/7+1)=3页

```java
if(rowCount%pageSize==0) {
pageCount=rowCount/pageSize;
} else {
pageCount=rowCount/pageSize+1;
} 

```

（3） 分页sql语句实现

#### 方式一：

select 字段名列表 from 表名 where id between ？ and ？ 

这个sql语句确实比较快，但是存在一个问题，即如果表的id被删除了，那么某页可能就会少一条记录。 

#### 方式二：

select  top pageSize 字段名列表 from 表名 where id not in(select top pageSize*(pageNow-1) id from 表名)

注意：sqlserver支持 top，mysql需要使用limit

 

#### 方式三：此为mysql最终使用方式（需强化limit用法）

select * from 字段名 limit pageSize*(pageNow-1)，pageSize

 

（4）利用sql命令建立测试数据

复制表的记录的语句可用： 

insert into 表名 (字段1,2,3…) select 字段 字段1,2,3… from 表名 

如下表的复制记录的用法为：

insert into表名(idepid,username,userPwd,userCode,userSex,userAge) select depid,username,userPwd,userCode,userSex,userAge from 表名

注：id是主键且自动增长，不能复制

![image-20190215215958020](https://ws3.sinaimg.cn/large/006tKfTcly1g07gffrwl7j30ec03lgm2.jpg)

#### 分页代码

```java
@WebServlet("/main")
public class Main extends HttpServlet {
	@Override
	protected void doGet(HttpServletRequest request,
			HttpServletResponse response) throws ServletException, IOException {
		// 指定接收参数的编码集
		request.setCharacterEncoding("UTF-8");
		response.setContentType("text/html;charset=UTF-8");
		PrintWriter out = response.getWriter();
		HttpSession session = request.getSession();
		User user = (User) session.getAttribute("user");
		if (user == null) {
			// 非法访问,未进行登录
			response.sendRedirect("login");
			return;
		}

		// 登录人用户名
		String username = user.getUsername();
		out.print("<h1>欢迎您" + username + "</h1>");
		Connection conn = null;
		PreparedStatement ps = null;
		ResultSet rs = null;

		int pageSize = 10; // 设置每页显示记录数
		int pageNow = 1; // 设置当前是第几页,默认情况下第一页
		int rowCount = 0; // 设置总记录数初始值为0
		int pageCount = 0; // 设置总页数初始值为 0
		if (request.getParameter("pageNow") != null) {
			pageNow = Integer.parseInt(request.getParameter("pageNow"));
		} else {
			pageNow = 1;
		}

		try {
			conn = DBUtil.getConnection();
			// 1.计算rowCount
			String sql = "select  count(*) as rowCount from tb_users ";
			ps = conn.prepareStatement(sql);
			rs = ps.executeQuery();
			if (rs.next()) {
				rowCount = rs.getInt("rowCount");
			}

			// 2.根据rowCount 计算pageCont
			if (rowCount % pageSize == 0) {
				pageCount = rowCount / pageSize;
			} else {
				pageCount = rowCount / pageSize + 1;
			}

			// 3.创建sql分页语句，并赋值给sql变量，建议先将sql写在数据库中运行，通过后在赋值到程序中运行
			sql = "select * from tb_users limit " + pageSize * (pageNow - 1)
					+ "," + pageSize;
			ps = conn.prepareStatement(sql);
			rs = ps.executeQuery();
			// 4. 输出表头
			out.print("<html>");
			out.print("<head><title>列表</title></head>");
			out.print("<body>");
			out.print("<table align='center' border='1' width='700px'>");
			out.print("<tr width='100px'>");
			out.print("<td width='50px'>id</td>");
			out.print("<td width='100px'>用户名</td>");
			out.print("<td width='100px'>密码</td>");
			out.print("<td width='100px'>部门</td>");
			out.print("<td width='50px'>年龄</td>");
			out.print("</tr>");
			// 5.循环显示列表信息
			while (rs.next()) {
				// 6.显示用户信息
				out.print("<tr>");
				out.print("<td>" + rs.getInt("id") + "</td>");
				out.print("<td>" + rs.getString("username") + "</td>");
				out.print("<td>" + rs.getString("userPwd") + "</td>");
				out.print("<td>" + rs.getInt("depId") + "</td>");
				out.print("<td>" + rs.getInt("userAge") + "</td>");
				out.print("</tr>");
			}
			// 7.输出表尾
			out.print("</table>");
			// 8.显示分页页码
			for (int i = 1; i <= pageCount; i++) {
				out.print("<a  href='main?pageNow=" + i + "'>[" + i + "]</a>");
			}
			out.print("</body>");
			out.print("</html>");
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
			// 关闭相关对象
			DBUtil.close(conn, ps, rs);
		}

	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp)
			throws ServletException, IOException {
		this.doGet(req, resp);
	}
}
```

最终显示效果

![image-20190215220045071](https://ws4.sinaimg.cn/large/006tKfTcly1g07gg9842xj30ej06nab4.jpg)

```html
<!DOCTYPE html>
<html>
<head>
	<title></title>
</head>
<body>
	<center>
		<table>
			<tr>
				<td>编号</td>
				<td>部门id</td>
				<td>姓名</td>
				<td>密码</td>
				<td>员工编号</td>
				<td>性别</td>
				<td>年龄</td>
				<td>权限</td>
			</tr>
			<tr>
				<td>1</td>
				<td>2</td>
				<td>tom</td>
				<td>123</td>
				<td>001</td>
				<td>男</td>
				<td>18</td>
				<td>0</td>
			</tr>
		</table>
	</center>
</body>
</html>
```



### 三、cookie

**什么是**Cookie

（1） 服务器在客户端保存用户的信息，比如登录名、密码……就是Cookie

（2） 这些信息就像是小甜饼一样，数据量并不大，服务器端在需要的时候可以从客户端读取

注意：Cookie是保存在客户端的，Session是保存在服务器端的

![img](https://ws3.sinaimg.cn/large/006tKfTcly1g07ghbyihvj304v03m3yi.jpg) 

了解：

- cookie是http协议制定的！先由服务器保存cookie到浏览器，在下次浏览器请求服务器时把上一次请求得到cookie在归还给服务器
- 由服务器创建保存到客户端浏览器的一个键值对
- 当浏览器请求服务时，会把该服务器保存的cookie岁请求发送给服务器，浏览器归还cookie的请求头
- http协议规定(保证不给浏览器太大压力)：
  - 1个cookie最大4kb
  - 1个服务器最多向1个浏览器保存20个cookie
  - 1个浏览器最多可以保存300个cookie
- 浏览器大战：因为浏览器竞争的很激烈，所以很多浏览器都会在一定范围内违反http规定，但也不会让一个cookie为4gb
- 不能跨浏览器

**4.2 Cookie**可以用来做什么

（1） 保存用户名、密码，在一定时间不用重新登录 

（2） 记录用户访问网站的喜好，比如有无背景音乐、网页的背景色是什么 

（3） 网站的个性化，比如定制网站的服务、内容

 

**4.3 Cookie**的应用范围

（1）cookie有点像一张表，分两列，一个是名字，一个是值，数据类型都是String （与session类似），但不能保存对象

| C_name              | uname             |
| ------------------- | ----------------- |
| **名字** **String** | **值** **String** |
|                     |                   |
|                     |                   |

 

**4.4 Cookie**的使用

（1）如何创建一个cookie（在服务器端创建的） 

//HttpSession s1 = request.getSession(true) 单例模式  单实例

Cookie c=new Cookie(String name, String val);//保存在客户端的

（2）设置保存时间 

cookie1.setMaxAge(14*24*3600);

cookie2.setMaxAge(14*24*3600); 

（3）设置可在同一应用服务器内共享cookie

- cookie的path并不是设置这个cookie在客户端的保存路径
- cookie的path由服务器创建cookie时设置
- 当浏览器访问服务器某个路径时，需要归还哪些cookie给服务器呢？由cookie的path决定
  - aCookie.path=/1901/;bCookie.path=/1901/jps/;cCookie.path=/1901/jsp/cookie/
  - 访问/1901/index.jsp返回aCookie
  - 访问/1901/jps/a.jsp返回aCookie,bCookie
  - /1901/jsp/cookie/b.jsp返回aCookie,bCookie，cCookie
- cookie的path默认值：当前访问路径的父路径，例如在访问/1901/jps/a.jsp时响应的cookie是/1901/jps

（4）如何将一个cookie添加到客户端 

response.addCookie(c);

（5）如何读取cookie（从客户端读到服务器 数组） 

Cookie[] cookie=request.getCookies(); 

//1.创建Cookie 对象		

Cookie cookie1 = **new** Cookie("username",uname);		

Cookie cookie2 = **new** Cookie("userpwd",upwd);				

//2.设置 Cookie的保存时间		

/*默认为-1：将cookie 保存至浏览器缓存中，浏览器关闭则情况	*

*	0:不保存cookie		
*	 正数：将cookie保存至客户端的本地磁盘中，参数为保存的时长，单位为s
*	*/		cookie1.setMaxAge(60*60*24); //保存1天		*
*	*cookie2.setMaxAge(60*60*24);			
*	​	//3.设置cookie可在当前应用范围内共享		
*	cookie1.setPath("/"); 		cookie2.setPath("/");		
*	​		//4.将cookie发送至客户端		response.addCookie(cookie1);		response.addCookie(cookie2);

 

 

编写cookie后，一定要设置保存时间，否则cookie在关闭浏览器后消失，cookie文件在不同的浏览器下存放的文件夹不同，如ie下查看cookie：工具àinternet选项à浏览历史记录下的设置à查看文件夹，在该文件夹下存在访问过的站点的cookie文件，该cookie文件以站点的名称存储。

 

![img](https://ws2.sinaimg.cn/large/006tKfTcly1g07ghcgraxj30cn03bab4.jpg) 

 

因为我在程序中，把用户名信息及密码信息写入到了客户端。因此在此文件中可看到

![img](https://ws4.sinaimg.cn/large/006tKfTcly1g07ghb5z1uj302c062wet.jpg) 

设置好cookie后，我们读取cookie

//1.读取cookie 注： cookie以数组结构保存在客户端，因此以数据格式去读取

​		Cookie[] cookies = request.getCookies();		

//2. 设置变量存储cookie信息		

String username = "";		

String password = "";		

//3.判断cookie是否为空		

**if** (cookies != **null**) {			

**for** (Cookie cookie : cookies) {				

//4.逐一判断客户端cookie文件的存储信息				

//因为客户端可能存有大量其他站点信息				

//因此要根据指定的key(键)去查询value(值)				

**if** ("username".equals(cookie.getName())) {					

username = cookie.getValue();				

}				

**if** ("password".equals(cookie.getName())) {	

​				password = cookie.getValue();				

}		

​	}		

}

 

如cookie确实存在，则读取其响应的值，这样用户二次登陆就可以直接把其用户名和密码赋值到登陆框了，如下图所示：

![img](https://ws3.sinaimg.cn/large/006tKfTcly1g07ghbj97aj30a6035jrp.jpg) 

**4.5 cookie****与****session****的比较**

（1）存在的位置 

cookie保存在客户端，session保存在服务器端 

（2）安全性 

比较而言，cookie的安全性比session要弱 

（3）网络传输量 

cookie通过网络在客户端与服务器端传输，而session保存在服务器端，不**需要传输**

 

###  作业：

- 上课内容代码敲一遍

- 只显示1-5页

- 选中当前页字体红色

- 对当前页进行验证判断

- 制作跳转页
  - 处理非数字问题
  - 处理非pagecount区间的数值问题
  - 用form表单

- 显示首页、上一页、尾页、下一页，如果是当前页为1，不显示首页、上一页，如果当前页为尾页，不显示下一页、尾页

- 当前页位置始终在中间

- 登录页使用cookie，记录用户名和密码
  - 要求中英文用户名都好使
  - 只有用户选中时保存cookie

- 显示新闻列表的分页，部门页

- 以上内容的代码，下节课内容要用

- 附加题：完成登录时显示上次登录时间

  









```java
package com.aishang.servlet02;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.List;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
@WebServlet("/page")
public class PageDemo extends HttpServlet {
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		req.setCharacterEncoding("utf-8");
		resp.setContentType("text/html;charset=utf-8");
		System.out.println("page");
		PrintWriter out = resp.getWriter();
		String username = req.getParameter("name");
		out.println("<h1>"+username+"</h1>");
		UserDao userDao = new UserDao();
		int pageNow = 1;
		if (req.getParameter("pageNow")!=null) {
			pageNow = Integer.parseInt(req.getParameter("pageNow"));
		}
		int pageSize = 5;
		int pageCount;
		int rowCount = userDao.rowCount();
		System.out.println("rowCount="+rowCount);
		if (rowCount%pageSize==0) {
			pageCount = rowCount/pageSize;
		}else {
			pageCount = rowCount/pageSize+1;
		}
		List<User> findAllUser = userDao.findAllUser(pageNow,pageSize);
		
		out.println("<!DOCTYPE html>");
		out.println("<html>");
		out.println("<head>");
		out.println("	<title></title>");
		out.println("</head>");
		out.println("<body>");
		out.println("	<center>");
		out.println("		<table border='1'>");
		out.println("			<tr>");
		out.println("				<td>编号</td>");
		out.println("				<td>用户名</td>");
		out.println("				<td>密码</td>");
		out.println("				<td>性别</td>");
		out.println("				<td>年龄</td>");
		out.println("				<td>地址</td>");
		out.println("				<td>电话</td>");
		out.println("			</tr>");
		
		for (User user : findAllUser) {
			out.println("			<tr>");
			out.println("				<td>"+user.getId()+"</td>");
			out.println("				<td>"+user.getUsername()+"</td>");
			out.println("				<td>"+user.getPassword()+"</td>");
			out.println("				<td>"+user.getSex()+"</td>");
			out.println("				<td>"+user.getAge()+"</td>");
			out.println("				<td>"+user.getAddr()+"</td>");
			out.println("				<td>"+user.getTel()+"</td>");
			out.println("			</tr>");
		}
		
		
		out.println("		</table>");
		if (pageNow != 1) {
			out.print("<a  href='/1901/page?pageNow=" + 1+ "'>[首页]</a>");
			out.print("<a  href='/1901/page?pageNow=" + (pageNow-1) + "'>[上一页]</a>");
		}
		//开始
		int start = 0;
		//结束
		int end = 0;
		if (pageNow>=1&&pageNow<=5) {
			start = 1;
			end = 5;
		}else if(pageNow>pageSize){
			start = pageNow-4;
			end = pageNow;
			
		}
		System.out.println(start+"--"+end);
		for (int i = start; i <= end; i++) {
			if (pageNow==i) {
				out.print("<a  href='/1901/page?pageNow=" + i + "' style='color:red'>[" + i + "]</a>");
			}else {
				out.print("<a  href='/1901/page?pageNow=" + i + "'>[" + i + "]</a>");
			}
		}
		if (pageNow !=pageCount) {
			out.print("<a  href='/1901/page?pageNow=" + (pageNow+1)+ "'>[下一页]</a>");
			out.print("<a  href='/1901/page?pageNow=" + pageCount+ "'>[尾页]</a>");
		}
		
		out.println("	</center>");
		out.println("</body>");
		out.println("</html>");
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		System.out.println("post");
		this.doGet(req, resp);
	}
}

```



改造，当前页始终在中间位置

```java
// 显示1-5页
		int start = 1;
		int end = 5;
		if (pageNow > 3) {
			start = pageNow - 2;
			if (pageNow == pageCount - 1) {
				end = pageNow + 1;
			} else if (pageNow == pageCount) {
				end = pageNow;
			} else {
				end = pageNow + 2;
			}
			for (int i = start; i <= end; i++) {
				if (pageNow == i) {
					out.println("<a href='/001/userController?flag=userList&pageNow=" + i + "&username=" + name
							+ "&depID=" + depID + "'><font style='color:red'>【" + i + "】</font></a>");
				} else {
					out.println("<a href='/001/userController?flag=userList&pageNow=" + i + "&username=" + name
							+ "&depID=" + depID + "'>【" + i + "】</a>");
				}
			}
		} else {
			for (int i = 1; i <= pageSize; i++) {
				if (pageNow == i) {
					out.println("<a href='/001/userController?flag=userList&pageNow=" + i + "&username=" + name
							+ "&depID=" + depID + "'><font style='color:red'>【" + i + "】</font></a>");
				} else {
					out.println("<a href='/001/userController?flag=userList&pageNow=" + i + "&username=" + name
							+ "&depID=" + depID + "'>【" + i + "】</a>");
				}
			}
		}
```

PageBean

```java
package com.aishang.servlet;

import java.util.ArrayList;
import java.util.List;

public class PageBean<T>{
	int pageNow;
	int pageSize;
	int rowCount;
	List<T> list;
	public int getPageNow() {
		return pageNow;
	}
	public void setPageNow(int pageNow) {
		this.pageNow = pageNow;
	}
	public int getPageSize() {
		return pageSize;
	}
	public void setPageSize(int pageSize) {
		this.pageSize = pageSize;
	}
	public int getRowCount() {
		return rowCount%pageSize==0?rowCount/pageSize:rowCount/pageSize+1;
	}
	
	public List<T> getList() {
		return list;
	}
	public void setList(List<T> list) {
		this.list = list;
	}
}

```





