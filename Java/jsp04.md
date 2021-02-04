一、获取元素

事件

id

class

name

子类

父类

改变样式

获取|设置值

添加html

添加文本

改变属性



```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
   <script src="https://cdn.bootcss.com/jquery/3.1.1/jquery.min.js"></script>
   <script>
      $(function(){
          $('#in1').click(function(){
            alert($('.in2').val())
            alert($('[name="in3"]').val())
            alert($('#box #in4').val())
            $('#in4').parent().css('backgroundColor','red')
            $('#in4').val('我是第四个input')
            $('#span').html('<a href="#">点我</a>')
            $('#span1').text('文本')
            $('img').attr('src','https://ws1.sinaimg.cn/large/006tNc79ly1fzdx4d9w7hj30pi0cnaho.jpg')
          })
      })
   </script>
</head>
<body>
    <div id='box' >
        <input type="text" value='input1' id="in1" name='in1' class='in1'>
        <input type="text" value='input2' id="in2" name='in2' class='in2'>
        <input type="text" value='input3' id="in3" name='in3'>
        <input type="text" value='input4' id="in4" name='in4'>
        <span id='span'></span>
        <span id='span1'></span>
        <img src='https://ws2.sinaimg.cn/large/006tNc79ly1g26gbdx9wmj30ku0a4aav.jpg'/>
    </div>

</body>
</html>
```



二、ajax验证

```html
<center>
		<form action="/1901/ajax" method="post" onsubmit="return check()">
			<table>
				<tr>
					<td>姓名</td>
					<td><input type="text" name="username" id="username"><span
						id='errName'></span></td>
				</tr>
				<tr>
					<td>密码</td>
					<td><input type="password" name="password" id="password"><span
						id='errpassword'></td>
				</tr>
				<tr>
					<td>性别</td>
					<td><input type="radio" name="sex" value="男" checked>男
						<input type="radio" name="sex" value="女">女</td>
				</tr>
				<tr>
					<td>部门</td>
					<td><select name="dep" id="dep">
							<option value="0">请选择</option>
							<option value="1">市场部</option>
					</select></td>
				</tr>
				<tr>
					<td>年龄</td>
					<td><input type="number" name="age" id="age"></td>
				</tr>
				<tr>
					<td></td>
					<td><input type='submit' value='注册' /></td>
				</tr>
			</table>
		</form>
	</center>
```

```js
$.ajax({
   type: "POST",
   url: "some.php",
   data: "name=John&location=Boston",
   success: function(msg){
     alert( "Data Saved: " + msg );
   }
});
```



三、高仿验证码

```java
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
   <script src="https://cdn.bootcss.com/jquery/3.1.1/jquery.min.js"></script>
   <script>
      $(function(){
          $('#btn').click(function(){
            $.ajax({
              url:'${pageContext.request.contextPath}/ajaxDemo',
              dataType:"text",
              success:function(data){
                console.log(data)
                $('img').attr('src','${pageContext.request.contextPath}'+data);
                $('img').attr('width','500px');
              }
            })
          })
      })
   </script>
</head>
<body>
<img src="/1901/images/avtar.png"  alt="">
<button id='btn'>看不清换一张</button>

</body>
</html>
```



四、ajax删除

```java
function del(id) {
		 $.ajax({
			 type:'get',
             url:'${pageContext.request.contextPath}/ajaxDemo?id='+id,
             dataType:"text",
             success:function(data){
               console.log(data)
             	/* window.location.reload(); */
             	$('#'+id).remove();
             }
           })
	}

简写

function del(id) {
		 $.ajax({
             url:'${pageContext.request.contextPath}/ajaxDemo?id='+id,
             success:function(data){
               console.log(data)
             	/* window.location.reload(); */
             	$('#'+id).remove();
             }
           })
	}
```

判断checkbox是否选中

```js
$("input[type='checkbox']").is(':checked')返回的是boolean

	function delBatch(){
		if($("input[type='checkbox']").is(':checked')){
			return true;
		}
		return false;
	}
```



```js
$(function() {
	$("#form").submit(function(){
		$.post("/1907s/userController?flag=ajax",$(this).serialize(),function(data){
			console.log(data);
		})
	})
})
```

![image-20191018123811818](/Users/liujiang/Documents/Typora/imgs/image-20191018123811818.png)

```js
	$.get("/1907s/userController?flag=ajax",{username:$("#username").val()},function(data){
			console.log(data);
		})
```

```java
private void ajax(HttpServletRequest req, HttpServletResponse resp) throws IOException {
		String username = req.getParameter("username");
		String password = req.getParameter("password");
		username = new String(username.getBytes("iso-8859-1"),"utf-8");
		System.out.println(username+password);
		
		
	}
```
