# Servlet第五课MVC分层

### 一、原有模式开发的弊端

我们在之前的登录模块中

- login
- loginok
- main

分了三个servlet

但是每个servlet都有向页面展示，连接数据库，控制逻辑的代码，所有的代码都耦合在一起，结构不清晰，如果出现问题，不利于维护

- 我们的目的是让代码分工明确，各司其职
- 有专门显示的，有专门处理逻辑的，有专门操作数据库的

### 二、MVC模式

- M：model即模型层，包含domain层（实体类）、dao层（数据库访问）
  - com.aishang.domain 或者也叫pojo、po
  - com.aishang.dao
  - com.aishang.service

- V：view即视图层，主要用于前台给客户的展现（Servlet、jsp编写）
  - com.aishang.view

- C：controller即控制层，主要用于前台数据的接收、封装及转发（Servlet编写）也叫action
  - com.aishang.controller

饭店点餐，菜单相当于view层，服务员相当于control层，大厨相当于service层，小工相当于dao层，原材料相当与domain层。相当于model

view层相当于展现层，为客户服务，为客户提供人机交互界面及接口，如我们去饭店吃饭，相当于饭店为我们提供的菜单；饭店的服务员（control）层，记录我们点餐信息，并将该信息反馈给大厨（service层），大厨通知小工切菜、洗菜（dao层），小工、大厨、服务员、菜单都需要使用材料（葱姜蒜）展现美味佳肴。

- 一张物理表对应一套mvc

### 三、改造代码

- userDao

  ```java
  package com.aishang.dao;
  
  import java.sql.Connection;
  import java.sql.PreparedStatement;
  import java.sql.ResultSet;
  import java.sql.SQLException;
  import java.util.ArrayList;
  import java.util.List;
  
  import com.aishang.domain.Users;
  import com.aishang.util.DBUtil;
  
  public class UserDao {
  	private Connection connection;
  	private PreparedStatement prepareStatement;
  	private ResultSet rs;
  	//验证用户
  	public boolean checkUser(Users users) {
  		connection = DBUtil.getConnection();
  		try {
  			prepareStatement = connection.prepareStatement("select * from tb_users where userName=? and userPwd=?");
  			prepareStatement.setString(1, users.getUserName());
  			prepareStatement.setString(2, users.getUserPwd());
  			rs = prepareStatement.executeQuery();
  			return rs.next();
  		} catch (SQLException e) {
  			// TODO Auto-generated catch block
  			e.printStackTrace();
  		}finally {
  			DBUtil.close(connection, prepareStatement, rs);
  		}
  		return false;
  	}
  	//rowCount
  	public int getRowCount() {
  		connection = DBUtil.getConnection();
  		try {
  			prepareStatement = connection.prepareStatement("select count(*) rowCount from tb_users");
  			rs = prepareStatement.executeQuery();
  			if (rs.next()) {
  				return rs.getInt("rowCount");
  			}
  		} catch (SQLException e) {
  			// TODO Auto-generated catch block
  			e.printStackTrace();
  		}finally {
  			DBUtil.close(connection, prepareStatement, rs);
  		}
  		return -1;
  	}
  	//分页查询
  	public List<Users> paging(int pageNow,int pageSize) {
  		List<Users> list = new ArrayList<Users>();
  		connection = DBUtil.getConnection();
  		try {
  			prepareStatement = connection.prepareStatement("select * from tb_users limit ?,?");
  			prepareStatement.setInt(1, pageSize*(pageNow-1));
  			prepareStatement.setInt(2, pageSize);
  			rs = prepareStatement.executeQuery();
  			while (rs.next()) {
  				Users users = new Users();
  				users.setId(rs.getInt("id"));
  				users.setDepID(rs.getInt("depID"));
  				users.setUserName(rs.getString("userName"));
  				users.setUserPwd(rs.getString("userPwd"));
  				users.setUserCode(rs.getString("userCode"));
  				users.setUserSex(rs.getString("userSex"));
  				users.setUserAge(rs.getInt("userAge"));
  				users.setUserPower(rs.getInt("userPower"));
  				list.add(users);
  			}
  		} catch (SQLException e) {
  			// TODO Auto-generated catch block
  			e.printStackTrace();
  		}finally {
  			DBUtil.close(connection, prepareStatement, rs);
  		}
  		return list;
  	}
  	//增加用户
  	//删除用户
  	//修改用户
  	//删除用户
  }
  
  ```

