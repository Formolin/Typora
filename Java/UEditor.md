# UEditor Docs

## UEditor 介绍

UEditor 是由百度「FEX前端研发团队」开发的所见即所得富文本web编辑器，具有轻量，可定制，注重用户体验等特点，开源基于MIT协议，允许自由使用和修改代码。

## 1 入门部署和体验

### 1.1 下载编辑器

到官网下载 UEditor 最新版：[[官网地址\]](http://ueditor.baidu.com/website/download.html#ueditor)

### 1.2 创建demo文件

解压下载的包，在解压后的目录创建 demo.html 文件，填入下面的html代码



```html
<!DOCTYPE HTML>
<html lang="en-US">

<head>
    <meta charset="UTF-8">
    <title>ueditor demo</title>
</head>

<body>
    <!-- 加载编辑器的容器 -->
    <script id="container" name="content" type="text/plain">
        这里写你的初始化内容
    </script>
    <!-- 配置文件 -->
    <script type="text/javascript" src="ueditor.config.js"></script>
    <!-- 编辑器源码文件 -->
    <script type="text/javascript" src="ueditor.all.js"></script>
    <!-- 实例化编辑器 -->
    <script type="text/javascript">
        var ue = UE.getEditor('container');
    </script>
</body>

</html>
```



### 1.3 在浏览器打开demo.html

如果看到了下面这样的编辑器，恭喜你，初次部署成功！

![部署成功](https://ws3.sinaimg.cn/large/006tNc79ly1g293yrabglj30eq08tt96.jpg)

### 1.4 传入自定义的参数

编辑器有很多可自定义的参数项，在实例化的时候可以传入给编辑器：

```javascript
var ue = UE.getEditor('container', {
    autoHeight: false
});
```



配置项也可以通过 ueditor.config.js 文件修改，具体的配置方法请看[前端配置项说明](http://fex.baidu.com/ueditor/#start-config)

### 1.5 设置和读取编辑器的内容

通 getContent 和 setContent 方法可以设置和读取编辑器的内容

```javascript
var ue = UE.getContent();
//对编辑器的操作最好在编辑器ready之后再做
ue.ready(function() {
    //设置编辑器的内容
    ue.setContent('hello');
    //获取html内容，返回: <p>hello</p>
    var html = ue.getContent();
    //获取纯文本内容，返回: hello
    var txt = ue.getContentTxt();
});
```

## 提交表单设置

1. 把容器放到form表单里面，设置好要提交的路径，如下面代码中的<form>标签



```html
<!DOCTYPE HTML>
<html lang="en-US">

<head>
    <meta charset="UTF-8">
    <title>ueditor demo</title>
</head>

<body>
    <form action="server.php" method="post">
        <!-- 加载编辑器的容器 -->
        <script id="container" name="content" type="text/plain">
            这里写你的初始化内容
        </script>
    </form>
    <!-- 配置文件 -->
    <script type="text/javascript" src="ueditor.config.js"></script>
    <!-- 编辑器源码文件 -->
    <script type="text/javascript" src="ueditor.all.js"></script>
    <!-- 实例化编辑器 -->
    <script type="text/javascript">
        var editor = UE.getEditor('container');
    </script>
</body>

</html>
```

### Eclipse部署

1. 在Eclipse中配置Tomcat 6.0。

2. 在Eclipse中创建一个名为

   ```
   ueditor1_4_3-utf8-jsp
   ```

   的“Dynamic Web Project”项目。如下图所示：

   

   

3. 解压下载的UEditor包到D盘根目录。

4. 在Eclipse中的项目

   ```
   ueditor1_4_3-utf8-jsp
   ```

   下的“WebContent”目录上右击，在弹出的菜单中选择：Import->Import...，弹出如下图所示对话框：

   

   

5. 在弹出的对话框中展开General项，双击“File System”后弹出对话框，如下图所示：![Eclipse导入截图](https://ws4.sinaimg.cn/large/006tNc79ly1g2943fr953j30hb0hu3zr.jpg)

6. 点击“Browse...”按钮，在弹出的文件选择对话框中选中第3步中解压出来的ueditor文件夹，并勾选全部文件，结果如下图所示：![Eclipse导入截图](https://ws2.sinaimg.cn/large/006tNc79ly1g2943ekmt9j30hb0hu75z.jpg)

7. 点击“Finish”按钮，完成导入。

8. 拷贝目录jsp/lib/下的所有jar包到WEB-INF/lib/目录下， 部署至此完成，最终项目的结构如下图所示：![Eclipse导入完成后的截图](https://ws1.sinaimg.cn/large/006tNc79ly1g2943fae91j30cd0k0dh6.jpg)

9. 添加该项目到Tomcat中，并启动Tomcat，在Eclipse的“Console”面板中出现如下图所示的界面时，启动成功。![Eclipse导入完成后的截图](https://ws2.sinaimg.cn/large/006tNc79ly1g2943euf0kj30lr0b941m.jpg)

10. 进入5 验证安装步骤，验证部署是否成功。

## 5 验证安装

在浏览器地址栏中输入如下URL：

```
http://localhost:8080/ueditor1_4_3-utf8-jsp/jsp/controller.jsp?action=config
```

出现类似下图所示内容，则配置成功，否则，即为失败。

