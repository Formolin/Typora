### python -m turtle

```python
import turtle  #引入一个绘图库
import math  #因为后面用到了根号，所以引入了math库
turtle.setup(650,350,200,200)  #确定画布的大小和位置
turtle.penup()  #提起画笔
turtle.pendown()  #放下画笔
turtle.pensize(7)  #设置画笔的粗细
turtle.pencolor("gold")  #设置画笔颜色
turtle.fillcolor('#33cc8c')  #设置填充颜色
turtle.seth(30)  #设置画笔绘制方向，参数值为直角坐标的角度
turtle.begin_fill()  #准备开始填充图形
turtle.forward(200)  #向指定方向直行
turtle.seth(-90)  #改变画笔绘制方向
turtle.fd(200)
turtle.seth(150)
turtle.fd(200)
turtle.seth(270)
turtle.circle(math.sqrt(3)*200/3,420)  #画圆，前一个参数是圆的半径，后一个参数是要画的角度
turtle.seth(90)
turtle.fd(200)
turtle.seth(-30)
turtle.fd(200)
turtle.seth(210)
turtle.fd(200)
turtle.end_fill()  #结束填充颜色
turtle.done()  #停止画笔绘制，使绘画窗口不关闭
```

```python
import turtle
t = turtle.Pen()
t.color("red")  #设置画笔颜色
t.begin_fill() #准备填充颜色
t.fillcolor('#33cc8c')  #填充的颜色
t.circle(100,360) #t.circle(radius,360)画笔方向逆时针画圆,半径100，旋转180度，即半圆,当radius值为正数时，圆心在当前位置/小海龟左侧。当radius值为负数时，圆心在当前位置/小海龟右侧。
t.end_fill() #结束填充
```









































1、Python编程基础（初级）（参考学时：12学时/8次课 + 按需辅导课）

    Phthon安装、熟悉开发环境
    
    输出文本、代码文件操作
    
    数值计算、变量
    
    字符串、列表、元组、字典
    
    条件语句
    
    循环语句
    
    简单作图
    
    （函数）

2、Python编程基础（中级）（参考学时：15学时/10次课 + 按需辅导课）

    （函数）
    
    类与对象
    
    Python自带函数
    
    Python常用模块
    
    高级作图
    
    简单的游戏开发

3、Python编程基础（高级）（参考学时：18学时/12次课 + 按需辅导课）

    用户界面开发
    
    音视频处理
    
    数据库
    
    动态网页制作
    
    数据分析
    
    人工智能


4、Python项目开发（初级）（参考学时：12学时/8次课 + 按需辅导课）

    游戏开发
    
    管理系统

5、Python项目开发（中级）（参考学时：15学时/10次课 + 按需辅导课）

    音视频系统开发
    
    动态网站制作

6、Python项目开发（高级）（参考学时：18学时/12次课 + 按需辅导课）

    大数据分析
    
    人工智能

7、Python竞赛辅导

【项目开发部分（4～6）会根据情况灵活调整，难度均在其接受范围之内】

Python少儿编程主要针对9-15岁的小孩子.

![img](http://ww2.sinaimg.cn/large/006tNc79ly1g3ut9fb2cnj30jg0irdgj.jpg)

2. 画笔

2.1 画笔的状态

        在画布上，默认有一个坐标原点为画布中心的坐标轴，坐标原点上有一只面朝x轴正方向小乌龟。这里我们描述小乌龟时使用了两个词语：坐标原点(位置)，面朝x轴正方向(方向)， turtle绘图中，就是使用位置方向描述小乌龟(画笔)的状态。

2.2 画笔的属性

        画笔(画笔的属性，颜色、画线的宽度等)
    
        1) turtle.pensize()：设置画笔的宽度；
    
        2) turtle.pencolor()：没有参数传入，返回当前画笔颜色，传入参数设置画笔颜色，可以是字符串如"green", "red",也可以是RGB 3元组。
    
        3) turtle.speed(speed)：设置画笔移动速度，画笔绘制的速度范围[0,10]整数，数字越大越快。



