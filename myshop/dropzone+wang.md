pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.aishang</groupId>
    <artifactId>dropzone-wang</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>dropzone-wang</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>


        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>

        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.0.2</version>
        </dependency>




        <!-- 添加servlet依赖模块 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <scope>provided</scope>
        </dependency>
        <!-- 添加jstl标签库依赖模块 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
        </dependency>
        <!--添加tomcat依赖模块.-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <scope>provided</scope>
        </dependency>
        <!-- 使用jsp引擎，springboot内置tomcat没有此依赖 -->
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- https://mvnrepository.com/artifact/jstl/jstl -->
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

application.properties

```properties

spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.url = jdbc:mysql://127.0.0.1:3306/shop?useUnicode=true&characterEncoding=utf-8
spring.datasource.username = root
spring.datasource.password = password
spring.datasource.driverClassName = com.mysql.cj.jdbc.Driver

spring.datasource.initialSize=5
spring.datasource.minIdle=5
spring.datasource.maxActive=20
spring.datasource.maxWait=60000
spring.datasource.timeBetweenEvictionRunsMillis=60000
spring.datasource.minEvictableIdleTimeMillis=300000
spring.datasource.validationQuery=SELECT 1 FROM DUAL
spring.datasource.testWhileIdle=true
spring.datasource.testOnBorrow=false
spring.datasource.testOnReturn=false
spring.datasource.poolPreparedStatements=true
spring.datasource.maxPoolPreparedStatementPerConnectionSize=20
spring.datasource.filters=stat,wall,log4j
spring.datasource.connectionProperties=druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000


mybatis.type-aliases-package=com.aishang.dropzone.wang.entity
mybatis.mapper-locations=classpath:mapper/*.xml



spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
```

html

```html
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/basic.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/dropzone.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/bootstrap/css/bootstrap.css">
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/jquery-1.11.1.min.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/bootstrap.js"></script>
    <style>
        #box{
            width: 1000px;
            margin: 0 auto;
            margin-top: 100px;
        }
    </style>
</head>
<body>

<div id="box">
    <form>
        <div class="form-group">
            <label for="Title">Title</label>
            <input type="text" class="form-control" id="Title" placeholder="Title">
        </div>
        <div class="form-group">
            <label for="Market_price">Market_price</label>
            <input type="number" class="form-control" id="Market_price" placeholder="Market_price">
        </div>
        <div class="form-group">
            <label for="shop_price">shop_price</label>
            <input type="number" class="form-control" id="shop_price" placeholder="shop_price">
        </div>



        <button type="submit" class="btn btn-success">Submit</button>
    </form>
</div>

</body>
</html>
```



Dropzone.js

```html
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/basic.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/dropzone.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/bootstrap/css/bootstrap.css">
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/jquery-1.11.1.min.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/bootstrap.js"></script>
    <style>
        #box{
            width: 1000px;
            margin: 0 auto;
            margin-top: 100px;
        }
    </style>
</head>
<body>

<div id="box">
    <form>
        <div class="form-group">
            <label for="Title">Title</label>
            <input type="text" class="form-control" id="Title" placeholder="Title">
        </div>
        <div class="form-group">
            <label for="Market_price">Market_price</label>
            <input type="number" class="form-control" id="Market_price" placeholder="Market_price">
        </div>
        <div class="form-group">
            <label for="shop_price">shop_price</label>
            <input type="number" class="form-control" id="shop_price" placeholder="shop_price">
        </div>

        <div class="form-group">
            <label for="upload">upload</label>
            <div id="upload" class="dropzone"></div>
        </div>

        <button type="submit" class="btn btn-success">Submit</button>
    </form>
</div>

</body>
<script>
    var myDropzone = new Dropzone("#upload", {
        url: "/file",
        paramName:"fileName",
        dictDefaultMessage:"拖拽或点击上传文件"
    });
</script>
</html>
```

css文本溢出

- 注意样式写在td上不好使

