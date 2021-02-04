## 导入 js 库（使用菜鸟教程提供的CDN）

```js
<script src="http://static.runoob.com/assets/jquery-validation-1.14.0/lib/jquery.js"></script>
<script src="http://static.runoob.com/assets/jquery-validation-1.14.0/dist/jquery.validate.min.js"></script>
```

## 默认校验规则

| 序号 | 规则               | 描述                                                         |
| :--- | :----------------- | :----------------------------------------------------------- |
| 1    | required:true      | 必须输入的字段。                                             |
| 2    | remote:"check.php" | 使用 ajax 方法调用 check.php 验证输入值。                    |
| 3    | email:true         | 必须输入正确格式的电子邮件。                                 |
| 4    | url:true           | 必须输入正确格式的网址。                                     |
| 5    | date:true          | 必须输入正确格式的日期。日期校验 ie6 出错，慎用。            |
| 6    | dateISO:true       | 必须输入正确格式的日期（ISO），例如：2009-06-23，1998/01/22。只验证格式，不验证有效性。 |
| 7    | number:true        | 必须输入合法的数字（负数，小数）。                           |
| 8    | digits:true        | 必须输入整数。                                               |
| 9    | creditcard:        | 必须输入合法的信用卡号。                                     |
| 10   | equalTo:"#field"   | 输入值必须和 #field 相同。                                   |
| 11   | accept:            | 输入拥有合法后缀名的字符串（上传文件的后缀）。               |
| 12   | maxlength:5        | 输入长度最多是 5 的字符串（汉字算一个字符）。                |
| 13   | minlength:10       | 输入长度最小是 10 的字符串（汉字算一个字符）。               |
| 14   | rangelength:[5,10] | 输入长度必须介于 5 和 10 之间的字符串（汉字算一个字符）。    |
| 15   | range:[5,10]       | 输入值必须介于 5 和 10 之间。                                |
| 16   | max:5              | 输入值不能大于 5。                                           |
| 17   | min:10             | 输入值不能小于 10。                                          |

## 默认提示

```js
messages: {
    required: "This field is required.",
    remote: "Please fix this field.",
    email: "Please enter a valid email address.",
    url: "Please enter a valid URL.",
    date: "Please enter a valid date.",
    dateISO: "Please enter a valid date ( ISO ).",
    number: "Please enter a valid number.",
    digits: "Please enter only digits.",
    creditcard: "Please enter a valid credit card number.",
    equalTo: "Please enter the same value again.",
    maxlength: $.validator.format( "Please enter no more than {0} characters." ),
    minlength: $.validator.format( "Please enter at least {0} characters." ),
    rangelength: $.validator.format( "Please enter a value between {0} and {1} characters long." ),
    range: $.validator.format( "Please enter a value between {0} and {1}." ),
    max: $.validator.format( "Please enter a value less than or equal to {0}." ),
    min: $.validator.format( "Please enter a value greater than or equal to {0}." )
}
```

jQuery Validate提供了中文信息提示包，位于下载包的 dist/localization/messages_zh.js，内容如下：

```js
(function( factory ) {
    if ( typeof define === "function" && define.amd ) {
        define( ["jquery", "../jquery.validate"], factory );
    } else {
        factory( jQuery );
    }
}(function( $ ) {

/*
 * Translated default messages for the jQuery validation plugin.
 * Locale: ZH (Chinese, 中文 (Zhōngwén), 汉语, 漢語)
 */
$.extend($.validator.messages, {
    required: "这是必填字段",
    remote: "请修正此字段",
    email: "请输入有效的电子邮件地址",
    url: "请输入有效的网址",
    date: "请输入有效的日期",
    dateISO: "请输入有效的日期 (YYYY-MM-DD)",
    number: "请输入有效的数字",
    digits: "只能输入数字",
    creditcard: "请输入有效的信用卡号码",
    equalTo: "你的输入不相同",
    extension: "请输入有效的后缀",
    maxlength: $.validator.format("最多可以输入 {0} 个字符"),
    minlength: $.validator.format("最少要输入 {0} 个字符"),
    rangelength: $.validator.format("请输入长度在 {0} 到 {1} 之间的字符串"),
    range: $.validator.format("请输入范围在 {0} 到 {1} 之间的数值"),
    max: $.validator.format("请输入不大于 {0} 的数值"),
    min: $.validator.format("请输入不小于 {0} 的数值")
});

}));
```

