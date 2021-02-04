# Web架构安全分析

# Web 工作机制

 

- ### 网页、网站

我么可通过浏览器上网看到精美的页面，一般都是经过浏览器渲染的.html 页面，其中包含css 等前端技术。多个网页的集合就是网站。

 

- ### Web容器

Web 容器，也叫Web 服务器，主要提供Web 服务，也就是常说的HTTP 服务。

常见的Web 容器有：Apache/IIS/Nginx 等。

 

- ### 静态页面

静态页面，都是些.html 文件，是纯文本文件。这些文件中包含html 代码。

HTML（HyperText Markup Language,超文本标记语言），在浏览器中解释运行。

 

- ### 中间件服务器

以上这种，只能单向给用户战术信息。随着Web 的发展，信息要双向流动，产生了交互的需求，也就是动态网页的概念；所谓动态就是利用flash、Php、asp、Java 等技术在网页中嵌入一些可以运行的脚本，用户浏览器在解释页面时，遇到脚本就启动运行它。

脚本的使用让Web 服务模式有了双向交流的能力，Web服务器模式也可以像传统的软件一样进行各种事务的处理，如编辑文件、利息计算、提交表单等，Web 架构的适用面大大扩展

这些脚本可以嵌入到页面中，如JS等。也可以以文件的形式单独存放在Web 服务器的目录里，如.asp、.php、jsp 文件等。这样功能性的脚本越来越多，形成常用的工具包，单独管理，Web 业务开发时，直接使用就可以了，这就是中间件服务器，它实际上时Web 服务器处理能力的扩展。

 

- ### 数据库的出现

静态网页与脚本都是事先设计好的，一般不经常改动，但网站上的很多内容需要经常更新，将这些变动的数据放在静态网页的程序中显然不合适，传统的办法是数据与程序分离，采用的专业的数据库。

Web 开发者在Web 服务器后边增加了一个数据库服务器，这些经常变动的数据被存进数据库，可以随时更新。当用户请求页面时，脚本根据用户请求的页面，涉及到动态数据的地方，利用SQL数据库语言，从数据中读取最新的数据，生产“完整”页面，最后送给用户

 

# HTTP 协议概述

HTTP（HyperText Transfer Protocol,超文本传输协议，是传递消息的规范和要求。

 

- ### 概述

| **@** | **1990 年提出的，当前版本1.1。**                             |
| ----- | ------------------------------------------------------------ |
| **@** | **HTTP 是用来将html 文档从Web 服务器传输到Web 浏览器。**     |
| **@** | **是一个请求和响应的协议。客户端发出请求，服务器端对请求给出回应。** |
| **@** | **HTTP 使用可靠的TCP 连接，默认端口80**                      |

 

- ### 特点

| **@** | **支持浏览器/服务器模式**                                    |
| ----- | ------------------------------------------------------------ |
| **@** | **简单快速：浏览器向服务器提出请求时，只需要传送请求方法和请求路径** |
| **@** | **灵活：HTTP 运行传输任意类型的数据对象**                    |

| .html |      | 纯文本 |
| ----- | ---- | ------ |
| .jpg  |      | 图片   |
| .mp3  |      | 音频   |

| **@** | **HTTP 协议是无状态的协议** |
| ----- | --------------------------- |
|       |                             |

 

- ### URL

统一资源定位符（网址），用来告诉Web 容器，浏览器所请求的资源（文件）的路径。例如：

<http://localhost/test/requests.php?id=32>

 

Schema://login:password@adress:port/path/to/resource/?query_string#fragment

 

| Port     |      | 80     |
| -------- | ---- | ------ |
| Login    |      | 用户名 |
| Password |      | 密码   |
| Fragment |      | 锚点   |

 

| **@** | **URL编码** |
| ----- | ----------- |
|       |             |

URL 只允许出现的字符是有限制的，URL 中path 开始允许直接出现[A-Z][a-z][0-9]

半角减号（-）、下划线句点（。）、波浪号（~）。其他字符均会被百分号编码（包括空格）

如下：

| #     |      | %23  |
| ----- | ---- | ---- |
| [   ] |      | %20  |

%+ASCII 码十六进制形式

在进行编程的时候，会[+] 代替空格。

| **@** | **报文分析工具** |
| ----- | ---------------- |
|       |                  |

1、F12

2、wireshark

3、fiddler

4、Burp suite

…

 

# HTTP 报文分析

Web 应用的所有通信的消息都要遵守HTTP 协议的规范和要求。

 

- ## HTTP 工作模式

- ## REQUEST

  - ### 请求报文，如下

```sh
GET /php/test/get.php HTTP/1.1

Host: 192.168.1.136

User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:75.0) Gecko/20100101 Firefox/75.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8

Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2

Accept-Encoding: gzip, deflate

Referer: <http://192.168.1.136/php/test/>

Connection: close

Upgrade-Insecure-Requests: 1

Cache-Control: max-age=0
```



HTTP 请求由请求行、请求头、请求正文三部分组成

**1、请求行：方法，资源路径，协议/版本**

方法：GET

资源路径：/php/test/get.php

协议/版本：HTTP/１.１

**2、请求头**

从请求报文第二行开始到第一个空行为止的内容。其中包含很多字段

**3、请求正文**

以上方法没有请求正文，后面会看到

 

- ### 请求方法：