- userService

  ```java
  package com.aishang.service;
  
  import java.util.List;
  
  import com.aishang.dao.UserDao;
  import com.aishang.domain.Users;
  
  public class UserService {
  	private UserDao userDao = new UserDao();
  
  	// 验证用户
  	public boolean checkUser(Users users) {
  		return userDao.checkUser(users);
  	}
  
  	// 获取总记录数
  	public int getRowCount() {
  		return userDao.getRowCount();
  	}
  
  	// 总页数
  	public int getPageCount(int pageSize) {
  		return getRowCount() % pageSize == 0 ? getRowCount() / pageSize : getRowCount() / pageSize + 1;
  	}
  
  	// 分页查询
  	public List<Users> paging(int pageNow, int pageSize) {
  		return userDao.paging(pageNow, pageSize);
  	}
  	
  	//显示5页,并且始终居中
  	public int[] showFivePage(int pageNow,int pageCount) {
  		int start = 1;
  		int end = 5;
  		if(pageNow>3) {
  			start = pageNow-2;
  			if (pageNow>pageCount-1) {
  				end = pageNow;
  			}else if (pageNow>pageCount-2) {
  				end = pageNow+1;
  			}else {
  				end = pageNow+2;
  			}
  		}
  		int[] arr = {start,end};
  		return arr;
  	}
  }
  
  ```

- userController

  ```java
  package com.aishang.controller;
  
  import java.io.IOException;
  import java.io.PrintWriter;
  import java.sql.Connection;
  import java.sql.DriverManager;
  import java.sql.PreparedStatement;
  import java.sql.ResultSet;
  import java.sql.SQLException;
  import java.util.List;
  
  import javax.servlet.ServletException;
  import javax.servlet.annotation.WebServlet;
  import javax.servlet.http.Cookie;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import javax.servlet.http.HttpSession;
  
  import com.aishang.domain.Users;
  import com.aishang.service.UserService;
  import com.mysql.cj.jdbc.Driver;
  
  /**
   * Servlet implementation class LoginOk
   */
  @WebServlet("/userController")
  public class UserController extends HttpServlet {
  	private static final long serialVersionUID = 1L;
  
  	/**
  	 * @see HttpServlet#HttpServlet()
  	 */
  	public UserController() {
  		super();
  		// TODO Auto-generated constructor stub
  	}
  
  	/**
  	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse
  	 *      response)
  	 */
  	protected void doGet(HttpServletRequest request, HttpServletResponse response)
  			throws ServletException, IOException {
  		request.setCharacterEncoding("utf-8");
  		response.setContentType("text/html;charset=utf-8");
  		PrintWriter out = response.getWriter();
  
  		String flag = request.getParameter("flag");
  		switch (flag) {
  			case "loginok":
  				System.out.println("loginok");
  				loginOk(request,response);
  				break;
  			case "userList":
  				System.out.println("userlist");
  				userList(request,response);
  		default:
  			break;
  		}
  
  	}
  
  	private void userList(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		int pageNow = 1;
  		if (request.getParameter("pageNow")!=null) {
  			pageNow = Integer.parseInt(request.getParameter("pageNow"));
  		}
  		int pageSize = 5;
  		UserService userService = new UserService();
  		int rowCount = userService.getRowCount();
  		int pageCount = userService.getPageCount(pageSize);
  		List<Users> userList = userService.paging(pageNow, pageSize);
  		for (Users users : userList) {
  			System.out.println(users);
  		}
  		int[] showFivePage = userService.showFivePage(pageNow, pageCount);
  		int start = showFivePage[0];
  		int end = showFivePage[1];
  		request.setAttribute("start", start);
  		request.setAttribute("end", end);
  		request.setAttribute("pageNow", pageNow);
  		request.setAttribute("pageSize", pageSize);
  		request.setAttribute("rowCount", rowCount);
  		request.setAttribute("pageCount", pageCount);
  		request.setAttribute("userList", userList);
  		request.getRequestDispatcher("main").forward(request, response);
  	}
  
  	private void loginOk(HttpServletRequest request, HttpServletResponse response) throws IOException {
  		// TODO Auto-generated method stub
  		String name = request.getParameter("username");
  		String password = request.getParameter("password");
  		String remember = request.getParameter("remember");
  
  		// 登陆成功\创建session
  		Users users = new Users();
  		users.setUserName(name);
  		users.setUserPwd(password);
  		UserService userService = new UserService();
  		boolean checkUser = userService.checkUser(users);
  		if (checkUser) {
  			HttpSession session = request.getSession();
  			session.setAttribute("username", name);
  			Cookie cookie1 = new Cookie("username", name);
  			Cookie cookie2 = new Cookie("password", password);
  			if (remember != null) {
  				cookie1.setMaxAge(60 * 60);
  				cookie2.setMaxAge(60 * 60);
  			} else {
  				cookie1.setMaxAge(0);
  				cookie2.setMaxAge(0);
  
  			}
  			response.addCookie(cookie1);
  			response.addCookie(cookie2);
  			response.sendRedirect("/1901/userController?flag=userList");
  		} else {
  			response.sendRedirect("/1901/login");
  		}
  
  	}
  
  	/**
  	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse
  	 *      response)
  	 */
  	protected void doPost(HttpServletRequest request, HttpServletResponse response)
  			throws ServletException, IOException {
  		// TODO Auto-generated method stub
  		doGet(request, response);
  	}
  
  }
  
  ```

