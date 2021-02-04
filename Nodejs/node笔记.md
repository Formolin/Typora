# Node.js

> 是一个js运行时的环境，既不是语言，也不是框架，它是一个平台，在服务器上用来解析js代码，以前只有浏览器可以解析执行js代码，现在ks可以脱离浏览器运行了

## 一、文件操作

```node
var fs = require('fs')
```

- 引入文件操作包

```node
fs.readFile('./data/hello.txt',function(error,data){
    第一个参数是要读取文件的路径
    第二个参数是一个回调函数
    	error和data
    	如果读取失败返回的是个对象
    	读取成功是读取到的数据
})
```

- 写文件

```node
fs.writeFile('./data/1.txt','这是第二个参数，写的是文件的内容'，function(error,data){
    //第三个参数是回调函数
    if(error){
        console.log(error)
    }else{
        console.log('写入成功')
        console.log(data)
        console.log(data.toString())
    }
})
```

## 二、服务器

1. 加载http核心模块

   ```node
   var http = require('http')
   ```

   

2. 使用方法创建web服务器，返回一个server实例

```nodejs
var server = http.createServer()
```

3. 请求/响应

   ```node
   server.on('request',function(request,response){
       //客户端请求，会调用第二个参数回调函数
       console.log('收到客户端信息了')
       console.log('请求路径='+request.url)
       //response对象有一个方法，write 可以用来给客户端发送响应数据
    	//write可以使用多次，最后一定要使用end技术响应，否则客户端一致等待
       response.write('hello')//一定结束后，才有结果
   	response.write('node')
       //告诉客户端我说完了，你可以给用户看了
       response.end()//更新文件后需重新运行下
   })
   ```

4. 绑定端口号，启动服务器

   ```node
   server.listen(3000,function(){
       console.log('服务器启动成功')
   })
   //端口自定义，只要不被占用
   //访问localhost:3000
   ```

## 三、不同访问路径返回不同数据

```node
var http = require('http')
var server = http.createServer()
server.on('request',function(req,res){
    //console.log('请求路径='+request.url)
    //res.end('可以简写，发送响应数据并结束')
   // 不同访问路径返回不同数据
   //req.url获取的是端口号之后的那部分路径，也就是说url是以/开头的
   var url = req.url
   //res.end(url)
   if(url === '/'){
       res.end('index page')
   }else if(url === '/login'){
       res.end('login page')
   }else{
       res.end('404')
   }
})
server.listen(3000,function(){
    console.log('服务器启动成功')
})
```

**模拟数据**

```node
if(url === '/produts'){
	var products = [
        {
            name:'iphone',
            price:10000
        },
        {
            name:'mi',
            price:3000
        },
        {
            name:'huawei'
            price:4000
        }
	]
	//响应的内容只能是二进制数据或者字符串，数字、对象、数组、布尔都不行
	
	//数组转字符串JSON
       res.end(JSON.stringify(products))
}
```

## 四、核心模块

1. fs模块
2. http模块
3. path模块
4. os模块

使用核心模块必须导入

```node
var fs = require('fs')//加载模块
```

> 详细方法可以查阅api文档

## 五、ip和端口号

```node
server.on('request',function(request,response){
    console.log('端口号：'+req.socket.remotePort)
	 console.log('地址：'+req.socket.remoteAddress)
})
```

1. ip地址用来定位计算机
2. 端口号用来定位具体的应用程序
3. 一切需要联网通信的软件都会占用一个端口号
4. 端口号的范围从0-65536之间
5. 在计算机中有一些默认的端口号，最好不要去使用，例如80
6. 我们在开发中使用一个3000、5000等没什么含义的
7. 可以同时开启多个服务，但端口号不能一致

## 六、编码

> 在服务器端默认发送的数据是utf-8编码的内容，但是浏览器不知道你是utf-8的内容，他会默认按照当前操作系统的编码进行解析，中文操作系统默认编码是GBK，解决方案就是正确告诉浏览器我要给你发送的内容是什么编码

```node
server.on('request',function(request,response){
   res.setHeader('Content-Type','text/plain;charset=utf-8')
   //text/html  还有html格式的文本 plain是普通文本
   res.end('我是中文')
   //图片是image/jpeg  可以查询不同文件类型，而图片就不需要在指定编码了
    res.setHeader('Content-Type','image/jpeg')
})
```

## 七、补充

```js
;['苹果'，'香蕉'].forEach(function(item){
    console.log(item)//这种语法前面加分号
})

``反引号，是es6新增的字符串方式，支持换行和方便的拼接变量

`name=${name}`  里面怎么换行怎么输出 类似与<pre></pre>标签

以 ( [ `  三个符号开头的  都建议加上；
```