```html
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8" %>
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/basic.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/dropzone.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/bootstrap/css/bootstrap.css">
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/jquery-1.11.1.min.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/bootstrap.js"></script>
    <style>
        #box {
            width: 1000px;
            margin: 0 auto;
            margin-top: 100px;
        }

        .tr {
            height: 150px;
        }

        td {
            vertical-align: middle !important;
        }

        .pdesc {
            margin: auto;
            width: 300px;
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
            cursor: pointer;
        }
    </style>
</head>
<body>

<div id="box">
    <table class="table table-condensed">
        <tr>
            <td>id</td>
            <td>名称</td>
            <td>原价</td>
            <td>打折</td>
            <td>描述</td>
            <td>添加日期</td>
            <td>分类</td>
            <td>预览</td>
            <td>编辑</td>
        </tr>

        <c:forEach var="proList" items="${allPro}">
            <tr>
                <td>${proList.pid}</td>
                <td>${proList.pname}</td>
                <td>${proList.marketPrice}</td>
                <td>${proList.shopPrice}</td>
                <td><p class="pdesc">${proList.pdesc}</p></td>
                <td>${proList.createdate}</td>
                <td>${proList.csid}</td>
                <td>
                    <img src="${pageContext.request.contextPath}/static/${proList.image}" alt="" width="100"
                         height="100">
                </td>
                <td>
                    <input class="btn btn-default" type="button" value="查看">
                    <input class="btn btn-primary" type="button" value="编辑">
                    <input class="btn btn-danger" type="button" value="删除">
                </td>
            </tr>
        </c:forEach>

    </table>
</div>

</body>
<script>
    var myDropzone = new Dropzone("#upload", {
        url: "/file",
        paramName: "fileName",
        dictDefaultMessage: "拖拽或点击上传文件"
    });
</script>
</html>
```

### EL表达式切割字符串

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/functions" prefix="fn"%>
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/basic.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/dropzone.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/bootstrap/css/bootstrap.css">
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/jquery-1.11.1.min.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/bootstrap.js"></script>
    <style>
        #box {
            width: 1000px;
            margin: 0 auto;
            margin-top: 100px;
        }

        .tr {
            height: 150px;
        }

        td {
            vertical-align: middle !important;
        }

        .pdesc {
            margin: auto;
            width: 300px;
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
            cursor: pointer;
        }
    </style>
</head>
<body>

<div id="box">
    <table class="table table-condensed">
        <tr>
            <td>id</td>
            <td>名称</td>
            <td>原价</td>
            <td>打折</td>
            <td>描述</td>
            <td>添加日期</td>
            <td>分类</td>
            <td>预览</td>
            <td>编辑</td>
        </tr>

        <c:forEach var="proList" items="${allPro}">
            <tr>
                <td>${proList.pid}</td>
                <td>${proList.pname}</td>
                <td>${proList.marketPrice}</td>
                <td>${proList.shopPrice}</td>
                <td><p class="pdesc">${proList.pdesc}</p></td>
                <td>${proList.createdate}</td>
                <td>${proList.csid}</td>
                <td>
                    <img src="${pageContext.request.contextPath}/static/${fn:split(proList.image,'__')[0]}" alt="" width="100"
                         height="100">
                </td>
                <td>
                    <input class="btn btn-default" type="button" value="查看">
                    <input class="btn btn-primary" type="button" value="编辑">
                    <input class="btn btn-danger" type="button" value="删除">
                </td>
            </tr>
        </c:forEach>

    </table>

    <nav aria-label="Page navigation">
        <ul class="pagination">
            <li>
                <a href="#" aria-label="Previous">
                    <span aria-hidden="true">&laquo;</span>
                </a>
            </li>
            <li class="active"><a href="#">1 <span class="sr-only">(current)</span></a></li>
            <li ><a href="#">2 <span class="sr-only">(current)</span></a></li>
            <li ><a href="#">3 <span class="sr-only">(current)</span></a></li>
            <li ><a href="#">4 <span class="sr-only">(current)</span></a></li>
            <li ><a href="#">5 <span class="sr-only">(current)</span></a></li>
            <li>
                <a href="#" aria-label="Next">
                    <span aria-hidden="true">&raquo;</span>
                </a>
            </li>
        </ul>
    </nav>
</div>

</body>
<script>
</script>
</html>
```

### 删除图片

```java
 @ResponseBody
    @RequestMapping("/delFile")
    public String delFile(String delFileName,HttpServletRequest request){
        System.out.println("delFileName"+delFileName);
        String realPath = request.getSession().getServletContext().getRealPath("/static/images");
        File file = new File(realPath,delFileName);
        file.delete();
        return "ok";
    }
```

```html
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/basic.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/dropzone.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/bootstrap/css/bootstrap.css">
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/jquery-1.11.1.min.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/bootstrap.js"></script>
    <style>
        #box{
            width: 1000px;
            margin: 0 auto;
            margin-top: 100px;
        }
    </style>