- userdomain

  ```java
  package com.aishang.domain;
  
  public class Users {
  	private int id;
  	private int depID;
  	private String userName;
  	private String userPwd;
  	private String userCode;
  	private String userSex;
  	private int userAge;
  	private int userPower;
  	public int getId() {
  		return id;
  	}
  	public void setId(int id) {
  		this.id = id;
  	}
  	public int getDepID() {
  		return depID;
  	}
  	public void setDepID(int depID) {
  		this.depID = depID;
  	}
  	public String getUserName() {
  		return userName;
  	}
  	public void setUserName(String userName) {
  		this.userName = userName;
  	}
  	public String getUserPwd() {
  		return userPwd;
  	}
  	public void setUserPwd(String userPwd) {
  		this.userPwd = userPwd;
  	}
  	public String getUserCode() {
  		return userCode;
  	}
  	public void setUserCode(String userCode) {
  		this.userCode = userCode;
  	}
  	public String getUserSex() {
  		return userSex;
  	}
  	public void setUserSex(String userSex) {
  		this.userSex = userSex;
  	}
  	public int getUserAge() {
  		return userAge;
  	}
  	public void setUserAge(int userAge) {
  		this.userAge = userAge;
  	}
  	public int getUserPower() {
  		return userPower;
  	}
  	public void setUserPower(int userPower) {
  		this.userPower = userPower;
  	}
  	@Override
  	public String toString() {
  		return "Users [id=" + id + ", depID=" + depID + ", userName=" + userName + ", userPwd=" + userPwd
  				+ ", userCode=" + userCode + ", userSex=" + userSex + ", userAge=" + userAge + ", userPower="
  				+ userPower + "]";
  	}
  }
  
  ```

- util

  ```java
  package com.aishang.util;
  
  import java.sql.Connection;
  import java.sql.DriverManager;
  import java.sql.PreparedStatement;
  import java.sql.ResultSet;
  import java.sql.SQLException;
  
  
  public class DBUtil {
  	static Connection connection ;
  	static PreparedStatement PrepareStatement ;
  	static ResultSet rs ;
  	static {
  		try {
  			Class.forName("com.mysql.cj.jdbc.Driver");
  		} catch (ClassNotFoundException e) {
  			// TODO Auto-generated catch block
  			e.printStackTrace();
  		}
  	}
  	
  	public static Connection getConnection() {
  		try {
  			connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/1901", "root", "password");
  		} catch (SQLException e) {
  			// TODO Auto-generated catch block
  			e.printStackTrace();
  		}
  		return connection;
  	}
  	public static void close(Connection connection,PreparedStatement preparedStatement,ResultSet rs) {
  		try {
  			if (rs!=null) {
  				rs.close();
  			}
  			if (preparedStatement!=null) {
  				preparedStatement.close();
  			}
  			if (connection!=null) {
  				connection.close();
  			}
  		} catch (Exception e) {
  			// TODO: handle exception
  		}
  	}
  }
  
  ```