(1)    画笔运动命令

| 命令                      | 说明                                               |
| ------------------------- | -------------------------------------------------- |
| turtle.forward(distance)  | 向当前画笔方向移动distance像素长度                 |
| turtle.backward(distance) | 向当前画笔相反方向移动distance像素长度             |
| turtle.right(degree)      | 顺时针移动degree°                                  |
| turtle.left(degree)       | 逆时针移动degree°                                  |
| turtle.pendown()          | 放下画笔                                           |
| turtle.goto(x,y)          | 将画笔移动到坐标为x,y的位置                        |
| turtle.penup()            | 提起笔移动，不绘制图形，用于另起一个地方绘制       |
| turtle.circle()           | 画圆，半径为正(负)，表示圆心在画笔的左边(右边)画圆 |
| setx( )                   | 将当前x轴移动到指定位置                            |
| sety( )                   | 将当前y轴移动到指定位置                            |
| setheading(angle)         | 设置当前朝向为angle角度                            |
| home()                    | 设置当前画笔位置为原点，朝向东。                   |
| dot(r)                    | 绘制一个指定直径和颜色的圆点                       |

 

(2)     画笔控制命令

| 命令                          | 说明                                      |
| ----------------------------- | ----------------------------------------- |
| turtle.fillcolor(colorstring) | 绘制图形的填充颜色                        |
| turtle.color(color1, color2)  | 同时设置pencolor=color1, fillcolor=color2 |
| turtle.filling()              | 返回当前是否在填充状态                    |
| turtle.begin_fill()           | 准备开始填充图形                          |
| turtle.end_fill()             | 填充完成                                  |
| turtle.hideturtle()           | 隐藏画笔的turtle形状                      |
| turtle.showturtle()           | 显示画笔的turtle形状                      |

 

(3)    全局控制命令

| 命令                                                        | 说明                                                         |
| ----------------------------------------------------------- | ------------------------------------------------------------ |
| turtle.clear()                                              | 清空turtle窗口，但是turtle的位置和状态不会改变               |
| turtle.reset()                                              | 清空窗口，重置turtle状态为起始状态                           |
| turtle.undo()                                               | 撤销上一个turtle动作                                         |
| turtle.isvisible()                                          | 返回当前turtle是否可见                                       |
| stamp()                                                     | 复制当前图形                                                 |
| turtle.write(s [,font=("font-name",font_size,"font_type")]) | 写文本，s为文本内容，font是字体的参数，分别为字体名称，大小和类型；font为可选项，font参数也是可选项 |

 

(4)    其他命令

| 命令                             | 说明                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| turtle.mainloop()或turtle.done() | 启动事件循环 -调用Tkinter的mainloop函数。必须是乌龟图形程序中的最后一个语句。 |
| turtle.mode(mode=None)           | 设置乌龟模式（“standard”，“logo”或“world”）并执行重置。如果没有给出模式，则返回当前模式。模式初始龟标题正角度standard向右（东）逆时针logo向上（北）顺时针 |
| turtle.delay(delay=None)         | 设置或返回以毫秒为单位的绘图延迟。                           |
| turtle.begin_poly()              | 开始记录多边形的顶点。当前的乌龟位置是多边形的第一个顶点。   |
| turtle.end_poly()                | 停止记录多边形的顶点。当前的乌龟位置是多边形的最后一个顶点。将与第一个顶点相连。 |
| turtle.get_poly()                | 返回最后记录的多边形。                                       |

/usr/local/Cellar/python/3.7.3/Frameworks/Python.framework/Versions/3.7/lib/python3.7/turtle.py

pycharm和turtle库有冲突，不能自己识别出turtle下的方法：

解决方案
找到turtle.py， 
你可以对turtle库的源码进行如下修改： 
注释掉原来的_ all_,新增如下：