</head>
<body>

<div id="box">
    <h1>商品上传</h1>
    <form action="${pageContext.request.contextPath}/pro" method="post">
        <div class="form-group">
            <label for="Title">Title</label>
            <input type="text" class="form-control" id="Title" name="pname" placeholder="Title">
        </div>
        <div class="form-group">
            <label for="Market_price">Market_price</label>
            <input type="number" class="form-control" id="Market_price" name="marketPrice" placeholder="Market_price">
        </div>
        <div class="form-group">
            <label for="shop_price">shop_price</label>
            <input type="number" class="form-control" id="shop_price" name="shopPrice" placeholder="shop_price">
        </div>
        <div class="form-group">
            <label for="date">date</label>
            <input type="datetime-local" class="form-control" id="date" name="createdate" placeholder="date">
        </div>
        <div class="form-group">
            <label for="categorySecond">CategorySecond</label>
            <select class="form-control" id="categorySecond" name="csid">
                <c:forEach var="csList" items="${allCategorySecond}">
                    <option value="${csList.csid}">${csList.csname}</option>
                </c:forEach>
            </select>
        </div>
        <div class="form-group" id="showImg">
            <label for="upload">upload</label>
            <div id="upload" class="dropzone"></div>

        </div>
        <div class="form-group">
            <label for="pdesc">product_desc</label>
            <input type="text" class="form-control" id="pdesc" name="pdesc" placeholder="pdesc">
        </div>

        <button type="submit" class="btn btn-success">Submit</button>
    </form>
</div>

</body>
<script>
    var myDropzone = new Dropzone("#upload", {
        url: "/file",
        paramName:"dropFile",
        dictDefaultMessage:"拖拽或点击上传文件",
        addRemoveLinks: true,
        dictRemoveFile: '删除',
        dictCancelUpload: "取消",
        maxFiles: 6,//一次性上传文件的最大数量
        maxFilesize: 2, //文件大小 MB
        parallelUploads: 1, // 同时上传多少张
        timeout: 300000,
        acceptedFiles: "image/*,.mp4,.MOV,.wmv,.jpg,.gif,.png,.jpeg",//上传类型
        init:function () {
            this.on("success",function (file,data) {
                //上传成功触发事件
                console.log("file="+file);
                console.log("文件名，data="+data);
                //disable后台接收不到参数
                $("#showImg").append(`<input class="form-control" name="showFileName" value='${'${data}'}' type="text" placeholder="FileName" readonly>`)
            })
            this.on("removedfile",function (file) {
                console.log(file)
                console.log(file.name)
                var fileNameArr = $("[name='showFileName']")
                console.log(fileNameArr.length)
                //获取的是整个input标签，只能用each遍历
                $(fileNameArr).each(function(){
                    if($(this).val().indexOf(file.name)>0){
                        console.log($(this).val())
                        $(this).remove()
                        $.ajax({
                            url:"${pageContext.request.contextPath}/delFile?delFileName="+$(this).val(),
                            success:function (data) {
                                console.log(data)
                            }
                        })
                    }
                })


            })
        }
    });
</script>
</html>
```

### 图片回显

```js
    //图片回显

    var mockFile = { name: "http://tool.oschina.net/img/logo.gif", accepted:true };

    myDropzone.emit("addedfile", mockFile);

    myDropzone.emit("thumbnail", mockFile, "http://tool.oschina.net/img/logo.gif");

    myDropzone.emit("complete", mockFile);






    //图片回显

    var mockFile = { name: "初始化图片", accepted:true };

    myDropzone.emit("addedfile", mockFile);

    myDropzone.emit("thumbnail", mockFile, "要显示的图片");

    myDropzone.emit("complete", mockFile);








    var mockFile = { name: "http://localhost:8080/static/images/15dba765-686e-4593-851e-0564a365be56_nginx.jpeg", accepted:true };
    var mockFile1 = { name: "http://localhost:8080/static/images/15dba765-686e-4593-851e-0564a365be56_nginx.jpeg", accepted:true };

    myDropzone.emit("addedfile", mockFile);
    myDropzone.emit("addedfile", mockFile1);

    myDropzone.emit("thumbnail", mockFile, "http://localhost:8080/static/images/15dba765-686e-4593-851e-0564a365be56_nginx.jpeg");
    myDropzone.emit("thumbnail", mockFile1, "http://localhost:8080/static/images/15dba765-686e-4593-851e-0564a365be56_nginx.jpeg");

    myDropzone.emit("complete", mockFile);
    myDropzone.emit("complete", mockFile1);

