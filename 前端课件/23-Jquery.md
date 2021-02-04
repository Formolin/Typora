# Jquery

## 一、jquery属性操作

#### 1、html() 取出或设置html内容

```js
//设置内容
$(“div”).html(“<span>这是一段内容</span>”);
//获取内容
$(“div”).html()


```

#### 2、text() 取出或设置text内容

```js
//设置内容
$(“div”).text(“<span>这是一段内容</span>”);
//获取内容
$(“div”).text()
```

#### 3、attr() 取出或设置某个属性的值,主要可以获取自定义属性

```js
// 取出图片的地址

var $src = $('#img1').attr('src');

// 设置图片的地址和alt属性

$('#img1').attr({ src: "test.jpg", alt: "Test Image" });
$('img').attr('src','../资料/2.jpeg')

<div index="1"></div>
$('div').attr('index');
```

- prop()
  - 专门操作属性值为boolean的属性
- attr()
  - 专门操作属性值为非boolean的属性

#### 案例：全选、全不选

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>10_属性</title>
</head>
<body>

<input type="checkbox">
<input type="checkbox">
<br>
<button>选中</button>
<button>不选中</button>



<script src="../资料/jquery.min.js" type="text/javascript"></script>
<script type="text/javascript">

//  1. 点击'全选'按钮实现全选
    $('button:first').click(function () {
      $(':checkbox').prop('checked',true)
    })
//  2. 点击'全不选'按钮实现全不选
    $('button:last').click(function () {
      $(':checkbox').prop('checked', false)
    })

</script>
</body>
</html>


```



#### 4、val（）

```none
设置值
var $src = $('input').val('设置值');
获取值
var $src = $('input').val();
```



#### 5、width方法与height方法

```javascript
//带参数表示设置高度
$(“div”).height(200);
//不带参数获取高度
$(“div”).height();
```

获取网页的可视区宽高`javascript //获取可视区宽度 $(window).width(); //获取可视区高度 $(window).height();`

#### 6、scrollTop与scrollLeft

> 设置或者获取垂直滚动条的位置

```javascript
//获取页面被卷曲的高度
$(window).scrollTop();
//获取页面被卷曲的宽度
$(window).scrollLeft();



$(window).scroll(function () {
    console.log($(window).scrollTop());
})

```

#### 【案例：固定菜单栏】

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title></title>
  <style>
    body {
        height: 2000px;
    }
    *{
        margin: 0;
        padding: 0;
    }
    div {
        width: 100%;
        height: 50px;
        background-color: pink;
    }
    .active{
        position: fixed;
    }
</style>
</head>
<body>
<script src="../资料/jquery.min.js" type="text/javascript"></script>
<div></div>
<script>
    $(window).scroll(function () {
       if($(this).scrollTop()>=300){
          $('div').addClass('active')
       }
    })

</script>
</body>
</html>


```

#### 【案例：小火箭返航案例】

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title></title>
  <style>
    body {
        height: 5000px;
    }

    * {
        margin: 0;
        padding: 0;
    }

    .d {
        width: 50px;
        height: 50px;
        background-color: pink;
        display: none;
        margin-top: 600px;
    }

    .active {
        position: fixed;
    }
</style>
<script src="../资料/jquery.min.js"></script>
</head>

<body>
<div>top</div>
<div class="d" id="d">返回顶部</div>
<script>
    $(window).scroll(function () {
        if ($(this).scrollTop() >= 1000) {
            $('.d').css('display', 'block')
            $('.d').addClass('active')
        } else {
            $('.d').css('display', 'none')
        }

    })
    $('#d').click(function () {
        $(window).scrollTop(0)
    })
</script>
</body>

</html>
```



#### 7、offset方法与position方法

> offset方法获取元素距离document的位置，position方法获取的是元素距离有定位的父元素的位置。

```javascript
//返回值为对象：{left:100, top:100}
$(selector).offset();
//获取相对于父元素左上角的坐标
$(selector).position();
```

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="../资料/jquery.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
        }

        .d1 {
            width: 100px;
            height: 100px;
            background-color: pink;
            position: relative;
            left: 10px;
        }

        .d2 {
            width: 50px;
            height: 50px;
            background-color: yellow;
            position: absolute;
            left: 10px;
        }
    </style>
