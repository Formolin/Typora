# Servlet第七课删除与ServletContext

### 一、删除

- 传递多个参数
  - userid、pageNow、searchName、depID等

```java
private void del(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		// TODO Auto-generated method stub
		int id = 0;
		if (req.getParameter("id")!=null) {
			id = Integer.parseInt(req.getParameter("id"));
		}
		UserService userService = new UserService();
		userService.del(id);
		Integer pageNow = Integer.parseInt(req.getParameter("pageNow"));
		String searchName = req.getParameter("searchName");
		User user = new User();
		user.setUserName(searchName);
		user.setDepID(0);
		int pageCount = userService.getPageCount(5, user);
		if (pageNow>pageCount) {
			pageNow = pageCount;
		}
		resp.sendRedirect("/1902s/userController?flag=userList&pageNow="+pageNow+"&pageCount="+pageCount+"&searchName="+searchName);
	}
```



### 二、批量删除

```java
<input type='checkbox' name='ids' value='"+user.getId()+"'/>
```

```java
String[] ids = request.getParameterValues("ids");
```

```java
//需先删除才可以
//批量删除时，需要重新查询一下rowCount和pageSize
		User user = new User();
		user.setUserName(username);
		user.setDepID(depID);
		int pageSize = 5;
		int rowCount = userService.getRowCount(user);
		pageCount = rowCount%pageSize==0?rowCount/pageSize:rowCount/pageSize+1;
		if (pageCount<pageNow) {
			pageNow = pageCount;
		}
```



### 三、网站计数器

- request一次请求有效

- session一次会话有效

- ServletContext：整个应用期间一直有效

  - 服务器启动就存在，所有用户共享他，就是浏览器都有效

  ```java
  ServletContext application = getServletContext();
  ```

- 演示网站计数器
- F5刷新有bug
- 改造后，如果服务器重启了，那么就应该持久化
  - 当服务器重启时，把当前的记录写到数据库
  - 当启动时，在把数据读回来
  - 涉及servlet生命周期

```java
ServletContext application = getServletContext();
		if (application.getAttribute("visited") == null) {
			application.setAttribute("visited", 1);
		}else {
			Integer visited = (Integer) application.getAttribute("visited");
			visited++;
			application.setAttribute("visited", visited);
		}
		out.println("<h1>访问次数：" + application.getAttribute("visited") + "</h1>");
```

不能放在main页，把他抽取到登陆成功中

```java
@Override
	public void init() throws ServletException {
		int readVisited = VisitedDao.readVisited();
		ServletContext application = getServletContext();
		application.setAttribute("visited", readVisited);
	}
	@Override
	public void destroy() {
		ServletContext application = getServletContext();
		Integer num = (Integer) application.getAttribute("visited");
		VisitedDao.writerVisited(num);
	}
```



### 作业：

- 最后一页删除后跳转到前一页

- 修改

- 新闻列表

- 部门列表

- 个人信息

- crud

  