```

```html
<script>
    var myDropzone = new Dropzone("#upload", {
        url: "/file",
        paramName:"dropFile",
        dictDefaultMessage:"拖拽或点击上传文件",
        addRemoveLinks: true,
        dictRemoveFile: '删除',
        dictCancelUpload: "取消",
        maxFiles: 6,//一次性上传文件的最大数量
        maxFilesize: 2, //文件大小 MB
        parallelUploads: 1, // 同时上传多少张
        timeout: 300000,
        acceptedFiles: "image/*,.mp4,.MOV,.wmv,.jpg,.gif,.png,.jpeg",//上传类型
        init:function () {
            this.on("success",function (file,data) {
                //上传成功触发事件
                console.log("file="+file);
                console.log("文件名，data="+data);
                //disable后台接收不到参数
                $("#showImg").append(`<input class="form-control" name="showFileName" value='${'${data}'}' type="text" placeholder="FileName" readonly>`)
            })
            this.on("removedfile",function (file) {
                console.log(file)
                console.log(file.name)
                var fileNameArr = $("[name='showFileName']")
                console.log(fileNameArr.length)
                //获取的是整个input标签，只能用each遍历
                $(fileNameArr).each(function(){
                    if($(this).val().indexOf(file.name)>0){
                        console.log($(this).val())
                        $(this).remove()
                        $.ajax({
                            url:"${pageContext.request.contextPath}/delFile?delFileName="+$(this).val(),
                            success:function (data) {
                                console.log(data)
                            }
                        })
                    }
                })
            })
        }
    });
    //图片回显

    var mockFile = { name: "http://tool.oschina.net/img/logo.gif", accepted:true };

    myDropzone.emit("addedfile", mockFile);

    myDropzone.emit("thumbnail", mockFile, "http://tool.oschina.net/img/logo.gif");

    myDropzone.emit("complete", mockFile);


//    wang
    var E = window.wangEditor
    var editor = new E('#editor')
    // 或者 var editor = new E( document.getElementById('editor') )
    editor.create()

</script>
```



### wang

```html
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/basic.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/dropzone.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/bootstrap/css/bootstrap.css">
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/jquery-1.11.1.min.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/bootstrap.js"></script>
    <script src="${pageContext.request.contextPath}/static/wang/wangEditor.min.js"></script>

    <style>
        #box{
            width: 1000px;
            margin: 0 auto;
            margin-top: 100px;
        }
    </style>
</head>
<body>

<div id="box">
    <h1>商品上传</h1>
    <form action="${pageContext.request.contextPath}/pro" method="post">
        <div class="form-group">
            <label for="Title">Title</label>
            <input type="text" class="form-control" id="Title" name="pname" placeholder="Title">
        </div>
        <div class="form-group">
            <label for="Market_price">Market_price</label>
            <input type="number" class="form-control" id="Market_price" name="marketPrice" placeholder="Market_price">
        </div>
        <div class="form-group">
            <label for="shop_price">shop_price</label>
            <input type="number" class="form-control" id="shop_price" name="shopPrice" placeholder="shop_price">
        </div>
        <div class="form-group">
            <label for="date">date</label>
            <input type="datetime-local" class="form-control" id="date" name="createdate" placeholder="date">
        </div>
        <div class="form-group">
            <label for="categorySecond">CategorySecond</label>
            <select class="form-control" id="categorySecond" name="csid">
                <c:forEach var="csList" items="${allCategorySecond}">
                    <option value="${csList.csid}">${csList.csname}</option>
                </c:forEach>
            </select>
        </div>
        <div class="form-group" id="showImg">
            <label for="upload">upload</label>
            <div id="upload" class="dropzone"></div>

        </div>
        <div class="form-group">
            <label for="pdesc">product_desc</label>
            <input type="text" class="form-control" id="pdesc" name="pdesc" placeholder="pdesc">
            <div id="editor">
                <p>欢迎使用 <b>wangEditor</b> 富文本编辑器</p>
            </div>
        </div>

        <button type="submit" class="btn btn-success">Submit</button>
    </form>
</div>

