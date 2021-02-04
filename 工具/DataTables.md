# Datatables

```js
//DataTables-1.10.15
<link
	href='${pageContext.request.contextPath }/datatables/dataTables.bootstrap.min.css'>
	<script type="text/javascript"
	src="${pageContext.request.contextPath }/datatables/jquery-1.11.1.min.js"></script>
<script type="text/javascript"
	src="${pageContext.request.contextPath }/datatables/jquery.dataTables.min.js"></script>
<script type="text/javascript"
	src="${pageContext.request.contextPath }/datatables/dataTables.bootstrap.min.js"></script>
	<script type="text/javascript"
	src="${pageContext.request.contextPath }/js/dt.js"></script>
```

### DT自动请求的参数(Sent parameters)

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
| `columns[i][searchable]`    | [`booleanJS`](http://datatables.net/reference/type/boolean) | 标记列是否能被搜索,为`true`代表可以，否则不可以，这个是由 [`columns.searchableOption` ](http://www.datatables.club/reference/option/columns.searchable.html)控制 |
| `columns[i][orderable]`     | [`booleanJS`](http://datatables.net/reference/type/boolean) | 标记列是否能排序,为 `true`代表可以，否则不可以，这个是由 [`columns.orderableOption` ](http://www.datatables.club/reference/option/columns.orderable.html)控制 |
| `columns[i][search][value]` | [`stringJS`](http://datatables.net/reference/type/string)   | 标记具体列的搜索条件                                         |
| `columns[i][search][regex]` | [`booleanJS`](http://datatables.net/reference/type/boolean) | 特定列的搜索条件是否视为正则表达式， 如果为 `true`代表搜索的值是作为正则表达式处理，为 `false`则不是。 注意：通常在服务器模式下对于大数据不执行这样的正则表达式，但这都是自己决定的 |

`order[i]`和 `columns[i]`参数发送到服务器是数组信息：

- `order[i]` - 是一个数组，定义了多少列要被排序。比如，数组长度为1，那么就 只有一个列被排序，否则就是多个列被排序
- `columns[i]` - 是一个数组，定了这个表格里所有的列

在这两个情况下，

 

```
i
```

is an integer which will change to indicate the array value. In most modern server-side scripting environments this data will automatically be available to you as an array.



### 服务器需要返回的数据(Returned data)

一旦 DataTables 发送了请求，上面的参数就会传送给服务器，那么你需要接受到这些参数并做相应的逻辑处理然后按照下面的格式讲组装好的JSON数据返回 （不是每个参数都需要接受处理，根据自己的业务需要）

| 名称              | 类型                                                        | 描述                                                         |
| ----------------- | ----------------------------------------------------------- | ------------------------------------------------------------ |
| `draw`            | [`integerJS`](http://datatables.net/reference/type/integer) | **必要**。上面提到了，Datatables发送的draw是多少那么服务器就返回多少。 这里注意，作者出于安全的考虑，强烈要求把这个转换为整形，即数字后再返回，而不是纯粹的接受然后返回，这是 为了防止跨站脚本（XSS）攻击。 |
| `recordsTotal`    | [`integerJS`](http://datatables.net/reference/type/integer) | **必要**。即没有过滤的记录数（数据库里总共记录数）           |
| `recordsFiltered` | [`integerJS`](http://datatables.net/reference/type/integer) | **必要**。过滤后的记录数（如果有接收到前台的过滤条件，则返回的是过滤后的记录数） |
| `data`            | [`arrayType`](https://datatables.net/reference/type/array)  | **必要**。表中中需要显示的数据。这是一个对象数组，也可以只是数组，区别在于 纯数组前台就不需要用 `columns`绑定数据，会自动按照顺序去显示 ，而对象数组则需要使用 `columns`绑定数据才能正常显示。 注意这个 `data`的名称可以由 [`ajaxOption` ](http://www.datatables.club/reference/option/ajax.html)[`ajax不定时一讲` ](http://www.datatables.club/manual/daily/2016/04/18/option-ajax.html)的 [`ajax.dataSrcOption` ](http://www.datatables.club/reference/option/ajax.dataSrc.html)[`ajax.dataSrc 1不定时一讲` ](http://www.datatables.club/manual/daily/2016/04/19/option-ajax-dataSrc1.html)[`ajax.dataSrc 2不定时一讲` ](http://www.datatables.club/manual/daily/2016/04/20/option-ajax-dataSrc2.html)控制 |
| `error`           | [`stringJS`](http://datatables.net/reference/type/string)   | **可选**。你可以定义一个错误来描述服务器出了问题后的友好提示 |

除了上面的返回参数以外你还可以加入下面的参数，来实现对表格数据的自动绑定

| 名称          | 类型                                                      | 描述                                                         |
| ------------- | --------------------------------------------------------- | ------------------------------------------------------------ |
| `DT_RowId`    | [`stringJS`](http://datatables.net/reference/type/string) | 自动绑定到 `tr`节点上                                        |
| `DT_RowClass` | [`stringJS`](http://datatables.net/reference/type/string) | 自动把这个类名添加到 `tr`                                    |
| `DT_RowData`  | [`objectJS`](http://datatables.net/reference/type/object) | 使用 [`jQuery.data()` ](http://api.jquery.com/data/)方法把数据绑定到row中，方便之后用来检索（比如加入一个点击事件） |
| `DT_RowAttr`  | [`objectJS`](http://datatables.net/reference/type/object) | 自动绑定数据到 `tr`上，使用 [`jQuery.attr()` ](http://api.jquery.com/attr/)方法，对象的键用作属性，值用作属性的值。注意这个 需要 `Datatables 1.10.5+`的版本才支持 |

如何使用上面的参数，参考下面展示的 [数据示例](http://www.datatables.club/manual/server-side.html#demodata)

## 特性(Features)

| 名称                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`jQueryUIOption`](http://www.datatables.club/reference/option/jQueryUI.html) | 控制是否使用jquerui的样式（需要引入jqueryui的css）           |
| [`autoWidthOption`](http://www.datatables.club/reference/option/autoWidth.html) | 控制Datatables是否自适应宽度                                 |
| [`infoOption`](http://www.datatables.club/reference/option/info.html) | 控制是否显示表格左下角的信息                                 |
| [`lengthChangeOption`](http://www.datatables.club/reference/option/lengthChange.html) | 是否允许用户改变表格每页显示的记录数                         |
| [`orderingOption`](http://www.datatables.club/reference/option/ordering.html) | 是否允许Datatables开启排序                                   |
| [`pagingOption`](http://www.datatables.club/reference/option/paging.html) | 是否开启本地分页                                             |
| [`processingOption`](http://www.datatables.club/reference/option/processing.html) | 是否显示处理状态(排序的时候，数据很多耗费时间长的话，也会显示这个) |
| [`scrollXOption`](http://www.datatables.club/reference/option/scrollX.html) | 设置水平滚动                                                 |
| [`scrollYOption`](http://www.datatables.club/reference/option/scrollY.html) | 设置垂直滚动                                                 |
| [`searchingOption`](http://www.datatables.club/reference/option/searching.html) | 是否允许Datatables开启本地搜索                               |
| [`serverSideOption`](http://www.datatables.club/reference/option/serverSide.html) | 是否开启服务器模式                                           |
| [`stateSaveOption`](http://www.datatables.club/reference/option/stateSave.html) | 保存状态 - 在页面重新加载的时候恢复状态（页码等内容）        |
| [`deferRenderOption`](http://www.datatables.club/reference/option/deferRender.html) | 控制Datatables的延迟渲染，可以提高初始化的速度               |



## 数据(Data)

| 名称                                                         | 说明                                     |
| ------------------------------------------------------------ | ---------------------------------------- |
| [`ajax.dataOption` ](http://www.datatables.club/reference/option/ajax.data.html)[`ajax.data不定时一讲`](http://www.datatables.club/manual/daily/2016/04/21/option-ajax-data.html) | 增加或修改通过Ajax提交到服务端的请求数据 |
| [`ajax.dataSrcOption` ](http://www.datatables.club/reference/option/ajax.dataSrc.html)[`ajax.dataSrc 1不定时一讲` ](http://www.datatables.club/manual/daily/2016/04/19/option-ajax-dataSrc1.html)[`ajax.dataSrc 2不定时一讲`](http://www.datatables.club/manual/daily/2016/04/20/option-ajax-dataSrc2.html) | 数据属性或操作表数据的方法               |
| [`ajaxOption` ](http://www.datatables.club/reference/option/ajax.html)[`ajax不定时一讲`](http://www.datatables.club/manual/daily/2016/04/18/option-ajax.html) | 从一个ajax数据源读取数据给表格内容       |
| [`dataOption`](http://www.datatables.club/reference/option/data.html) | 用来显示表格的数据                       |

# 案列

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>data tables</title>
<link
	href='${pageContext.request.contextPath }/datatables/dataTables.bootstrap.min.css' rel="stylesheet">
<script type="text/javascript"
	src="${pageContext.request.contextPath }/datatables/jquery-1.11.1.min.js"></script>
<script type="text/javascript"
	src="${pageContext.request.contextPath }/datatables/jquery.dataTables.min.js"></script>
<script type="text/javascript"
	src="${pageContext.request.contextPath }/datatables/dataTables.bootstrap.min.js"></script>
<script type="text/javascript"
	src="${pageContext.request.contextPath }/js/dt.js"></script>
<link
	href='${pageContext.request.contextPath }/bootstrap/css/bootstrap.min.css' rel="stylesheet">
<script type="text/javascript"
	src="${pageContext.request.contextPath }/bootstrap/js/bootstrap.min.js"></script>
</head>
<body>
	<form action='${pageContext.request.contextPath }/delbatch' method='post'>
		<table id="dataTables" class="table table-hover">
			<thead>
				<tr>
					<th><input type="submit" value='批量删除'></input></th>
					<th>ID</th>
					<th>部门</th>
					<th>用户名</th>
					<th>密码</th>
					<th>工号</th>
					<th>性别</th>
					<th>年龄</th>
					<th>权限</th>
					<th>编辑</th>
				</tr>
			</thead>
		</table>
	</form>

</body>
</html>
```

```js
	// 获取路径
	var pathName = window.document.location.pathname;
	// 截取，得到项目名称
	var projectName = pathName
			.substring(0, pathName.substr(1).indexOf('/') + 1);

$(function() {

	$('#dataTables').DataTable({
//		文档中的key是加双引号的
		autoWidth : true,// 控制Datatables是否自适应宽度
		info:true,//控制是否显示表格左下角的信息
		lengthChange:false,//是否允许用户改变表格每页显示的记录数
		ordering:false,//是否开启排序
		paging : true,//是否开启本地分页
		processing:true,//是否显示处理状态(排序的时候，数据很多耗费时间长的话，也会显示这个)
		serverSide:true,//是否开启服务器模式
		searching:false,//是否允许Datatables开启本地搜索
		deferRender:true,//控制Datatables的延迟渲染，可以提高初始化的速度
		ajax:{
			url:projectName+"/dt",
			data:{
				username:"jerry",
			}
		},
		//字段值
		columns: [
			{
				"data":function(row,type,val,meta){
					return "<input type='checkbox' name='ids' value='"+row.id+"'></input>";
				}
			},
	        { "data": "id" },
	        { "data": "depID" },
	        { "data": "userName" },
	        { "data": "userPwd" },
	        { "data": "userCode" },
	        { "data": "userSex" },
	        { "data": "userAge" },
	        { "data": "userPower" },
	        {
				"data":function(row,type,val,meta){
					return "<a href='' >修改</a>&nbsp;&nbsp;&nbsp;"+"<a href='"+projectName+"/del?id="+row.id+"' >删除</a>&nbsp;&nbsp;&nbsp;";
				}
			},
	    ],
	    //显示信息
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
	      //初始化结束后的回调函数
	      "initComplete": function(settings, json) {
	    	//alert( '初始化结束后的调用' );
	      },
	      //表格每次重绘回调函数
	      "drawCallback": function( settings ) {
//	          alert( '表格重绘了' );
	      }
	});
});
//function delBatch() {
//	 var ids = new Array;
//     $("input[name='ids']:checked").each(function() {
//         ids.push($(this).val());
//         n = $(this).parents("tr").index() + 1; // 获取checkbox所在行的顺序
//         $("table#dataTables").find("tr:eq(" + n + ")").remove();
//     });
//
//     $.ajax({
//         url : projectName + "/delbatch",
//         data : "ids=" + ids,
//         type : "post",
//         success : function(data) {
//             dataTable.reloadTable();
//         }
//     });
//}
```

```java
package com.aishang.controller;

import java.io.IOException;
import java.util.Enumeration;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.aishang.domain.User;
import com.aishang.service.UserService;
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;

/**
 * datatables 分页案列
 * @param req
 * @param resp
 * @throws IOException
 */
@WebServlet("/dt")
public class DT extends HttpServlet {
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		request.setCharacterEncoding("utf-8");
		response.setContentType("text/html;charset=utf-8");
		System.out.println("dt");
		String username = request.getParameter("username");
		System.out.println(username);
		//datatables 中 让把draw转换成int类型
		int draw =request.getParameter("draw")==null?0:Integer.parseInt(request.getParameter("draw"));
		int start =request.getParameter("start")==null?0:Integer.parseInt(request.getParameter("start"));
		int length =request.getParameter("length")==null?0:Integer.parseInt(request.getParameter("length"));
		System.out.println("draw="+draw);//1
		System.out.println("start="+start);//0
		System.out.println("length="+length);//10
		
		UserService userService = new UserService();
		User user = new User();
		List<User> userList = userService.page(user,start,length);
		//recordsTotal  没有过滤的记录数（数据库里总共记录数）
		//recordsFiltered 过滤后的记录数（如果有接收到前台的过滤条件，则返回的是过滤后的记录数）
		/*
		 * data
		 * 表中中需要显示的数据。这是一个对象数组，
		 * 也可以只是数组，区别在于 纯数组前台就不需要用 
		 * columns绑定数据，会自动按照顺序去显示 ，
		 * 而对象数组则需要使用 columns绑定数据才能正常显示。
		 * 注意这个 data的名称可以由 ajaxOption
		 * ajax不定时一讲 的 ajax.dataSrcOption
		 * ajax.dataSrc 1
		 * ajax.dataSrc 2
		 * 控制
		 */

		int rowCount = userService.getRowCount(user);
		Map<String, Object> map = new HashMap<String, Object>();
		map.put("draw", draw);
		map.put("recordsTotal", rowCount);
		map.put("recordsFiltered", rowCount);
		map.put("data", userList);
		
		JSONObject json = new JSONObject(map);
		String jsonString = JSON.toJSONString(json);
		System.out.println(jsonString);
		response.getWriter().print(jsonString);
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}

```

```java
package com.aishang.controller;

import java.io.IOException;
import java.util.Enumeration;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.aishang.domain.User;
import com.aishang.service.UserService;
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;

/**
 * datatables 分页案列
 * 
 * @param req
 * @param resp
 * @throws IOException
 */
@WebServlet("/del")
public class Del extends HttpServlet {
	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		request.setCharacterEncoding("utf-8");
		response.setContentType("text/html;charset=utf-8");
		// 删除
		String id = request.getParameter("id");
		if (id != null) {
			System.out.println("id=" + id);
		}
		response.sendRedirect("/1907s/dt.jsp");
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}

```

```java
package com.aishang.controller;

import java.io.IOException;
import java.util.Enumeration;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.aishang.domain.User;
import com.aishang.service.UserService;
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;

/**
 * datatables 分页案列
 * 
 * @param req
 * @param resp
 * @throws IOException
 */
@WebServlet("/delbatch")
public class DelBatch extends HttpServlet {
	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		request.setCharacterEncoding("utf-8");
		response.setContentType("text/html;charset=utf-8");
		System.out.println("delbatch");
		String[] ids = request.getParameterValues("ids");
		if (ids!=null) {
			for (String sid : ids) {
				System.out.println("批量删除的id"+sid);
			}
		}
		response.sendRedirect("/1907s/dt.jsp");
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}

```