- view

  - login

  ```java
  package com.aishang.view;
  
  import java.io.IOException;
  import java.io.PrintWriter;
  
  import javax.servlet.ServletException;
  import javax.servlet.annotation.WebServlet;
  import javax.servlet.http.Cookie;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  
  /**
   * Servlet implementation class Login
   */
  @WebServlet("/login")
  public class Login extends HttpServlet {
  	private static final long serialVersionUID = 1L;
         
      /**
       * @see HttpServlet#HttpServlet()
       */
      public Login() {
          super();
          // TODO Auto-generated constructor stub
      }
  
  	/**
  	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
  	 */
  	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		request.setCharacterEncoding("utf-8");
  		response.setContentType("text/html;charset=utf-8");
  		PrintWriter out = response.getWriter();
  		Cookie[] cookies = request.getCookies();
  		String name = "";
  		String password = "";
  		String checked = "";
  		if (cookies!=null) {
  			for (Cookie cookie : cookies) {
  				if ("username".equals(cookie.getName())) {
  					name = cookie.getValue();
  					checked = "checked='checked'";
  				}
  				if ("password".equals(password)) {
  					password = cookie.getValue();
  				}
  			}
  		}
  		
  		out.println("<!DOCTYPE html>");
  		out.println("<html>");
  		out.println("<head>");
  		out.println("	<title></title>");
  		out.println("</head>");
  		out.println("<body>");
  		out.println("	<center>");
  		out.println("	<form action='/1901/userController?flag=loginok' method='post'>");
  		out.println("		<table>");
  		out.println("			<tr>");
  		out.println("				<td>");
  		out.println("					用户名");
  		out.println("				</td>");
  		out.println("				<td>");
  		out.println("					<input type='text' name='username' value='"+name+"'>");
  		out.println("				</td>");
  		out.println("			</tr>");
  		out.println("			<tr>");
  		out.println("				<td>");
  		out.println("					密码");
  		out.println("				</td>");
  		out.println("				<td>");
  		out.println("					<input type='text' name='password' value='"+password+"'>");
  		out.println("				</td>");
  		out.println("			</tr>");
  		out.println("			<tr>");
  		out.println("				<td>");
  		out.println("					记住我");
  		out.println("				</td>");
  		out.println("				<td>");
  		out.println("					<input type='checkbox' name='remember' "+checked+">");
  		out.println("				</td>");
  		out.println("			</tr>");
  		out.println("			<tr>");
  		out.println("				<td>");
  		out.println("					");
  		out.println("				</td>");
  		out.println("				<td>");
  		out.println("					<input type='submit' value='登录'>");
  		out.println("				</td>");
  		out.println("			</tr>");
  		out.println("		</table>");
  		out.println("	</form>");
  		out.println("	</center>");
  		out.println("</body>");
  		out.println("</html>");
  	}
  
  	/**
  	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
  	 */
  	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  		// TODO Auto-generated method stub
  		doGet(request, response);
  	}
  
  }
  
  ```

  - main

  ```java
  package com.aishang.view;
  
  import java.io.IOException;
  import java.io.PrintWriter;
  import java.sql.Connection;
  import java.sql.DriverManager;
  import java.sql.PreparedStatement;
  import java.sql.ResultSet;
  import java.sql.SQLException;
  import java.util.ArrayList;
  import java.util.List;
  
  import javax.servlet.ServletException;
  import javax.servlet.annotation.WebServlet;
  import javax.servlet.http.Cookie;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import javax.servlet.http.HttpSession;
  
  import com.aishang.domain.Users;
  
  /**
   * Servlet implementation class Main
   */
  @WebServlet("/main")
  public class Main extends HttpServlet {
  	private static final long serialVersionUID = 1L;
  
  	/**
  	 * @see HttpServlet#HttpServlet()
  	 */
  	public Main() {
  		super();
  		// TODO Auto-generated constructor stub
  	}
  
  	/**
  	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse
  	 *      response)
  	 */
  	protected void doGet(HttpServletRequest request, HttpServletResponse response)
  			throws ServletException, IOException {
  		request.setCharacterEncoding("utf-8");
  		response.setContentType("text/html;charset=utf-8");
  		PrintWriter out = response.getWriter();
  		HttpSession session = request.getSession();
  		String name = (String) session.getAttribute("username");
  		if (name == null) {
  			response.sendRedirect("/1901/login");
  			return;
  		}
  		Integer pageNow = (Integer) request.getAttribute("pageNow");
  		Integer pageSize = (Integer) request.getAttribute("pageSize");
  		Integer rowCount = (Integer) request.getAttribute("rowCount");
  		Integer pageCount = (Integer) request.getAttribute("pageCount");
  		List<Users> userList = (List<Users>) request.getAttribute("userList");
  		out.println("<h1>欢迎登陆" + name + "</h1>");
  
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
  		out.println("				<td>部门</td>");
  		out.println("				<td>用户名</td>");
  		out.println("				<td>密码</td>");
  		out.println("				<td>工号</td>");
  		out.println("				<td>性别</td>");
  		out.println("				<td>年龄</td>");
  		out.println("				<td>权限</td>");
  		out.println("			</tr>");
  		for (Users users : userList) {
  			out.println("			<tr>");
  			out.println("				<td>" + users.getId() + "</td>");
  			out.println("				<td>" + users.getDepID() + "</td>");
  			out.println("				<td>" + users.getUserName() + "</td>");
  			out.println("				<td>" + users.getUserPwd() + "</td>");
  			out.println("				<td>" + users.getUserCode() + "</td>");
  			out.println("				<td>" + users.getUserSex() + "</td>");
  			out.println("				<td>" + users.getUserAge() + "</td>");
  			out.println("				<td>" + users.getUserPower() + "</td>");
  			out.println("			</tr>");
  		}
  
  		out.println("		</table>");
  
  		pageCount = rowCount % pageSize == 0 ? rowCount / pageSize : rowCount / pageSize + 1;
  		if (pageNow != 1) {
  			out.println("<a href='/1901/userController?flag=userList&pageNow=1'>首页</a>");
  			out.println("<a href='/1901/userController?flag=userList&pageNow=" + (pageNow - 1) + "'>上一页</a>");
  		}
  		int start = (int) request.getAttribute("start");
  		int end = (int) request.getAttribute("end");
  
  		for (int i = start; i <= end; i++) {
  			if (pageNow == i) {
  				out.println("<a href='/1901/userController?flag=userList&pageNow=" + i + "'><font style='color:red'>[" + i + "]</font></a>");
  			} else {
  				out.println("<a href='/1901/userController?flag=userList&pageNow=" + i + "'>[" + i + "]</a>");
  			}
  
  		}
  		if (pageNow != pageCount) {
  			out.println("<a href='/1901/userController?flag=userList&pageNow=" + (pageNow + 1) + "'>下一页</a>");
  			out.println("<a href='/1901/userController?flag=userList&pageNow=" + pageCount + "'>尾页</a>");
  		}
  		out.println("	</center>");
  		out.println("</body>");
  		out.println("</html>");
  
  	}
  
  	/**
  	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse
  	 *      response)
  	 */
  	protected void doPost(HttpServletRequest request, HttpServletResponse response)
  			throws ServletException, IOException {
  		// TODO Auto-generated method stub
  		doGet(request, response);
  	}
  
  }
  
  ```

  用户显示部门查询语句

  ```sql
  select users.id,dep.depName,userName,userPwd,userCode,userSex,userAge,userPower from  tb_users users,tb_department dep where users.depID = dep.id limit ?,?
  ```

  - 显示部门名称

  ```java
  for (Users users : userList) {
  			out.println("			<tr>");
  			out.println("				<td>" + users.getId() + "</td>");
  			for (Department department : depList) {
  				if (users.getDepID() == department.getId()) {
  					out.println("				<td>" + department.getDepName() + "</td>");
  					break;
  				}
  			}
  		
  			out.println("				<td>" + users.getUserName() + "</td>");
  			out.println("				<td>" + users.getUserPwd() + "</td>");
  			out.println("				<td>" + users.getUserCode() + "</td>");
  			out.println("				<td>" + users.getUserSex() + "</td>");
  			out.println("				<td>" + users.getUserAge() + "</td>");
  			out.println("				<td>" + users.getUserPower() + "</td>");
  			out.println("			</tr>");
  		}
  ```

  