</head>

<body>
    <div class="d1">
        <div class="d2"></div>
    </div>

    <script>
        var d1offset = $('.d1').offset();
        var d2position = $('.d2').position();
        console.log(d1offset.left);
        console.log(d2position.left);

    </script>
</body>

</html>
```



## 二、jquery元素节点操作

### 遍历

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="../资料/jquery.min.js"></script>
</head>

<body>
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <div>4</div>
    <script>
        $('div').each(function (i, item) {
            console.log(i);//索引
            console.log(item);//dom元素
            // 转换成jquery对象
            $(item).css('color','red')
        })
    </script>
</body>

</html>
```

- $.each 主要处理数据（数组,对象）
  - function里的第一个参数是索引或者对象的key

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="../资料/jquery.min.js"></script>
</head>

<body>
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <div>4</div>
    <script>
        var arr =  ['red','blue','green']
        $.each(arr, function (i, item) { 
             console.log(item);
        });

        $.each({
            name:'tom',
            age:18
        }, function (i, item) { 
             console.log(item);
        });
    </script>
</body>

</html>
```

### 创建节点

```none
var div = $('<div></div>');
var div2 = $('<div>这是一个div元素</div>');
```

### 插入节点

 1、append()：在现存元素的内部，从后面插入元素

2、prepend()：在现存元素的内部，从前面插入元素

3、after('内容')：把内容放入目标元素后面

4、before（‘内容’）：把内容放入目标元素前面

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="../资料/jquery.min.js"></script>
</head>

<body>
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <div>4</div>
    <script>
        var div = $('<div></div>');
        var div2 = $('<div>这是一个div元素</div>');
        $('div:first').prepend(div)
        $('div:first').append(div2)
        $('div:first').after(div2)
        $('div:last').before(div)
    </script>
</body>

</html>
```



### 删除节点

- remove()删除节点
- empty()删除匹配元素里的子节点

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="../资料/jquery.min.js"></script>
</head>

<body>
    <div>1</div>
    <div>
        <p>111</p>
    </div>
    <div>3</div>
    <div>4</div>
    <script>
        $('div:first').remove()//删除节点
        $('div:eq(0)').empty()//删除p标签-》删除匹配元素里的子节点
    </script>
</body>

</html>
```

## 三、事件

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="../资料/jquery.min.js"></script>
</head>

<body>
    <div>1</div>
    <script>
        $(function () {
            // 1. 单个事件注册
            $("div").click(function () {
                $(this).css("background", "purple");
            });
            $("div").mouseenter(function () {
                $(this).css("background", "skyblue");
            });
        })
    </script>
</body>

</html>
```

事件函数列表：

```none
blur() 元素失去焦点
focus() 元素获得焦点
change() 表单元素的值发生变化
click() 鼠标单击
dblclick() 鼠标双击
mouseover() 鼠标进入（进入子元素也触发）
mouseout() 鼠标离开（离开子元素也触发）
mouseenter() 鼠标进入（进入子元素不触发）
mouseleave() 鼠标离开（离开子元素不触发）
hover() 同时为mouseenter和mouseleave事件指定处理函数
mouseup() 松开鼠标
mousedown() 按下鼠标
mousemove() 鼠标在元素内部移动
keydown() 按下键盘
keypress() 按下键盘
keyup() 松开键盘
load() 元素加载完毕
ready() DOM加载完成
resize() 浏览器窗口的大小发生改变
scroll() 滚动条的位置发生变化
select() 用户选中文本框中的内容
submit() 用户递交表单
toggle() 根据鼠标点击的次数，依次运行多个函数
unload() 用户离开页面
```

### on()可以绑定一个或多个事件

- on(): 用于事件绑定，目前最好用的事件绑定方法

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="../资料/jquery.min.js"></script>
    <style>
        div{
            width: 100px;
            height: 100px;
            background-color: antiquewhite;
        }
        .current{
            font-size: 44px;
            color: red;
            background-color: lightcyan;
        }
    </style>
</head>