</body>
<script>
   

//    wang
    var E = window.wangEditor
    var editor = new E('#editor')
    // 或者 var editor = new E( document.getElementById('editor') )
    editor.create()
</script>
</html>
```

### 获取内容

```html
<div id="div1">
    <p>欢迎使用 wangEditor 编辑器</p>
</div>
<button id="btn1">获取html</button>
<button id="btn2">获取text</button>

<script type="text/javascript" src="/wangEditor.min.js"></script>
<script type="text/javascript">
    var E = window.wangEditor
    var editor = new E('#div1')
    editor.create()

    document.getElementById('btn1').addEventListener('click', function () {
        // 读取 html
        alert(editor.txt.html())
    }, false)

    document.getElementById('btn2').addEventListener('click', function () {
        // 读取 text
        alert(editor.txt.text())
    }, false)

</script>
```

html获取的内容带标签

text不带标签

```html
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/basic.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/dropzone/dropzone.css">
    <link rel="stylesheet" href="${pageContext.request.contextPath}/static/bootstrap/css/bootstrap.css">
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/dropzone/dropzone.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/jquery-1.11.1.min.js"></script>
    <script src="${pageContext.request.contextPath}/static/bootstrap/js/bootstrap.js"></script>
    <script src="${pageContext.request.contextPath}/static/wang/wangEditor.min.js"></script>

    <style>
        #box{
            width: 1000px;
            margin: 0 auto;
            margin-top: 100px;
        }
    </style>
</head>
<body>

<div id="box">
    <h1>商品上传</h1>
    <form action="${pageContext.request.contextPath}/pro" method="post">
        <div class="form-group">
            <label for="Title">Title</label>
            <input type="text" class="form-control" id="Title" name="pname" placeholder="Title">
        </div>
        <div class="form-group">
            <label for="Market_price">Market_price</label>
            <input type="number" class="form-control" id="Market_price" name="marketPrice" placeholder="Market_price">
        </div>
        <div class="form-group">
            <label for="shop_price">shop_price</label>
            <input type="number" class="form-control" id="shop_price" name="shopPrice" placeholder="shop_price">
        </div>
        <div class="form-group">
            <label for="date">date</label>
            <input type="datetime-local" class="form-control" id="date" name="createdate" placeholder="date">
        </div>
        <div class="form-group">
            <label for="categorySecond">CategorySecond</label>
            <select class="form-control" id="categorySecond" name="csid">
                <c:forEach var="csList" items="${allCategorySecond}">
                    <option value="${csList.csid}">${csList.csname}</option>
                </c:forEach>
            </select>
        </div>
        <div class="form-group" id="showImg">
            <label for="upload">upload</label>
            <input type="hidden" name="pdesc" id="content">
            <div id="upload" class="dropzone"></div>

        </div>
        <div class="form-group">
            <div id="editor" id="pdesc">
            </div>
        </div>

        <button type="submit" class="btn btn-success" id="btn">Submit</button>
    </form>
</div>

</body>
<script>
    var myDropzone = new Dropzone("#upload", {
        url: "/file",
        paramName:"dropFile",
        dictDefaultMessage:"拖拽或点击上传文件",
        addRemoveLinks: true,
        dictRemoveFile: '删除',
        dictCancelUpload: "取消",
        maxFiles: 6,//一次性上传文件的最大数量
        maxFilesize: 2, //文件大小 MB
        parallelUploads: 1, // 同时上传多少张
        timeout: 300000,
        acceptedFiles: "image/*,.mp4,.MOV,.wmv,.jpg,.gif,.png,.jpeg",//上传类型
        init:function () {
            this.on("success",function (file,data) {
                //上传成功触发事件
                console.log("file="+file);
                console.log("文件名，data="+data);
                //disable后台接收不到参数
                $("#showImg").append(`<input class="form-control" name="showFileName" value='${'${data}'}' type="text" placeholder="FileName" readonly>`)
            })
            this.on("removedfile",function (file) {
                console.log(file)
                console.log(file.name)
                var fileNameArr = $("[name='showFileName']")
                console.log(fileNameArr.length)
                //获取的是整个input标签，只能用each遍历
                $(fileNameArr).each(function(){
                    if($(this).val().indexOf(file.name)>0){
                        console.log($(this).val())
                        $(this).remove()
                        $.ajax({
                            url:"${pageContext.request.contextPath}/delFile?delFileName="+$(this).val(),
                            success:function (data) {
                                console.log(data)
                            }
                        })
                    }
                })
            })
        }
    });


