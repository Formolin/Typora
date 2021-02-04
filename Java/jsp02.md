# 连接池

目的：解决建立数据库连接（Connection）耗费资源和时间很多的问题，提高性能。

原理：应用程序加载的时候产生一个连接池，连接池里事先先产生若干个连接，每次用连接的时候不是用DriverManger.getConnection()实例化Connection对象，而是从已经有若干连接对象实例的连接池中获取Connection对象，用完后将这个Connection交还给连接池，以便供后面的访问使用

### 模拟连接池

```java
package com.aishang.util;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.LinkedList;

import org.junit.Test;

import com.sun.org.apache.bcel.internal.generic.Select;

import jdk.internal.dynalink.beans.StaticClass;

public class MyDataSource {
	// 创建连接池
	static LinkedList<Connection> pool = new LinkedList<Connection>();
	static {
		for (int i = 0; i < 10; i++) {
			// 初始化连接
			Connection connection = DBUtil.getConnection();
			// 添加到连接池
			pool.add(connection);
		}
	}

//	通过连接池获取连接
	public static Connection getConnection() {
		Connection connection = null;
		if (pool.size() > 0) {// 说明池子里还有连接
			connection = pool.removeFirst();
			return connection;
		} else {
			throw new RuntimeException("服务器正忙.......");
			// 服务器正忙的解决思路：
			// 1.等待
			// 2.等待超时新创建一个连接用DBUtil产生，用完之后，不用交回池，真的close()
		}

	}

//	释放连接，归还池子
	public static void release(Connection conn) {
		pool.addLast(conn);
	}

//	测试
	@Test
	public void fun() {
		Connection connection = MyDataSource.getConnection();
		PreparedStatement prepareStatement = null;
		ResultSet rs = null;
		try {
			prepareStatement = connection.prepareStatement("select * from tb_users");
			rs = prepareStatement.executeQuery();
			while (rs.next()) {
				System.out.println(rs.getString("userName"));
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
			MyDataSource.release(connection);
			DBUtil.close(null, prepareStatement, rs);
		}

	}
}

```

### 商业开发中的连接池

- DBCP
- c3p0
  - c3p0-config.xml 放在src下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
  <default-config>
	<property name="driverClass">com.mysql.jdbc.Driver</property>
	<property name="jdbcUrl">jdbc:mysql://localhost:3306/1901</property>
	<property name="user">root</property>
	<property name="password">password</property>
    <property name="initialPoolSize">10</property>
    <property name="maxIdleTime">30</property>
    <property name="maxPoolSize">100</property>
    <property name="minPoolSize">10</property>

  </default-config>
</c3p0-config>
```

```java
public class C3P0Util {
	//得到一个数据源
	private static DataSource dataSource = new ComboPooledDataSource();
	
	//从数据源中得到一个连接对象
	public static Connection getConnection(){
	try {
			return dataSource.getConnection();
		} catch (SQLException e) {
			throw new RuntimeException("服务器错误");
		}
	}
public static void release(Connection conn,Statement stmt,ResultSet rs){
		//关闭资源
		if(rs!=null){
		  try {
			rs.close();
		  } catch (Exception e) {
			e.printStackTrace();
		  }

		}
		if(stmt!=null){
		  try {
			stmt.close();
		  } catch (Exception e) {
			e.printStackTrace();
		  }

		}
		if(conn!=null){
		  try {
			conn.close();  //关闭,其实是交还给池子来处理
		  } catch (Exception e) {
			e.printStackTrace();
		  }

		}
	}
}
```

```java
package com.aishang.util;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import javax.sql.DataSource;

import org.junit.jupiter.api.Test;

import com.mchange.v2.c3p0.ComboPooledDataSource;

public class MU {
	private static DataSource dataSource = new ComboPooledDataSource();