<body>
    <div>1</div>
    <ul>
        <li>我们都是好孩子</li>
        <li>我们都是好孩子</li>
        <li>我们都是好孩子</li>
    </ul>
    <ol></ol>
    <script>
        $(function () {
            // 1. 单个事件注册
            // $("div").click(function () {
            //     $(this).css("background", "purple");
            // });
            // $("div").mouseenter(function () {
            //     $(this).css("background", "skyblue");
            // });
            // 2、on可以绑定1个或者多个事件处理程序
            // $("div").on({
            //     mouseenter: function () {
            //         $(this).css("background", "purple");
            //     },
            //     click: function () {
            //         $(this).css("background", "skyblue");
            //     }
            // })
            
            $('div').on("mouseenter click",function(){
                $(this).toggleClass('current')
            })

            // 3 on可以实现事件委托（委派）
       
            // $("ul li").click();每个li都绑定了事件
            $("ul").on("click", "li", function() {
                     // click 是绑定在ul 身上的，但是 触发的对象是 ul 里面的小li
                alert(11);
            });

            // (3) on可以给未来动态创建的元素绑定事件
            $("ol").on("click", "li", function() {
                alert(11);
            })
            var li = $("<li>我是后来创建的</li>");
            $("ol").append(li);
        })
    </script>
</body>

</html>
```

### 案例：发布微博案例

```html
<!DOCTYPE html>
<html>

<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <style>
        * {
            margin: 0;
            padding: 0
        }
        
        ul {
            list-style: none
        }
        
        .box {
            width: 600px;
            margin: 100px auto;
            border: 1px solid #000;
            padding: 20px;
        }
        
        textarea {
            width: 450px;
            height: 160px;
            outline: none;
            resize: none;
        }
        
        ul {
            width: 450px;
            padding-left: 80px;
        }
        
        ul li {
            line-height: 25px;
            border-bottom: 1px dashed #cccccc;
            
        }
        
        input {
            float: right;
        }
        
        ul li a {
            float: right;
        }
    </style>
    <script src="../资料/jquery.min.js"></script>
    <script>

    </script>
</head>

<body>
    <div class="box" id="weibo">
        <span>微博发布</span>
        <textarea name="" class="txt" cols="30" rows="10"></textarea>
        <button class="btn">发布</button>
        <ul>
        </ul>
    </div>
</body>

</html>
```

```js
$(function() {
    // 1.点击发布按钮， 动态创建一个小li，放入文本框的内容和删除按钮， 并且添加到ul 中
    $(".btn").on("click", function() {
        var li = $("<li></li>");
        li.html($(".txt").val() + "<a href='javascript:;'> 删除</a>");
        $("ul").prepend(li);
        $(".txt").val("");
    })

    // 2.点击的删除按钮，可以删除当前的微博留言li
    // $("ul a").click(function() {  // 此时的click不能给动态创建的a添加事件
    //     alert(11);
    // })
    // on可以给动态创建的元素绑定事件
    $("ul").on("click", "a", function() {
        $(this).parent().remove();
    })

})
```





# 作业

改造之前项目特效

- 全选反选
- 广告两侧跟随
- 固定导航栏
- 返回顶部
- 放大镜
- 轮播图



全选反选

```html
<!DOCTYPE html>
<html>

<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <style>
        * {
            padding: 0;
            margin: 0;
        }

        .wrap {
            width: 300px;
            margin: 100px auto 0;
        }

        table {
            border-collapse: collapse;
            border-spacing: 0;
            border: 1px solid #c0c0c0;
        }

        th,
        td {
            border: 1px solid #d0d0d0;
            color: #404060;
            padding: 10px;
        }

        th {
            background-color: #09c;
            font: bold 16px "微软雅黑";
            color: #fff;
        }

        td {
            font: 14px "微软雅黑";
        }

        tbody tr {
            background-color: #f0f0f0;
        }

        tbody tr:hover {
            cursor: pointer;
            background-color: #fafafa;
        }
    </style>
    <script src="jquery-1.11.1.js"></script>
    <script>
        jQuery(document).ready(function () {
            //需求1: 点击上面的input，下面所有input的checked和上面的保持一致;
            //需求2: 点击下面的每一个input，下面所有input被选中的个数为4个上面才被选中，否则不被选中;


            //需求1: 点击上面的input，下面所有input的checked和上面的保持一致;
            $("#j_cbAll").click(function () {
                $("#j_tb input").prop("checked",$("#j_cbAll").prop("checked"));
            });
            //需求2: 点击下面的每一个input，下面所有input被选中的个数为4个上面才被选中，否则不被选中;
            $("#j_tb input").click(function () {
                var bool = $("#j_tb input:checked").length===4;
                $("#j_cbAll").prop("checked", bool);
            });
        });
    </script>