//    wang
    var E = window.wangEditor
    var editor = new E('#editor')
    // 或者 var editor = new E( document.getElementById('editor') )
    editor.create()
        // 读取 html
        // alert(editor.txt.text())
        <%--$.ajax({--%>
            <%--url:"${pageContext.request.contextPath}/pro?pdesc="+editor.txt.text(),--%>
            <%--success:function (data) {--%>
                <%--console.log(data)--%>
            <%--}--%>
        <%--})--%>

        <%--$('#myForm').submit(function () {--%>
            <%--alert(editor.txt.text())--%>
            <%--var pro =$("form").serialize();--%>
            <%--&lt;%&ndash;$('form').ajaxSubmit({&ndash;%&gt;--%>
                <%--&lt;%&ndash;url : '${pageContext.request.contextPath}/pro?pdesc='+editor.txt.text(),&ndash;%&gt;--%>
                <%--&lt;%&ndash;data :{&ndash;%&gt;--%>
                    <%--&lt;%&ndash;product:pro&ndash;%&gt;--%>
                <%--&lt;%&ndash;},&ndash;%&gt;--%>
                <%--&lt;%&ndash;success: function(data) {&ndash;%&gt;--%>
                    <%--&lt;%&ndash;//返回数据处理&ndash;%&gt;--%>
                    <%--&lt;%&ndash;alert(data);&ndash;%&gt;--%>
                <%--&lt;%&ndash;}&ndash;%&gt;--%>
            <%--&lt;%&ndash;});&ndash;%&gt;--%>
            <%--return false;         //阻止表单默认提交--%>
        <%--});--%>

        $("#btn").click(function () {
            var content  = editor.txt.html();
            $("#content").val(content);

        })
</script>
</html>
```

```java
 @RequestMapping("/pro")
    public String pro(Product product,HttpServletRequest request){
        System.out.println(product);
        if (request.getParameterValues("showFileName")!=null){
            System.out.println(product);
            String[] showFileNames = request.getParameterValues("showFileName");
            StringBuffer stringBuffer = new StringBuffer();
            for (String showFileName : showFileNames) {
                stringBuffer.append("images/"+showFileName+"#");
            }
            String fileNames = stringBuffer.toString();
            fileNames  = fileNames.substring(0,fileNames.length()-1);
            //处理最后一个“_”
            System.out.println(fileNames);
            product.setImage(fileNames);

            //添加
            productService.insertPro(product);
        }
        return "redirect:/proList";
    }
```

上传图片

```java
    @ResponseBody
    @RequestMapping("/file")
    public String file(MultipartFile dropFile, MultipartFile wangFile, HttpServletRequest request) {
        if (dropFile!= null) {
            //文件名
            String filename = dropFile.getOriginalFilename();
            filename = UUID.randomUUID() + "_" + filename;
            //     /代表/Users/liujiang/Documents/IDEA/dropzone-wang/src/main/webapp/
            //webapp下
            String filePath = request.getSession().getServletContext().getRealPath("/static/images");
            File file = new File(filePath);
            if (!file.exists()) {
                file.mkdir();
            }
            file = new File(filePath, filename);
            try {
                dropFile.transferTo(file);
//            int a = 1/0;
            } catch (Exception e) {
                System.out.println(file.getName());
                e.printStackTrace();
                boolean delete = file.delete();
                System.out.println(delete);
            }
            String json = JSON.toJSONString(file.getName());

            return json;

        }
        if (wangFile!= null) {
            //文件名
            String filename = wangFile.getOriginalFilename();
            filename = UUID.randomUUID() + "_" + filename;
            //     /代表/Users/liujiang/Documents/IDEA/dropzone-wang/src/main/webapp/
            //webapp下
            String filePath = request.getSession().getServletContext().getRealPath("/static/images");
            File file = new File(filePath);
            if (!file.exists()) {
                file.mkdir();
            }
            file = new File(filePath, filename);
            try {
                wangFile.transferTo(file);
//            int a = 1/0;
            } catch (Exception e) {
                System.out.println(file.getName());
                e.printStackTrace();
                boolean delete = file.delete();
                System.out.println(delete);
            }
            Map<String,Object> map = new HashMap<>();
            map.put("errno",0);
            String serverPath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort();
            map.put("data",new String[]{serverPath+"/static/images/"+file.getName()});
            String s = JSON.toJSONString(map);
            System.out.println(s);
            return s;

        }
        return "no";
    }