```sh
# __all__ = (_tg_classes + _tg_screen_functions + _tg_turtle_functions +
#            _tg_utilities + ['Terminator']) # + _math_functions)
__all__ = ['ScrolledCanvas', 'TurtleScreen', 'Screen', 'RawTurtle', 'Turtle', 'RawPen', 'Pen', 'Shape', 'Vec2D', 'back',
           'backward', 'begin_fill', 'begin_poly', 'bk', 'addshape', 'bgcolor', 'bgpic', 'bye', 'clearscreen',
           'colormode', 'delay', 'exitonclick', 'getcanvas', 'getshapes', 'listen', 'mainloop', 'mode', 'numinput',
           'onkey', 'onkeypress', 'onkeyrelease', 'onscreenclick', 'ontimer', 'register_shape', 'resetscreen',
           'screensize', 'setup', 'Terminator', 'setworldcoordinates', 'textinput', 'title', 'tracer', 'turtles',
           'update', 'window_height', 'window_width', 'write_docstringdict', 'done', 'circle', 'clear', 'clearstamp',
           'clearstamps', 'clone', 'color', 'degrees', 'distance', 'dot', 'down', 'end_fill', 'end_poly', 'fd',
           'fillcolor', 'filling', 'forward', 'get_poly', 'getpen', 'getscreen', 'get_shapepoly', 'getturtle', 'goto',
           'heading', 'hideturtle', 'home', 'ht', 'isdown', 'isvisible', 'left', 'lt', 'onclick', 'ondrag', 'onrelease',
           'pd', 'pen', 'pencolor', 'pendown', 'pensize', 'penup', 'pos', 'position', 'pu', 'radians', 'right', 'reset',
           'resizemode', 'rt', 'seth', 'setheading', 'setpos', 'setposition', 'settiltangle', 'setundobuffer', 'setx',
           'sety', 'shape', 'shapesize', 'shapetransform', 'shearfactor', 'showturtle', 'speed', 'st', 'stamp', 'tilt',
           'tiltangle', 'towards', 'turtlesize', 'undo', 'undobufferentries', 'up', 'width', 'write', 'xcor', 'ycor']

```

![VIPCODE-少儿Python编程，打开人工智能时代的大门](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3xca3d370j31wk0tghb0.jpg)

![VIPCODE-少儿Python编程，打开人工智能时代的大门](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3xcafotbyj31v60qmts4.jpg)

![VIPCODE-少儿Python编程，打开人工智能时代的大门](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3xcbfs8wbj31vk0pu1kx.jpg)

![VIPCODE-少儿Python编程，打开人工智能时代的大门](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3xcbzu0vij31vy0p2kc6.jpg)

**每天晚上7点-8点，半小时学习，半小时创作
每天一小时，7天掌握7个类型的小程序
课程目录
我们在课程中为孩子搭梯子，难度逐渐提高，新知识学习和老知识复习合理搭配，循序渐进**

关注公众号转发文章至朋友圈集赞99个赞即可领取十节IT编程课程，还享报名8折优惠，名额有限，先到先得。



少儿 编程培训

针对6-18岁,名师一对一指定学习方案

少儿 编程培训 中国青少儿逻辑思维提升中心研发能力



20天编程基础知识学习，覆盖25个编程基础知识点，25个基础知识点对任何编程语言都很重要。了解了25个点就从整体上掌握了程序的基本结构以及编程的基本思路！ 





被编进小学教材、纳入高考科目
python的时代已经到来
领先同龄人10年
别再观望，战斗才刚打响



python 30节课





第一阶段
基础编程逻辑的认知
循环语句的详细使用
画线、画图、转角等操作
if条件判断语句的执行流程
变量、赋值、随机数的讲解

第二阶段
基础数据结构的使用
文本数据类型的操作
利用循环访问可迭代的内容
输入、输出函数的综合应用
列表的追加、插入、倒置等函数

第三阶段
循环语句
判断语句
分支语句
利用编程思维解决现实问题
用python去解决奥数题目
文本数据类型

第四阶段
数据类型转换
代码提取
输入输出
列表操作
从面向过程到面向对象