</head>
<body>


    <div class="wrap">
        <table>
            <thead>
                <tr>
                    <th>
                        <input type="checkbox" id="j_cbAll"/>
                    </th>
                    <th>课程名称</th>
                    <th>所属学院</th>
                </tr>
            </thead>
            <tbody id="j_tb">
                <tr>
                    <td>
                        <input type="checkbox"/>
                    </td>
                    <td>JavaScript</td>
                    <td>前端与移动开发学院</td>
                </tr>
                <tr>
                    <td>
                        <input type="checkbox"/>
                    </td>
                    <td>css</td>
                    <td>前端与移动开发学院</td>
                </tr>
                <tr>
                    <td>
                        <input type="checkbox"/>
                    </td>
                    <td>html</td>
                    <td>前端与移动开发学院</td>
                </tr>
                <tr>
                    <td>
                        <input type="checkbox"/>
                    </td>
                    <td>jQuery</td>
                    <td>前端与移动开发学院</td>
                </tr>
                <!--<tr>
                    <td><input type="checkbox"/></td>
                    <td>HTML5</td>
                    <td>前端与移动开发学院</td>
                </tr>
                <tr>
                    <td><input type="checkbox"/></td>
                    <td>CSS3</td>
                    <td>前端与移动开发学院</td>
                </tr>-->
            </tbody>
        </table>
</div>


</body>
</html>

```

穿梭框

```html

<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        select {
            width: 200px;
            height: 300px;
            font-size: 22px;
            background-color: #80ff45;
        }
    </style>
    <script src="jquery-1.11.1.js"></script>
    <script>
        jQuery(window).ready(function () {
            //需求1：点击前两个按钮，一侧的所有option移动到另一侧的select标签中;
            //需求2：点击后两个按钮，一侧的所有option中"被选中"的全部移动到另一侧的select标签中;


            //以前我们要一把一把操作需要借助for循环;有了jQuery之后，就不用for循环;
            //需求1：点击前两个按钮，一侧的所有option移动到另一侧的select标签中;
            $("button").eq(0).click(function () {
                //左侧所有option全部移动到右侧select中(不用for循环)
                $("#sel2").append($("#sel1>option"));
            });
            $("button").eq(1).click(function () {
                //右侧所有option全部移动到左侧select中(不用for循环)
                $("#sel1").append($("#sel2>option"));
            });

            //需求2：点击后两个按钮，一侧的所有option中"被选中"的全部移动到另一侧的select标签中;
                    //(:selected)
            $("button").eq(2).click(function () {
                //左侧所有option全部移动到右侧select中(不用for循环)
                $("#sel2").append($("#sel1>option:selected"));
            });
            $("button").eq(3).click(function () {
                //右侧所有option全部移动到左侧select中(不用for循环)
                $("#sel1").append($("#sel2>option:selected"));
            });


            //优化封装;
//            function fn(str1,str2) {
//                //右侧所有option全部移动到左侧select中(不用for循环)
//                $(str1).append($(str2));
//            }
        });
    </script>
</head>
<body>

    <select name="" id="sel1" size="4" multiple>
        <option value="">香蕉</option>
        <option value="">大鸭梨</option>
        <option value="">苹果</option>
        <option value="">大西瓜</option>
    </select>

    <button>&gt;&gt;&gt;</button>
    <button>&lt;&lt;&lt;</button>
    <button>&gt;</button>
    <button>&lt;</button>

    <select name="" id="sel2" size="4" multiple></select>




</body>
</html>
```