```

```js
  //    wang
    var E = window.wangEditor;
    var editor = new E('#editor');
    //其中/upload是上传图片的服务器端接口
    editor.customConfig.uploadImgServer = '/file';
    // // 将图片大小限制为 3M
    // editor.customConfig.uploadImgMaxSize = 3 * 1024 * 1024;
    // // 限制一次最多上传 5 张图片
    // editor.customConfig.uploadImgMaxLength = 5;
    editor.customConfig.uploadFileName = 'wangFile';
    editor.create();
```

### 服务端验证spring validation

pom

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
我们只需要引入spring-boot-starter-web依赖即可，包含如下的依赖：

<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>5.3.4.Final</version>
</dependency>


```

```html
 <div class="form-group">
            <label for="Title">Title</label>
            <input type="text" class="form-control" id="Title" name="pname" placeholder="Title" value="${errMap==null?'':errMap.pname}">
        </div>
```

```java
 @RequestMapping("/pro")
    public String pro(@Validated Product product, BindingResult bindingResult, HttpServletRequest request,Model model) {
        System.out.println(product);
        if(bindingResult.hasErrors()){
            Map<String,String> errMap = new HashMap<>();
            for (FieldError fieldError : bindingResult.getFieldErrors()) {
                //...
                System.out.println(fieldError.getField());
                System.out.println(fieldError.getDefaultMessage());
                errMap.put(fieldError.getField(),fieldError.getDefaultMessage());
            }
            model.addAttribute("errMap",errMap);
            return "index";
        }
        if (request.getParameterValues("showFileName") != null) {
            System.out.println(product);
            String[] showFileNames = request.getParameterValues("showFileName");
            StringBuffer stringBuffer = new StringBuffer();
            for (String showFileName : showFileNames) {
                stringBuffer.append("images/" + showFileName + "#");
            }
            String fileNames = stringBuffer.toString();
            fileNames = fileNames.substring(0, fileNames.length() - 1);
            //处理最后一个“_”
            System.out.println(fileNames);
            product.setImage(fileNames);

            //添加
            productService.insertPro(product);
        }
        return "redirect:/proList";
    }
```

```java
package com.aishang.dropzone.wang.entity;

import lombok.Data;
import org.hibernate.validator.constraints.Length;

import javax.validation.constraints.Min;
import javax.validation.constraints.NotBlank;

@Data
public class Product {
    private Integer pid;
    @NotBlank
    @Length(min = 6,max = 20,message = "名称不符合规范")
    private String pname;
    private double marketPrice;
    private double shopPrice;
    private String image;
    private String pdesc;
    private Integer is_hot;
    private String createdate;
    private Integer csid;
    private Integer stock;
    private String startTime;
    private String endTime;
}





public class Foo {

    @NotBlank
    private String name;

    @Min(18)
    private Integer age;

    @Pattern(regexp = "^1(3|4|5|7|8)\\d{9}$",message = "手机号码格式错误")
    @NotBlank(message = "手机号码不能为空")
    private String phone;

    @Email(message = "邮箱格式错误")
    private String email;

    //... getter setter

}

```

- 参数Foo前需要加上@Validated注解，表明需要spring对其进行校验，而校验的信息会存放到其后的BindingResult中。注意，必须相邻，如果有多个参数需要校验，形式可以如下。foo(@Validated Foo foo, BindingResult fooBindingResult ，@Validated Bar bar, BindingResult barBindingResult);即一个校验类对应一个校验结果。

- 校验结果会被自动填充，在controller中可以根据业务逻辑来决定具体的操作，如跳转到错误页面。