	public static Connection getConnection() {
		try {
			
			Connection connection = dataSource.getConnection();
			return connection;
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return null;
	}

	public static void relase(Connection connection, PreparedStatement preparedStatement, ResultSet rs) {
		// 关闭资源
		if (rs != null) {
			try {
				rs.close();
			} catch (Exception e) {
				e.printStackTrace();
			}

		}
		if (preparedStatement != null) {
			try {
				preparedStatement.close();
			} catch (Exception e) {
				e.printStackTrace();
			}

		}
		if (connection != null) {
			try {
				connection.close(); // 关闭,其实是交还给池子来处理
			} catch (Exception e) {
				e.printStackTrace();
			}

		}

	}
	
	@Test
	public void fun() throws SQLException {
		Connection connection = MU.getConnection();
		PreparedStatement prepareStatement = null;
		ResultSet rs = null;
		try {
			prepareStatement = connection.prepareStatement("select * from tb_users");
			rs = prepareStatement.executeQuery();
			while(rs.next()) {
				System.out.println(rs.getString("userName"));
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally {
			MU.relase(connection, prepareStatement, rs);
		}
		
	
		
	}
}








```



- druid
  - 导包

#### 配置文件

- 在src下创建properties文件

```properties
driverClassName=com.mysql.cj.jdbc.Driver //驱动加载 
url=jdbc:mysql://localhost:3306/1901 //连接数据库
username=root //连接数据库的用户名 
password=password //连接数据库的密码。 
filters=stat //属性类型的字符串，通过别名的方式配置扩展插件， 监控统计用的stat 日志用log4j 防御sql注入:wall 
initialSize=20 //初始化时池中建立的物理连接个数。 
maxActive=30 //最大的可活跃的连接池数量 
minIdle=5 //最小连接数量
maxWait=60000 //获取连接时最大等待时间，单位毫秒，超过连接就会失效。配置了maxWait之后，缺省启用公平锁，并发效率会有所下降， 如果需要可以通过配置useUnfairLock属性为true使用非公平锁。 
timeBetweenEvictionRunsMillis=60000 // 连接回收器的运行周期时间，时间到了清理池中空闲的连接，testWhileIdle根据这个判断 
minEvictableIdleTimeMillis=300000 
validationQuery=SELECT 1 //用来检测连接是否有效的sql，要求是一个查询语句。 
testWhileIdle=true //建议配置为true，不影响性能，并且保证安全性。 申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis， 执行validationQuery检测连接是否有效。 
testOnBorrow=false //申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。设置为false 
testOnReturn=false //归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能,设置为flase 
poolPreparedStatements=false //是否缓存preparedStatement，也就是PSCache。 
maxPoolPreparedStatementPerConnectionSize=200 // 池中能够缓冲的preparedStatements语句数量	
```

```properties
driverClassName=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/1901
username=root
password=password
initialSize=20
maxActive=30
minIdle=5
maxWait=60000
```

- 创建DruidUtil

```java
package com.aishang.util;

import java.io.IOException;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Properties;

import javax.sql.DataSource;
import javax.sql.PooledConnection;

import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.pool.DruidDataSourceFactory;
import com.alibaba.druid.pool.DruidPooledConnection;

public class DBUtil {
	private static DruidDataSource ds;
    //创建连接池
	private static DruidPooledConnection connection;

	static {
		Properties properties = new Properties();
		try {
			properties.load(DBUtil.class.getClassLoader().getResourceAsStream("druid.properties"));
			ds = (DruidDataSource) DruidDataSourceFactory.createDataSource(properties);
			connection = ds.getConnection();
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	//从连接池中获取连接，不用关闭，自动归还
	public static Connection getConnection() {
			return connection.getConnection();
	}
    	public static void close(Connection connection, PreparedStatement preparedStatement, ResultSet rs) {

//		if (rs != null) {
//			try {
//				rs.close();
//			} catch (SQLException e) {
//				// TODO Auto-generated catch block
//				e.printStackTrace();
//			}
//		}
//		if (preparedStatement != null) {
//			try {
//				preparedStatement.close();
//			} catch (SQLException e) {
//				// TODO Auto-generated catch block
//				e.printStackTrace();
//			}
//		}
//		if (connection != null) {
//			try {
//				connection.close();
//			} catch (SQLException e) {
//				// TODO Auto-generated catch block
//				e.printStackTrace();
//			}
//		}
	}

}

```

- web.xml 可视化配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://java.sun.com/xml/ns/javaee"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
	id="WebApp_ID" version="3.0">

	<servlet>
		<servlet-name>DruidStatView</servlet-name>
		<servlet-class>com.alibaba.druid.support.http.StatViewServlet</servlet-class>
		<init-param>
        <!-- 用户名 -->
        <param-name>loginUsername</param-name>
        <param-value>root</param-value>
    </init-param>
    <init-param>
        <!-- 密码 -->
        <param-name>loginPassword</param-name>
        <param-value>root</param-value>
    </init-param>
		
	</servlet>
	<servlet-mapping>
		<servlet-name>DruidStatView</servlet-name>
		<url-pattern>/druid/*</url-pattern>
	</servlet-mapping>
	

</web-app>


```

http://localhost:8080/1904/druid/index.html

# 过滤器

拦截所有，只放行登录页和loginok

```java
package com.aishang.util;

import java.io.IOException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import com.aishang.dao.UserDao;
import com.aishang.domain.User;
@WebFilter("/*")
public class MyFilter implements Filter {

	public void destroy() {
		// TODO Auto-generated method stub
		System.out.println("过滤器销毁");
	}

	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		System.out.println("过滤器开始");
		HttpServletRequest req = (HttpServletRequest) request;
		HttpServletResponse resp = (HttpServletResponse) response;
		int uri = req.getRequestURI().indexOf("login");
		String loginOK = req.getParameter("flag");
		if (uri>0||"loginOK".equals(loginOK)) {
			System.out.println("fangxing");
		}else {
			User user = (User) req.getSession().getAttribute("user");
			if (user==null) {
				req.getRequestDispatcher("login").forward(request, response);
				return;
			}
		}
		chain.doFilter(request, response);

		
	}

	public void init(FilterConfig fConfig) throws ServletException {
		// TODO Auto-generated method stub
		System.out.println("过滤器init");
	}

}

```

改进

```java
package com.aishang.util;

import java.io.IOException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import com.aishang.dao.UserDao;
import com.aishang.domain.User;

public class MyFilter implements Filter {

	public void destroy() {
		// TODO Auto-generated method stub
		System.out.println("过滤器销毁");
	}

	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		System.out.println("过滤器开始");
		HttpServletRequest req = (HttpServletRequest) request;
		HttpServletResponse resp = (HttpServletResponse) response;
		int uri = req.getRequestURI().indexOf("login");
		String loginOK = req.getParameter("flag");
		if (!(uri>0||"loginOK".equals(loginOK))) {
			User user = (User) req.getSession().getAttribute("user");
			if (user==null) {
				req.getRequestDispatcher("login").forward(request, response);
        //resp.sendRedirect("/1906t/login");都可以
				return;
			}
		}
		chain.doFilter(request, response);

		
	}

	public void init(FilterConfig fConfig) throws ServletException {
		// TODO Auto-generated method stub
		System.out.println("过滤器init");
	}

}

```

jsp

```java
package com.aishang.util;

import java.io.IOException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import com.aishang.dao.UserDao;
import com.aishang.domain.User;

public class MyFilter implements Filter {

	public void destroy() {
		// TODO Auto-generated method stub
		System.out.println("过滤器销毁");
	}

	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		System.out.println("过滤器开始");
		HttpServletRequest req = (HttpServletRequest) request;
		HttpServletResponse resp = (HttpServletResponse) response;
		int uri = req.getRequestURI().indexOf("login");
		int css = req.getRequestURI().indexOf("css");
		int images = req.getRequestURI().indexOf("images");
		String loginOK = req.getParameter("flag");
		if (!(uri>0||"loginOK".equals(loginOK)||css>0||images>0)) {
			User user = (User) req.getSession().getAttribute("user");
			if (user==null) {
				resp.sendRedirect("/1906t/login.jsp");
				return;
			}
		}
		chain.doFilter(request, response);

		
	}

	public void init(FilterConfig fConfig) throws ServletException {
		// TODO Auto-generated method stub
		System.out.println("过滤器init");
	}

}

```

# MD5

导包commons-codec-1.10.jar



### DigestUtils

- 添加

```java
String password = request.getParameter("password");
		String username = request.getParameter("userName");
		String md5Pass=DigestUtils.md5Hex(password);
		System.out.println(md5Pass);
		UserDao userDao = new UserDao();
		User user = new User();
		user.setUserName(username);
		user.setUserPwd(md5Pass);
		user.setDepID(1);
		user.setUserAge(18);
		user.setUserCode("123");
		user.setUserPower(1);
		user.setUserSex("男");
		userDao.addUser(user);
```

- 登陆

```java
		String password = request.getParameter("password");
		String username = request.getParameter("userName");
		String md5Pass=DigestUtils.md5Hex(password);
		System.out.println(md5Pass);
		UserDao userDao = new UserDao();
		User checkUser = userDao.checkUser(username, md5Pass);
		if (checkUser!=null) {
			System.out.println("登陆成功");
		}else {
			System.out.println("登陆失败");
		}
```