你可以将该本地化信息文件 dist/localization/messages_zh.js 引入到页面：

```js
<script src="http://static.runoob.com/assets/jquery-validation-1.14.0/dist/localization/messages_zh.js"></script>
```

```html
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<!-- jquery-validation依赖包 -->
	<script src="https://static.runoob.com/assets/jquery-validation-1.14.0/lib/jquery.js"></script>
	<script src="https://static.runoob.com/assets/jquery-validation-1.14.0/dist/jquery.validate.min.js"></script>
	<!-- 支持中文 -->
	<script src="https://static.runoob.com/assets/jquery-validation-1.14.0/dist/localization/messages_zh.js"></script>
	<!-- 引入外部文件 -->
	<script src="1.js"></script>
    <script type="text/javascript">
		$.validator.setDefaults({
		    submitHandler: function() {
		      alert("验证通过后触发此事件!");
		    }
		});
	</script>
    <style>
        .error{
            color:red;
        }
	</style>
</head>
<body>
<form class="cmxform" id="signupForm" method="get" action="">
  <fieldset>
    <legend>验证完整的表单</legend>
    <p>
      <label for="username">用户名</label>
      <input id="username" name="username" type="text">
    </p>
    <p>
      <label for="password">密码</label>
      <input id="password" name="password" type="password">
    </p>
    <p>
      <label for="confirm_password">验证密码</label>
      <input id="confirm_password" name="confirm_password" type="password">
    </p>
    <p>
      <label for="email">Email</label>
      <input id="email" name="email" type="email">
    </p>
    <p>
      <label for="phone">Phone</label>
      <input type="text"  id="phone" name="phone">
    </p>
    <p>
      <label for="date">Date</label>
      <input type="date"  id="date" name="date">
    </p>
    <p>
      <label for="sex">sex</label>
      <input type="radio"  id="sex" name="sex">男
      <input type="radio"   name="sex">女
    </p>
   
    <p>
      <label for="hobby">爱好</label>
      <input type="checkbox" class="checkbox" id="hobby" name="hobby">🏀
      <input type="checkbox" class="checkbox" id="hobby" name="hobby">⚽️
      <input type="checkbox" class="checkbox" id="hobby" name="hobby">⚾️
      <input type="checkbox" class="checkbox" id="hobby" name="hobby">🏉

    </p>
   
    <p>
      <input class="submit" type="submit" value="提交">
    </p>
  </fieldset>
</form>
</body>
</html>
```