```java
JSR提供的校验注解：         
@Null   被注释的元素必须为 null    
@NotNull    被注释的元素必须不为 null    
@AssertTrue     被注释的元素必须为 true    
@AssertFalse    被注释的元素必须为 false    
@Min(value)     被注释的元素必须是一个数字，其值必须大于等于指定的最小值    
@Max(value)     被注释的元素必须是一个数字，其值必须小于等于指定的最大值    
@DecimalMin(value)  被注释的元素必须是一个数字，其值必须大于等于指定的最小值    
@DecimalMax(value)  被注释的元素必须是一个数字，其值必须小于等于指定的最大值    
@Size(max=, min=)   被注释的元素的大小必须在指定的范围内    
@Digits (integer, fraction)     被注释的元素必须是一个数字，其值必须在可接受的范围内    
@Past   被注释的元素必须是一个过去的日期    
@Future     被注释的元素必须是一个将来的日期    
@Pattern(regex=,flag=)  被注释的元素必须符合指定的正则表达式    


Hibernate Validator提供的校验注解：  
@NotBlank(message =)   验证字符串非null，且长度必须大于0    
@Email  被注释的元素必须是电子邮箱地址    
@Length(min=,max=)  被注释的字符串的大小必须在指定的范围内    
@NotEmpty   被注释的字符串的必须非空    
@Range(min=,max=,message=)  被注释的元素必须在合适的范围内
```

#### 自定义校验

业务需求总是比框架提供的这些简单校验要复杂的多，我们可以自定义校验来满足我们的需求。自定义spring validation非常简单，主要分为两步。

1 自定义校验注解
我们尝试添加一个“字符串不能包含空格”的限制。

```java
@Target({METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER})
@Retention(RUNTIME)
@Documented
@Constraint(validatedBy = {CannotHaveBlankValidator.class})<1>
public @interface CannotHaveBlank {

    //默认错误消息
    String message() default "不能包含空格";

    //分组
    Class<?>[] groups() default {};

    //负载
    Class<? extends Payload>[] payload() default {};

    //指定多个时使用
    @Target({FIELD, METHOD, PARAMETER, ANNOTATION_TYPE})
    @Retention(RUNTIME)
    @Documented
    @interface List {
        CannotHaveBlank[] value();
    }

}
```

我们不需要关注太多东西，使用spring validation的原则便是便捷我们的开发，例如payload，List ，groups，都可以忽略。

<1> 自定义注解中指定了这个注解真正的验证者类。

```java
public class CannotHaveBlankValidator implements <1> ConstraintValidator<CannotHaveBlank, String> {

    @Override
    public void initialize(CannotHaveBlank constraintAnnotation) {
    }


    @Override
    public boolean isValid(String value, ConstraintValidatorContext context <2>) {
        //null时不进行校验
        if (value != null && value.contains(" ")) {
            <3>
            //获取默认提示信息
            String defaultConstraintMessageTemplate = context.getDefaultConstraintMessageTemplate();
            System.out.println("default message :" + defaultConstraintMessageTemplate);
            //禁用默认提示信息
            context.disableDefaultConstraintViolation();
            //设置提示语
            context.buildConstraintViolationWithTemplate("can not contains blank").addConstraintViolation();
            return false;
        }
        return true;
    }
}

```

<1> 所有的验证者都需要实现ConstraintValidator接口，它的接口也很形象，包含一个初始化事件方法，和一个判断是否合法的方法。

```java
public interface ConstraintValidator<A extends Annotation, T> {

    void initialize(A constraintAnnotation);

    boolean isValid(T value, ConstraintValidatorContext context);
}

```

<2> ConstraintValidatorContext 这个上下文包含了认证中所有的信息，我们可以利用这个上下文实现获取默认错误提示信息，禁用错误提示信息，改写错误提示信息等操作。

<3> 一些典型校验操作，或许可以对你产生启示作用。

值得注意的一点是，自定义注解可以用在METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER之上，ConstraintValidator的第二个泛型参数T，是需要被校验的类型。


### maven手动解决jar依赖

```xml
<plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-install-plugin</artifactId>
                <version>2.5.2</version>
                <executions>
                    <execution>
                        <!--唯一的，自己写，我要安装验证码-->
                        <id>install-kaptcha</id>
                        <!--触发时机-->
                        <phase>clean</phase>
                        <configuration>
                            <!--依赖文件的位置-->
                            <file>/Users/liujiang/Documents/jar/kaptcha-2.3.jar</file>
                            <!--默认仓库位置-->
                            <repositoryLayout>default</repositoryLayout>
                            <!--自定义坐标-->
                            <groupId>com.aishang.kaptcha</groupId>
                            <artifactId>kaptcha</artifactId>
                            <version>2.3</version>
                            <packaging>jar</packaging>
                            <!--是否自动生成pom文件-->
                            <generatePom>true</generatePom>
                        </configuration>
                        <goals>
                            <goal>install-file</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
```

终端命令mvn clean