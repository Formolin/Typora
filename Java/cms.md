```jsp
onfocus="if(this.value==this.defaultValue){this.value='';this.style.color='black'}"
```

```jsp
<input type="text" class="text"
				value="${msg==null?cookie.username.value:username }"
				name="username" id="username" onclick="show()"
				>

根据错误信息判断时候显示cookie，主要在登陆页面的form提交
```