```html
<!DOCTYPE html>
<html>

<head>
	<title></title>
	<!-- jquery-validation依赖包 -->
	<script src="https://static.runoob.com/assets/jquery-validation-1.14.0/lib/jquery.js"></script>
	<script src="https://static.runoob.com/assets/jquery-validation-1.14.0/dist/jquery.validate.min.js"></script>
	<!-- 支持中文 -->
	<script src="https://static.runoob.com/assets/jquery-validation-1.14.0/dist/localization/messages_zh.js"></script>
	<script type="text/javascript">
		$.validator.setDefaults({
			submitHandler: function () {
				alert("提交事件!");
			}
		});
		$().ready(function () {

			// 添加方法自定义验证方法  位置：和$('form').validate({}) 同级别
			//增加手机号验证规则
			$.validator.addMethod("isMobile", function (value, element) {
				var length = value.length;
				var mobile = /^(13[0-9]{9})|(18[0-9]{9})|(14[0-9]{9})|(17[0-9]{9})|(15[0-9]{9})$/;
				return this.optional(element) || (length == 11 && mobile.test(value));
			}, "请正确填写您的手机号码");
			// 在键盘按下并释放及提交后验证提交表单
			$("#signupForm").validate({

				rules: {
					username: {
						required: true,
						minlength: 2
					},
					password: {
						required: true,
						minlength: 5
					},
					confirm_password: {
						required: true,
						minlength: 5,
						equalTo: "#password"
					},
					email: {
						required: true,
						email: true
					},
					phone: {
						required: true,
						isMobile: true
					},
					date: {
						required: true
					},
					hobby: {
						required: true,
						minlength: 2
					},


				},
				messages: {
					username: {
						required: "请输入用户名",
						minlength: "用户名必需由两个字母组成"
					},
					password: {
						required: "请输入密码",
						minlength: "密码长度不能小于 5 个字母"
					},
					confirm_password: {
						required: "请输入密码",
						minlength: "密码长度不能小于 5 个字母",
						equalTo: "两次密码输入不一致"
					},
					email: "请输入一个正确的邮箱",
					phone: "请正确填写您的手机号码",
					date: "日期不能为空",
					hobby: "请选择两个hobby"

				}
			});



		});




	</script>
	<style>
		.error {
			color: red;
		}
	</style>
</head>

<body>
	<form class="cmxform" id="signupForm" method="get" action="">
		<fieldset>
			<legend>验证完整的表单</legend>
			<p>
				<label for="username">用户名</label>
				<input id="username" name="username" type="text">
			</p>
			<p>
				<label for="password">密码</label>
				<input id="password" name="password" type="password">
			</p>
			<p>
				<label for="confirm_password">验证密码</label>
				<input id="confirm_password" name="confirm_password" type="password">
			</p>
			<p>
				<label for="email">Email</label>
				<input id="email" name="email" type="email">
			</p>
			<p>
				<label for="phone">Phone</label>
				<input type="text" id="phone" name="phone">
			</p>
			<p>
				<label for="date">Date</label>
				<input type="date" id="date" name="date">
			</p>
			<p>
				<label for="sex">sex</label>
				<input type="radio" checked="" id="sex" name="sex">男
				<input type="radio" name="sex">女
			</p>

			<p>
				<label for="hobby">爱好</label>
				<input type="checkbox" class="checkbox" name="hobby">🏀
				<input type="checkbox" class="checkbox" name="hobby">⚽️
				<input type="checkbox" class="checkbox" name="hobby">⚾️
				<input type="checkbox" class="checkbox" name="hobby">🏉

			</p>

			<p>
				<input class="submit" type="submit" value="提交">
			</p>
		</fieldset>
	</form>
</body>

</html>
```



```js
// 重置表单
$().ready(function() {
 var validator = $("#signupForm").validate({
        submitHandler:function(form){
            alert("submitted");   
            form.submit();
        }    
    });
    $("#reset").click(function() {
        validator.resetForm();
    });

});
```

## 常用方法及注意问题

### 1、用其他方式替代默认的 SUBMIT

```
$().ready(function() {
 $("#signupForm").validate({
        submitHandler:function(form){
            alert("提交事件!");   
            form.submit();
        }    
    });
});
```

使用 ajax 方式

```
 $(".selector").validate({     
 submitHandler: function(form) 
   {      
      $(form).ajaxSubmit();     
   }  
 }) 
```

可以设置 validate 的默认值，写法如下：

```
$.validator.setDefaults({
  submitHandler: function(form) { alert("提交事件!");form.submit(); }
});
```

如果想提交表单, 需要使用 form.submit()，而不要使用 $(form).submit()。

### 2、debug，只验证不提交表单

如果这个参数为true，那么表单不会提交，只进行检查，调试时十分方便。

```
$().ready(function() {
 $("#signupForm").validate({
        debug:true
    });
});
```

如果一个页面中有多个表单都想设置成为 debug，则使用：

```
$.validator.setDefaults({
   debug: true
})
```

### 3、ignore：忽略某些元素不验证

```
ignore: ".ignore"
```

### 4、更改错误信息显示的位置

```
errorPlacement：Callback
```

指明错误放置的位置，默认情况是：error.appendTo(element.parent());即把错误信息放在验证的元素后面。

```
errorPlacement: function(error, element) {  
    error.appendTo(element.parent());  
}
```