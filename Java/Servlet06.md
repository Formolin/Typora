# Servlet第六课复合查询和分页

### 一、js前端校验

- 减轻服务器压力

- 后端必须校验

  ```java
  out.println("<head>");
  out.println("	<title></title>");
  out.println("	<script src='"+request.getContextPath()+"/js/addUser.js'></script>");
  out.println("</head>");
  ```

  

- 验证-作业

### 二、复合查询

只有两个的复合条件查询就需要4条查询语句

```sql
没有条件=全部查询
select * from tb_users

查询用户名

select * from tb_users where username like ?

查询部门

select * from tb_users where depID = ?

查询部门和用户名

select * from tb_users where username like ? and depID = ?
```

```java
//复合查询
	public List<Users> userSearch(String username, String dep) {
		connection = DBUtil.getConnection();
		List<Users> list = new ArrayList<Users>();
		try {
			String sql = "select * from tb_users where 1=1 ";
			if (username!=null) {
				sql = sql+"and username like '%"+username+"%'";
			}
			if (!"0".equals(dep)) {
				sql = sql + "and depID = "+Integer.parseInt(dep);
			}
			prepareStatement = connection.prepareStatement(sql);
			rs = prepareStatement.executeQuery();
			while(rs.next()) {
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
```

改造上面的代码：

- stringbuffer
- 用问号代替
- 中文乱码



```java
out.println("	<form action='' method='post'>");
		out.println("			<table border='1'>");
		out.println("				<tr>");
		out.println("					<td>用户名</td>");
		out.println("					<td><input type='text' name='searchName'></td>");
		
		out.println("					<td>");
		out.println("						<select name='searchDep'>");
		out.println("							<option value='0'>请选择部门</option>");
		out.println("						</select>");
		out.println("					</td>");
		out.println("	<td><input type='submit' value='查询'/></td>");
		out.println("				</tr>");
		out.println("			</table>");
		out.println("		</form>");
```