| GET     | 最常用的方法，通常用户请求服务器发送的某个资源。       |
| ------- | ------------------------------------------------------ |
| POST    | 可以向服务器提交参数以及表单，包括文件流等             |
| HEAD    | 与GET 方法类似，但在服务器响应中只返回首部             |
| PUT     | 与GET 从服务器读取文档相反，PUT 方法会向服务器写入文档 |
| TRACE   | 回显浏览器的请求                                       |
| OPTIONS | 请求Web 服务器告知其支持的各种功能                     |
| DELETE  | 请求服务器删除请求URL所指定的资源                      |

 

- ### 实验

使用telnet 模式浏览器发送HTTP 请求

利用telnet 传送GET 参数

 

Telnet 192.168.1.36 80

\----------------

GET /php/test/get.php HTTP/1.1

Host: 192.168.1.136

\-------

 

Telnet [www.baidu.com](http://www.baidu.com/) 80

\----------------

GET / HTTP/1.1

Host: [www.baidu.com](http://www.baidu.com/)

\-----------

 

利用OPTIONS 方法测试Web 服务器允许的HTTP请求

\---------------

OPTIONS / HTTP/1.1

Host: 192.168.1.136

\-------

 

利用telnet 传送GET 参数

\-------------

> GET /php/test/get.php?name=AJEST&pwd=123456 HTTP/1.1
>
> Host： 192.168.1.136

 

![img](51、Web架构安全分析/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI1MjIwNA==,size_16,color_FFFFFF,t_70-20201110152120054.png)

 

利用telnet 模拟POST,请求传递参数

\---------------

POST /php/test/post.php?http://192.168.1.136/php/test/post.php HTTP/1.1
Host: 192.168.1.136
Content-Type: application/x-www-form-urlencoded
Content-Length: 19

name=GGG&pwd=123456

\-----

![img](51、Web架构安全分析/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI1MjIwNA==,size_16,color_FFFFFF,t_70-20201110152120037.png)

- ### 主要字段

| Host           | 主要用与指定被请求资源的Internet 主机和端口号              |
| -------------- | ---------------------------------------------------------- |
| User-Agent     | 浏览器指纹                                                 |
| Referer        | 包含一个URL，代表当前的URL的上一个URL                      |
| Cookie         | 记录请求者的身份认证信息                                   |
| Accept-Charset | 用户指定客户端接受的字符集                                 |
| Content-Type   | 用于向接收方知识实体的介质类型（数据类型）                 |
| Content-Length | 用于指明实体正文的长度，以字节的方式存储的十进制数字来表示 |
| Last-Modified  | 用于指示资源的最后修改时间和日期                           |

 

- ## RESPONSE

  - ### 响应报文如下：

\-----------------------------------------------------------------------------------------

HTTP/1.1 200 OK

Date: Sat, 25 Apr 2020 09:50:59 GMT

Server: Apache/2.4.23 (Win32) OpenSSL/1.0.2j PHP/5.4.45

X-Powered-By: PHP/5.4.45

Content-Length: 13

Connection: close

Content-Type: text/html

 

array(0) {

}

\------------------------

响应报文由状态行、响应报头、响应正文三部分组成。

1、状态行：协议/版本，状态代码，描述短语

协议/版本：HTTP/1.1

状态代码：200

描述短语：OK

2、响应报头

第二行开始到第一个空行为止的所有内容，其中包含了关于HTTP响应的重要字段。

3、响应正文

服务器返回资源的内容，即浏览器接收到的HTML 代码。

- ### 状态码

| 100~199 | 信息性状态码     |
| ------- | ---------------- |
| 200~299 | 成功状态码       |
| 300~399 | 重定向状态码     |
| 400~499 | 客户端错误状态码 |
| 500~599 | 服务器错误状态码 |

- ### 主要字段

| Server         | 服务器指纹                                         |
| -------------- | -------------------------------------------------- |
| Set-Cookie     | 向浏览器端设置Cookie                               |
| Last-Modified  | 服务器通过这个头信息告诉浏览器，资源的最后修改时间 |
| Content-Length | 请求正文长度                                       |
| Location       | 重定向目标页面                                     |
| Refresh        | 服务器通过Refresh头告诉浏览器定时刷新浏览器        |

 

- ## 同源策略的条件

  - URL 的主机（FQDN：Fully Qualified Domain Name 全程域名）一致
  - Schema 一致
  - 端口号一致

同源策略的保护对象不仅仅时iframe 内文档。比如实现Ajax 时所使用的XMLHttpRequest 对象能够访问的 URL也受到了同源策略的限制。

 

- ## 同源策略的探究

  - ### 准备两个页面

-------index.html

```html
<html>

<head>

<title> 跨 frame 的读取实验 </title>

</head>

<body>

src="http://192.168.1.136/sop/iframe.html"

\>

</iframe>

<input type="button" οnclick="go()" value=" 密码: ">

function go(){

try {

var x = iframe1.document.form1.passwd.value;

document.getElementById('out').innerHTML = x;

} catch (e){

alert(e.message);

}

}

</script>

<span id="out"></span>

</body>

</html>
```



\--------

 

--------iframe.html

```html
<html>

<head>

</head>

<body>

<form name="form1"> iframe 的内层

 

密码 <input type="text" name="passwd" value="password1">

</form>

</body>

</html>
```



\--------

这里我们使用我们服务器的浏览器测试这个实验（因为我们要使用不同的两个URL来访问我们的网页）

 

我们用指定的IP访问，可以正常读取

![img](51、Web架构安全分析/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI1MjIwNA==,size_16,color_FFFFFF,t_70-20201110152120073.png)

 

当时当我们使用我们本机的回环地址去访问时，打开页面，无法读取（这就是由同源策略限制，无法访问内层iframe）

# ![img](51、Web架构安全分析/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzI1MjIwNA==,size_16,color_FFFFFF,t_70-20201110152120179.png)