### 四、添加用户模块

![image-20190404134945154](https://ws4.sinaimg.cn/large/006tKfTcly1g1qk08pn5aj30d003z74a.jpg)

提交进行非空验证



### AddUserView

```java
out.println("<html>");
		out.println("<head>");
		out.println("	<title></title>");
		out.println("</head>");
		out.println("<body>");
		out.println("<center>");
		out.println("	<form action='/1901/userController?flag=checkAddUser' method='post'>");
		out.println("		<table>");
		out.println("			<tr>");
		out.println("				<td>");
		out.println("					用户名");
		out.println("				</td>");
		out.println("				<td>");
		out.println("					<input type='text' name='username'>");
		out.println("				</td>");
		out.println("				<td>");
		out.println("					<span></span>");
		out.println("				</td>");
		out.println("			</tr>");
		out.println("			<tr>");
		out.println("				<td>");
		out.println("					密码");
		out.println("				</td>");
		out.println("				<td>");
		out.println("					<input type='password' name='password'>");
		out.println("				</td>");
		out.println("				<td>");
		
		out.println("					<span></span>");
		out.println("				</td>");
		out.println("			</tr>");
		out.println("			<tr>");
		out.println("				<td>");
		out.println("					部门");
		out.println("				</td>");
		out.println("				<td>");
		out.println("					<select name='deps'>");
		out.println("						<option value='0'>请选择</option>");
		out.println("					</select>");
		out.println("				</td>");
		out.println("				<td>");
		
		out.println("					<span></span>");
		out.println("				</td>");
		out.println("			</tr>");
		out.println("			<tr>");
		out.println("				<td>");
		out.println("					工号");
		out.println("				</td>");
		out.println("				<td>");
		out.println("					<input type='text' name='userCode'>");
		out.println("				</td>");
		out.println("				<td>");
		
		out.println("					<span></span>");
		out.println("				</td>");
		out.println("			</tr>");
		out.println("			<tr>");
		out.println("				<td>");
		out.println("					性别");
		out.println("				</td>");
		out.println("				<td>");
		out.println("					<input type='radio' name='userSex' value='男' checked='checked'>男");
		out.println("					<input type='radio' name='userSex' value='女'>女");
		out.println("				</td>");
		out.println("			</tr>");
		out.println("			<tr>");
		out.println("				<td>");
		out.println("					年龄");
		out.println("				</td>");
		out.println("				<td>");
		out.println("					<input type='number' name='userAge' min=1 max=120>");
		out.println("					");
		out.println("				</td>");
		out.println("			</tr>");
		out.println("			<tr>");
		out.println("				<td>");
		out.println("					权限");
		out.println("				</td>");
		out.println("				<td>");
		out.println("					<input type='radio' name='userPower' value='0' checked='checked'>普通用户");
		out.println("					<input type='radio' name='userPower' value='1'>管理员");
		out.println("					");
		out.println("				</td>");
		out.println("			</tr>");
		out.println("			<tr>");
		out.println("				<td>");
		out.println("					");
		out.println("				</td>");
		out.println("				<td>");
		out.println("					<input type='submit' value='添加'>");
		out.println("					");
		out.println("				</td>");
		out.println("			</tr>");
		out.println("		</table>");
		out.println("	</form>");
		out.println("</center>");
		out.println("</body>");
		out.println("</html>");
```

### Main

```java
out.println("<!DOCTYPE html>");
		out.println("<html>");
		out.println("<head>");
		out.println("	<title>");
		out.println("	</title>");
		out.println("</head>");
		out.println("<body>");
		out.println("	<center>");
		out.println("<a href='/001/userController?flag=addUser'>添加用户</a>");
		
		

		
		out.println("			<table border='1'>");
		out.println("				<tr>");
		out.println("					<td>编号</td>");
		out.println("					<td>部门</td>");
		out.println("					<td>用户名</td>");
		out.println("					<td>密码</td>");
		out.println("					<td>工号</td>");
		out.println("					<td>性别</td>");
		out.println("					<td>年龄</td>");
		out.println("					<td>权限</td>");
		out.println("				</tr>");
		
		for (User user : userList) {
			out.println("				<tr>");
			out.println("					<td>" + user.getId() + "</td>");
			
			out.println("					<td>" +user.getDepID()+ "</td>");

			out.println("					<td>" + user.getUserName() + "</td>");
			out.println("					<td>" + user.getUserPwd() + "</td>");
			out.println("					<td>" + user.getUserCode() + "</td>");
			out.println("					<td>" + user.getUserSex() + "</td>");
			out.println("					<td>" + user.getUserAge() + "</td>");
			out.println("					<td>" + user.getUserPower() + "</td>");
			out.println("				</tr>");
		}
		out.println("			</table>");
		out.println("		</form>");
		if (pageNow != 1) {
			out.println("<a href='/001/userController?flag=userList&pageNow=1'>【首页】</a>");
			out.println("<a href='/001/userController?flag=userList&pageNow=" + (pageNow - 1) + "'>【上一页】</a>");
		}

		for (int i = 1; i <= pageCount; i++) {
			if (pageNow == i) {
				out.println("<a href='/001/userController?flag=userList&pageNow=" + i + "'><font style='color;red'>【"
						+ i + "】</font></a>");
			} else {
				out.println("<a href='/001/userController?flag=userList&pageNow=" + i + "'>【" + i + "】</a>");
			}
		}
		if (pageNow != pageCount) {
			out.println("<a href='/001/userController?flag=userList&pageNow=" + (pageNow + 1) + "'>【下一页】</a>");
			out.println("<a href='/001/userController?flag=userList&pageNow=" + pageCount + "'>【尾页】</a>");
		}

		out.println("	</center>");
		out.println("</body>");
		out.println("</html>");
```

### 
